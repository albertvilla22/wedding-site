function thim_next_event_card_shortcode() {

    if ( ! post_type_exists( 'tp_event' ) ) {
        return '<p>WP Events Manager not active.</p>';
    }

    $args = array(
        'post_type'      => 'tp_event',
        'posts_per_page' => 1,
        'post_status'    => 'publish',
        'orderby'        => 'meta_value',
        'order'          => 'ASC',
        'meta_key'       => 'tp_event_date_start',
    );

    $query = new WP_Query( $args );

    if ( ! $query->have_posts() ) {
        return '<p>No upcoming events.</p>';
    }

    ob_start();

    while ( $query->have_posts() ) {
        $query->the_post();

        $event_id = get_the_ID();
        $event_url = get_permalink();

        // Try multiple meta keys (ThimPress versions differ)
        $start_time = get_post_meta( $event_id, 'tp_event_date_start', true );

        if ( ! $start_time ) {
            $start_time = get_post_meta( $event_id, 'tp_event_start_time', true );
        }

        $formatted_time = $start_time
            ? date( 'l @ g:i A', intval( $start_time ) )
            : 'Schedule TBD';
        ?>
        <div class="event-card">
            <h3 class="event-title"><?php the_title(); ?></h3>

            <p class="event-time">
                Next call: <?php echo esc_html( $formatted_time ); ?>
            </p>

            <div class="event-actions">
                <a href="<?php echo esc_url( $event_url ); ?>?ical=1" class="btn calendar">
                    Add to Calendar
                </a>

                <a href="<?php echo esc_url( $event_url ); ?>" class="btn join">
                    Join
                </a>
            </div>
        </div>
        <?php
    }

    wp_reset_postdata();
    return ob_get_clean();
}

add_shortcode( 'next_event_card', 'thim_next_event_card_shortcode' );
