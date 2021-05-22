## Disponivél Title disabilitar/habilitar Credits Aquila Indian, dissasembly by NaelsonBrasil:naelson.g.saraiva@gmail.com
### Key META BOX
---

#####

```PHP
$the_post_id = get_the_ID();
$hide_title = get_post_meta($the_post_id, "_hide_page_title", true);
$heading_class = !empty($hide_title) && "yes" === $hide_title ? "hide d-none" : "";

$has_post_thumbnail = get_the_post_thumbnail($the_post_id);

// Title, enable, disable box in post
// hooks in aquila theme
// add _hide_page_title postmeta

if (is_single() || is_page()) {
  printf('<h1 class="page-title text-dark %1$s">%2$s</h1>',esc_attr($heading_class), wp_kses_post(get_the_title()));
} else {
  printf('<h2 class="entry-title mb-3"><a class="text-dark" href="%1$s">%2$s</a></h2>',esc_url(get_the_permalink()),wp_kses_post(get_the_title()));
}
```

---

```PHP
	/**
	 * Add custom meta box.
	 *
	 * @return void
	 */
	 function add_custom_meta_box()
	{
		$screens = ['post'];
		foreach ($screens as $screen) {
            /*
            Unique ID
            Box title
            Content callback, must be of type callable
            Post type
            context
            */
			add_meta_box('hide-page-title',__('Hide page title', 'aquila'),[$this, 'custom_meta_box_html'],$screen,'side');
		}
	}

	/**
	 * Custom meta box HTML( for form )
	 *
	 * @param object $post Post.
	 *
	 * @return void
	 */
	 function custom_meta_box_html($post)
	{

		$value = get_post_meta($post->ID, '_hide_page_title', true);

		/**
		 * Use nonce for verification.
		 * This will create a hidden input field with id and name as
		 * 'hide_title_meta_box_nonce_name' and unique nonce input value.
		 */
		wp_nonce_field(plugin_basename(__FILE__), 'hide_title_meta_box_nonce_name');
?>
		<label for="aquila-field"><?php esc_html_e('Hide the page title', 'aquila'); ?></label>
		<select name="aquila_hide_title_field" id="aquila-field" class="postbox">
			<option value=""><?php esc_html_e('Select', 'aquila'); ?></option>
			<option value="yes" <?php selected($value, 'yes'); ?>>
				<?php esc_html_e('Yes', 'aquila'); ?>
			</option>
			<option value="no" <?php selected($value, 'no'); ?>>
				<?php esc_html_e('No', 'aquila'); ?>
			</option>
		</select>
<?php>
	}

	/**
	 * Save post meta into database
	 * when the post is saved.
	 *
	 * @param integer $post_id Post id.
	 *
	 * @return void
	 */
	 function save_post_meta_data($post_id)
	{

		/**
		 * When the post is saved or updated we get $_POST available
		 * Check if the current user is authorized
		 */
		if (!current_user_can('edit_post', $post_id))
			return;


		/**
		 * Check if the nonce value we received is the same we created.
		 */
		if (!isset($_POST['hide_title_meta_box_nonce_name']) || !wp_verify_nonce($_POST['hide_title_meta_box_nonce_name'], plugin_basename(__FILE__)))
			return;


		if (array_key_exists('aquila_hide_title_field', $_POST))
			update_post_meta($post_id, '_hide_page_title', $_POST['aquila_hide_title_field']);

	}
    ?>
```

---

```PHP
    add_action('add_meta_boxes', [$this, 'add_custom_meta_box']);
    add_action('save_post', [$this, 'save_post_meta_data']);
```
