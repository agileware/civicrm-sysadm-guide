# Secret keys

CiviCRM relies on a handful of secret keys to facilitate system security, secure credentials and authenticate users.

| Name | Purpose | Recommended Size | Version |
| -- | -- | -- | -- |
| `CIVICRM_CRED_KEYS` | Encrypt stored credentials | 256 bits | v5.34+ |
| `CIVICRM_SIGN_KEYS` | Verify digital signatures | 256 bits | v5.36+ |
| `CIVICRM_SITE_KEY`  | Multiple uses | 96 - 190 bits | v2+ |

<!-- 128-bits here is a bit arbitray - past docs advised 16-32 chars, and this -->

In this chapter, we will consider how to manage these secrets and conclude with extra discussion about how each secret works.

## Management

### Edit settings

Secret keys are defined in a PHP file, `civicrm.settings.php`. For example, the file might declare:

```php
<?php
if (!defined('CIVICRM_CRED_KEYS')) {
  define('CIVICRM_CRED_KEYS', 'aes-cbc::ExAMPlevQOa1H2g7k0Wfp1WEh/VvC7botciDX7nDErg');
}
if (!defined('CIVICRM_SIGN_KEYS')) {
  define('CIVICRM_SIGN_KEYS', 'jwt-hs256::dUmmYdbEjbbIxjb0LnuJj9+cnWRwbEZU8o/0y98N27s');
}
if (!defined('CIVICRM_SITE_KEY')) {
  define('CIVICRM_SITE_KEY', 'DEM0a528jRFv2x3s');
}
```

The file location will depend on the environment:

* **In Drupal** , `civicrm.settings.php` is located in `sites/default`.

* **In Joomla,** there are two copies of the `civicrm.settings.php` file.  By default these are located in `administrator/components/com_civicrm` and `components/com_civicrm`. It is important to edit both and keep them synchronized.

* **In WordPress** , `civicrm.settings.php` is usually located in `wp-content/uploads/civicrm`. In some older deployments the location may be `wp-content/plugins/civicrm`.

To view or change the secrets, simply edit this file.

<!-- WISHLIST: Add some discussion about the externalizing the keys, eg `file_get_contents('/etc/keys/civicrm_cred_keys')` and how this can play into more secure backup policy -->

### Generate secrets

Secrets are automatically initialized during installation of CiviCRM. However, if you upgrade a pre-existing site, then you may need to add values for any missing secrets.

Each secret should be generated randomly, and it should be long enough to resist attacks.

??? question "How long should a secret be?"

    Security analysts measure the length of a secret in bits.  However, in a configuration screen, you will usually write a secret as alphanumeric
    characters (A-Z, a-z, 0-9) or as hexadecimal digits (0-9, A-F). This table translates from bits into more familiar measues:

    | Bits | Bytes | Hexadecimal | Alphanumeric / Base64 |
    | -- | -- | -- | -- |
    | 96 bits | 12 bytes | 24 digits | ~16 characters |
    | 128 bits | 16 bytes | 32 digits | ~22 characters |
    | 256 bits | 32 bytes | 64 digits | ~43 characters |
    | 384 bits | 48 bytes | 96 digits | ~65 characters |

    For this document, we will mostly work with alphanumeric representations.

    <!-- Base64 is 6 bits per char. Alphanumeric is 5.95 bits per char. Key lengths are normalized via HKDF. -->

Here are a few ways to generate keys:

??? example "Generate keys with PHP-CLI"

    ```bash
    ## Generate a 256-bit key
    $ php -r 'echo trim(base64_encode(random_bytes(32)), "=") . "\n";'
    voSjOOcifMwoLEqS85Ho1pV9N5GN0Qpy8PQwKmn3zuI

    ## Generate a 96-bit key
    $ php -r 'echo trim(base64_encode(random_bytes(12)), "=") . "\n";'
    ymy53cCPn/yGpVzr
    ```

??? example "Generate keys with bash"

    ``` bash
    ## Generate a 256-bit key
    $ head -c32 /dev/urandom | base64
    83cQTAyTRcCqbIG5DCWAJzgfuq5hhSp7DEn4nuVo8DM=

    ## Generate a 96-bit key
    $ head -c12 /dev/urandom | base64
    3TKh9Mjztx5NsaX6
    ```

??? example "Generate keys with PowerShell"

    ``` powershell
    ## Generate a 256-bit key
    -join (((48..57)+(65..90)+(97..122)) * 80 |Get-Random -Count 43 |%{[char]$_})
    M1kQMRrZioO0EEsYhVwvvjc97WyewBMisSuX9r75vnc

    ## Generate a 96-bit key
    -join (((48..57)+(65..90)+(97..122)) * 80 |Get-Random -Count 16 |%{[char]$_})
    OjVxMkq89tdDP96g
    ```

These formulas occasionally generate a few symbols (`/` `+` `=`).  These characters are legal, but you may find some tasks easier if you change them
to arbitrary alphanumerics.

## Secrets

### CIVICRM_CRED_KEYS

__Description__: Some credentials may be stored in the database in encrypted format.  Setting `CIVICRM_CRED_KEYS` will enable this feature.

__Examples__:

```php
<?php
define('CIVICRM_CRED_KEYS', 'aes-cbc:hkdf-sha256:RANDOM_1');
define('CIVICRM_CRED_KEYS', 'aes-ctr-hs:b64:RANDOM_2 aes-ctr-hs:b64:RANDOM_3');
define('CIVICRM_CRED_KEYS', '::MY_NEW_KEY ::MY_OLD_KEY');
```

__Format__:

* This is a space-delimited list of keys (ordered by priority).
* Put the preferred key first.  You may retain old/deprecated keys to ensure that old data can be decrypted.
* Each key is in format `<cipher-suite>:<key-encoding>:<key-content>`.
* `<cipher-suite>` may be `aes-cbc`, `aes-ctr`, `aes-cbc-hs`, `aes-ctr-hs`. (These are combinations of AES-256 with CBC or CTR mode - and optionally with an HMAC signature). If blank, it defaults to `aes-cbc`.
* `<key-encoding>` may be `hkdf-sha256` or `b64`. `hkdf-sha256` is a more forgiving format that accommodates secrets of varying size.  If blank, it defaults to `hkdf-sha256`.
* Generally, the `<key-content>` should match the cipher-suite and key-encoding. Specifically, for AES with HKDF, this would be satisfied by a 43 character case-sensitive alphanumeric or a 64-digit hexadecimal.

??? caution "Changing your credential encryption key."

    If you change your preferred credential encryption key, you need to tell CiviCRM to rotate the key and re-encrypt credentials with an APIv4 command. As an example you could run:

    ``` bash
    cv api4 System.rotateKey tag=CRED
    ```

    For more information please see [this core PR](https://github.com/civicrm/civicrm-core/pull/19251).

### CIVICRM_SIGN_KEYS

__Description__: The signing key is used to generate and verify security tokens.

__Examples__:

```php
<?php
define('CIVICRM_SIGN_KEYS', 'jwt-hs256:hkdf-sha256:RANDOM_1');
define('CIVICRM_SIGN_KEYS', 'jwt-hs256::RANDOM_2 jwt-hs256::RANDOM_3');
define('CIVICRM_SIGN_KEYS', 'jwt-hs256:b64:RANDOM_4 jwt-hs256:b64:RANDOM_5');
```

__Format__:

* This is a space-delimited list of keys (ordered by priority).
* Put the preferred key first.  You may retain old/deprecated keys to ensure that old tokens remain valid.
* Each key is in format `<cipher-suite>:<key-encoding>:<key-content>`.
* `<cipher-suite>` may be `jwt-hs256` (JWT with HMAC-SHA256) or `jwt-hs384` (JWT with HMAC-SHA384).  If blank, it defaults to `jwt-hs256`.
* `<key-encoding>` may be `hkdf-sha256` or `b64`. `hkdf-sha256` is a more forgiving format that accommodates secrets of varying size.  If blank, it defaults to `hkdf-sha256`.
* Generally, the `<key-content>` should match the cipher-suite and key-encoding.  Specifically, for `jwt-hs256` with `hkdf-sha256`, this would be satisfied by a 43 character case-sensitive alphanumeric or a 64-digit hexadecimal.


### CIVICRM_SITE_KEY

__Description__:

* The CIVICRM_SITE_KEY is used for multiple purposes.
* The primary purpose involves select utility scripts (eg `bin/cron.php`, `extern/rest.php`). Any request for these scripts must include the `site_key` in addition to user credentials.

__Examples__:

```php
<?php
define('CIVICRM_SITE_KEY', 'RANDOM_123');
```

__Format__:

* This is a singular random string.
* The traditional recommendation for this value has been 16-32 alphanumeric characters (96-190 bits).
  <!-- AFAIK, there is no particular math behind this range. -->

