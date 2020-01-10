# WP THEME DEVELOPMENT TRICKS

### What should we do first?
Disable `config` files from `cs-framework.php` file.

<hr>

### Useful Tricks
Find the Page Template: `_wp_page_template`

---

### Limit Search Results For Specific Post Types
```php
function searchfilter($query) {
    if ($query->is_search && !is_admin() ) {
        $query->set('post_type',array('post','page'));
    }
 
	return $query;
}
 
add_filter('pre_get_posts','searchfilter');
```

---

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

<hr>

### Add Social Profile on the user profile (dashboard)
```php
function verum_user_contactmethods( $cm ) {
	$cm['facebook']    = __( 'Facebook', 'verum' );
	$cm['twitter']     = __( 'Twitter', 'verum' );
	$cm['google-plus'] = __( 'Google Plus', 'verum' );
	$cm['pinterest']   = __( 'Pinterest', 'verum' );

	return $cm;
}

add_filter( 'user_contactmethods', 'verum_user_contactmethods' );

// Show those social links on the frontend
<?php $verum_user_cm = wp_get_user_contact_methods(); ?>
<div class="s-links">
	<?php
	foreach ( $verum_user_cm as $verum_ucm_key => $verum_ucm_value ) :
		$verum_cm_link = get_user_meta( get_the_author_meta( 'ID' ), $verum_ucm_key, true );
		if ( $verum_cm_link ) :
			?>
            <a href="<?php echo esc_url( $verum_cm_link ); ?>" target="_blank">
                <i class="fa fa-<?php echo esc_attr( $verum_ucm_key ); ?>"></i>
            </a>
		<?php
		endif;
	endforeach;
	?>
</div>
```

--- 

### Admin column on custom post type
```php
// Add the custom columns to the book post type: (replace book by your post type name)
add_filter( 'manage_book_posts_columns', 'set_custom_edit_book_columns' );
function set_custom_edit_book_columns($columns) {
    unset( $columns['author'] );
    $columns['book_author'] = __( 'Author', 'your_text_domain' );
    $columns['publisher'] = __( 'Publisher', 'your_text_domain' );

    return $columns;
}

// Add the data to the custom columns for the book post type:
add_action( 'manage_book_posts_custom_column' , 'custom_book_column', 10, 2 );
function custom_book_column( $column, $post_id ) {
    switch ( $column ) {

        case 'book_author' :
            $terms = get_the_term_list( $post_id , 'book_author' , '' , ',' , '' );
            if ( is_string( $terms ) )
                echo $terms;
            else
                _e( 'Unable to get author(s)', 'your_text_domain' );
            break;

        case 'publisher' :
            echo get_post_meta( $post_id , 'publisher' , true ); 
            break;

    }
}
```

---

### Show Post Excerpt / limited number of words
```php
echo wp_trim_words( get_the_content(), 50, false );
```

---

### Show CMB2 metabox based on Post Types
```php
function admin_backend_scripts() {
	if ( get_post_type() == 'post' ) :
		?>
        <script>
            (function ($) {
                $(document).ready(function () {
                    $('#post_video').hide();
                    $('#post_audio').hide();
                    $('#post_gallery').hide();
                    $('#post_quote').hide();

                    let post_ID = $('input[class="post-format"]:checked').attr('id');

                    if (post_ID === 'post-format-video') {
                        $('#post_video').show();
                    }else {
                        $('#post_video').hide();
                    }

                    if (post_ID === 'post-format-audio') {
                        $('#post_audio').show();
                    }else {
                        $('#post_audio').hide();
                    }

                    if (post_ID === 'post-format-gallery') {
                        $('#post_gallery').show();
                    }else {
                        $('#post_gallery').hide();
                    }

                    $('.post-format').change(function () {
                        let post_ID = $('input[class="post-format"]:checked').attr('id');

                        if (post_ID === 'post-format-video') {
                            $('#post_video').show();
                        }else {
                            $('#post_video').hide();
                        }

                        if (post_ID === 'post-format-audio') {
                            $('#post_audio').show();
                        }else {
                            $('#post_audio').hide();
                        }

                        if (post_ID === 'post-format-gallery') {
                            $('#post_gallery').show();
                        }else {
                            $('#post_gallery').hide();
                        }

                    });
                });
            })(jQuery);
        </script>
	<?php
	endif;
}

add_action( 'admin_print_scripts', 'admin_backend_scripts', 100 );
```

---

## WordPress Widget Development

### Latest Posts
```php
// First Register widget on widgets_init hook
register_widget( 'cometpro_latest_post' );

// Then write those code avobe the hook
class cometpro_latest_post extends WP_Widget {
	public function __construct() {
		parent::__construct( 'cometpro_latest_post', __( 'Comet Latest Post', 'cometpro' ), array(
			'description' => __( 'Comet top 5 latest post', 'cometpro' )
		) );
	}

	public function widget( $args, $instance ) {
		$title = $instance['title'];

		echo $args['before_widget'];
		echo $args['before_title'];
		echo $title;
		echo $args['after_title'];
		?>
        <ul class="nav">
			<?php
			$latest_post = new WP_Query( array(
				'post_type'     => 'post',
				'posts_per_page' => 5
			) );
			while ( $latest_post->have_posts() ) : $latest_post->the_post();
				?>
                <li>
                    <a href="<?php the_permalink(); ?>"><?php the_title(); ?><i class="ti-arrow-right"></i><span><?php echo get_the_date( 'F d, Y' ); ?></span></a>
                </li>
			<?php
			endwhile;
			wp_reset_query();
			?>
        </ul>
		<?php
		echo $args['after_widget'];
	}

	public function form( $instance ) {
		$title = $instance['title'];
		?>
        <p>
            <lable for=""><?php _e( 'Title:', 'cometpro' ); ?></lable>
            <input type="text" name="<?php echo $this->get_field_name( 'title' ); ?>" value="<?php echo $title; ?>" class="widefat">
        </p>
		<?php
	}
}
```

---

### Search Box
```php
// First Register widget on widgets_init hook
register_widget( 'cometpro_search' );

// Then write those code avobe the hook
class cometpro_search extends WP_Widget {
	public function __construct() {
		parent::__construct( 'cometpro_search', __( 'Comet Search', 'cometpro' ), array(
			'description' => __( 'Comet Search Box', 'cometpro' )
		) );
	}

	public function widget( $args, $instance ) {
		$title = $instance['title'];

		echo $args['before_widget'];
		echo $args['before_title'];
		echo $title;
		echo $args['after_title'];
		?>
        <form method="GET">
            <input type="text" name="s" placeholder="Search.." class="form-control">
        </form>
		<?php
		echo $args['after_widget'];
	}

	public function form( $instance ) {
		$title = $instance['title'];
		?>
        <p>
            <lable for=""><?php _e( 'Title:', 'cometpro' ); ?></lable>
            <input type="text" name="<?php echo $this->get_field_name( 'title' ); ?>" value="<?php echo $title; ?>" class="widefat">
        </p>
		<?php
	}
}
```