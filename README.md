> This project is no longer maintained, use [http-basic](https://www.npmjs.com/package/http-basic) instead.

# hyperdirect

Follow redirects for hyperquest GET requests.  Process all other requests exactly as normal.

## Installation

    $ npm install hyperdirect

## Usage

```js
//basic usage
var request = require('hyperdirect').request;

request('https://github.com/ForbesLindesay/hyperdirect/archive/master.tar.gz')
  .pipe(require('fs').createWriteStream(__dirname + '/hyperdirect.tar.gz'));

//moderate usage
var request = require('hyperdirect')(2/* Max Redirects to follow, defaults to 10 */);

request('https://github.com/ForbesLindesay/hyperdirect/archive/master.tar.gz')
  .pipe(require('fs').createWriteStream(__dirname + '/hyperdirect.tar.gz'));

//advanced usage
var hyperdirect = require('hyperdirect')(5, require('hyperquest'));
```

## API

### hyperdirect(maxRedirects, subquest)

Returns a new function with the same API as [hyperquest](https://github.com/hyperquest/hyperquest) but which follows redirects for GET requests.  Both `maxRedirects` and `subquest` are optional and can be in either order.

If provided, `subquest` should be a function which matches the API of [hyperquest](https://github.com/hyperquest/hyperquest) or an object of the form `{request: hyperquest}` where `hyperquest` is a function matching the API of [hyperquest](https://github.com/hyperquest/hyperquest).

`maxRedirects` will default to `10`.  Once the request has been redirected more times than that it will give up and throw an error.  The error has a `res` and `statusCode` property set to the apropriate properties from the last request made.

The returned stream will emit `redirect` events with the response of any request that results in another redirect.  This means you could trace the path something a request took by doing:

```js
require('hyperdirect')(100)(url)
  .on('redirect', function (res) {
    console.log('REDIRECT ' + res.statusCode + ': ' + res.headers.location);
  })
  .on('response', function (res) {
    console.log('FINAL URL: ' + res.url);
  });
```

### hyperdirect.request(uri, opts, cb)

Follows exactly the same API as [hyperquest](https://github.com/hyperquest/hyperquest) but automatically follows up to 10 redirects for GET requests.  It's the same as what you get by calling `hyperdirect()`.

## License

MIT
