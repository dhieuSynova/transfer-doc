#E-types Backend Transfer Document

##1. Setup a Wordpress site:

1. Install basic Wordpress site
2. Install basic plugin: etypes_basic (Include some required plugin: ACF, Post Type, Taxonomy, SEO, WPML), WP Migrate DB (Backup database)
3. Install blank Wordpress template

##2. Template and JSON API:

1. Setup page template
2. Override index.php in wordpress root folder

```<?php
/**
 * Front to the WordPress application. This file doesn't do anything, but loads
 * wp-blog-header.php which does and tells WordPress to load the theme.
 *
 * @package WordPress
 */
if ((!isset($_GET['dataType']) || ($_GET['dataType']!="json"))) {
    require_once 'wp-content/themes/{template-slug-here}/www/index.php';
    exit();
}
/**
 * Tells WordPress to load the WordPress theme and output it.
 *
 * @var bool
 */
define('WP_USE_THEMES', true);
/** Loads the WordPress Environment and Template */
require( dirname( __FILE__ ) . '/wp-blog-header.php' );```
