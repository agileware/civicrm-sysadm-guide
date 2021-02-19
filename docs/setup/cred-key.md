# Credential Encryption Keys

The way CiviCRM stores SMTP passwords (and other credentials) will changed in version 5.34. 

You will need to add a credential encryption key to your `civicrm.settings.php` file. This key will be used to encrypt credentials in CiviCRM. Changing this key will invalidate stored credentials.

???+ note "Generating a credential encryption key."
    The credential encryption key should, ideally, be a 256-bit (32 byte) randomly generated string.

    Some examples to generate a suitable credential key are given below.
    
    **PHP:**
    
    ``` php
    php -r 'echo trim(base64_encode(random_bytes(32)), "=") . "\n";'
    ```

    **Bash:**

    ``` bash
    od  -vN "16" -An -tx1             /dev/urandom | tr -d " \n" ; echo
    ```

    **PowerShell:**
    ``` powershell
    -join (((48..57)+(65..90)+(97..122)) * 80 |Get-Random -Count 32 |%{[char]$_})
    ```
    
Once you have your 32 byte key (recommended!) you should add this to your `civicrm.settings.php` file as follows:

``` php
define('CIVICRM_CRED_KEYS', '::<KEY>');
```

Note that the `::` before your key is required.

??? caution "Changing your credential encryption key."
    If you change your credential encryption key at any point post-upgrade you need to tell CiviCRM to rotate the key and re-encrypt credentials with an APIv4 command. As an example you could run:

    ``` bash
    cv api4 System.rotateKey tag=CRED
    ```

    For more information please see [this core PR](https://github.com/civicrm/civicrm-core/pull/19251).
