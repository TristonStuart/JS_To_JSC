# JS To JSC

## About
Ran in the web-browser with web assembly. <br>
This is a wrapper for JSC.js (https://github.com/mbbill/JSC.js) to make it easier to convert js to jsc bytecode.

## Pitfalls
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
