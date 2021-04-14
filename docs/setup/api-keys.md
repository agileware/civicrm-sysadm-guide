# API Keys

## Overview

If a third-party application needs to read or write data in CiviCRM, then you may need to generate an *API key*. 

Each API key is attached to a specific **contact record** or **user account** which determines the [permissions](https://docs.civicrm.org/dev/en/latest/security/permissions/). When configuring a new application, you should determine the contact ID and double-check their roles or permissions.

Each API key must be **unique** and **difficult to guess**. To generate a good API key, use a random sequence of 20+ alphanumeric characters.

## Assigning keys

Assuming you have identified a suitable **contact record**, you may assign a key through any of the following tools:

### "API Key" Extension

The optional [API Key Extension](https://civicrm.org/extensions/api-key) provides a simplified web interface for managing keys.

![API Key Extension](https://civicrm.org/sites/civicrm.org/files/API_key.png)

### APIv3 Explorer

The "APIv3 Explorer" provides a powerful, built-in way to manipulate CiviCRM data through the administrative web-site. 

See ["Stack Exchange: How do I set up an API key for a user?"](http://civicrm.stackexchange.com/questions/9945/how-do-i-set-up-an-api-key-for-a-user)

### CLI

If you have shell access and [cv](https://github.com/civicrm/cv), then you can generate and assign API keys:

1. Generate a random key:

    ```bash
    head -c32 /dev/urandom | base64 | tr -d '+/='
    ```
    ```bash
    T0PS3CR3T6ateeUXgTOJ9e3PpXvDZF9Al55WAmBsNQI
    ```

2. Assign this new value to the contact. In this example, we update contact `#100`:

    ```bash
    cv api contact.create id=100 api_key="T0PS3CR3T6ateeUXgTOJ9e3PpXvDZF9Al55WAmBsNQI"
    ```

### MySQL

If you have direct access to the MySQL database, then you can generate and assign API keys:

1. Generate a random key:

    ```sql
    SELECT @api_key := REPLACE(REPLACE(REPLACE(
      TO_BASE64(RANDOM_BYTES(32)), "+", ""), "/", ""), "=", "") AS api_key;
    ```
    ```sql
    +---------------------------------------------+
    | api_key                                     |
    +---------------------------------------------+
    | T0PS3CR3T6ateeUXgTOJ9e3PpXvDZF9Al55WAmBsNQI |
    +---------------------------------------------+
    ```

2. Assign this new value to the contact. In this example, we update contact `#100`:

    ```sql
    UPDATE civicrm_contact SET api_key = @api_key WHERE id = 100;
    ```

