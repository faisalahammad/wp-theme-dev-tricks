# WP THEME DEVELOPMENT TRICKS

### What should we do first?
Disable `config` files from `cs-framework.php` file.

<hr>

### Useful Tricks
Find the Page Template: `_wp_page_template`

<hr>

### Get the Page or Post ID
```php
if ( isset( $_REQUEST['post'] ) || isset( $_REQUEST['post_ID'] ) ) {
    $page_id = empty( $_REQUEST['post_ID'] ) ? $_REQUEST['post'] : $_REQUEST['post_ID'];
}
```

<hr>

### Get CSF Meta Value (Checked/Selected)
```php
$section_id = 0;

if ( isset( $_REQUEST['post'] ) || isset( $_REQUEST['post_ID'] ) ) {
	$section_id = empty( $_REQUEST['post_ID'] ) ? $_REQUEST['post'] : $_REQUEST['post_ID'];
}

if ( 'section' != get_post_type( $section_id ) ) {
	return $metaboxes;
}

$section_meta = get_post_meta( $section_id, 'mark-section-type', true );

if ( ! $section_meta ) {
	return $metaboxes;
} else if ( 'banner' != $section_meta['section-type'] ) {
	return $metaboxes;
}
```

<hr>

### Find Page Template & show conditional CSF metabox
```php
$page_id = 0;

if ( isset( $_REQUEST['post'] ) || isset( $_REQUEST['post_ID'] ) ) {
	$page_id = empty( $_REQUEST['post_ID'] ) ? $_REQUEST['post'] : $_REQUEST['post_ID'];
}

$current_page_template = get_post_meta( $page_id, '_wp_page_template', true );

if ( 'page-templates/landing.php' != $current_page_template ) {
	return $metaboxes;
}
```

<hr>

### Show CPT Title on CSF Group Metabox
```php
// Add this code on csf->fields->group->group.php (line 57)
if ( 'section' == $field_id ) {
$title = get_the_title($this->value[$key][$field_id]);
}
```

<hr>

### Query all page sections on Landing page
```php
$mark_sections = get_post_meta( get_the_ID(), 'mark-page-section', true );
if ( isset( $mark_sections['sections'] ) ) {
	foreach ( $mark_sections['sections'] as $mark_section ) {
		$mark_section_meta = get_post_meta( $mark_section['section'], 'mark-section-type', true );
		$mark_section_type = $mark_section_meta['section-type'];
		get_template_part( "section-template/{$mark_section_type}" );
	}
}
```

<hr>

### Codestar metabox data print
```php
$variable_name = get_post_meta( get_the_ID(), 'page-metabox', true );
echo esc_html( $variable_name['page-heading'] );
```

<hr>

###  Destroy CSS & JS cache
```php
if ( site_url() == 'https://domain-name.dev' ) {
	define( 'VERSION', time() );
} else {
	define( 'VERSION', wp_get_theme()->get( 'Version' ) );
}
```

<hr>

### Set default content width
```php
if ( ! isset( $content_width ) ) {
	$content_width = 960;
}
```

<hr>

### Register Sidebar
```php
register_sidebar(
    array(
        'name'          => __( 'About Page Sidebar', 'philosophy' ),
        'id'            => 'about-us',
        'description'   => __( 'Widgets in this area will be shown an About us page.', 'philosophy' ),
        'before_widget' => '<div id="%1$s" class="col-block %2$s">',
        'after_widget'  => '</div>',
        'before_title'  => '<h3 class="quarter-top-margin">',
        'after_title'   => '</h3>',
    )
);
```

<hr>

### Custom Excerpt length
```php
function custom_excerpt_length( $length ) {
	return 20;
}
```

<hr>

### Pagination
```php
function philosophy_pagination() {
	global $wp_query;
	$links = paginate_links( array(
		'current'  => max( 1, get_query_var( 'paged' ) ),
		'total'    => $wp_query->max_num_pages,
		'type'     => 'list',
		'mid_size' => 3,
	) );

    // Replace String
	$links = str_replace( 'page-numbers', 'pgn__num', $links );
	$links = str_replace( '<ul class=\'pgn__num\'>', '<ul>', $links );
	echo wp_kses_post( $links );
}
```

<hr>

### Total Posts in current Category
```php
$category = get_queried_object();
$total_post = $category->count;
echo $total_post;
```

<hr>

### Get the Author Page link
```php
<?php echo get_author_posts_url( get_the_author_meta( 'ID' ) ); ?>
```

<hr>

### Remove **srcset** or disable responsive image
```php
add_filter('wp_calculate_image_srcset_meta', '__return_empty_array');
```

<hr>

### Add Class to Footer Nav menu ul
```php
function mark_widget_nav_menu_args($nav_menu_args, $nav_menu, $args, $instance) {
	$nav_menu_args['menu_class'] = 'list-unstyled short-links';
	return $nav_menu_args;
}
add_filter('widget_nav_menu_args', 'mark_widget_nav_menu_args', 10, 4);
```

<hr>

### Add class on menu li
```php
function mark_menu_css_class( $classes, $item, $args ) {
	if ( 'top-menu' === $args->theme_location ) {
		$classes[] = "nav-item";
	}

	return $classes;
}

add_filter( 'nav_menu_css_class', 'mark_menu_css_class', 10, 3 );
```

<hr>

### Add dynamic section ID to section
```php
<section id="<?php echo get_post_field( 'post_name', $mark_section['section'] ); ?>" class="client-section">
```

<hr>

### One Page theme section url replace to #sectionName
```php
function mark_change_nav_menu_link( $menus ) {
	$string_to_replace = home_url( '/' ) . 'section/';
	if ( is_front_page() ) {
		foreach ( $menus as $menu ) {
			$new_url = str_replace( $string_to_replace, '#', $menu->url );

			if ( $new_url != $menu->url ) {
				$new_url = rtrim( $new_url, '/' );
			}
			$menu->url = $new_url;
		}
	}

	return $menus;
}

add_filter( 'wp_nav_menu_objects', 'mark_change_nav_menu_link' );
```

<hr>

### Remove static image size from post thumbnails (html)
```php
function verum_remove_thumbnail_dimensions( $html ) {
	$html = preg_replace( '/(width|height)=\"\d*\"\s/', "", $html );

	return $html;
}

add_filter( 'post_thumbnail_html', 'verum_remove_thumbnail_dimensions' );
```

<hr>

### Show Older/Newer Pagination based on condition
```php
<?php
$verum_prev_post_link = get_previous_posts_link();
if ( ! $verum_prev_post_link ) :
	?>
    <div class="older full">
		<?php next_posts_link( __( 'Older Posts >', 'verum' ) ); ?>
    </div>
<?php else: ?>
    <div class="older">
		<?php next_posts_link( __( 'Older Posts >', 'verum' ) ); ?>
    </div>
<?php endif; ?>

<?php
$verum_next_post_link = get_next_posts_link();
if ( ! $verum_next_post_link ) :
	?>
    <div class="newer full">
		<?php previous_posts_link( __( '< Newer Posts' ) ); ?>
    </div>
<?php else: ?>
    <div class="newer">
		<?php previous_posts_link( __( '< Newer Posts' ) ); ?>
    </div>
<?php endif; ?>
```

<hr>

### Get the post categories
```php
<?php
$verum_categories = get_the_category();
if ( $verum_categories ):
	echo '<ul class="cat">';
	foreach ( $verum_categories as $verum_category ) {
		printf( '<li><a href="%s">%s</a></li>', get_category_link( $verum_category ), $verum_category->name );
	}
	echo '</ul>';
	?>
<?php endif; ?>
```