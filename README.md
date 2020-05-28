# JS To JSC
A wrapper by Triston Stuart for JSC.js

## About
Ran in the web-browser with web assembly. <br>
This is a wrapper for JSC.js (https://github.com/mbbill/JSC.js) to make it easier to convert js to jsc bytecode.

## Pitfalls
You can not use the `new` operator at this time. This is a bug with compiling js to jsc. Unfortunately mbbill will have to wait for webkit to make an update to fix this.

The jsc bytecode is ran through a jsc engine in web assembly. This will mean that performance is slower. It is also not possible for js that is running in the jsc engine to access variables running in the normal js engine. Hopefully someone will add this feature but as of now this is not possible.

## Passing Data
Although you can not directly access normal js engine variables you can pass data in a string using `JSON.stringify()` then in your jsc engine code use `JSON.parse()` to convert the string into a data type. (Note: This will not work with circular objects)

## Writing Code
No changes to js are necessary as it is ran through JavascriptCore (https://en.wikipedia.org/wiki/WebKit#JavaScriptCore). It is simply compiled to JavascriptCore byte code (jsc), and later ran.

## Running JSC Byte Code
A few things are needed, your code as a .jsc file, and the JS to JSC wrapper. This wrapper will import the jsc engine (in web assembly) which is about 4MB.

A minified version of the wrapper can be found in ./production and a linkable version is available (https://tristonstuart.github.io/production/jstojsc.min.js).

Include the wrapper using a script tag. You can interact with the wrapper using `JStoJSC`. The first thing you need to do is to initialize the wrapper with `JStoJSC.init()`. This will download the jsc engine and link it with javascript. The wrapper functions will then be accessible.

## Wrapper Functions :
`hexToPlain` - Converts 2 hex chars to 1 utf8 char.
<br>
`plainToHex` - Converts utf8 char to 2 hex chars.
<br>
`UTF8ToHex` - Same as plainToHex.
<br>
`hexToUTF8` - Same as hexToPlain.
<br>
`UTF8ToArray` - Converts a utf8 string to an array with char codes.
<br>
`arrayToUTF8` - Converts a char code array to a utf8 string.
<br>
`UTF8toRaw` - Converts plain (aka shifted) UTF8 to 0-255 "raw" UTF8.
<br>
`rawToUTF8` - Converts "raw" UTF8 to plain UTF8.
<br>
`init` - Downloads and hooks jsc engine.
<br>
`js_compile` - Compiles JS to JSC.
<br>
`js_to_jsc` - Compiles JS into JSC and downloads the file.
<br>
`js_eval` - Runs JS in the jsc engine.
<br>
`jsc_eval` - Evaluates JSC in the jsc engine.
<br>
`jsc_file_get` - Gets a JSC file and sends data (array, utf8, or hex) to a callback function.
<br>
`jsc_file_eval` - Gets a JSC file and evaluates it, sending the result to a callback function.

## Other Wrapper Features :
By default the jsc.wasm (or jsc engine) is pulled from https://tristonstuart.github.io/jsc.js%20compiled/jsc.wasm. You can change this by doing `JStoJSC.init({wasmBinaryFile: 'your_url'})`.

The wrapper still allows access to the Module object so you can directly make calls to the jsc engine using cwrap.

The wrapper has `._utils` which contains a function to generate a file download with a name and contents (Could use to download jsc byte code in hex form).

The wrapper also has `.settings` which contains global settings for the wrapper.

### JSC Encoding
There are a few forms of JSC byte code. JSC is just bytes ranging from 0-255 and it can be expressed in multiple ways.
<br>
<br>
`Hex` - Two chars representing a hex number from 0-255. This is used by the jsc engine natively to convert to an array of bytes for execution. Although this is twice as long as a UTF8 encoding.
<br>
`UTF8-Plain` - Hex number representation as a UTF8 character. Instead of using UTF8 chars from 0-255 the range is shifted to 183-438. This helps prevent issues when copying/pasting jsc byte code.
<br>
`UTF8-Raw` - UTF8 Encoding that is in the range 0-255. This form is extremely hard to work with but represents the raw bytes of jsc.
<br>
`Array` - Bytes of jsc in an array. (Non Raw Array will have the bytes shifted by +183)
<br>
`Raw Array` - Raw bytes of jsc in an array. (This is non shifted and is in the range of 0-255)

### .JSC Files
Storing jsc byte code as a .jsc file is a convenient way to store and load your byte code.
<br>
<br>
Although there are a few things to note. You can store .jsc files as any jsc encoding (although only UTF8-Plain is technically supported).
<br>
<br>
`UTF8-Plain` is the default to be used in `jsc_file_get` and `jsc_file_eval`.
<br>
<br>
`UTF8-Raw` is 2x more compact than `UTF8-Plain` but you can not copy and paste the contents and you will have to manually evaluate the file by doing `jsc_file_get` with the callback function getting the `UTF8-Plain` with `rawToUTF8`. After that you can use `jsc_eval`.
<br>
<br>
`Hex` is beneficial if you can not use UTF-8 Chars. Although it takes up 2x more size than `UTF8-Plain` and 4x more size than `UTF8-Raw`
<br>
<br>
`Array` is not beneficial as it takes more size than `Hex` and any `UTF8` plus all other encodings can be converted to array.
