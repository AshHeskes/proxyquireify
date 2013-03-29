# proxyquireify [![build status](https://secure.travis-ci.org/thlorenz/proxyquireify.png)](http://travis-ci.org/thlorenz/proxyquireify)

browserify version of proxyquire. Mocks out browserify's require to allow stubbing out dependencies while testing

## Status

Very alpha, although the main functionality is there.

## Example

Lets say we have `bar.js`:

```js
exports.wunder = function () { 
  return 'wunderbar'; 
};
```

and `foo.js`:

```js
var bar = require('./bar');

module.exports = function () {
  return bar.kinder() + ' ist ' + bar.wunder();
};
```

then we can stub `bar` in a test like so:

```js
var proxyquire = require('proxyquireify')(require);

var stubs = { 
  './bar': { 
      wunder: function () { return 'wirklich wunderbar'; }
    , kinder: function () { return 'schokolade'; }
  }
};

var foo = proxyquire('./src/foo', stubs);

// This require to be called AFTER proxyquire in order for browserify to include it in the bundle
require('./src/foo');

console.log(foo()); 
```

Finally build the bundle via `build.js`:

```js
browserify()
  .require(require.resolve('./test'), { entry: true })
  .transform(require('proxyquireify').transform)
  .bundle({ debug: true })
  .pipe(fs.createWriteStream(__dirname + '/bundle.js'));
```

load it in the browser and see:

    schokolade ist wirklich wunderbar
