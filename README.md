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
