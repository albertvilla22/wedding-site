function render_single_active_ld_track() {
    if ( !function_exists('learndash_user_get_enrolled_courses') || !is_user_logged_in() ) {
        return '';
    }

    $user_id = get_current_user_id();
    $enrolled = learndash_user_get_enrolled_courses($user_id);

    if ( empty($enrolled) ) {
        return 'No active tracks found.';
    }

    ob_start();
    echo '<div class="ld-dashboard-wrapper">';

    foreach ( $enrolled as $c_id ) {
        // Use course-level step count (Lessons only)
        $completed_steps = learndash_course_get_completed_steps($user_id, $c_id);
        $total_steps = learndash_get_course_steps_count($c_id);
        $percentage = ($total_steps > 0) ? round(($completed_steps / $total_steps) * 100) : 0;

        if ($percentage >= 100) continue;

        // Force 'sfwd-lessons' post type only to exclude topics
        $lessons = learndash_get_lesson_list($c_id, array('num' => -1));
        $active_lesson_title = '';

        foreach ($lessons as $lesson) {
            // Strict lesson completion check
            if ( !learndash_is_lesson_complete($user_id, $lesson->ID, $c_id) ) {
                $active_lesson_title = get_the_title($lesson->ID);
                break; 
            }
        }

        if ( !empty($active_lesson_title) ) {
            ?>
            <div class="ld-dash-track-card">
                <div class="ld-dash-badge"><?php echo esc_html(get_the_title($c_id)); ?></div>
                
                <div class="ld-lessons-container">
                    <ul class="ld-lesson-status-list">
                        <li>
                            <span class="lesson-name"><?php echo esc_html($active_lesson_title); ?></span>
                            <span class="lesson-status-label status-inprogress">In Progress</span>
                        </li>
                    </ul>
                </div>
                
                <div class="ld-dash-progress-container">
                    <div class="ld-dash-progress-header">
                        <span class="track-text">Track Progress</span>
                        <span class="perc-text"><?php echo (int)$percentage; ?>%</span>
                    </div>
                    <div class="ld-dash-progress-bar-bg">
                        <div class="ld-dash-progress-bar-fill" style="width: <?php echo (int)$percentage; ?>%;"></div>
                    </div>
                </div>
            </div>
            <?php
            break; 
        }
    }

    echo '</div>';
    return ob_get_clean();
}
// Fixed spacing to prevent critical errors during save
add_shortcode('ld_dynamic_dashboard_track', 'render_single_active_ld_track');

function ld_user_next_progress_card() {

    if ( ! is_user_logged_in() ) {
        return '';
    }

    if ( ! function_exists( 'learndash_user_get_enrolled_courses' ) ) {
        return '<p>LearnDash not active.</p>';
    }

    $user_id = get_current_user_id();
    $courses = learndash_user_get_enrolled_courses( $user_id );

    if ( empty( $courses ) ) {
        return '<p>No enrolled courses.</p>';
    }

    foreach ( $courses as $course_id ) {

        // Get lessons first
        $lessons = learndash_get_course_lessons_list( $course_id, $user_id );

        if ( empty( $lessons ) ) {
            continue;
        }

        foreach ( $lessons as $lesson ) {

            $lesson_id = $lesson['post']->ID;

            // If lesson not completed → this is next
            if ( ! learndash_is_lesson_complete( $user_id, $lesson_id ) ) {

                // Check topics inside the lesson
                $topics = learndash_get_topic_list( $lesson_id, $course_id );

                if ( ! empty( $topics ) ) {
                    foreach ( $topics as $topic ) {
                        if ( ! learndash_is_topic_complete( $user_id, $topic->ID ) ) {
                            return ld_render_next_up_card(
                                get_the_title( $topic->ID ),
                                get_permalink( $topic->ID ),
                                get_the_title( $course_id )
                            );
                        }
                    }
                }

                // No topics or all topics done → lesson is next
                return ld_render_next_up_card(
                    get_the_title( $lesson_id ),
                    get_permalink( $lesson_id ),
                    get_the_title( $course_id )
                );
            }
        }
    }

    return '<p>You are all caught up 🎉</p>';
}

/**
 * Card HTML
 */
function ld_render_next_up_card( $title, $link, $course ) {
    ob_start(); ?>
    
    <div class="ld-next-up-card">
        <div class="ld-next-up-header">
            <span class="ld-sparkle">✨</span>
            <span>Next up</span>
        </div>

        <h3 class="ld-week"><?php echo esc_html( $title ); ?></h3>
        <p class="ld-course-name"><?php echo esc_html( $course ); ?></p>

        <a href="<?php echo esc_url( $link ); ?>" class="ld-continue-btn">
            Continue
        </a>
    </div>

    <?php
    return ob_get_clean();
}

add_shortcode( 'ld_next_progress', 'ld_user_next_progress_card' );
