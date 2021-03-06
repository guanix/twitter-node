# twitter-node

Creates a streaming connection with twitter, and pushes any incoming statuses to a tweet event.

## Installation

Depends on ntest.

There's no packaging system for node.js yet, so I've just been creating symlinks
in my `~/.node_libraries` path.

    $ ln -s /path/to/twitter-node/lib ~/.node_libraries/twitter-node

## Events

TwitterNode emits these events:

* tweet(json) - This is emitted when a new tweet comes in.  If the format is set to 'json' (default), then this is a parsed JSON object.
* limit(json) - This is emitted when a new limit command comes in.  Currently, limit detection only works with parsed JSON objects.
* delete(json) - This is emitted when a new delete command comes in.  Currently, delete detection only works with parsed JSON objects.
* close(response) - This is emitted when the http connection is closed.  The HTTP response object is sent.

See the [streaming API docs][api-docs] for examples of the limit and delete commands.

[api-docs]: http://apiwiki.twitter.com/Streaming-API-Documentation

## Usage

    var TwitterNode = require('twitter-node').TwitterNode;
    // you can pass args to create() or set them on the TwitterNode instance
    var twit = new TwitterNode({
      user: 'username', 
      password: 'password',
      track: ['baseball', 'football'],
      follow: [12345, 67890]})

    // adds to the track array set above
    twit.track('foosball')

    // adds to the following array set above
    twit.follow(2345)

    twit.params['count'] = 100
    twit.headers['User-Agent'] = 'whatever'
    twit.action = 'sample' // 'filter' is default

    twit
      .addListener('tweet', function(tweet) {
        sys.puts("@" + tweet.user.screen_name + ": " + tweet.text)
      })

      .addListener('limit', function(limit) {
        sys.puts("LIMIT: " + sys.inspect(limit))
      })

      .addListener('delete', function(del) {
        sys.puts("DELETE: " + sys.inspect(del))
      })

      .addListener('close', function(resp) {
        sys.puts("wave goodbye... " + resp.statusCode)
      })

      .stream()

## Pre-Launch Checklist

See http://apiwiki.twitter.com/Streaming-API-Documentation.  Keep these points in mind when getting ready to use TwitterNode in production:

* Not purposefully attempting to circumvent access limits and levels?
* Creating the minimal number of connections?
* Avoiding duplicate logins?
* Backing off from failures: none for first disconnect, seconds for repeated network (TCP/IP) level issues, minutes for repeated HTTP (4XX codes)?
* Using long-lived connections?
* Tolerant of other objects and newlines in markup stream? (Non <status> objects...)
* Tolerant of duplicate messages?

## TODO

* Handle failures as recommended from the Twitter stream documentation.

## Copyright

Copyright (c) 2010 rick. See LICENSE for details.
