# Upgrading CiviCRM for Drupal 8

???+ tldr "About this document"

    Use this document to upgrade CiviCRM installations on Drupal 8 to the latest CiviCRM release.

    The document assumes that:

    * CiviCRM and Drupal 8 are already installed and working.
    * You are able to run `composer` and manage `composer.json`.
    * You have identified the root of your D8 project -- such as `/var/www/d8.example.org`.

!!! caution "Before upgrading"
    Make sure you have done the steps listed in [before upgrading](index.md#before-upgrading) first.

Drupal 8 (D8) sites are typically administered with [Composer](https://www.getcomposer.org). Composer is a *dependency management* tool which can add, upgrade, and remove software *packages* for your site.

CiviCRM is published as a suite of related packages. Our goal is to use Composer to update CiviCRM's code. Then, we can upgrade the database.

## Review "composer.json"

The file `composer.json` lists a series of *required* packages. Each package has a *version constraint*. For example, a typical file might specify:

```javascript
{
  "require": {
    "civicrm/civicrm-core": "~5.29.0",
    "civicrm/civicrm-packages": "~5.29.0",
    "civicrm/civicrm-drupal-8": "~5.29.0",
    "civicrm/civicrm-asset-plugin": "~1.0.0"
  }
}
```

Look at your configuration and answer these questions:

??? question "What is the version constraint?"

    In the above example, it uses a version-constraint: `~5.29.0`. This matches `5.29.0` and roughly-equivalent
    versions (`5.29.1`, `5.29.2`, etc). However, it does *not* match `5.30.0`.

    A common alternative might be `~5.29`. This matches `5.29` and roughly-equivalent versions (`5.30`, `5.31`, etc).
    However, it doe *not* match `6.0`.

    If the constraint looks unfamiliar, review [Composer: Writing Version Constraints](https://getcomposer.org/doc/articles/versions.md#writing-version-constraints).

??? question "Which plugin is responsible for CiviCRM's JS+CSS files?"

    CiviCRM-D8 requires a *composer plugin* to handle JS+CSS assets. There are two common plugins. `composer.json` may specify:

    * **[civicrm/civicrm-asset-plugin](https://lab.civicrm.org/dev/civicrm-asset-plugin/)**: Used on newer deployments. Better compatibility with different variations of Drupal 8/9.
    * **[roundearth/civicrm-composer-plugin](https://gitlab.com/roundearth/civicrm-composer-plugin)**: Used on older deployments. Better compatibility with older versions of CiviCRM.
    * **Neither**: The significance of omission depends on the version. For Civi-D8 v5.31+ (Nov 2020), omission implies `civicrm/civicrm-asset-plugin`. In older versions, omission is likely a misconfiguration.

    For our purposes, we assume that you should keep the current plugin. Switching to a different plugin requires extra effort.
    <!-- TODO: When there's a migration guide, link to it. -->

    ??? note "What if both plugins are listed in `composer.json`?"

        This is most likely a mistake. The signifance depends on the versions:

        * If `civicrm/civicrm-asset-plugin` is `1.1.1` (Oct 2020) or newer, it will defer to `roundearth/civicrm-composer-plugin`.
        * If `civicrm/civicrm-asset-plugin` is older, then the behavior is unknown/undefined/unsupported.

## Upgrade the plugin

If `composer.json` lists `civicrm/civicrm-asset-plugin` as `~1.0.0`, then it needs the first update.

```bash
composer require civicrm/civicrm-asset-plugin:~1.1
```

Otherwise, continue to the next step.

## Upgrade the code {:#download}

Choose one of the following methods:

* Run the command `composer update`. This finds the newest version which *matches the current constraint*.  For example, if the
  constraint specifies `~5.29.0`, then `composer update` will find `5.29.2` or `5.29.3` or similar.

    ```bash
    composer update civicrm/civicrm-{core,packages,drupal-8}
    ```

* Run the command `composer require`. This *changes the current constraint*. For example, to switch to `~5.31.0`, run:

    ```bash
    composer require civicrm/civicrm-{core,packages,drupal-8}:~5.31.0
    ```

## Upgrade the translations

In some workflows for internationalized deployments, you would need [re-download the translations](https://docs.civicrm.org/installation/en/latest/drupal8/#i18n).

<!-- FIXME when https://lab.civicrm.org/documentation/docs/installation/-/issues/4 is closed -->

## Upgrade the database

Choose one of the following methods:

* Run the following `cv` command from within your site:

    ```bash
    cv upgrade:db
    ```

    !!! tip "Tip: Preview upgrade steps"

        The `cv upgrade:db` command accepts a `--dry-run` option to show what changes will be executed.
        For the maximum detail, use `cv upgrade:db --dry-run -vv`.

* Or, run the following `drush` (v8) command from within your site:

    ```bash
    drush civicrm-upgrade-db
    ```

* Or, point your web browser to the following URL and follow the on-screen instructions.

    ```
    http://example.org/civicrm/upgrade?reset=1
    ```

## Clear cached files

<!-- Hrm.  You shouldn't need to clear caches manually - if that's needed, there's probably a bug.  There have been longstanding bugs in
years past, so it makes sense to habituate to this.  OTOH, in 2020, there have been notable fixes like #17126 and #17740.  Still, it's hard
to say that the habit is no longer needed. :shrug: -->

Delete all files in sites/default/files/civicrm/templates_c/, or clear the caches using one of the command line tools, such s `cv flush` or `drush cache-clear civicrm`.

!!! note
    If you disabled modules, or placed the site in maintenance mode, remember to reverse those steps after the upgrade.

