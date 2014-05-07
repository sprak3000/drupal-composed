# Drupal Composed
I have been developing [Drupal 7](http://drupal.org) sites, modules, and themes for a few years now at work.
I have also been using [Composer](http://getcomposer.org) at work to manage dependencies for non-Drupal projects at work
and at home. Increasingly, Drupal has become a dependency for the sites being worked upon. While Drupal 8 is wedded to
modern tools like Composer, the sad reality is most of us will still need to use D7 until D8 has been officially
released and allowed to mature.

Enter "Drupal Composed", my effort to create a Composer project that will install Drupal 7 with one easy command. This
package will install my [Linguo](http://simpsons.wikia.com/wiki/Linguo) installation profile, enable French and German
translations, and use a [Bootstrap](http://getbootstrap.com/) theme for the site. My Linguo installation profile includes
all the modules I have found useful in buildling a robust multi-lingual site.

## Requirements
This project requires [Composer](http://getcomposer.org) and [drush](https://github.com/drush-ops/drush) to be globally
installed on your server. You will also need the [drush language commands](https://drupal.org/project/drush_language)
installed.

## Usage

### Install Linguo Drupal

To install the Linguo Drupal profile, run the following command replacing `/path/to/your/site`, `dbusername`,
`dbpassword`, `dbserver`, and `dbname` with the appropriate values:

```bash
$ DRUPAL_ROOT='/path/to/your/site/public/' DB_URL_DRUPAL='mysql://dbusername:dbpassword@dbserver/dbname' composer install
```

Once it has finished, you can setup a virtual host pointing to `/path/to/your/site/public`. Part of the installation output
will be a line with your Drupal administration credentials: `Installation complete.  User name: admin  User password: randomstringhere`.