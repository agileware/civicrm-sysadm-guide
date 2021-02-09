# WordPres integration

## CiviCRM extensions

The following CiviCRM extensions have been developed to improve the integration between CiviCRM and WordPress.

### [Wordpress integration for CiviMail with Mosaico](https://github.com/skyslasher/de.ergomation.wp-civi-mosaico)

This CiviCRM extension integrates WordPress with the CiviCRM mail editor replacement Mosaico. The gallery uses the WordPress media library. It also contains an enhanced Versafix template with a new template block. This block comes with a property editor that shows all available WordPress posts. It applies the post title, excerpt with adjustable length an a read more button with just one click. In addition, section margins can be applied and href anchors can be set to allow an easy in-mail navigation via a TOC. As a bonus, embedding images into HTML mails can be optionally set (Mailings->WordPress - Mosaico integration settings). This feature generates larger emails, but images are immediately visible when viewing the mail, without having to allow remote images in the mail program.

## Plugins

There are several plugins that have been developed to improve the integration between CiviCRM and WordPress.

### [CiviCRM Admin Utilities](https://wordpress.org/plugins/civicrm-admin-utilities/)

CiviCRM Admin Utilities modifies CiviCRM’s behaviour in single site and multisite installs. It does a number of useful things:

* Provides quick links to miscellaneous CiviCRM tasks
* Adds a handy CiviCRM Shortcuts menu to the WordPress Admin Bar
* Resolves issues with the CiviCRM Menu and WordPress Access Control form 
* Allows you to choose which Post Types the CiviCRM shortcode button appears
* Allows you to prevent various CiviCRM & Shoreditch stylesheets from loading
* In WordPress multisite, allows you to hide CiviCRM on sub-sites
* Provides an option for Wordpress styling throughout CiviCRM - creating a more unified Wordpress/CiviCRM experience. 

[Download](https://wordpress.org/plugins/civicrm-admin-utilities/)

### [WordPress CiviCRM UX](https://github.com/agileware/wp-civicrm-ux)

WordPress CiviCRM UX - A better user experience for integrating WordPress and CiviCRM.
This plugin adds many useful shortcodes, some new Caldera Forms smart tags and provides additional functionality to improve the user experience for integrating CiviCRM with a WordPress site.

Read the [WordPress CiviCRM UX usage documentation](https://github.com/agileware/wp-civicrm-ux/blob/master/USAGE.md) for more details.

[Download](https://github.com/agileware/wp-civicrm-ux/)

### [CiviCRM WordPress Profile Sync](https://wordpress.org/plugins/civicrm-wp-profile-sync/)

The CiviCRM WordPress Profile Sync plugin keeps the “First Name”, “Last Name”, “Email Address” and “Website” fields of a WordPress (and BuddyPress) user profile in sync with the corresponding fields of a CiviCRM contact. The synchronisation takes place regardless of whether the changes are made in WordPress, BuddyPress or CiviCRM.

[Download](https://wordpress.org/plugins/civicrm-wp-profile-sync/)

### [CiviCRM WordPress Member Sync](https://wordpress.org/plugins/civicrm-wp-member-sync/)

CiviCRM WordPress Member Sync keeps a WordPress user in sync with a CiviCRM membership by granting either a role or capabilities to a WordPress user who has that membership.

This enables you to have, among other things, members-only content on your website that is only accessible to current members as defined by the membership types and status rules that you set up in CiviCRM. This plugin is compatible with both **[Members](https://wordpress.org/plugins/members/)** and **[Groups](https://wordpress.org/plugins/groups/)** for managing member-only content in WordPress. See the installation section for details.

[Download](https://wordpress.org/plugins/civicrm-wp-member-sync/)

### [CiviCRM Groups Sync](https://develop.tadpole.cc/plugins/civicrm-groups-sync)

*CiviCRM Groups Sync* is a WordPress plugin that keeps Contacts in CiviCRM Groups in sync with WordPress Users in groups provided by the [Groups plugin](https://wordpress.org/plugins/groups/).

This allows you to create new Wordpress / CiviCRM groups and synchronise them in both directions.
It is not currently possible to disable the synchronisation or synchronise an existing group.

[Documentation](https://develop.tadpole.cc/plugins/civicrm-groups-sync/blob/master/README.md)

[Download](https://develop.tadpole.cc/plugins/civicrm-groups-sync)

### [CiviCRM ACF Integration](https://github.com/christianwach/civicrm-acf-integration)

CiviCRM ACF (Advanced Custom Fields) Integration is a WordPress plugin that enables integration between CiviCRM Entities and WordPress Entities with data synced via [Advanced Custom Fields](https://wordpress.org/plugins/advanced-custom-fields/) plugin.

[Documentation](https://github.com/christianwach/civicrm-acf-integration/blob/master/README.md)

[Download](https://github.com/christianwach/civicrm-acf-integration)

**Requirements**

To use this plugin, the following is needed:

* WordPress
* CiviCRM 5.x and above
* [Advanced Custom Fields](https://wordpress.org/plugins/advanced-custom-fields/) plugin installed

### [Caldera Forms CiviCRM](https://github.com/mecachisenros/caldera-forms-civicrm)

A WordPress plugin that integrates the [Caldera Forms plugin](https://wordpress.org/plugins/caldera-forms/) with CiviCRM.

The Caldera Forms CiviCRM plugin contains a set of form processors that interact with CiviCRM's API to retrieve, create and update data in CiviCRM. With this plugin, you can create responsive forms that expose CiviCRM fields and entities like Activities, Relationships, Tags, Groups and more.

Features:

* Add up to 10 Contacts on the same form
* Auto-populate form if the user is logged in
* Define Contact Type: Organization, Individual, Household, and Custom Contact Subtypes
* Map Custom Fields data
* Add Relationships to each contact
* Create Activities on form submission
* Checksum support to auto-populate form with URLs like _example.com/support?cid={contact.contact_id}&{contact.checksum}_

**Requirements**

To use this plugin, the following is needed:

* WordPress
* CiviCRM 4.6 and above
* [Caldera Forms](https://wordpress.org/plugins/caldera-forms/) to be installed

[Download](https://github.com/mecachisenros/caldera-forms-civicrm)

### [Contact Form 7 CiviCRM integration](https://wordpress.org/plugins/contact-form-7-civicrm-integration/)

This plugin adds integration for CiviCRM to contact form 7. With this plugin it is possible to submit a contact for to an external CiviCRM.

[Download](https://wordpress.org/plugins/contact-form-7-civicrm-integration/)

### [CiviCRM Event Calendar](https://github.com/osseed/com.osseed.eventcalendar/)

Event Calendar Extension allows you to show all CiviCRM events in a calendar by month, day or week. The setting page allows you to select which events should be shown on the calendar with the color you want for particular event type. The setting page configuration for event types filters allows you to filter by particular event types on calendar and change colors.

After installing the extension in CiviCRM - copy the folder `yourextensiondirectory/com.osseed.eventcalendar/wordpress/wordpress-event-calendar` to `wordpressdirectory/wp-content/plugins/` and activate the wordpress plugin.  You will then be able to add a shortcode to any page or post in which you would like to display a calendar of your events. 

[Download](https://github.com/osseed/com.osseed.eventcalendar/releases)

### [CiviEvent Widget](https://aghstrategies.com/civievent-widget)

You can use the CiviEvent widget to add two types of widgets for upcoming public events from CiviCRM. There’s no limit to the number of widgets you can add of either type. You can include the widgets in the sidebar like normal, or you can include them via shortcodes in the body of your posts.

The list functionality provides a basic, ﬂexible listing of upcoming events that are marked as public. You have options to customize the appearance and number of events. Can be used as a widget or embedded on a page/post.  There is the option to add the event’s city, state, and/or country to the listing if “Show location” is enabled on the event. The single event functionality allows you to embed a single public event from CiviCRM on a WordPress post or page. 

[Download](https://wordpress.org/plugins/civievent-widget/)

### [CiviCRM Event Organiser](https://github.com/christianwach/civicrm-event-organiser)

A WordPress plugin for syncing Event Organiser plugin Events with CiviCRM Events. The plugin syncs Event Organiser Events, Venues and Event Categories to their corresponding entities in CiviCRM.

Requires:

* WordPress 3.6 or later
* CiviCRM 4.6 or later
* [Event Organiser](https://wordpress.org/plugins/event-organiser/) version 3.0 or greater
* [Radio Buttons for Taxonomies](http://wordpress.org/plugins/radio-buttons-for-taxonomies/) to ensure only one event type is selected

[Download](https://github.com/christianwach/civicrm-event-organiser)

### [Content Views CiviCRM with CiviCRM Data Processor](https://github.com/agileware/content-views-civicrm-data-processor)

Plugin for integrating [Content Views](https://wordpress.org/plugins/content-views-query-and-display-post-page/) with [CiviCRM](https://civicrm.org) using the [CiviCRM Data Processor](https://lab.civicrm.org/extensions/dataprocessor) as the data source.

Requires:

* [CiviCRM Data Processor](https://lab.civicrm.org/extensions/dataprocessor) extension to be installed in CiviCRM
* [Content Views](https://wordpress.org/plugins/content-views-query-and-display-post-page/) plugin to be installed in WordPress

[Download](https://github.com/agileware/content-views-civicrm-data-processor)


## Widgets

WordPress Widgets add content and features to your Sidebars. Examples are the default widgets that come with WordPress: for Categories, Tag cloud, Search, etc. Plugins will often add their own widgets.

### [CiviEvent Widget](https://wordpress.org/plugins/civievent-widget/)

You can use the CiviEvent widget to add two types of widgets for upcoming public events from CiviCRM. There’s no limit to the number of widgets you can add of either type. You can include the widgets in the sidebar like normal, or you can include them via shortcodes in the body of your posts.

[Download](https://wordpress.org/plugins/civievent-widget/)

### [CiviCRM Contribution Page Widget](https://wordpress.org/plugins/civicrm-contribution-page-widget/)

CiviCRM contribution pages allow you to generate a “widget” showing the progress toward a goal. This plugin makes it easy to include one or more contribution page “widgets” as actual WordPress widgets on your sidebar.

[Download](https://wordpress.org/plugins/civicrm-contribution-page-widget/)

### BuddyPress

[BuddyPress](https://wordpress.org/plugins/buddypress/) is a suite of components that are common to a typical social network, and allows for great add-on features through WordPress’s extensive plugin system.

#### [BP Groups CiviCRM Sync](https://wordpress.org/plugins/bp-groups-civicrm-sync/)

A port of the Drupal civicrm_og_sync module for WordPress that enables two-way synchronisation between BuddyPress Groups and CiviCRM. It does not rely on any core CiviCRM files, since any required (or adapted) methods are included.

For each BuddyPress group, the plugin will automatically create two CiviCRM groups:

* A “normal” (mailing list) group containing a contact record for each corresponding BuddyPress group member. This group is assigned the same name as the linked BuddyPress group.
* An “ACL” group containing the contact record of the administrators of the corresponding BuddyPress group. This gives BuddyPress group admins the ability to view and edit members of their group in CiviCRM.

When a new user is added to (or joins) a BuddyPress group, they are automatically added to the corresponding CiviCRM group. Likewise, when a contact is added to the “normal” CiviCRM group, they will be added as a member to the corresponding BuddyPress group. If a contact is added to the CiviCRM “ACL” group, they will be added to the BuddyPress group as an administrator.

[Download](https://wordpress.org/plugins/bp-groups-civicrm-sync/)

#### [BP XProfile WordPress User Sync](https://wordpress.org/plugins/bp-xprofile-wp-user-sync/)

The plugin replaces the default BuddyPress xProfile Name field with two fields called (surprisingly) First Name and Last Name. These field values are kept in sync with the corresponding WordPress user profile fields as well as the BuddyPress xProfile Name field itself.

To be used alongside CiviCRM WordPress Profile Sync

[Download](https://wordpress.org/plugins/bp-xprofile-wp-user-sync/)
