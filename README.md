<?php

/**
 * Attributes shortcode callback.
 */
function so_39394127_attributes_shortcode( $atts ) {

    global $product;

    if( ! is_object( $product ) || ! $product->has_attributes() ){
        return;
    }

    // parse the shortcode attributes
    $args = shortcode_atts( array(
        'attributes' => array_keys( $product->get_attributes() ), // by default show all attributes
    ), $atts );

    // is pass an attributes param, turn into array
    if( is_string( $args['attributes'] ) ){
        $args['attributes'] = array_map( 'trim', explode( '|' , $args['attributes'] ) );
    }

    // start with a null string because shortcodes need to return not echo a value
    $html = '';

    if( ! empty( $args['attributes'] ) ) {

        foreach ( $args['attributes'] as $attribute ) {

            // get the WC-standard attribute taxonomy name
            $taxonomy = strpos( $attribute, 'pa_' ) === false ? wc_attribute_taxonomy_name( $attribute ) : $attribute;

            if( taxonomy_is_product_attribute( $taxonomy ) ){

                // Get the attribute label.
                $attribute_label = wc_attribute_label( $taxonomy );

                // Build the html string with the label followed by a clickable list of terms.
                // Updated for WC3.0 to use getters instead of directly accessing property.
                $html .= get_the_term_list( $product->get_id(), $taxonomy, '<tr><td>' . esc_html( $attribute_label ) . ': ' , ', ', '</td></tr>' );
            }

        }

		$html = '<table class="product-attributes">' . $html . '</table>';

    }

    return $html;
}
add_shortcode( 'display_attributes', 'so_39394127_attributes_shortcode' );
