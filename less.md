## Literals

### Variables

```less
@less-blue: #1E375C;

#header {
  color: @less-blue;
}
```

#### Interpolation

```less
// in selectors
@my-selector: banner;

.@{my-selector} {
  font-weight: bold;
  line-height: 40px;
  margin: 0 auto;
}

// in URLs
@images: "../img";

body {
  color: #444;
  background: url("@{images}/white-sand.png");
}

// in import statements
@themes: "../../src/themes";

@import "@{themes}/tidal-wave.less";

// in property names
@property: color;

.widget {
  @{property}: #EEE;
  background-@{property}: #999;
}

// define variables with a variable name
@fnord:  "I am fnord.";
@var:    "fnord";
content: @@var; // "I am fnord."
```

#### Lazy Loading

```less
// variables are lazy loaded
// they do not have to be declared before being used
.lazy-eval {
  width: @var;
}

@var: @a;
@a: 9%;
```

#### Overriding

```less
// You can easily override a variable by putting the definition afterwards.
@base-color: green;

@import "library.less";
@base-color: red;
```

### Operations

```less
// numbers are converted into the same units
@conversion-1: 5cm + 10mm; // > cm
@conversion-2: 2 - 3cm - 5mm; // > cm

// can be used with variables
@base: 5%;
@filler: @base * 2;
@other: @base + @filler;

// can be used with colors
@color: #224488 / 2;
background-color: #112244 + #111;
```

### Comments

```less
/* A Block-
Style Comment */
@var: red;

// a line comment
@var: white;
```

## Mixins

```less
.bordered {
  border-top: dotted 1px black;
  border-bottom: solid 2px black;
}

#menu a {
  .bordered;
  color: #111;
}

.post a {
  .bordered;
  color: red;
}

// mixins can contain properties and selectors
.my-hover-mixin() {
  &:hover {
    border: 1px solid red;
  }
}

button {
  .my-hover-mixin();
}

// use guards on mixins as a condition
.mixin() when (@mode=huge) { /* ... */ }

// mixin more complicated selectors by nesting (also known as namespacing)
#outer {
  .inner {
    color: red;
  }

  .my-mixin() {
    color: black;
  }
}

.c {
  #outer > .inner;
  #outer > .my-mixin();
}
```

### Guards

```less
// use of keyword 'when'
// comparison operators: >, >=, =, =<, <
.mixin (@a) when (lightness(@a) >= 50%) {
  background-color: black;
}
.mixin (@a) when (lightness(@a) < 50%) {
  background-color: white;
}
.mixin (@a) {
  color: @a;
}

.class1 { .mixin(#ddd) } // matches first one
.class2 { .mixin(#555) } // matches second one

// use of keyword 'true'
.truth (@a) when (@a) { /* ... */ }
.truth (@a) when (@a = true) { /* ... */ }

// compare arguments which each other, or with non-arguments
@media: mobile;

.mixin (@a) when (@media = mobile) { ... }
.mixin (@a) when (@media = desktop) { ... }

.max (@a; @b) when (@a > @b) { width: @a }
.max (@a; @b) when (@a < @b) { width: @b }

// use of 'and' to combine guards
.mixin (@a) when (isnumber(@a)) and (@a > 0) { /* ... */ }

// emulate OR with a comma
.mixin (@a) when (@a > 10), (@a < -10) { /* ... */ }

// use of 'not' to negate conditions
.mixin (@b) when not (@b > 0) { /* ... */ }

// use 'default()' function similar to 'else' or 'default'
.mixin (@a) when (@a > 0) { ...  }
.mixin (@a) when (default()) { ... } // matches only if first mixin does not, i.e. when @a <= 0
```

#### Type Checking Functions

* `iscolor`
* `isnumber`
* `isstring`
* `iskeyword`
* `isurl`
* `ispixel`
* `ispercentage`
* `isem`
* `isunit`

#### Applied to CSS Selectors

```less
button when (@my-option = true) {
  color: white;
}

// achieve an 'if' type statement
& when (@my-option = true) {
  button {
    color: white;
  }
  a {
    color: blue;
  }
}
```

### Namespaces and Accessors

```less
// bundle some mixins and variables to ensure they won't conflict with another library
#bundle {
  .button {
    display: block;
    border: 1px solid black;
    background-color: grey;
    &:hover {
      background-color: white;
    }
  }

  .tab { /* ... */ }
  .citation { /* ... */ }
}

// mixin the .button class
#header a {
  color: orange;
  #bundle > .button;
}

// use guards on a namespace as a condition
#namespace when (@mode=huge) {
  .mixin() { /* ... */ }
}
```

### Parametric Mixins

```less
.border-radius(@radius: 5px) {
  -webkit-border-radius: @radius;
     -moz-border-radius: @radius;
          border-radius: @radius;
}

#header {
  .border-radius(4px);
}

.button {
  .border-radius(6px);
}

ul li {
  .border-radius; // uses default 5px
}

// parametric mixins without any parameters are not included in the CSS output
.wrap() {
  text-wrap: wrap;
  white-space: -moz-pre-wrap;
  white-space: pre-wrap;
  word-wrap: break-word;
}

pre { .wrap }

// legal to have multiple mixins with the same name and number of parameters
// LESS will use all that apply
.mixin(@color) { // MATCHED
  .color-1: @color;
}

.mixin(@color) { // MATCHED
  .color-2: @color;
  .padding-2: @padding;
}

.mixin(@color; @padding; @margin: 2) {
  .color-3: @color;
  .padding-3: @padding;
  margin: @margin;
}

.some .selector div {
  .mixin(#008000);
}

// can supply parameter values by their names as well
.mixin(@color: black; @margin: 10px; @padding: 20px) {
  color: @color;
  margin: @margin;
  padding: @padding;
}

.class1 {
  .mixin(@margin: 20px; @color: #33ACFE);
}

.class2 {
  .mixin(#EFCA44; @padding: 40px);
}

// use the '@arguments' variable inside a mixin when
// you don't want to deal with individual parameters
.box-shadow(@x: 0; @y: 0; @blur: 1px; @color: #000) {
  -webkit-box-shadow: @arguments;
     -moz-box-shadow: @arguments;
          box-shadow: @arguments;
}

.big-block {
  .box-shadow(2px; 5px);
}

// change the behavior of a mixin based on the
// parameters you pass to it
.mixin(dark; @color) {
  color: darken(@color, 10%);
}

.mixin(light; @color) { // MATCHED
  color: lighten(@color, 10%);
}

.mixin(@var; @color) {
  display: block;
}

@switch: light;

.class {
  .mixin(@switch; #888);
}

// use mixins almost like functions
.average(@x; @y) {
  @average: ((@x + @y) / 2);
}

div {
  .average(16px; 50px); // "call" the mixin
  padding: @average; // use its "return" value
}

// mixins defined in mixins act as a return value
.unlock(@value) { // outer mixin
  .doSomething() { // nested mixin
    declaration: @value;
  }
}

#namespace {
  .unlock(5); // unlock doSomething mixin
  .doSomething(); //nested mixin was copied here and is usable
}
```

> Parameters can be separated using either a comma (`,`) or a semicolon (`;`). However, it is recommended to use a semicolon.

### Detached Rulesets

```less
// detached rulesets
@detached-ruleset: {
  background-color: red;
};

.top {
  @detached-ruleset();
}

// allows mixins to wrap code
.desktop-and-old-ie(@rules) {
  @media screen and (min-width: 1200px) { @rules(); }
  html.lt-ie9 &                         { @rules(); }
}

header {
  background-color: blue;

  .desktop-and-old-ie({
    background-color: red;
  });
}
```

### Loops

```less
// a mixin can call itself
// recursive mixins with guard expressions and pattern matching
.loop(@counter) when (@counter > 0) {
  .loop((@counter - 1));    // next iteraction
  width: (10px * @counter); // code for each iteration
}

div {
  .loop(5); // launch the loop
  // width: 10px;
  // width: 20px;
  // width: 30px;
  // width: 40px;
  // width: 50px;
}

// using a recursive loop to generate CSS grid classes
.generate-columns(4);

.generate-columns(@n, @i: 1) when (@i =< @n) {
  .column-@{i} {
    width: (@i * 100% / @n);
  }

  .generate-columns(@n, (@i + 1));
}

// .column-1 {
//   width: 25%;
// }
// .column-2 {
//   width: 50%;
// }
// .column-3 {
//   width: 75%;
// }
// .column-4 {
//   width: 100%;
// }
```

## Built-In Functions

### Miscellaneous

#### `color(@string)`: Parses a color, so a string representing a color becomes a color.

* `@string` - a string of the specified color

```less
color("aaa"); // > #aaa
```

#### `image-size(@string)`: Gets the image dimensions from a file.

* `@string` - the file to get the dimensions for

```less
image-size("file.png"); //> 10px 10px
```

> Only available in the `node` environment.

#### `image-width(@string)`: Gets the image width from a file.

* `@string` - the file to get the dimensions for

```less
image-width("file.png"); //> 10px
```

> Only available in the `node` environment.

#### `image-height(@string)`: Gets the image height from a file.

* `@string` - the file to get the dimensions for

```less
image-height("file.png"); // > 10px
```

> Only available in the `node` environment.

#### `convert(@number, @unit)`: Convert a number from one unit into another.

* `@number` - a floating point number with units
* `@unit` - an identifier, string, or escaped value of the units to convert to

```less
convert(9s, "ms");  // > 9000ms
convert(14cm, mm);  // > 140mm
convert(8, mm);     // > 8
```

> If the units are not compatible, the first argument is returned unmodified.

> Compatible unit groups:
> * lengths: `m`, `cm`, `mm`, `in`, `pt`, `pc`
> * time: `s`, `ms`
> * angle: `rad`, `deg`, `grad`, `turn`

#### `data-uri(@mimetype, @url)`: Inlines a resource and falls back to `url()` if the `ieCompat` option is one and the resource is too large, or if you use the function in the browser. If the MIME type is not given then node uses the mime package to determine the correct mime type.

* `@mimetype` - a MIME type string (optional)
* `@url` - the URL of the file to inline

```less
data-uri('../data/image.jpg');
// > url('data:image/jpeg;base64,bm90IGFjdHVhbGx5IGEganBlZyBmaWxlCg==');
// (browser) > url('../data/image.jpg');
data-uri('image/jpeg;base64', '../data/image.jpg');
// > url('data:image/jpeg;base64,bm90IGFjdHVhbGx5IGEganBlZyBmaWxlCg==');
data-uri('image/svg+xml;charset=UTF-8', 'image.svg');
// > url("data:image/svg+xml;charset=UTF-8,%3Csvg%3E%3Ccircle%20r%3D%229%22%2F%3E%3C%2Fsvg%3E");
```

> If there is no `mimetype`, `data-uri` guesses it from the filename suffix.

> Text and svg files are encoded in utf-8 and anything else is encoded in base64.

> If `mimetype` ends with `;base64`, it is encoded into base64.

#### `default()`: Available only inside guard conditions and returns `true` only if no other mixin matches, `false` otherwise.

```less
.mixin(1)                   {x: 11}
.mixin(2)                   {y: 22}
.mixin(@x) when (default()) {z: @x}

div {
  .mixin(3); // > z: 3;
}

div.special {
  .mixin(1); // > x: 11;
}
```

> Only available inside guard expressions.

#### `unit(@dimension, @unit)`: Remove or change the unit of a dimension.

* `@dimension` - a number, with or without dimension
* `@unit` - the unit to change to, or if omitted it will remove the unit (optional)

```less
unit(5, px); //> 5px
unit(5em);   //> 5
```

#### `get-unit(@number)`: Returns units of a number.

* `@number` - a number with or without units

```less
get-unit(5px); //> px
get-unit(5);   //> nothing
```

#### `svg-gradient(@direction, @list)`: Generates multi-stop svg gradients.

* `@direction` - specifies a gradient type and direction
  * `to bottom`
  * `to right`
  * `to bottom right`
  * `to top right`
  * `ellipse`
  * `ellipse at center`
* `@list` - list of two or more color stops
  * positions of first and last colors are optional
  * all other colors must have positions specified
  * can be supplied inside a list or in separate arguments

```less
// color stops in list
div {
  @list: red, green 30%, blue;
  background-image: svg-gradient(to right, @list);
}

// color stops in arguments
div {
  background-image: svg-gradient(to right, red, green 30%, blue);
}

// > div {
//   background-image: url('data:image/svg+xml,%3C%3Fxml%20version%3D%221.0%22%20%3F%3E%3Csvg%20xmlns%3D%22http%3A%2F%2Fwww.w3.org%2F2000%2Fsvg%22%20version%3D%221.1%22%20width%3D%22100%25%22%20height%3D%22100%25%22%20viewBox%3D%220%200%201%201%22%20preserveAspectRatio%3D%22none%22%3E%3ClinearGradient%20id%3D%22gradient%22%20gradientUnits%3D%22userSpaceOnUse%22%20x1%3D%220%25%22%20y1%3D%220%25%22%20x2%3D%22100%25%22%20y2%3D%220%25%22%3E%3Cstop%20offset%3D%220%25%22%20stop-color%3D%22%23ff0000%22%2F%3E%3Cstop%20offset%3D%2230%25%22%20stop-color%3D%22%23008000%22%2F%3E%3Cstop%20offset%3D%22100%25%22%20stop-color%3D%22%230000ff%22%2F%3E%3C%2FlinearGradient%3E%3Crect%20x%3D%220%22%20y%3D%220%22%20width%3D%221%22%20height%3D%221%22%20fill%3D%22url(%23gradient)%22%20%2F%3E%3C%2Fsvg%3E');
// }
```

### String

#### `escape(@string)`: Applies URL-encoding to special characters found in the input string.

* `@string` - a string to escape
  * most common encoded characters: `\<space\>`, `#`, `^`, `(`, `)`, `{`, `}`, `|`, `:`, `>`, `<`, `;`, `[`, `]`, `=`
  * characters not encoded: `,`, `/`, `?`, `@`, `&`, `+`, `'`, `~`, `!`, `$`

```less
escape('a=1'); //> a%3D1
```

> If the parameter is not a string, output is `undefined`.

#### `e(@string)`: CSS escaping, replaced with `~"value"` syntax.

* `@string` - a string to escape

```less
filter: e("ms:alwaysHasItsOwnSyntax.For.Stuff()");
// > filter: ms:alwaysHasItsOwnSyntax.For.Stuff();
```

> The function also accepts `~""` escaped values and numbers as parameters. Anything else returns an error.

#### `%(@string, @arguments...)`: Formats a string.

* `@string` - a string with placeholders
  * all placeholders start with `%` followed by `s`, `S`, `d`, `D`, `a`, `A`
  * uppercase placeholders escape special characters into their utf-8 escape codes
  * lowercase placeholders leave special characters as they are
  * function does not escape `(`, `)`, `'`, `~`, `!`
  * space is encoded as `%20`
* `@arguments...` - expressions to replace placeholders
  * escape percent symbol if you need it (`%%`)

```less
format-a-d: %("repetitions: %a file: %d", 1 + 2, "directory/file.less");
format-a-d-upper: %('repetitions: %A file: %D', 1 + 2, "directory/file.less");
format-s: %("repetitions: %s file: %s", 1 + 2, "directory/file.less");
format-s-upper: %('repetitions: %S file: %S', 1 + 2, "directory/file.less");

// Output:
// > format-a-d: "repetitions: 3 file: "directory/file.less"";
// > format-a-d-upper: "repetitions: 3 file: %22directory%2Ffile.less%22";
// > format-s: "repetitions: 3 file: directory/file.less";
// > format-s-upper: "repetitions: 3 file: directory%2Ffile.less";
```

#### `replace(@string, @pattern, @replacement, @flags)`: Replaces a text within a string.

* `@string` - the string to search and replace in
* `@pattern` - a string or regular expression pattern to search for
* `@replacement` - the string to replace the matched pattern with
* `@flags` - regular expression flags (optional)

```less
replace("Hello, Mars?", "Mars\?", "Earth!"); // > "Hello, Earth!";
replace("One + one = 4", "one", "2", "gi"); // > "2 + 2 = 4";
replace('This is a string.', "(string)\.$", "new $1."); // > 'This is a new string.';
replace(~"bar-1", '1', '2'); // > bar-2;
```

### List

#### `length(@list)`: Returns the number of elements in a value list.

* `@list` - a comma or space separated list of values

```less
length(1px solid #0080ff); // > 3

@list: "banana", "tomato", "potato", "peach";
n: length(@list); // > 4
```

#### `extract(@list, @index)`: Returns the value at a specified position in a list.

* `@list` - a comma or space separated list of values
* `@index` - an integer that specifies a position of a list element to return

```less
extract(8px dotted red, 2); //> dotted

@list: apple, pear, coconut, orange;
value: extract(@list, 3); //> coconut
```

### Math

#### `ceil(@number)`: Rounds up to the next highest integer.

* `@number` - a floating point number

```less
ceil(2, 4); //> 3
```

#### `floor(@number)`: Rounds down to the next lowest integer.

* `@number` - a floating point number

```less
floor(2.6); //> 2
```

#### `percentage(@number)`: Converts a floating point number into a percentage string.

* `@number` - a floating point number

```less
percentage(0.5); //> 50%
```

#### `round(@number, @decimalPlaces)`: Applies rounding.

* `@number` - a floating point number
* `@decimalPlaces` - the number of decimal places to round to (optional, defaults to 0)

```less
round(1.67); //> 2
round(1.67, 1); //> 1
```

#### `sqrt(@number)`: Calculates the square root of a number. Keeps units as they are.

* `@number` - a floating point number

```less
sqrt(25cm); //> 5cm
sqrt(18.6%); //> 4.312771730569565%
```

#### `abs(@number)`: Calculates absolute value of a number. Keeps units as they are.

* `@number` - a floating point number

```less
abs(25cm); //> 25cm
abs(-18.6%); //> 18.6%
```

#### `sin(@number)`: Calculates sine function.

* `@number` - a floating point number; assumes radians on numbers without units

```less
sin(1); // sine of 1 radian
sin(1deg); // sine of 1 degree
sin(1grad); // sine of 1 gradian

// 0.8414709848078965; // sine of 1 radian
// 0.01745240643728351; // sine of 1 degree
// 0.015707317311820675; // sine of 1 gradian
```

#### `asin(@number)`: Calculates arcsine (inverse of sine) function.

* `@number` - a floating point number from `[-1, 1]` interval; returns a number in radians (`(-π/2, π/2)`)

```less
asin(-0.8414709848078965); // -1rad
asin(0); // 0rad
asin(2); // NaNrad
```

#### `cos(@number)`: Calculates cosine function.

* `@number` - a floating point number. Assumes radians on numbers without units.

```less
cos(1); // cosine of 1 radian
cos(1deg); // cosine of 1 degree
cos(1grad); // cosine of 1 gradian

// 0.5403023058681398 // cosine of 1 radian
// 0.9998476951563913 // cosine of 1 degree
// 0.9998766324816606 // cosine of 1 gradian
```

#### `acos(@number)`: Calculates arccosine (inverse of cosine) function.

* `@number` - a floating point number from `[-1, 1]` interval. Returns number in radians `(0, π)`.

```less
acos(0.5403023058681398); //> 1 rad
acos(1); //> 0 rad
acos(2); //> NaNrad
```

#### `tan(@number)`: Calculates tangent function.

* `@number` - a floating point number. Assumes radians on numbers without units.

```less
tan(1); // tangent of 1 radian
tan(1deg); // tangent of 1 degree
tan(1grad); // tangent of 1 gradian

// 1.5574077246549023   // tangent of 1 radian
// 0.017455064928217585 // tangent of 1 degree
// 0.015709255323664916 // tangent of 1 gradian
```

#### `atan(@number)`: Calculates arctangent (inverse of tangent) function.

* `@number` - a floating point number. Returns number in radians `(-π/2, π/2)`.

```less
atan(-1.5574077246549023); //> -1rad
atan(0); //> 0rad
round(atan(22), 6); // arctangent of 22 rounded to 6 decimal places //> 1.525373rad;
```

#### `pi()`: Returns π (pi).

```less
pi(); //> 3.141592653589793
```

#### `pow(@base, @exponent)`: Returns the value of the first argument raised to the power of the second argument.

* `@base` - a floating point number
* `@exponent` - a floating point number

```less
pow(0cm, 0px); //> 1cm
pow(25, -2); //> 0.0016
pow(25, 0.5); //> 5
pow(-25, 0.5); //> NaN
pow(-25%, -0.5); //> NaN%
```

#### `mod(@number, @number)`: Returns the value of the first argument modulus second argument.

* `@number` - a floating point number
* `@number` - a floating point number

```less
mod(0cm, 0px); //> NaNcm
mod(11cm, 6px); //> 5cm
mod(-26%, -5); //> -1%
```

#### `min(@value1, ..., @valueN)`: Returns the lowest of one or more values.

* `@valueN` - a list of one or more values to compare

```less
min(5, 10); //> 5
min(3px, 42px, 1px, 16px); //> 1px
```

#### `max(@value1, ..., @valueN)`: Returns the highest of one or more values.

* `@valueN` - a list of one or more values to compare

```less
max(5, 10); //> 10
max(3%, 42%, 1%, 16%); //> 42%
```

### Type

#### `isnumber(@value)`: Returns `true` if a value is a number, `false` otherwise.

* `@value` - a value or variable being evaluated

```less
isnumber(#ff0);     // false
isnumber(blue);     // false
isnumber("string"); // false
isnumber(1234);     // true
isnumber(56px);     // true
isnumber(7.8%);     // true
isnumber(keyword);  // false
isnumber(url(...)); // false
```

#### `isstring(@value)`: Returns `true` if a value is a string, `false` otherwise.

* `@value` - a value or variable being evaluated

```less
isstring(#ff0);     // false
isstring(blue);     // false
isstring("string"); // true
isstring(1234);     // false
isstring(56px);     // false
isstring(7.8%);     // false
isstring(keyword);  // false
isstring(url(...)); // false
```

#### `iscolor(@value)`: Returns `true` if a value is a color, `false` otherwise.

* `@value` - a value or variable being evaluated

```less
iscolor(#ff0);     // true
iscolor(blue);     // true
iscolor("string"); // false
iscolor(1234);     // false
iscolor(56px);     // false
iscolor(7.8%);     // false
iscolor(keyword);  // false
iscolor(url(...)); // false
```

#### `iskeyword(@value)`: Returns `true` if a value is a keyword, `false` otherwise.

* `@value` - a value or variable being evaluated

```less
iskeyword(#ff0);     // false
iskeyword(blue);     // false
iskeyword("string"); // false
iskeyword(1234);     // false
iskeyword(56px);     // false
iskeyword(7.8%);     // false
iskeyword(keyword);  // true
iskeyword(url(...)); // false
```

#### `isurl(@value)`: Returns `true` if a value is a url, `false` otherwise.

* `@value` - a value or variable being evaluated

```less
isurl(#ff0);     // false
isurl(blue);     // false
isurl("string"); // false
isurl(1234);     // false
isurl(56px);     // false
isurl(7.8%);     // false
isurl(keyword);  // false
isurl(url(...)); // true
```

#### `ispixel(@value)`: Returns `true` if a value is a number in pixels, `false` otherwise.

* `@value` - a value or variable being evaluated

```less
ispixel(#ff0);     // false
ispixel(blue);     // false
ispixel("string"); // false
ispixel(1234);     // false
ispixel(56px);     // true
ispixel(7.8%);     // false
ispixel(keyword);  // false
ispixel(url(...)); // false
```

#### `isem(@value)`: Returns `true` if a value is an em value, `false` otherwise.

* `@value` - a value or variable being evaluated

```less
isem(#ff0);     // false
isem(blue);     // false
isem("string"); // false
isem(1234);     // false
isem(56px);     // false
isem(7.8em);    // true
isem(keyword);  // false
isem(url(...)); // false
```

#### `ispercentage(@value)`: Returns `true` if a value is a percentage value, `false` otherwise.

* `@value` - a value or variable being evaluated

```less
ispercentage(#ff0);     // false
ispercentage(blue);     // false
ispercentage("string"); // false
ispercentage(1234);     // false
ispercentage(56px);     // false
ispercentage(7.8%);     // true
ispercentage(keyword);  // false
ispercentage(url(...)); // false
```

#### `isunit(@value)`: Returns `true` if a value is a number in specified unites, `false` otherwise.

* `@value` - a value or variable being evaluated

```less
isunit(11px, px);  // true
isunit(2.2%, px);  // false
isunit(33px, rem); // false
isunit(4rem, rem); // true
isunit(56px, "%"); // false
isunit(7.8%, '%'); // true
isunit(1234, em);  // false
isunit(#ff0, pt);  // false
isunit("mm", mm);  // false
```

#### `isruleset(@value)`: Returns `true` if a value is a ruleset, `false` otherwise.

* `@value` - a value or variable being evaluated

```less
@rules: {
    color: red;
}

isruleset(@rules);   // true
isruleset(#ff0);     // false
isruleset(blue);     // false
isruleset("string"); // false
isruleset(1234);     // false
isruleset(56px);     // false
isruleset(7.8%);     // false
isruleset(keyword);  // false
isruleset(url(...)); // false
```

### Color Definition

#### `rgb(@red, @green, @blue)`: Creates an opaque color object from decimal red, green, and blue (RGB) values.

* `@red` - an integer (0-255) or percentage (0-100%)
* `@green` - an integer (0-255) or percentage (0-100%)
* `@blue` - an integer (0-255) or percentage (0-100%)

```less
rgb(90, 129, 32); //> #5a8120
```

> Literal color values (`#ff0000`) can also be used to define colors.

#### `rgba(@red, @green, @blue, @alpha)`: Creates a transparent color object from decimal red, green, blue, and alpha values.

* `@red` - an integer (0-255) or percentage (0-100%)
* `@green` - an integer (0-255) or percentage (0-100%)
* `@blue` - an integer (0-255) or percentage (0-100%)
* `@alpha` - a number (0-1) or percentage (0-100%)

```less
rgba(90, 129, 32, 0.5); //> rgba(90, 129, 32, 0.5)
```

#### `argb(@color)`: Creates a hex representation of a color in `#AARRGGBB` format.

* `@color` - a color object

```less
argb(rgba(90, 23, 148, 0.5)); //> #805a1794
```

> This format is used in IE, .NET, and Android development.

#### `hsl(@hue, @saturation, @lightness)`: Creates an opaque color object from hue, saturation, and lightness values.

* `@hue` - an integer (0-360) representing degrees
* `@saturation` - a percentage (0-100%) or number (0-1)
* `@lightness` - a percentage (0-100%) or number (0-1)

```less
hsl(90, 100%, 50%); //> #80ff00
```

> This is useful if you want to create a new color based on another color's channel.
> `@new: hsl(hue(@old), 45%, 90%)`
> * `@new` will have `@old`'s *hue*, and its own saturation and lightness

#### `hsla(@hue, @saturation, @lightness, @alpha)`: Creates a transparent color object from hue, saturation, lightness, and alpha values.

* `@hue` - an integer (0-360) representing degrees
* `@saturation` - a percentage (0-100%) or number (0-1)
* `@lightness` - a percentage (0-100%) or number (0-1)
* `@alpha` - a percentage (0-100%) or number (0-1)

```less
hsl(90, 100%, 50%, 0.5); //> rgba(128, 255, 0, 0.5)
```

#### `hsv(@hue, @saturation, @value)`: Creates an opaque color object from hue, saturation, and value values.

* `@hue` - an integer (0-360) representing degrees
* `@saturation` - a percentage (0-100%) or number (0-1)
* `@value` - a percentage (0-100%) or number (0-1)

```less
hsv(90, 100%, 50%); //> #408000
```

#### `hsva(@hue, @saturation, @value, @alpha)`: Creates a transparent color object from hue, saturation, value, and alpha values.

* `@hue` - an integer (0-360) representing degrees
* `@saturation` - a percentage (0-100%) or number (0-1)
* `@value` - a percentage (0-100%) or number (0-1)
* `@alpha` - a percentage (0-100%) or number (0-1)

```less
hsva(90, 100%, 50%, 0.5); //> rgba(64, 128, 0, 0.5)
```

### Color Channel

#### `hue(@color)`: Extracts the hue channel of a color object in the HSL color space.

* `@color` - a color object

```less
hue(hsl(90, 100%, 50%)); //> 90
```

#### `saturation(@color)`: Extracts the saturation channel of a color object in the HSL color space.

* `@color` - a color object

```less
saturation(hsl(90, 100%, 50%)) //> 100%
```

#### `lightness(@color)`: Extracts the lightness channel of a color object in the HSL color space.

* `@color` - a color object

```less
lightness(hsl(90, 100%, 50%)) //> 100%
```

#### `hsvhue(@color)`: Extracts the hue channel of a color object in the HSV color space.

* `@color` - a color object

```less
hsvhue(hsl(90, 100%, 50%)) //> 90
```

#### `hsvsaturation(@color)`: Extracts the saturation channel of a color object in the HSV color space.

* `@color` - a color object

```less
hsvsaturation(hsl(90, 100%, 50%)) //> 100%
```

#### `hsvvalue(@color)`: Extracts the value channel of a color object in the HSV color space.

* `@color` - a color object

```less
hsvvalue(hsl(90, 100%, 50%)) //> 50%
```

#### `red(@color)`: Extracts the red channel of a color object.

* `@color` - a color object

```less
red(rgb(10, 20, 30)) //> 10
```

#### `green(@color)`: Extracts the green channel of a color object.

* `@color` - a color object

```less
green(rgb(10, 20, 30)) //> 20
```

#### `blue(@color)`: Extracts the blue channel of a color object.

* `@color` - a color object

```less
blue(rgb(10, 20, 30)) //> 30
```

#### `alpha(@color)`: Extracts the alpha channel of a color object.

* `@color` - a color object

```less
alpha(rgba(10, 20, 30, 0.5)) //> 0.5
```

#### `luma(@color)`: Calculates the `luma` (perceptual brightness) of a color object.

* `@color` - a color object

```less
luma(rgb(100, 200, 30)) //> 44%
```

> Uses SMPTE C / Rec. 709 coefficients, as recommended in [WCAG 2.0](http://www.w3.org/TR/2008/REC-WCAG20-20081211/#relativeluminancedef). This calculation is all used in the contrast function.

#### `luminance(@color)`: Calculates the value of the `luma` without gamma correction.

* `@color` - a color object

```less
luminance(rgb(100, 200, 30)) //> 65%
```

### Color Operation

#### `saturate(@color, @amount, @method)`: Increase the saturation of a color in the HSL color space by an absolute amount.

* `@color` - a color object
* `@amount` - a percentage (0-100%)
* `@method` - set to `relative` for the adjustment to be relative to the current value (optional)

```less
saturate(hsl(90, 80%, 50%), 20%); //> #80ff00 // hsl(90, 100%, 50%)
```

#### `desaturate(@color, @amount, @method)`: Decrease the saturation of a color in the HSL color space by an absolute amount.

* `@color` - a color object
* `@amount` - a percentage (0-100%)
* `@method` - set to `relative` for the adjustment to be relative to the current value (optional)

```less
desaturate(hsl(90, 80%, 50%), 20%); //> #80cc33 // hsl(90, 60%, 50%)
```

#### `lighten(@color, @amount, @method)`: Increase the lightness of a color in the HSL color space by an absolute amount.

* `@color` - a color object
* `@amount` - a percentage (0-100%)
* `@method` - set to `relative` for the adjustment to be relative to the current value (optional)

```less
lighten(hsl(90, 80%, 50%), 20%); //> #b3f075 // hsl(90, 80%, 70%)
```

#### `darken(@color, @amount, @method)`: Decrease the lightness of a color in the HSL color space by an absolute amount.

* `@color` - a color object
* `@amount` - a percentage (0-100%)
* `@method` - set to `relative` for the adjustment to be relative to the current value (optional)

```less
darken(hsl(90, 80%, 50%), 20%); //> #4d8a0f // hsl(90, 80%, 30%)
```

#### `fadein(@color, @amount, @method)`: Decrease the transparency (or increase the opacity) of a color, making it more opaque.

* `@color` - a color object
* `@amount` - a percentage (0-100%)
* `@method` - set to `relative` for the adjustment to be relative to the current value (optional)

```less
fadein(hsla(90, 90%, 50%, 0.5), 10%); //> rgba(128, 242, 13, 0.6) // hsla(90, 90%, 50%, 0.6)
```

> Has no effect on opaque colors.

#### `fadeout(@color, @amount, @method)`: Increase the transparency (or decrease the opacity) of a color, making it less opaque.

* `@color` - a color object
* `@amount` - a percentage (0-100%)
* `@method` - set to `relative` for the adjustment to be relative to the current value (optional)

```less
fadeout(hsla(90, 90%, 50%, 0.5), 10%); //> rgba(128, 242, 13, 0.4) // hsla(90, 90%, 50%, 0.4)
```

#### `fade(@color, @amount)`: Set the absolute transparency of a color. Can be applied to colors whether they already have an opacity value or not.

* `@color` - a color object
* `@amount` - a percentage (0-100%)

```less
fade(hsl(90, 90%, 50%), 10%); //> rgba(128, 242, 13, 0.1) //hsla(90, 90%, 50%, 0.1)
```

#### `spin(@color, @angle)`: Rotate the hue angle of a color in either direction.

* `@color` - a color object
* `@angle` - a number of degrees to rotate (+ or -)

```less
spin(hsl(10, 90%, 50%), 30); //> #f2a60d // hsl(40, 90%, 50%)
spin(hsl(10, 90%, 50%), -30); //> #f20d59 // hsl(340, 90%, 50%)
```

#### `mix(@color1, @color2, @weight)`: Mix two colors together in variable proportion. Opacity is included in the calculations.

* `@color1` - a color object
* `@color2` - a color object
* `@weight` - a percentage balance point between two colors (optional, defaults to 50%)

```less
mix(#ff0000, #0000ff, 50%); //> #800080
mix(rgba(100,0,0,1.0), rgba(0,100,0,0.5), 50%); //> #rgba(75, 75, 0, 0.75)
```

#### `tint(@color, @weight)`: Mix color with the white variable proportion.

* `@color` - a color object
* `@weight` - a percentage balance point between two colors (optional, defaults to 50%)

```less
no-alpha: tint(#007fff, 50%); //> #80bfff
with-alpha: tint(rgba(00,0,255,0.5), 50%); //> rgba(191, 191, 255, 0.75)
```

#### `shade(@color, @weight)`: Mix color with black in variable proportion.

* `@color` - a color object
* `@weight` - a percentage balance point between two colors (optional, defaults to 50%)

```less
no-alpha: shade(#007fff, 50%); //> #004080
with-alpha: shade(rgba(00,0,255,0.5), 50%); //> rgba(0, 0, 64, 0.75)
```

#### `greyscale(@color)`: Remove all saturation from a color in the HSL color space.

* `@color` - a color object

```less
greyscale(hsl(90, 90%, 50%)); //> #808080 // hsl(90, 0%, 50%)
```

> Because the saturation is not affected by hue, the resulting color mapping may be somewhat dull or muddy; `luma` may provide a better result as it extract perceptual rather than linear brightness.

#### `contrast(@string)`: Choose which of two colors provides the greatest contrast with another.

* `@color` - a color object
* `@dark` - a designated dark color (optional, defaults to black)
* `@light` - a designated light color (optional, defaults to white)
* `@threshold` - a percentage (0-100%) specifying where the transition from "dark" to "light" is (optional, defaults to 43% matching SASS)
  * used to bias the contrast one way or another
  * set this lower for "lighter" palettes
  * set this higher for "darker" palettes

```less
p {
    a: contrast(#bbbbbb); //> #000
    b: contrast(#222222, #101010); //> #fff
    c: contrast(#222222, #101010, #dddddd); //> #ddd
    d: contrast(hsl(90, 100%, 50%), #000000, #ffffff, 30%); //> #000
    e: contrast(hsl(90, 100%, 50%), #000000, #ffffff, 80%); //> #fff
}
```

> Useful for ensuring that a color is readable against a background, which is also useful for accessibility compliance.

> Works the same way as the contrast function in Compass for Sass.

> In accordance with WCAG 2.0, colors are compared using their gamma-corrected `luma` value, not their lightness.

### Color Blending

#### `multiply(@color1, @color2)`: Multiply two colors. Corresponding RGB channels from each of the two colors are multiplied together then divided by 255. The result is a darker color.

* `@color1` - a color object
* `@color2` - a color object

```less
multiply(#ff6600, #000000); //> #000000
multiply(#ff6600, #333333); //> #331400
multiply(#ff6600, #666666); //> #662900
multiply(#ff6600, #999999); //> #993d00
multiply(#ff6600, #cccccc); //> #cc5200
multiply(#ff6600, #ffffff); //> #ff6600
multiply(#ff6600, #ff0000); //> #ff0000
multiply(#ff6600, #00ff00); //> #006600
multiply(#ff6600, #0000ff); //> #000000
```

#### `screen(@color1, @color2)`: Do the opposite of `multiply`. The result is a brighter color.

* `@color1` - a color object
* `@color2` - a color object

```less
screen(#ff6600, #000000); //> #ff6600
screen(#ff6600, #333333); //> #ff8533
screen(#ff6600, #666666); //> #ffa366
screen(#ff6600, #999999); //> #ffc299
screen(#ff6600, #cccccc); //> #ffe0cc
screen(#ff6600, #ffffff); //> #ffffff
screen(#ff6600, #ff0000); //> #ff6600
screen(#ff6600, #00ff00); //> #ffff00
screen(#ff6600, #0000ff); //> #ff66ff
```

#### `overlay(@base, @overlay)`: Combines the effects of both `multiply` and `screen`. Conditionally makes light channels lighter and dark channels darker.

* `@base` - a base color object; also the determinant color to make the result lighter or darker
* `@overlay` - a color object to overlay

```less
overlay(#ff6600, #000000); //> #ff0000
overlay(#ff6600, #333333); //> #ff2900
overlay(#ff6600, #666666); //> #ff5200
overlay(#ff6600, #999999); //> #ff7a00
overlay(#ff6600, #cccccc); //> #ffa300
overlay(#ff6600, #ffffff); //> #ffcc00
overlay(#ff6600, #ff0000); //> #ff0000
overlay(#ff6600, #00ff00); //> #ffcc00
overlay(#ff6600, #0000ff); //> #ff0000
```

#### `softlight(@softlight, @softlighten)`: Similar to `overlay`, but avoid pure black resulting in pure black, and pure white resulting in pure white.

* `@softlight` - a color object to soft light another
* `@softlighten` - a color object to be soft lighten

```less
softlight(#ff6600, #000000); //> #ff2900
softlight(#ff6600, #333333); //> #ff4100
softlight(#ff6600, #666666); //> #ff5a00
softlight(#ff6600, #999999); //> #ff7200
softlight(#ff6600, #cccccc); //> #ff8a00
softlight(#ff6600, #ffffff); //> #ffa100
softlight(#ff6600, #ff0000); //> #ff2900
softlight(#ff6600, #00ff00); //> #ffa100
softlight(#ff6600, #0000ff); //> #ff2900
```

#### `hardlight(@overlay, @base)`: The same as `overlay`, but with the color roles reversed.

* `@overlay` - a color object to overlay
* `@base` - a base color object; also the determinant color to make the result lighter or darker

```less
hardlight(#ff6600, #000000); //> #000000
hardlight(#ff6600, #333333); //> #662900
hardlight(#ff6600, #666666); //> #cc5200
hardlight(#ff6600, #999999); //> #ff8533
hardlight(#ff6600, #cccccc); //> #ffc299
hardlight(#ff6600, #ffffff); //> #ffffff
hardlight(#ff6600, #ff0000); //> #ff0000
hardlight(#ff6600, #00ff00); //> #00ff00
hardlight(#ff6600, #0000ff); //> #0000ff
```

#### `difference(@minuend, @subtrahend)`: Subtracts the second color from the first color on a channel-by-channel basis. Negative values are inverted. Subtracting black results in no change; subtracting white results in color inversion.

* `@minuend` - a color object to act as minuend
* `@subtrahend` - a color object to act as the subtrahend

```less
difference(#ff6600, #000000); //> #ff6600
difference(#ff6600, #000000); //> #cc3333
difference(#ff6600, #666666); //> #990066
difference(#ff6600, #999999); //> #663399
difference(#ff6600, #cccccc); //> #3366cc
difference(#ff6600, #ffffff); //> #0099ff
difference(#ff6600, #ff0000); //> #006600
difference(#ff6600, #00ff00); //> #ff9900
difference(#ff6600, #0000ff); //> #ff66ff
```

#### `exclusion(@minuend, @subtrahend)`: A similar effect to `difference` with lower contrast.

* `@minuend` - a color object to act as the minuend
* `@subtrahend` - a color object to act as the subtrahend

```less
exclusion(#ff6600, #000000); //> #ff6600
exclusion(#ff6600, #333333); //> #cc7033
exclusion(#ff6600, #666666); //> #997a66
exclusion(#ff6600, #999999); //> #668599
exclusion(#ff6600, #cccccc); //> #338fcc
exclusion(#ff6600, #ffffff); //> #0099ff
exclusion(#ff6600, #ff0000); //> #006600
exclusion(#ff6600, #00ff00); //> #ff9900
exclusion(#ff6600, #0000ff); //> #ff66ff
```

#### `average(@color1, @color2)`: Compute the average of two colors on a per-channel (RGB) basis.

* `@color1` - a color object
* `@color2` - a color object

```less
average(#ff6600, #000000); //> #803300
average(#ff6600, #333333); //> #994d1a
average(#ff6600, #666666); //> #b36633
average(#ff6600, #999999); //> #cc804d
average(#ff6600, #cccccc); //> #e69966
average(#ff6600, #ffffff); //> #ffb380
average(#ff6600, #ff0000); //> #ff3300
average(#ff6600, #00ff00); //> #80b300
average(#ff6600, #0000ff); //> #803380
```

#### `negation(@minuend, @subtrahend)`: Do the opposite effect to `difference`.

* `@minuend` - a color object to act as the minuend
* `@subtrahend` - a color object to act as the subtrahend

```less
negation(#ff6600, #000000); //> #ff6600
negation(#ff6600, #333333); //> #cc9933
negation(#ff6600, #333333); //> #99cc66
negation(#ff6600, #999999); //> #66ff99
negation(#ff6600, #cccccc); //> #33cccc
negation(#ff6600, #ffffff); //> #0099ff
negation(#ff6600, #ff0000); //> #006600
negation(#ff6600, #00ff00); //> #ff9900
negation(#ff6600, #0000ff); //> #ff66ff
```

> The result is a brighter color.

> The *opposite* effect doesn't mean the *inverted* effect as resulting from an *addition* operation.

## Rules

### Nesting

```less
#header {
  color: black;

  .navigation {
    font-size: 12px;
  }

  .logo {
    width: 300px;
  }
}
```

#### Nesting Directives (Bubbling)

```less
.screen-color {
  @media screen {
    color: green;

    @media (min-width: 768px) {
      color: red;
    }
  }

  @media tv {
    color: black;
  }
}

#a {
  color: blue;
  // will bubble up as-is
  @font-face {
    src: url(./made/up/font.ttf);
  }
  padding: 2px;
}
```

#### Parent Selectors

```less
// '&' represents the parent selectors of a nested rule
a {
  color: blue;
  &:hover { //<-- a:hover
    color: green;
  }
}

// use to produce repetitive class names
.button {
  &-ok { /* ... */ }
  &-cancel { /* ... */ }
  &-custom { /* ... */ }
}

// use of multiple '&'
.link {
  & + & { /* ... */ }   //<-- .link + .link
  & & { /* ... */ }     //<-- .link .link
  && { /* ... */ }      //<-- .link.link
  &, &ish { /* ... */ } //<-- .link, .linkish
}

// '&' represents all parent selectors (not just the nearest ancestor)
.grand {
  .parent {
    & > & { /* ... */ }   //<-- .grand .parent > .grand .parent
    & & { /* ... */ }     //<-- .grand .parent .grand .parent
    && { /* ... */ }      //<-- .grand .parent.grand .parent
    &, &ish { /* ... */ } //<-- .grand .parent, .grand .parentish
  }
}

// prepend a selector to the parent selectors
.header {
  .menu {
    border-radius: 5px;
    .no-borderradius & { //<-- .no-borderradius .header .menu
      background-image: url('images/button-background.png');
    }
  }
}

// combinatorial explosion
// use to generate every possible permutation of selectors in a comma separated list
p, a, ul, li {
  border-top: 2px dotted #366;
  & + & {
    border-top: 0;
  }
}

// p,
// a,
// ul,
// li {
//   border-top: 2px dotted #366;
// }
// p + p,
// p + a,
// p + ul,
// p + li,
// a + p,
// a + a,
// a + ul,
// a + li,
// ul + p,
// ul + a,
// ul + ul,
// ul + li,
// li + p,
// li + a,
// li + ul,
// li + li {
//   border-top: 0;
// }
```

### Scope

```less
// variables and mixins are first looked for locally
// if not found, the compiler will look in parent scope, and so on...
// variables and mixins do not have to be declared before being used
@var: red;

#page {
  #header {
    color: @var; // white
  }

  @var: white;
}
```

### Importing

```less
@import "library"; // library.less
@import "typo.css";
@import "foo.php"; // imported as a less file
```

####Import Options

```less
@import (keyword) "filename";
@import (keyword, keyword) "filename";
```

* `reference`: use a Less file but do not output it
* `inline`: include the source file in the output but do not process it
* `less`: treat the file as a Less file, no matter what the file extension
* `css`: treat the file as a CSS file, no matter what the file extension
* `once`: only include the file once (default behavior)
* `multiple`: include the file multiple times
* `optional`: continue compiling when file is not found

### Extend

```less
// wherever the '.inline' class appears,
// 'nav ul' will be listed below it
nav ul {
  &:extend(.inline);
  background: blue;
}

.inline {
  color: red;
}

// attached to a selector
.a:extend(.b) { }

// placed into a ruleset
.a {
  &:extend(.b);
}

// matches classes EXACTLY by default
.c:extend(.d) {
  // extends only instances of ".d"
}

// optional keyword 'all'
.c:extend(.d all) {
  // extends all instances of ".d" (e.g. ".x.d", ".d.x")
}

// multiple classes to extend
.e:extend(.f, .g) { }

// matches nested selectors
.bucket {
  tr {
    color: blue;
  }
}
.some-class:extend(.bucket tr) { }

// matches selectors inside nested media
@media screen {
  .selector {
    color: blue;
  }

  @media (min-width: 1023px) {
    .selector {
      color: red;
    }
  }
}

.topLevel:extend(.selector) { }

// if written INSIDE a media declaration, then nested media will not match
@media screen {
  .screenClass:extend(.selector) { }
  @media (min-width: 1023px) {
    .selector { // DOES NOT MATCH
      color: blue;
    }
  }
}
```

> If a selector contains a variable, extend will ignore it.

> `:extend` written inside a media declaration should match only selectors inside the same media declaration.

#### Uses Cases for Extend

* To avoid adding a base class.
* To reduce css being generated. Mixins copy all of the properties into a selector. But with `extend`, you can put the selector on property blocks you which to use.
* As an advanced alternative to a mixin, since mixins can only be used with simple selectors.

### Merging Property Values

```less
// append property value with comma
.mixin() {
  box-shadow+: inset 0 0 10px #555;
}

.myclass {
  .mixin();
  box-shadow+: 0 0 20px black;
  // > box-shadow: inset 0 0 10px #555, 0 0 20px black;
}

// append property value with space
.mixin() {
  transform+_: scale(2);
}

.myclass {
  .mixin();
  transform+_: rotate(15deg);
  // > transform: scale(2) rotate(15deg);
}
```