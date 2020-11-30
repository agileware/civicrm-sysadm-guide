# Clean URLs

Support for clean URLs in WordPress front-end (user-facing) pages was introduced in CiviCRM 5.13 and enabled by default from CiviCRM 5.23.

Prior to this change CiviCRM URLs had the format of a base page (e.g. `/civicrm`) followed by a query string. For example a Contribution Page would look something like this:

`https://example.org/civicrm?civiwp=CiviCRM&q=civicrm/contribute/transact&reset=1&id=1` 

With clean URLs enabled this would become: 

`https://example.org/civicrm/contribute/transact/?reset=1&id=1`

The format of other clean URLs is as follows:

Page | URL
-----|-----
Contribution Page | `https://example.org/civicrm/contribute/transact/?reset=1&id=1`
Event Info | `https://example.org/civicrm/event/info/?reset=1&id=1`
Event Registration | `https://example.org/civicrm/event/register/?reset=1&id=1`
Profile (edit mode) | `https://example.org/civicrm/profile/edit/?gid=1&reset=1`
Profile (create mode) | `https://example.org/civicrm/profile/create/?gid=1&reset=1`
Profile (listings mode) | `https://example.org/civicrm/profile/?gid=1&reset=1`
User's Contact Dashboard | `https://example.org/civicrm/user/?reset=1`

## Enable clean URLs

Clean URLs should be enabled by default for sites installed with CiviCRM 5.23 or later. If your site was installed prior to this you will need to enable clean URLs manually. You can do so as follows.

* Start by locating and backing up your `civicrm.settings.php` file (see [Backup the settings file](https://docs.civicrm.org/installation/en/latest/wordpress/#backup-the-settings-file)).

* Find the following section of code in `civicrm.settings.php` (usually near the end of the file).

<!-- markdownlint-disable MD046 -->
```php
if (!defined('CIVICRM_CLEANURL')) {
  if ( function_exists('variable_get') && variable_get('clean_url', '0') != '0') {
    define('CIVICRM_CLEANURL', 1 );
  }
  elseif ( function_exists('config_get') && config_get('system.core', 'clean_url') != 0) {
    define('CIVICRM_CLEANURL', 1 );
  }
  else {
    define('CIVICRM_CLEANURL', 0);
  }
}
```
<!-- markdownlint-enable MD046 -->

* Replace the above code with:

<!-- markdownlint-disable MD046 -->
```php
if (!defined('CIVICRM_CLEANURL')) {
  // check for Drupal clean URLs
  if ( function_exists('variable_get') && variable_get('clean_url', '0') != '0') {
    define('CIVICRM_CLEANURL', 1 );
  }
  elseif ( function_exists('config_get') && config_get('system.core', 'clean_url') != 0) {
    define('CIVICRM_CLEANURL', 1 );
  }
  // check for WordPress clean URLs
  elseif( function_exists('get_option') && get_option('permalink_structure') != '' ) {
    define('CIVICRM_CLEANURL', 1 );
  }
  else {
    define('CIVICRM_CLEANURL', 0 );
  }
}
```
<!-- markdownlint-enable MD046 -->

* Go to **Administer > System Settings > CMS Database Integration** and make sure the base page points to an existing WordPress page (usually `/civicrm`).

* Go to **Settings >> Permalinks**. This will flush the rewrite rules in WordPress and enable clean URLs to work.

    ![WordPress Permalink settings.](../../img/wp-permalinks.png)

* Clean URLs are now enabled. 

!!! note "Notes"
    Old style URLs will still work so you won't break any existing links to your website.

    Clean URLs don't effect how shortcodes work in CiviCRM.
