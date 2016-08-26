#E-types Backend Transfer Document - Hoi Pham

##1. Setup source code folder:
1) Folder Structure:
```
// Current folder: /srv/site/etypes/project-name
// ls -fla
apache/{Apache setting}
src/{Source code}
```

2) htaccess file:
```
Header set Access-Control-Allow-Origin "*"
<IfModule mod_rewrite.c>
RewriteEngine On

RewriteCond %{HTTP_USER_AGENT} baiduspider|facebookexternalhit|twitterbot|rogerbot|linkedinbot|embedly|quora\ link\ preview|showyoubot|outbrain|pinterest [NC,OR]
RewriteCond %{QUERY_STRING} _escaped_fragment_
RewriteRule ^(?!.*?(\.html|\.php|\.js|\.css|\.xml|\.less|\.png|\.jpg|\.jpeg|\.gif|\.pdf|\.doc|\.txt|\.ico|\.rss|\.zip|\.mp3|\.rar|\.exe|\.wmv|\.doc|\.avi|\.ppt|\.mpg|\.mpeg|\.tif|\.wav|\.mov|\.psd|\.ai|\.xls|\.mp4|\.m4a|\.swf|\.dat|\.dmg|\.iso|\.flv|\.m4v|\.torrent|\.ttf|\.woff))(.*) http://localhost:3000/http://%{HTTP_HOST}/$1 [P,L]

RewriteBase /
RewriteRule ^index\.php$ - [L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . /index.php [L]
</IfModule>
```
3) .gitignore: 
```
wp-config.php
/src/wp-content/uploads
*.phpproj
*.phpproj.user
*.sln
.DS_Store
.vs
*.orig
*.map
.tmp
*.config
*.sublime-project
*.sublime-workspace
node_modules
/src/wp-content/themes/bangolufsen/www-dev/vendor
/src/wp-content/themes/bangolufsen/www-dev/js/template.js
/src/wp-content/themes/bangolufsen/www-dev/css/app.css
/src/.htaccess
.htaccess
```

##2. Setup a Wordpress site:

1. Install basic Wordpress site
2. Install basic plugin: etypes_basic (Include some required plugin: ACF, Post Type, Taxonomy, SEO, WPML), WP Migrate DB (Backup database)
3. Install blank Wordpress template

##3. Template and JSON API:

1. Setup page template
2. Override index.php in wordpress root folder

```
<?php
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
require( dirname( __FILE__ ) . '/wp-blog-header.php' );
```

3. Sample Template file:

```
<?php
// Template Name: Frontpage
$post_id = $post->ID;


$data = array();
$listPost1st = get_field('list_post_1st');
$listPost2nd = get_field('list_post_2nd');
$tilangVideo = get_field('tilang_video');
function getListPost($postArray)
{
    foreach ($postArray as $value) {

        if ($value->post_type == 'project') {
            $shortDescription = get_field('text', $value->ID);

            $featureImages = array(
                'landscapeImage' => getImageData(get_field('landscape_image', $value->ID)),
                'squareImage' => getImageData(get_field('square_image', $value->ID)),
                'portraitImage' => getImageData(get_field('portrait_image', $value->ID)),
            );
        } else {
            $shortDescription = get_field('lead_text', $value->ID, false);
            $featureImages = array(
                'landscapeImage' => getImageData(get_field('hero_landscape_image', $value->ID)),
                'squareImage' => getImageData(get_field('hero_square_image', $value->ID)),
                'portraitImage' => getImageData(get_field('hero_portrait_image', $value->ID))
            );
        }


        $data = array(

            'id' => $value->ID,
            'postTitle' => $value->post_title,
            'postType' => $value->post_type,
            'slug' => $value->post_name,
            'excerpt' => $shortDescription,
            'term' => wp_get_post_terms($value->ID, 'project_category'),
            'featuredImage' => $featureImages

        );

        $listPostData[] = $data;
    }
    return $listPostData;
}


$tilangVideo = array(
    'title' => $tilangVideo[0]["video_title"],
    'text' => $tilangVideo[0]["video_text"],

    'image' => array(
        'fullscreen_image_desktop' => getImageDataWithChild($tilangVideo[0], 'video_thumnail')
    ),
    'video' => array(

        'fullscreen_video_desktop' => get_type_video_file($tilangVideo[0]["video"]),
    )
);


$data = array(
    'topText' => (String)get_field('top_text', $post_id),
    'headerText' => (String)get_field('header_text', $post_id),
    'listPost1st' => getListPost($listPost1st),
    'tilgangVideo' => $tilangVideo,
    'listPost2nd' => getListPost($listPost2nd)
);

$result = $data;

array_walk_recursive($result, 'replacer');
echo wp_send_json_success($result);
?>
```

##4. Social network api
- Linkedin (https://developer.linkedin.com/docs/rest-api)
- Instagram (https://www.instagram.com/developer/)
- Facebook (https://developers.facebook.com/docs/)

##5. Optimize 

#### Link to this file: https://github.com/dev01synova/transfer-doc/blob/master/README.md
