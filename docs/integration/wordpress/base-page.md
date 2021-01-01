# Base page

CiviCRM public pages are built by overlaying the WordPress front page with CiviCRM content, thus using the front page's template. To use a different template, we must tell Civi to use a different page as a starting point. Follow these steps:

* Add a new WordPress page. We can call it anything, but let's give it a permalink of 'civi'.
* Leave the body blank (it won't be used anyway).
* Select the desired theme template for the page.
* Click Publish.
* Edit your base page through the admin menu at **Administer > Settings > CMS Database Integration**

Note the legacy method was to define it in the `wp-content/plugins/civicrm/civicrm.settings.php` file - e.g by setting `define( 'CIVICRM_UF_WP_BASEPAGE', 'civi');`

URLs that were previously written like:

```
http://example.org/?page=CiviCRM&q=civicrm/contribute/transact&reset=1&id=X
```

will now be written like

```
http://example.org/civi?page=CiviCRM&q=civicrm/contribute/transact&reset=1&id=X
```

For more details, check the issue: [CRM-10682](http://issues.civicrm.org/jira/browse/CRM-10682)
