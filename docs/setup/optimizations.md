This section is intended to describe some tips & tricks to optimize a CiviCRM system.

## Smarty

### Disable Compile Check

By default the Smarty option `compile_check` is set to `TRUE` which forces Smarty templates to be recompiled on every call.
Recompiling does not need to happen unless a template or config file is changed. Typically you enable this during development, and disable for production.
To disable this in CiviCRM, edit this constant in `civicrm.settings.php`:

```php
define('CIVICRM_TEMPLATE_COMPILE_CHECK', FALSE);
```

## Smart Group and ACL Performance

CiviCRM has caching for smart groups and ACLs. However, the configuration that works best is very site specific. If you take no action you will get the following behaviour:
- Every time a contact is edited the ACL caches (`civicrm_acl_cache`, `civicrm_acl_contact_cache`) will be emptied, regardless of whether you use ACLs.
- Every time a contact is edited any smart groups that were created more than 5 minutes ago will be deleted from the `civicrm_group_contact_cache` table.
- Whenever a smart group is accessed the cache for that group is rebuilt.
- Whenever a user who does not have 'edit all contacts' and 'view all contacts' attempts to do anything in CiviCRM the ACL cache for that contact will be rebuilt.

This is likely NOT optimal for your site and there are settings and jobs you can tweak.

**Firstly - it is worth thinking about whether you NEED the caches to be populated.**

- if your site doesn't use ACLs then although CiviCRM will attempt to populate the ACL caches it will never have any data to put in them. In this case there is no advantage to ever cleaning up the ACL caches and the clearing of these caches, even when empty, is a known cause of deadlocks (which means the server can't handle all the requests and some of them fail).

- for smart groups it is likely to be less clear cut. All sites generally use smart group but there are differences in how they use them. The cases where the smart group cache is helpful are when you are frequently using the same groups (within the smart group time out period) or when you want your users to be able to see all the groups a contact is a member of. The smart group cache (`civicrm_group_contact_cache`) was added to CiviCRM for this latter scenario. However, supporting this comes at a high performance cost both when the caches are populated and when they are cleared out. This might be worth it on sites of a few thousand contacts but is likely to crash medium+ sized sites. 

**Settings/ crons you can adjust**
Not all these settings are exposed in the UI but they can all be accessed through API Explorer.

- `contact_smart_group_display` (**Administer > Customize Data and Screens > Display Preferences**). By default this is set to 'Show Smart groups on demand'. This means the group will show as a closed accordian and if the user wants to see all the contacts the person is a member of they can click to expand it from their groups tab. Depending on the size of your site and whether you have the cron set up to rebuild smart groups this will either show the groups the contact is a member of, or crash your site. It's important to understand that each smart group is a query and the only way to know if a contact is in a given query is to run it - so every smart group query needs to be run to determine if a contact is a member of them. Smart group caching was added to support the ability to provide this accordian but the ability to disable the accordian (but not to fully disable smart group caching) was added when it was discovered that it can bring a server to its knees. (This is also the reason why smart groups are not included in exports when you export groups and are not shown on the My Contact dashboard).

- Rebuild Smart Group Cache [scheduled job](../setup/jobs.md). If you have the above smart group accordian enabled AND it is used frequently it MAY make sense to enable this job. This job populates the smart group cache by running the query for every smart group you have. It only makes sense to run this if people are expanding the aforementioned accordian more often than your smart group cache time out. This cron can take a long time to run, can crash other processes and will usually make the cache clearing processes slower and more likely to crash (as the cache will hold more data).

- `smart_group_cache_refresh_mode` - this is not exposed in the UI. If you have access to set up cron jobs you can set this to `TRUE` and set up a cron and instead of it trying to cleanup caches every time you edit a contact it will do so when the cron runs. In general the queries involved in clearing out the group `civicrm_group_contact_cache` are more likely to crash your site than the ones used in building them (unless your users are expanding the smart group accordian). Enabling this setting can change it so they run every few minutes rather than many times a second.

- Flush smart group cache job. In conjunction with the above setting you can add a [scheduled job](../setup/jobs.md). You need to manually enter the entity ('Job') and the action ('group_cache_flush'). This should run slightly more often than you smart group cache timeout is set to.

- `acl_cache_refresh_mode` - this is not exposed in the UI. This is the same as the `smart_group_cache_refresh_mode` setting except for the ACL cache. Busy sites in particular will benefit from fewer deadlocks with this set (constant conflicting attempts to clear this cache are a major source of deadlocks even on sites with no ACLs configured.)

- Flush ACL cache job. This is the same as the smart group cache flush job but the action is `acl_cache_flush`. If you don't use ACLs you do not need to configure this but you still need to add the above setting to get the performance benefit.

- `smartGroupCacheTimeout` (**Administer > Customize Data and Screens > Search Preferences**). This is how long the resolved contacts in a given group remain in the cache table before being cleaned out (on edit or via cron). Traditional wisdom is that this should be between 5 and 10 minutes but many sites will get better peformance by setting this to zero. The key thing to consider is how often your contacts access the same smart group more than once within 5 minutes. If the group is already cached it may be quicker to retrieve (depending on how fast the query runs). However, the more rows in the smart group cache table the slower it is to resolve new groups into it and the slower it is to clear it out. While the official recommentation is to set this to 'at least 5 minutes' that was written without testing on a real site so the best thing you can do is experiment with this setting and don't be afraid to test zero as a valid option.

## Exclude dirs that do not need to be scanned

This was introduced in 5.29.

CiviCRM has a utility called `CRM_Utils_File::findFiles()` which is used to locate various glob patterns of file, such as `*.info` files to find extensions and `*.xml` files etc. By default this will search every directory under the docroot, except those that begin with a dot, e.g. `.git`. However, there are a number of directories that may be large but will never contain things CiviCRM is looking for, so you can improve the efficiency of this process by excluding them.

You do this by providing a regex in a constant called `CIVICRM_EXCLUDE_DIRS_PATTERN` in your `civicrm.settings.php` file. The regex will be used to match on file system *absolute* paths, e.g. `/var/www/example.org/sites/default/files/civicrm/ext`.

For example, you might consider (if it makes sense in your environment) the following, which would exclude:

- All dirs starting with a dot (`.git`, `.svn`, `.secret`, `.env` etc.)
- All `node_modules` dirs - these can be enormous.
- All `js` and `css` dirs - if you're sure CiviCRM doesn't need to look in there (it doesn't as of 5.29)
- All `bower_components` and `packages` and `vendor` dirs - these contain 3rd party library code. On Drupal8 we need to *include* `vendor/civicrm/civicrm-core/ext` otherwise core extensions won't be found.
- The `sites/default/files/private` dir, which is a local example that might be where Drupal's private managed files are kept.

```php
<?php
if (!defined('CIVICRM_EXCLUDE_DIRS_PATTERN')) {
   define('CIVICRM_EXCLUDE_DIRS_PATTERN', '@/(?:\.|node_modules|js/|css/|bower_components|packages/|vendor/(?!civicrm/civicrm-core/ext)|sites/default/files/private)@');
}
```

Depending on a lot of local factors, this can speed up cache clears, extension installs etc. significantly (e.g. 10× faster has been measured).

## If you use mysql replication consider RPOW to reduce queries on production

The [RPOW](https://github.com/totten/rpow) extension can redirect read only queries to another DB server. This can be helpful if an occassional slow search causes hangs on your main server. You should consider using REDIS as your cache methodology if you are using RPOW (or anyway if you can).
