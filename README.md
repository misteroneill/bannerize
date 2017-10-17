# Bannerize [![Build Status](https://travis-ci.org/misteroneill/bannerize.svg?branch=master)](https://travis-ci.org/misteroneill/bannerize)

Add dynamic banner/license comments to files in a build process.

```sh
$ npm install -g bannerize
```

## Banner Templates

Banner templates use the [EJS](https://www.npmjs.com/package/ejs) templating language. Templates are passed the following properties:

- `pkg`: A representation of the nearest `package.json` file.
- `date`: A JavaScript Date object.

A simple banner might look something like:

```
/*! <%= pkg.name %> | <%= pkg.version %>
 *  (c) <%= date.getFullYear() %> <%= pkg.license %>
 */
```

And render to:

```
/*! bannerize | 1.0.0
 *  (c) 2015 MIT
 */
```

## CLI

`bannerize` ships with a CLI command. Its options vary from the programmatic API. To see all its options, use:

```sh
$ bannerize --help
```

An example usage might look like:

```sh
$ bannerize *.js *.css --banner=foo/bar.ejs
```

## API

The `bannerize` module can be used in your programs. It exports a single function, `bannerize`, which takes two arguments:

### `bannerize(patterns, [options])`

- `pattern` `{String|Array}`: A string or array of glob pattern(s) to which to apply the banner.
- `[options]` `{Object}`: An object containing optional values.

The return value of `bannerize()` is a `Promise` that resolves with an array of all the file paths it modified.

### Options

- `banner`
  A banner file location. Defaults to `banner.ejs` in the `cwd`.

- `cwd`
  Override the `cwd` for all paths passed to `bannerize`. Relative paths will be relative to `process.cwd()`. Defaults to `process.cwd()`.
  
- `lineBreak` (or `lineBreaks`)
  Sets the linebreak (`'CRLF'`, `'LF'`). Defaults to `'LF'`.

## API Example

Let's say you have a project with a structure like this:

```
├─┬ dist/
│ └── output.min.js
├─┬ scripts/
│ ├── add-banner.js
| └── banner.ejs
└── package.json
```

And let's say you want `scripts/add-banner.js` to add a banner to `dist/output.min.js` using `scripts/banner.ejs` as a template. You'll need to know the location of the output file and the banner template and the script might look like this:

```js
const bannerize = require('bannerize');

// The paths here are relative to the current working directory. For the sake
// of example, let's say we don't know what it might be; so, we'll use
// __dirname (which is scripts/ in our outlined directory structure above).
//
// We pass in the path to our output file(s), relative to __dirname:
bannerize('../dist/output.min.js', {

  // This is the path to our banner template file, relative to __dirname.
  banner: 'banner.ejs',

  // Finally, we specify the cwd as the directory where this file is 
  // (i.e. scripts/)
  cwd: __dirname
}).

// bannerize returns a Promise which resolves with a list of files. We log
// the list here for debugging.
then((files) => {
  console.log('Added banner(s) to ' + files.join(', '));
}, () => {
  console.error('Failed adding banner(s)!');
});
```
