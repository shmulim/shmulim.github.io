---
layout: post
title:  "Desktop Notifications for WordPress Events"
date:   2016-11-29 06:44:15 +0000
---

In this article we take a look at how to create realtime desktop notifications for events that occur within WordPress.

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

Then with OpenShift...

```
var app = require('express')();
var server = require('http').Server(app);
var io = require('socket.io')(server);

var port = process.env.OPENSHIFT_NODEJS_PORT || 8080;
var ip = process.env.OPENSHIFT_NODEJS_IP || '127.0.0.1';

io.origins('*:*')

app.get('/', function (req, res) {
  if (req.query.event_name && req.query.site_name){
    var event_name  = req.query.event_name;
    var site_name   = req.query.site_name;
    io.emit('notification', { event_name: event_name, site_name: site_name });
    res.send('');
  }
});
    
server.listen(port, ip);
```

Then in Electron...

```
var socket = require('socket.io-client')('ws://notifications-shmulim.rhcloud.com:80');
var notifier = require('node-notifier');
```

and..

```
socket.on('notification', function (data) {
  notifier.notify({
  title: 'Event Published!',
  message: data.event_name+' event published on '+data.site_name,
  icon: 'cyp-pin.png',
  sound: true,
  wait: true
}, function (err, response) {
     if (err) console.log(err);
  });
});
```

Finally, add `show: false` to the `BrowserWindow` options.

Install `electron-packager` and run `electron-packager . --darwin` from the project directory.
