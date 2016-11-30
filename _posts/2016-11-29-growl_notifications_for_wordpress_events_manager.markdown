---
layout: post
title:  "Growl Notifications for WordPress Events Manager"
date:   2016-11-29 06:44:15 +0000
---

```
<?php
/**
 * Plugin Name: Growl Notifications for Event Manager
 * Description: Pings a notification server when an event is published. 
 * Version: 1.0.0
 * Author: Shmuli Markel
 * Author URI: http://shmulimarkel.com/
 */

if ( !defined( 'ABSPATH' ) ) exit;

function ping_notification_server( $post_id, $post, $update ) {
    $url = add_query_arg( array(
      'site_name' => get_bloginfo('name'),
      'event_name' => get_the_title($post_id),
    ), 'https://notifications-shmulim.rhcloud.com/' );
    wp_remote_get($url);
}
add_action( 'publish_event', 'ping_notification_server', 10, 3 );
add_action( 'publish_event_recurring', 'ping_notification_server', 10, 3);
```
