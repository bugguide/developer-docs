# How BugGuide Works With Images

BugGuide has 1.6 million images and growing. If you put 1.6 million images into a directory, your filesystem will be very sad. Thus, BugGuide has to do special things to keep the filesystem happy and the system responsive. "Special things" basically boils down to storing images like this:

    files/raw/7HD/HIH/7HDHIHAHXHTHMH1HKLDH0L6ZHLCHQL5ZRLGZ4H4ZRLVH8HVH4H4Z4H6ZGLLR4L6ZSLTHNH3HEH1HMH.jpg

BugGuide assigns an md5 hash to each image when it is uploaded. You can view this hash as the "base" column of the bgimage table, or as the field_bgimage_base_value column in field_data_field_bgimage_base table (sadly in the code it is called node->uuid which is confusing but more accurate). Because the chances of an md5 collision were small at BugGuide's beginning, duplicates were not checked for and definitely exist.

A longer and unique ID is constructed by using an obfuscation function from ./images/image_obfuscate.inc which is loaded by sites/all/modules/custom/bgimage/bgimage.module.

## file_managed and file_usage Tables ##

Each image has an entry in the files_managed table. For example:

    fid: 1
    filename: 0040JQN000U0Z0N0JRU03RQQYR7QBRX0AR40DQ20Q0IQCRP0L0903Q90YRLQYRP0H060H090FRW0.jpg
    uri: public://raw/004/0JQ/0040JQN000U0Z0N0JRU03RQQYR7QBRX0AR40DQ20Q0IQCRP0L0903Q90YRLQYRP0H060H090FRW0.jpg
    filemime: image/jpeg
    filesize: 141583
    status: 1
    timestamp: 1519290088

Note the above row does not have a nid. Files are related to nids in the file_usage table:

    fid: 1
    module: file
    type: node
    id: 12929
    count: 1

The base for the file can be found in field_data_field_bgimage_base:

    entity_type: node
    bundle: bgimage
    deleted: 0
    entity_id: 12929
    revision_id: 12929
    language: und
    delta: 0
    field_bgimage_base_value: ba4002d88b8860b6a684ade8357aba56
    field_bgimage_base_format: NULL (or blank on new entries)

Running this base value through image_obfuscate($s) in ./images/image_obfuscate.inc results in:

    $s = 'ba4002d88b8860b6a684ade8357aba56';
    echo image_obfuscate($s);
    QH4R0H7ZQHIZBLXZOLZZTZLZJZQRZH2RCLKZHHGRKHIRKHXZ9LXZHH5RLH5RKHMZSH

which is NOT the correct obfuscation string stored in file_managed! Instead, the nid is concatenated together with the base and that concatenated string is obfuscated:

    $s = 'ba4002d88b8860b6a684ade8357aba56' . '12929';
    echo image_obfuscate($s);
    0040JQN000U0Z0N0JRU03RQQYR7QBRX0AR40DQ20Q0IQCRP0L0903Q90YRLQYRP0H060H090FRW0

## Derivative Images ##

Because Drupal core doesn't know about our scalable image storing, we have to tell it. 

A core hack to core image.module detects when the original image is being requested and slips in the correct URI.

A second core hack to core image.module runs a hook that invokes bgimage_image_style_path_alter() when a derivative image is requested:


```
#!php

diff modules/image/image.module modules/image/image.module.orig 
829,835d828
<   
<   // Core hack: substitute URI of derivative's original -JV
<   // If the image being requested is a derivative of a raw image,
<   // substitute the URI of the real raw image.
<   $image_uri = bgimage_original_from_derivative_path($scheme, $target);
<   // End core hack.
<   
1112,1127d1104
<   // Core hack: Addition of drupal_alter('image_style_path') -JV
<   // See https://www.drupal.org/node/1358896
<   // Addition of drupal_alter() allows modification of paths for
<   // derivatives. In our case we do not want guessable original paths
<   // from derivative paths.
<   $style_path = $scheme . '://styles/' . $style_name . '/' . $scheme . '/' . $path;
<   $context = array(
<     'style_name' => $style_name,
<     'uri' => $uri,
<     'scheme' => $scheme,
<     'path' => $path,
<   );
<   drupal_alter('image_style_path', $style_path, $context);
<   return $style_path;
<   // End core hack.
<   

```

See ca. line 615 in sites/all/modules/custom/bgimage/bgimage.module for the annotated code that creates the derivative path.
