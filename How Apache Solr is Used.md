BugGuide uses the [Apache Solr Framework](https://www.drupal.org/project/apachesolr) to perform slow queries like traversing the tree or finding images under a certain taxon. There is an initial effort in moving to Search API, which is used by the [faceted search page](https://beta.bugguide.net/search).

# Architecture
A Solr server exists at http://bgsolr.ent.iastate.edu/solr. It has two cores: http://bgsolr.ent.iastate.edu:8983/solr/#/bugguide for apachesolr module and http://bgsolr.ent.iastate.edu:8983/solr/#/bugguide_search_api for search_api_module.

## Apachesolr

Http Parallel Request Library is used to index faster than Solr normally would. The following settings are enforced in settings.php on beta.bugguide.net:

```
$conf['apachesolr_environments']['solr']['url'] = 'http://bgsolr.ent.iastate.edu:8983/solr/bugguide';
$conf['apachesolr_cron_limit'] = 3000;
$conf['apachesolr_parallel_num_cpu'] = 6;
```

Searching for `apachesolr_drupal` shows where it is currently being used:

```
./sites/all/modules/custom/bgpage/bgpage.ds.field.inc:54:    $query = apachesolr_drupal_query('browse_images', array());
./sites/all/modules/custom/bgpage/bgpage.ds.field.inc:144:    $query = apachesolr_drupal_query('images', array());
./sites/all/modules/custom/bgpage/bgpage.ds.field.inc:261:  $query = apachesolr_drupal_query('representative_images_' . $bgpage_nid, array());
./sites/all/modules/custom/bgpage/bgpage.module:527:    $query = apachesolr_drupal_query('apachesolr', array());
./sites/all/modules/custom/bgapi/src/Plugin/resource/DataProvider/DataProviderApacheSolr.php:118:    $query = apachesolr_drupal_query('bgimage_search', array());
````

## Search API

Search API powers faceted search at https://beta.bugguide.net/search/global. Fields to be indexed are selected via it's [web interface](https://beta.bugguide.net/admin/config/search/search_api/index/bugguide/fields). Facets need to be [enabled first](https://beta.bugguide.net/admin/config/search/search_api/index/bugguide/facets) and then they can be added to the sidebar at admin/structure/block.

# Next steps

Search API provides a lot of flexibility for the global search but there are a few challenges:

1. Custom queries for apachesolr would need to be converted to Search API ones.
2. There is (as of 2018) no contributed module to speed up indexing in Search API like there is for apachesolr. One option would be to port [apachesolr_parallel](https://www.drupal.org/project/apachesolr_parallel).

It may be worth it to try settinp up faceted search with apachesolr module. If the results are satisfying, then moving to search_api could be done at a later stage.

Likewise, it may be possible to move all apachesolr stuff to search api.
