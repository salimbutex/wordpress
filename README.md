wordpress
=========
Add shortcode
============================
<?php

function box($atts, $content = null) {
	return('<div id="boxed_style">'.$content.'</div>');
}
add_shortcode("box", "box");

function youtube($atts, $content = null) {
	return ('<iframe width="560" height="315" src="http://www.youtube.com/embed/'.$content.'" frameborder="0" allowfullscreen></iframe>');
}
add_shortcode("youtube", "youtube"); 

function vimeo($atts, $content = null) {
	return ('<iframe src="http://player.vimeo.com/video/'.$content.'" width="400" height="300" frameborder="0" webkitAllowFullScreen mozallowfullscreen allowFullScreen></iframe>'); 
}
add_shortcode("vimeo", "vimeo");


function rrf_buttons() {
	add_filter ("mce_external_plugins", "my_external_js");
	add_filter ("mce_buttons", "our_awesome_buttons");
}

function my_external_js($plugin_array) {
	$plugin_array['wptuts'] = plugins_url('js/custom-button.js', __FILE__);
	return $plugin_array;
}

function our_awesome_buttons($buttons) {
	array_push ($buttons, 'boxed', 'youtube', 'vimeo');
	return $buttons;
}
add_action ('init', 'rrf_buttons'); 

or 
function events_shortcode(){
        $args = array( 'post_type' => 'events');
        $loop = new WP_Query( $args );
        if ($loop):
        while ( $loop->have_posts() ) : $loop->the_post();
        the_title();
        echo '<br/>';
        endwhile;
        endif;
}
add_shortcode('events_list', 'events_shortcode');


shortcode attribute
====================================================================================
function basic_rrf_shortcode ($atts, $content = null){
            return '<div style="background:red;color:#fff;padding:15px">'.$content.'</div>';
    }
    add_shortcode ('rrf_shortcode', 'basic_rrf_shortcode');
     
     
    function basic_rrf_shortcode_extend ($atts){
     
            extract ( shortcode_atts ( array (
                    'content' => 'null',
                    'color' => 'red',
                    'background' => 'yellow',
            ), $atts, 'rrf_shortcode_extend') );
     
            return '<div style="background:'.$background.';color:'.$color.';padding:15px">'.$content.'</div>';
    }
    add_shortcode ('rrf_shortcode_extend', 'basic_rrf_shortcode_extend');
	
	
	
Add meta box/ metabox
==============================================================================
view page
<?php 
               
    echo get_post_meta($post->ID, 'rs_rush_hours', true ); 
                
?>





function rs_coffeeshop_metafield() {

    add_meta_box('rs_coffee_meta_box', 'RS CoffeeShop Details', 'display_rs_coffee_meta_box', 'events', 'normal', 'high');

}
add_action('admin_init', 'rs_coffeeshop_metafield');



function display_rs_coffee_meta_box($events) {

	$info=array();
	$coffee_fields=array('rs_rush_hours','rs_street','rs_zipcode','rs_city','rs_country','rs_ratecoffee','rs_ratewifi','rs_address_longitude','rs_address_latitude');

	if(isset($coffee_fields) && is_array($coffee_fields) && count($coffee_fields)>0){
		
            foreach($coffee_fields as $rs_meta_key){
			$info[$rs_meta_key]=get_post_meta($events->ID,$rs_meta_key, true);
		}
	}
	
        print_r($info)
	?>
    <table>
        <tr>
            <td style="width: 50%">Rush Hours</td>
			<td>
				<select name="rs_rush_hours">
					<option value="7am-9pm" <?php selected($info['rs_rush_hours'], '7am-9pm'); ?>>7am-9pm</option>
					<option value="8am-9pm" <?php selected($info['rs_rush_hours'], '8am-9pm'); ?>>8am-9pm</option>
					<option value="10am-10pm" <?php selected($info['rs_rush_hours'], '10am-10pm'); ?>>10am-10pm</option>
				</select>
			</td>
        </tr>
        <tr>
            <td style="width: 50%">Street</td>
            <td><input type="text" size="40" name="rs_street" class="" value="<?php echo $info['rs_street']; ?>" /></td>
        </tr>	
        <tr>
            <td style="width: 50%">Zipcode</td>
            <td><input type="text" size="40" name="rs_zipcode" class="" value="<?php echo $info['rs_zipcode']; ?>" /></td>
        </tr>	
        <tr>
            <td style="width: 50%">City</td>
            <td><input type="text" size="40" name="rs_city" class="" value="<?php echo $info['rs_city']; ?>" /></td>
        </tr>	
        <tr>
            <td style="width: 50%">Cofee Rating(Percentage(%))</td>
            <td><input type="text" size="40" name="rs_ratecoffee" class="" value="<?php echo $info['rs_ratecoffee']; ?>" /></td>
        </tr>	
        <tr>
            <td style="width: 50%">Wifi Rating(Percentage(%))</td>
            <td><input type="text" size="40" name="rs_ratewifi" class="" value="<?php echo $info['rs_ratewifi']; ?>" /></td>
        </tr>
		
        <tr>
            <td style="width: 50%">Address Latitude</td>
            <td><input type="text" size="40" name="rs_address_latitude" class="" value="<?php echo $info['rs_address_latitude']; ?>" /></td>
        </tr>
		
        <tr>
            <td style="width: 50%">Address Longitude</td>
            <td><input type="text" size="40" name="rs_address_longitude" class="" value="<?php echo $info['rs_address_longitude']; ?>" /></td>
        </tr>

		
    </table>
    <?php
}



function add_rs_coffeeshop_fields($rs_coffee_id, $rs_coffees) {
    
    if ($rs_coffees->post_type == 'events') {
	$coffee_fields=array('rs_rush_hours','rs_street','rs_zipcode','rs_city','rs_country','rs_ratecoffee','rs_ratewifi','rs_address_longitude','rs_address_latitude');
                    if(isset($coffee_fields) && is_array($coffee_fields) && count($coffee_fields)>0){
		
			foreach($coffee_fields as $rs_meta_key){			
				if (isset($_POST[$rs_meta_key]) && $_POST[$rs_meta_key] != '') {
		            update_post_meta($rs_coffee_id, $rs_meta_key, $_POST[$rs_meta_key]);
		        } else {
		            delete_post_meta($rs_coffee_id, $rs_meta_key);
		        }			
			}	
		}
	}
}	
add_action('save_post', 'add_rs_coffeeshop_fields',10,2);




Multiple image upload with metabox
====================================================================================
functions or plugins.php te hobe
/*Metabox with multiple image upload*/

function image_upload_metafield() {

    add_meta_box('image_upload_meta_box', 'Upload Images', 'image_upload_meta', 'post', 'normal', 'high');

}
add_action('admin_init', 'image_upload_metafield');



function image_upload_meta($post) {

	$info=array();
	
	   //Multiple Image  Fields
    $img_link =get_post_meta( $post->ID, 'multiimg', true );
	
	?>
    <table>	
        <tr>
            <td>Image </td>
            <td>
               <ul id="_imageMulti">
                <?php
                    if(isset($img_link) && count($img_link) >0 && is_array($img_link)){
                        foreach($img_link as $img_val){
                    ?>
                    <li class="">
                    <div>
                        <img width="220" src="<?php echo $img_val;?>">
                        <input type="hidden" value="<?php echo $img_val; ?>" name="multiimg[]">
                        <a class="remove_this_parent" href="#">[ X ]</a>
                    </div>
                    </li>           
                <?php
                        }//End Foreach
                    }
                ?>

                </ul>
                <input class="button-primary mhs_theme_image_uploader_multi" type="button" value="Image Upload" data-target_div="_imageMulti" />
            </td>           
        </tr>
	</table>
	
	
	<script type="text/javascript">
		jQuery(document).ready(function(){
	//Upload Multiple Images    
    jQuery( this ).delegate( ".remove_this_parent", "click", function(e) {
        e.preventDefault();
        jQuery(this).parent().remove();
    });
   
    jQuery( document ).delegate( ".mhs_theme_image_uploader_multi", "click", function(e) {
        e.preventDefault();
        var target_div = jQuery(this).attr('data-target_div');
        var target_img_size = '220';
        if(jQuery(this).attr('data-target_img_size')){
            target_img_size = jQuery(this).attr('data-target_img_size');
        }
       
        mhs_theme_image_frame = wp.media({
            title : 'Add Image',
            frame: 'post',
            multiple : false, // set to false if you want only one image
            library : { type : 'image'},
            button : { text : 'Add Image' }
        });
        mhs_theme_image_frame.on('close',function(data) {
            images = mhs_theme_image_frame.state().get('selection');
            images.each(function(image) {
                jQuery('#'+target_div).append('<li class="ui-state-default"><div><img width="'+target_img_size+'" src="'+image.attributes.url+'" /><input type="hidden" name="multiimg[]" value="'+image.attributes.url+'" /><a href="#" class="remove_this_parent">[ X ]</a></div></li>');
            });

        });
        mhs_theme_image_frame.open();
    });
   
//Upload Multiple Images 	
		});
	</script>
    <?php
}



function add_rs_coffeeshop_fields($rs_coffee_id, $rs_coffees) {
    
    if ($rs_coffees->post_type == 'post') {
	$coffee_fields=array('multiimg');
                    if(isset($coffee_fields) && is_array($coffee_fields) && count($coffee_fields)>0){
		
			foreach($coffee_fields as $rs_meta_key){			
				if (isset($_POST[$rs_meta_key]) && $_POST[$rs_meta_key] != '') {
		            update_post_meta($rs_coffee_id, $rs_meta_key, $_POST[$rs_meta_key]);
		        } else {
		            delete_post_meta($rs_coffee_id, $rs_meta_key);
		        }			
			}	
		}
	}
}	
add_action('save_post', 'add_rs_coffeeshop_fields',10,2);


view page
===========================================
<div class="multiple_image_area">
			<div class="left_image_content">
				<p>More Images</p>
			</div>
			<div class="left_image_content">
				<ul id="_imageMulti">
					<?php
						$img_link =get_post_meta( $post->ID, 'multiimg', true );
						echo count($img_link);
						if(isset($img_link) && count($img_link) >0 && is_array($img_link)){
							foreach($img_link as $img_val){
						?>
						<li class="">
						<div id="single_image">
							<img width="100" src="<?php echo $img_val;?>">
							<input type="hidden" value="<?php echo $img_val; ?>" name="multiimg[]">
						</div>
						</li>           
					<?php
							}//End Foreach
						}
					?>
				</ul>
			</div>
		</div>
