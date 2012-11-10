#twit

Twitter API Client for node

Supports both the **REST** and **Streaming** API.

#Installing

```
npm install twit

```

##Usage:

```javascript
var Twit = require('twit')

var T = new Twit({
    consumer_key:         '...'
  , consumer_secret:      '...'
  , access_token:         '...'
  , access_token_secret:  '...'
})

//
//  tweet 'hello world!'
//
T.post('statuses/update', { status: 'hello world!' }, function(err, reply) {
  //  ...
})
      
//
//  search twitter for all tweets containing the word 'banana' since Nov. 11, 2011
//
T.get('search/tweets', { q: 'banana since:2011-11-11' }, function(err, reply) {
  //  ...
})

//
//  get the list of user id's that follow @tolga_tezel
//
T.get('followers/ids', { screen_name: 'tolga_tezel' },  function (err, reply) {
  //  ...    
})

//
//  stream a sample of public statuses
//
var stream = T.stream('statuses/sample')

stream.on('tweet', function (tweet) {
  console.log(tweet)
})
      
//
//  filter the twitter public stream by the word 'mango'. 
//
var stream = T.stream('statuses/filter', { track: 'mango' })

stream.on('tweet', function (tweet) {
  console.log(tweet)
})

//
// filter the public stream by the latitude/longitude bounded box of San Francisco
//
var sanFrancisco = [ '-122.75', '36.8', '-121.75', '37.8' ]

var stream = T.stream('statuses/filter', { locations: sanFrancisco })

stream.on('tweet', function (tweet) {
  console.log(tweet)
})

```

# twit API:

Just 3 methods. They cover the full twitter API.

##`T.get(path, [params], callback)`         
GET any of the REST API Endpoints.

##`T.post(path, [params], callback)`        
POST any of the REST API Endpoints.

##`T.stream(path, [params])`               
Use this with the Streaming API.

Note: Omit the `.json` from `path` (i.e. use `'statuses/sample'` instead of `'statuses/sample.json'`).

###params

Params is an optional object, allowing you to pass in parameters to Twitter when making a request. Any Arrays passed into `params` get converted to comma-separated strings, allowing you to do requests like:

```javascript
//
// I only want to see tweets about my favorite fruits
//

//same result as doing { track: 'bananas,oranges,strawberries' }
var stream = T.stream('statuses/filter', { track: ['bananas', 'oranges', 'strawberries'] })

stream.on('tweet', function (tweet) {
  //...
})
```

# Using the Streaming API

`T.stream(path, [params])` keeps the connection alive, and returns an `EventEmitter`.

###path


* If `path` is `'user'`, the User stream of the authenticated user will be streamed.
* If `path` is `'site'`, the Site stream of the authenticated application will be streamed.
* If `path` is anything other than `'user'` or `'site'`, the Public stream will be streamed.

The following events are emitted:

##event: 'tweet'

```javascript
stream.on('tweet', function (tweet) {
  //...
})
```

Emitted each time a status (tweet) comes into the stream.

##event: 'delete'

```javascript
stream.on('delete', function (deleteMessage) {
  //...
})
```

Emitted each time a status (tweet) deletion message comes into the stream.

##event: 'limit'

```javascript
stream.on('limit', function (limitMessage) {
  //...
})
```

Emitted each time a limitation message comes into the stream.

##event: 'scrub_geo'

```javascript
stream.on('scrub_geo', function (scrubGeoMessage) {
  //...
})
```

Emitted each time a location deletion message comes into the stream.

##event: 'disconnect'

```javascript
stream.on('disconnect', function (disconnectMessage) {
  //...
})
```

Emitted when a disconnect message comes from Twitter. This occurs if you have multiple streams connected to Twitter's API. Upon receiving a disconnect message from Twitter, `Twit` will close the connection and emit this event with the message details received from twitter.

##event: 'connect'

```javascript
stream.on('connect', function (request) {
  //...
})
```

Emitted when a connection attempt is made to Twitter. The http `request` object is emitted.

##event: 'reconnect'

```javascript
stream.on('reconnect', function (request, response, connectInterval) {
  //...
})
```

Emitted when a reconnection attempt is made to Twitter. The http `request` and `response` objects are emitted, along with the time (in milliseconds) left before the reconnect occurs. `Twit` follows Twitter's guidelines on reconnecting to the Streaming API.

##stream.stop()

Call this function on the stream to stop streaming (closes the connection with Twitter).

##stream.start()

Call this function to restart the stream after you called `.stop()` on it.
Note: there is no need to call `.start()` to begin streaming. `Twit.stream` calls `.start()` for you.

-------

#What do I have access to?

Anything in the Twitter API:

* REST API Endpoints:       https://dev.twitter.com/docs/api
* Public stream endpoints:  https://dev.twitter.com/docs/streaming-api/methods
* User stream endpoints:    https://dev.twitter.com/docs/streaming-api/user-streams
* Site stream endpoints:    https://dev.twitter.com/docs/streaming-api/site-streams

-------

Go here to create an app and get OAuth credentials (if you haven't already): https://dev.twitter.com/apps/new


#How do I run the tests?

Clone the repo

```
git clone git@github.com:ttezel/twit.git
```

Install [mocha](https://github.com/visionmedia/mocha)

```
npm install mocha -g
```

Note: When the `-g` flag is invoked, the package will be installed globally. In order to use `mocha` from the command line, you must use the `-g` flag. This is necessary to run the tests with `npm test`.

Create a `config.js` file in the root of the cloned repo. It should export the oauth credentials. It should look something like this:

```
module.exports = {
    consumer_key: '...'
  , consumer_secret: '...'
  , access_token: '...'
  , access_token_secret: '...'
}
```

Then run the tests:

```
npm test
```

You can also run the example:

```
node examples/rtd2.js
```

![iRTD2](http://dl.dropbox.com/u/32773572/RTD2_logo.png)

The example is a twitter bot named [RTD2](https://twitter.com/#!/iRTD2) written using `twit`. RTD2 tweets about **github** and curates its social graph.

-------

## License 

(The MIT License)

Copyright (c) by Tolga Tezel <tolgatezel11@gmail.com>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.

## Changelog

###1.0.0
  * now to stop and start the stream, use `stream.stop()` and `stream.start()` instead of emitting the `start` and `stop` events
  * If twitter sends a `disconnect` message, closes the stream and emits `disconnect` with the disconnect message received from twitter

###0.2.0
  * Updated `twit` for usage with v1.1 of the Twitter API.

###0.1.5

  * **BREAKING CHANGE** to `twit.stream()`. Does not take a callback anymore. It returns 
    immediately with the `EventEmitter` that you can listen on. The `Usage` section in 
    the Readme.md has been updated. Read it.


###0.1.4

  * `twit.stream()` has signature `function (path, params, callback)`