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

### Generate secrets {:#generate-secrets}

Secrets are automatically initialized during installation of CiviCRM. However, if you upgrade a pre-existing site, then you may need to add values for any missing secrets.

Each secret should be generated randomly, and it should be long enough to resist attacks.

??? question "How long should a secret be?"

    Security analysts measure the length of a secret in bits.  However, in a configuration screen, you will usually write a secret in a more familiar notation (e.g. alphanumeric). This table translates from bits into more familiar measures:

    | Binary digits | Binary bytes | Hexadecimal | Alphanumeric / Base64 |
    | -- | -- | -- | -- |
    | 96 bits | 12 bytes | 24 digits | ~16 characters |
    | 128 bits | 16 bytes | 32 digits | ~22 characters |
    | 256 bits | 32 bytes | 64 digits | ~43 characters |
    | 384 bits | 48 bytes | 96 digits | ~65 characters |

    For this document, we will mostly work with alphanumeric and Base64 representations.

    The table presents "Alphanumeric" and "Base64" together because they are very similar. The length in this column is approximate because the optimal length may be +/- a character depending on context and value.

    <!-- Alphanum is 5.95 bits per char, which doesn't divide evenly. Base64 is 6 bits per char, which may not divide evenly, and it has padding fluctuations. -->

??? example "Generate secrets with PHP-CLI"

    ```bash
    ## Generate a 256-bit key
    $ php -r 'echo trim(base64_encode(random_bytes(32)), "=") . "\n";'
    voSjOOcifMwoLEqS85Ho1pV9N5GN0Qpy8PQwKmn3zuI

    ## Generate a 128-bit key
    $ php -r 'echo trim(base64_encode(random_bytes(16)), "=") . "\n";'
    ymy53cCPn/yGpVzr
    ```

??? example "Generate secrets with bash"

    ``` bash
    ## Generate a 256-bit key
    $ head -c32 /dev/urandom | base64
    83cQTAyTRcCqbIG5DCWAJzgfuq5hhSp7DEn4nuVo8DM=

    ## Generate a 128-bit key
    $ head -c16 /dev/urandom | base64
    3TKh9Mjztx5NsaX6
    ```

??? example "Generate secrets with PowerShell"

    ``` powershell
    ## Generate a 256-bit key
    -join (((48..57)+(65..90)+(97..122)) * 80 |Get-Random -Count 43 |%{[char]$_})
    M1kQMRrZioO0EEsYhVwvvjc97WyewBMisSuX9r75vnc

    ## Generate a 128-bit key
    -join (((48..57)+(65..90)+(97..122)) * 80 |Get-Random -Count 16 |%{[char]$_})
    OjVxMkq89tdDP96g
    ```

These formulas primarily output letters and numbers, but they may sometimes output three symbols (`/` `+` `=`). These symbols are legal in most cases.

??? tip "Tip: Avoid symbols in `CIVICRM_SITE_KEY`"

    The [CIVICRM_SITE_KEY](#civicrm_site_key) may be used in `curl` or `wget` commands. It will be easier to write
    these commands if there are no symbols. It is safe to substitute or remove symbols, provided that the resulting
    key still has enough random letters and numbers.

## Secrets

### CIVICRM_CRED_KEYS

__Description__: Some credentials (such as the SMTP password) may be stored in the database in encrypted format.  Setting `CIVICRM_CRED_KEYS` will enable this feature.

__Examples__:

```php
<?php
define('CIVICRM_CRED_KEYS', '::RANDOM');
define('CIVICRM_CRED_KEYS', '::RANDOM_NEW ::RANDOM_OLD');
define('CIVICRM_CRED_KEYS', 'aes-cbc:hkdf-sha256:RANDOM');
define('CIVICRM_CRED_KEYS', 'aes-ctr:b64:RAN/DOM_NEW+= aes-cbc:b64:RAN/DOM_OLD+=');
define('CIVICRM_CRED_KEYS', 'plain');
```

__Format__:

* This is a space-delimited list of keys (ordered by priority).
* Put the preferred key first.  You may retain old/deprecated keys to ensure that old data can be decrypted.
* Each key is in format `[<cipher-suite>]:[<key-encoding>]:[<key-content>]`.
* The `<cipher-suite>` indicates the cryptographic algorithm and mode. It may be one of:
    * `aes-cbc` (*default*): AES-256 with CBC block mode
    * `aes-cbc-hs`: AES-256 with CBC block mode plus HMAC-SHA256 authentication
    * `aes-ctr`: AES-256 with CTR block mode
    * `aes-ctr-hs`: AES-256 with CTR block mode plus HMAC-SHA256 authentication
    * `plain`: Plain-text; no encryption (*Note: This does not use key-encoding or key-content.*)
      <!-- Note: The default does not use HMAC-SHA256 because this is a data-storage system not a data-transit system. -->
* The `<key-encoding>` indicates how the `<key-content>` is written. The `<key-encoding>` may be one of:
    * `b64`: The `<key-content>` is a Base64-encoded binary value. It is compatible with any cipher-suite, provided the length is correct.
    * `hkdf-sha256` (*default*): The `<key-content>` is a passphrase. This is compatible with 256-bit cipher-suites (including `aes-*`).
       For an optimal value, use a random alphanumeric string with 43 characters, or use a random hexadecimal string with 64 digits.

??? howto "How-To: Rotate the encryption key"

    1. [Generate a new key](#generate-secrets)
    2. Add the new key as the *first* item in `CIVICRM_CRED_KEYS`.
    3. Run `System.rotateKey`, as in:
       ``` bash
       cv api4 System.rotateKey tag=CRED
       ```
    4. Remove the old key(s) from `CIVICRM_CRED_KEYS`.

    For more information please see [this core PR](https://github.com/civicrm/civicrm-core/pull/19251).

### CIVICRM_SIGN_KEYS

__Description__: The signing key is used to sign and verify JSON Web Tokens. For example, when the system sends an automated email with a URL, the URL may include a "JWT" (with the recipient ID,  expiration date, and digital signature). The security of the link depends on having a secret signing key.

__Examples__:

```php
<?php
define('CIVICRM_SIGN_KEYS', 'jwt-hs256::RANDOM');
define('CIVICRM_SIGN_KEYS', 'jwt-hs256::RANDOM_NEW jwt-hs256::RANDOM_OLD');
define('CIVICRM_SIGN_KEYS', 'jwt-hs256:hkdf-sha256:RANDOM');
define('CIVICRM_SIGN_KEYS', 'jwt-hs256:b64:RAN/DOM+=');
```

__Format__:

* This is a space-delimited list of keys (ordered by priority).
* Put the preferred key first.  You may retain old/deprecated keys to ensure that old tokens remain valid.
* Each key is in format `[<cipher-suite>]:[<key-encoding>]:[<key-content>]`.
* The `<cipher-suite>` indicates cryptographic algorithms used to generate/validate signatures. It may be one of:
    * `jwt-hs256` (*recommended*): JSON Web Token with HMAC-SHA256
    * `jwt-hs384`: JSON Web Token with HMAC-SHA384
    * __Note__: This must be set explicitly. Do not rely on a default.
* The `<key-encoding>` indicates how the `<key-content>` is written. The `<key-encoding>` may be one of:
    * `b64`: The `<key-content>` is a Base64-encoded binary value. It is compatible with any cipher-suite, provided the length is correct.
    * `hkdf-sha256` (*default*): The `<key-content>` is a passphrase. This is compatible with 256-bit cipher-suites (including `jwt-hs256`).
       For an optimal value, use a random alphanumeric string with 43 characters, or use a random hexadecimal string with 64 digits.

??? howto "How-To: Rotate the signing key"

    1. [Generate a new key](#generate-secrets)
    2. Add the new key as the *first* item in `CIVICRM_SIGN_KEYS`.
    4. If you are rotating the key in reaction to a compromie (e.g. "Bad actors have a copy of the old key"), then you should 
       promptly disable the old key. This will invalidate any signatures or tokens based on the old key.
    3. If you are rotating the key proactively (e.g. "Policy dictates that keys should change every 6 months"), then you
       should allow a grace period. Keep the old key, wait a few weeks, then remove it.

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
* The random string is usually alphanumeric (no symbols). This ensures that it is easy to call utility scripts (e.g `bin/cron.php`) via `curl`/`wget`.
* The traditional recommendation for this value has been 16-32 alphanumeric characters (96-190 bits).
  <!-- AFAIK, there is no particular math behind this range. -->
