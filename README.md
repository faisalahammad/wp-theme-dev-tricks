# WP THEME DEVELOPMENT TRICKS

### What should we do first?
- Disable `config` files from `cs-framework.php` file.

<hr>

### Useful Tricks
- Find the Page Template: `_wp_page_template`

<hr>

- Get the Page or Post ID
```php
if ( isset( $_REQUEST['post'] ) || isset( $_REQUEST['post_ID'] ) ) {
    $page_id = empty( $_REQUEST['post_ID'] ) ? $_REQUEST['post'] : $_REQUEST['post_ID'];
}
```

<hr>

- Codestar metabox data print
```php
$variable_name = get_post_meta( get_the_ID(), 'page-metabox', true );
echo esc_html( $variable_name['page-heading'] );
```

<hr>

- Destroy CSS & JS cache
```php
if ( site_url() == 'https://domain-name.dev' ) {
	define( 'VERSION', time() );
} else {
	define( 'VERSION', wp_get_theme()->get( 'Version' ) );
}
```

<hr>

- Set default content width
```php
if ( ! isset( $content_width ) ) {
	$content_width = 960;
}
```

<hr>

- Register Sidebar
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

- Custom Excerpt length
```php
function custom_excerpt_length( $length ) {
	return 20;
}
```

<hr>

- Pagination
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
