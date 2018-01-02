# Drupal Composed

***This repository has been archived and left as reference material. It is no longer maintained.***

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

### Updating Linguo Drupal
To keep up with changes to Linguo, you can run the following command replacing `/path/to/your/site` with the appropriate values:

```bash
$ DRUPAL_ROOT='/path/to/your/site/public/' composer update
```

## Sample Apache virtual host configuration
```ApacheConf
<VirtualHost *:80>
  ServerName www.example.com

  DocumentRoot  /sites/drupal-composed/public/

  FileETag none

  RewriteEngine On

  CustomLog ${APACHE_LOG_DIR}/www.example.com_access_log f5-combined

  # Also send access and error logs to syslog with descriptive tag
  CustomLog "|/usr/bin/logger -t apache2-access-www.example.com -i -p local1.info" f5-combined
  ErrorLog  "|/usr/bin/logger -t apache2-error-www.example.com -i -p local1.error"

  <Directory "/sites/drupal-composed/public/">
    AllowOverride All

    <IfModule mod_rewrite.c>
      Options +FollowSymLinks
      Options +Indexes

      # FIRE UP THE ENGINES, CAPTAIN
      RewriteEngine On

      # NOTE: For the Drupal Scheduler module
      # If we have scheduled asset paths, route them to the drupal index
      RewriteCond %{REQUEST_FILENAME} -s
      RewriteRule ^(/?(en|fr|de)/?)?sites/default/files/unpublished(.+\.(jpg|jpeg|gif|png|txt|pdf|mp3|mov|m4v|mp4|mpeg|avi|wmv))$ index.php?relpath=$3 [NC,L]
      RewriteCond %{REQUEST_FILENAME} -s
      RewriteRule ^(/?(en|fr|de)/?)?sites/default/files/styles/[^/]+/public/unpublished(.+\.(jpg|jpeg|gif|png|txt|pdf|mp3|mov|m4v|mp4|mpeg|avi|wmv))$ index.php?relpath=$3 [NC,L]

      # Now check for an existing file/image/directory
      # If found, just return that asset
      RewriteCond %{REQUEST_FILENAME} -s [OR]
      RewriteCond %{REQUEST_FILENAME} -l
      RewriteRule ^.*$ - [NC,L]

      # make sure that /en/ is at the start of the URL
      # RewriteCond %{REQUEST_URI} !^/index.php$
      # RewriteCond %{REQUEST_URI} !^/(en|fr|de)/?.*$
      # RewriteRule ^/?(.*) /en/$1 [R,NE,L]

      # if we get this far, hand it off to drupal (the root index.php script)
      RewriteRule ^.*$ index.php [NC,L]
    </IfModule>

    AllowOverride None

    Order allow,deny
    Allow from all
  </Directory>
</VirtualHost>
```