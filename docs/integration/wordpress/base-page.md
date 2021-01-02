# Base page

CiviCRM public pages are built by overlaying the WordPress front page with CiviCRM content, thus using the front page's template. To use a different template, we must tell CiviCRM to use a different page as a starting point. Follow these steps:

* Add a new WordPress page. We can call it anything, but let's give it a permalink of `civicrm`.
* Leave the body blank (it won't be used anyway).
* Select the desired theme template for the page.
* Click Publish.
* Add the URL for the base page at **Administer > Settings > CMS Database Integration**.

URLs that were previously of the form:

```
http://example.org/?civiwp=CiviCRM&q=civicrm/contribute/transact&reset=1&id=X
```

will now look like this:

```
http://example.org/civicrm?civiwp=CiviCRM&q=civicrm/contribute/transact&reset=1&id=X
```

!!! note
    The legacy method was to define the base page in the `wp-content/plugins/civicrm/civicrm.settings.php` file - e.g by setting `define('CIVICRM_UF_WP_BASEPAGE', 'civicrm');`

For more details, check the issue: [CRM-10682](http://issues.civicrm.org/jira/browse/CRM-10682).
