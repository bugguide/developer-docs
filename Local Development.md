# Local Development

BugGuide is a Drupal 7 site.

Production is running on RHEL8 and Apache 2.4 plus PHP 7.3, with Percona as the database.

## Installing on MacOS running macports-lamp

Clone the bugguide repository into your Sites directory:

```
cd ~/Sites
git clone git@github.com:bugguide/bugguide.git
```

We could access the site at http://local.test/bugguide but that would be annoying since BugGuide itself is running on a base domain. We want to access it at http://bugguide.test. So let's set up a special vhost just for BugGuide.

Add the following to /opt/local/etc/apache2/extra/httpd-vhosts.conf:

```
<VirtualHost *:80>
    ServerName bugguide.test
    DocumentRoot /Users/jvandyk/Sites/bugguide
    <Directory /Users/jvandyk/Sites/bugguide>
      Options Indexes FollowSymLinks
      DirectoryIndex index.php index.html
      Require local
      AllowOverride All
    </Directory>
</VirtualHost>
```

Restart Apache so it will read the new vhost.

That's not enough, though. We need to have local DNS resolve the bugguide.test hostname to your machine. Do that by editing /etc/hosts and adding bugguide.test behind local.test:

```
127.0.0.1	localhost
255.255.255.255	broadcasthost
::1             localhost
127.0.0.1 local.test bugguide.test
```

Now tell MacOS to read the new hosts file with this flood of arcane commands:

```
  sudo killall -HUP mDNSResponder
  sudo killall mDNSResponderHelper
  sudo dscacheutil -flushcache
```

Use drush to perform the Drupal 7 installation:

```
cd ~/Sites/bugguide
drush si standard -y -v --db-url=mysql://$DBCREDS@localhost/bugguide --site-name=BugGuide --account-name=admin install_configure_form.update_status_module='array(FALSE,FALSE)'
```

Make the files directory at the root of the site, not in sites/default/files. Make it owned by the Apache user.

```
mkdir files
sudo chown _www files
```

Note the password that is shown in the output; you will use it to log in.

Now, in Terminal, open your browser and point it to http://bugguide.test. (Opening this way might prevent the browser from trying to go https://bugguide.test instead.)

```
open -a Firefox http://bugguide.test
```

If everything worked, you should be looking at a Drupal 7 site. Log in and go to Configuration / File System. Change the files path from sites/default/files to files.

Now we will do a bunch of setup with drush:

```
drush -y en bg
drush -y en php better_formats views views_ui forum
drush -y features-revert-all --force
drush -y vset date_default_timezone "America/Chicago"
drush -y vset configurable_timezones 1
drush -y vset empty_timezone_message 1
drush -y vset user_default_timezone "2"
drush -y vset image_allow_insecure_derivatives 1
drush -y vset views_ui_show_advanced_help_warning 0
drush -y vset ds_extras_hide_page_title 1
drush sql-query "UPDATE menu_links SET hidden = 1 WHERE link_path REGEXP '^user/logout$';"
drush sql-query "UPDATE menu_links SET hidden = 1 WHERE link_title REGEXP '^My account$';"
```

Finally we will add some lines to sites/default/settings.php:

```
$conf['apachesolr_environments']['solr']['url'] = 'http://bgsolr.ent.iastate.edu:8983/solr/bugguide';
$conf['apachesolr_site_hash'] = 'acuqgc';
$conf['search_api_solr_site_hash'] = 'nxomws';
$conf['bg_disable_email'] = TRUE;
$conf['cron_key'] = 's7kWHAEjEnWneFdQvhgSWwyp1UWrmkRE7BKoPOpS2cg';
$conf['search_api_solr_site_hash'] = 'nxomws';
$conf['bg_display_build_date'] = TRUE;
$conf['apachesolr_read_only'] = "1";
```

Create some content as follows:

- Node 1: Page, titled About
- Node 2: Page, titled Page 2
- Node 3: Guide Page, Scientific Name "Arthropoda", Taxon 1100, no parent
- Node 4: Guide Page, Scientific Name "Chelicerata", Taxon 1200, parent 3
- Node 5: Guide Page, Scientific Name "Arachnida", Taxon 1400, parent 3,4
- Node 6: Guide Page, no Scientific Name, Common Name "ID Request", no Taxon, no parent

Now we have a tree of three nodes (Arthropoda -> Chelicerata -> Arachnida) and ID Request which is hardcoded to node 6.

This should now be a site that is functional enough to add an image to ID Request, test theming, and poke around.
