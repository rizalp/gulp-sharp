[gulp](http://gulpjs.com/)-sharp
=======================================

> resize image in your gulp build with [sharp](https://github.com/lovell/sharp)

## Prerequisites

* Node.js v0.10+
* libvips v7.38.5+

On Ubuntu 14.04, installing libvips is as easy as :

```
sudo apt-get install libvips-dev
```

For other system, more info can be seen at [sharp Prerequisites](https://github.com/lovell/sharp#prerequisites). Or, if you'd like to build from source, head over to [libvips](https://github.com/jcupitt/libvips)

## Installation

In your project directory, type:

```
npm install --save-dev gulp-sharp
```

Then, you can use `gulp-sharp` by simply requiring it

```
var gulpSharp = require('gulp-sharp');
```

## Usage examples

`gulp-sharp` can consume emitted data from `gulp.src` in two ways:

1. As-is, meaning it will read the `file.contents` property of `vinyl` file objects
2. Only read the `file.path` property of `vinyl` file objects, by specifying `{read:false}` in `gulp.src` options.

The former way is more compatible with other gulp plugins (since it just modify the contents of the file (`Buffer`), aka the standard way). While the latter may provide better performance due to the use of [mmap](http://en.wikipedia.org/wiki/Mmap) internally by libvips.

First Way:

```javascript
var gulp = require('gulp');
var gulpSharp = require('gulp-sharp');

gulp.task('image-resize', function(){

  return gulp.src( 'content/**/*.+(jpeg|jpg|png|tiff|webp)' )
    .pipe(gulpSharp({
      resize : [1280, 800],
      max : true,
      quality : 60,
      progressive : true
    }))
    .pipe(gulp.dest('output'));

});
```

Second Way, by specifying `options.read` as `false` in `gulp.src` so the `file.contents` property will be null [documentation](https://github.com/gulpjs/gulp/blob/master/docs/API.md#optionsread) :

```javascript
var gulp = require('gulp');
var gulpSharp = require('gulp-sharp');

gulp.task('image-resize', function(){

  return gulp.src( 'content/**/*.+(jpeg|jpg|png|tiff|webp)', {read : false} )
    .pipe(gulpSharp({
      resize : [1280, 800],
      max : true,
      quality : 60,
      progressive : true
    }))
    .pipe(gulp.dest('output'));

});
```

## Options

Type: `Object`

This `options` will be used internally to assemble `sharp` pipeline.

### options.resize

Type: `Array`; Default: `undefined`

Required property. `gulp-sharp` will throw an error if you omit this property. It contains two element, `[width, height]` which is a `Number`. You can pass `null` or `undefined` to auto-scale.

Example:

```
{ read : [1024, 600] } // resize width to 1024px, and height 600px
{ read : [1024] } // resize width to 1024px and auto-height
{ read : [,600] } // auto-width, and resize to 600px
```

### options.withoutEnlargement

Type: `Boolean`; Default: `true`

Optional. Do not enlarge the output image if the input image width or height are already less than the required dimensions.

### options.max

Type: `Boolean`; Default: `false`

Optional. Use this if you'd like to preserve aspect ratio of the image when you specify both `width` and `height` in the `options.resize` property.

Both width and height must be provided via resize otherwise the behaviour will default to crop.

### options.crop

Type: `String`; Default: `''`; Possible Value: `'north'`, `'east'`, `'south'`, `'west'`, `'center'`

Optional. Crop the image with the `gravity` (Possible Values) to the exact size specified by `options.resize`.

### options.interpolateWith

Type: `String`; Default: `''`; Possible Value: `'nearest'`, `'bilinear'`, `'bicubic'`, `'vertexSplitQuadraticBasisSpline'`, `'locallyBoundedBicubic'`, `'nohalo'`

Optional. You can omit this property, and `sharp` will use `bilinear` interpolation by default. Head over to [interpolateWith](https://github.com/lovell/sharp#interpolatewithinterpolator) for more information about other interpolation algorithm

### options.embedWhite

Type: `Boolean`; Default: `false`

Optional. Embed the resized image on a white background of the exact size specified.

### options.embedBlack

Type: `Boolean`; Default: `false`

Optional. Embed the resized image on a black background of the exact size specified.

### options.rotate

Type: `Boolean` or `Number`; Default: `false`; Possible Value: `true`, `0`, `90`, `180`, `270`

Optional. Rotate the output image by either an explicit angle (`Number`) or auto-orient based on the EXIF `Orientation` tag (`true`).

### options.sharpen

Type: `Boolean`; Default: `false`

Optional. Perform a mild sharpen of the output image. This typically reduces performance by 10%.

### options.gamma

Type: `Number`; Default: `0`; Possible Value: Real Number between 1 and 3

Optional. Apply a gamma correction by reducing the encoding (darken) pre-resize at a factor of 1/`gamma` then increasing the encoding (brighten) post-resize at a factor of `gamma`.

Recomended value is `2.2`, a suitable approximation for sRGB images.

This can improve the perceived brightness of a resized image in non-linear colour spaces.

JPEG input images will not take advantage of the shrink-on-load performance optimisation when applying a gamma correction.

### options.grayscale

Type: `Boolean`; Default: `false`

Optional. Convert to grayscale

### options.withMetadata

Type: `Boolean`; Default: `false`

Optional. Include all metadata (ICC, EXIF, XMP) from the input image in the output image. The default behaviour is to strip all metadata.

### options.quality

Type: `Number`; Default: `80`; Possible Value: Number between `1` and `100`

Optional. The output quality to use for lossy JPEG, WebP and TIFF output formats. The default quality is 80. This property is ignored for PNG images

### options.progressive

Type: `Boolean`; Default: `false`

Optional. Use progressive (interlace) scan for JPEG and PNG output. This typically reduces compression performance by 30% but results in an image that can be rendered sooner when decompressed.

### options.compressionLevel

Type: `Number`; Default: `6`; Possible Value: Number between `-1` and `9`

Optional. An advanced setting for the zlib compression level of the lossless PNG output format. The default level is 6. This property is ignored if image input is not `png`

### options.output

Type: `String`; Default: `''`; Possible Value: `'jpeg'`, `'png'`, `'webp'`

Optional. Use this property if you'd like to convert image input into another image format.

## License

MIT © Mohammad Shahrizal Prabowo
