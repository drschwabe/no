# node-html

For rapid prototyping & building of webpages or apps in as few lines as possible.

node-html is small lib with a set of convenience functions to get you quickly going with a functional HTML/CSS/JS page or app - including one-liners for generating an HTML skeleton,  starting an Express server, serving a static directory, and building a client-side bundle (compile or watch) 

The watch & compile functions are pre-configured to accommodate using ES6 module import + CommonJS require formats *at the same time* - such that you can use either format including both formats in the same JS file if you so please. 

### usage
```bash
npm install node-html
```
 
```javascript
//In my nodejs file ie: server.js 
const no = require('node-html')
```

### api

**html**   
`no.html(css,body,script)`  
Outputs an HTML skeleton string.  Optionally provide CSS (in the form of a raw CSS string), HTML content to go in the body, or a script filename.  [See template] 

```javascript
no.html(null,`<h1>hello world</h1>`, 'client.bundle.js')
// <html><head><style></style></head><body>
//   <h1>hello world</h1>
//   <script src="client.bundle.js"></script>
// </body></html>  
```
<br>


**makeIndex**   
`no.makeIndex(path,html)`  
Writes an HTML file to the filesystem.  Provide an optional `path` (defaults to cwd + 'index.html') and `html` (string)  If `html` is not provided it will write the default `no.html()` skeleton.  

```javascript
let html = no.html(null,`<h1>hello world</h1>`, 'client.bundle.js')
no.makeIndex(null,html)
// outputs index.html to cwd with the html output from previous example
```
<br>


**server**   
`no.server(port)`  
Creates and returns an Express web server on optional port (default `8000`).  

```javascript
no.server(8555)
// Express server created at: http://localhost:8555
```
<br>


**static**   
`no.static(route, directory)`  
Serves a given filesystem directory with optional route.  Run this after `no.server()`
Both params are optional, will use '/' and your `cwd` by default.

```javascript
no.server()
no.static()
//^ all files in your cwd now being served by Express
no.static('/assets', __dirname + '/assets')
//^ or call on specific directories
```
<br>


**index**   
`no.index(html, port)`  
Serves the given html (`string`) at optional port.  If a server is not already running it will be created. 

```javascript
no.index( no.html(null,null,'myscript.js') )
// http://localhost:8000/ now serving a blank HTML page + <script src="myscript.js">
```
<br>


**index**   
`no.css(html, port)`  
Returns a string of [MassCSS]

```javascript
no.html(no.css,`<h1 class="green">Now we can use CSS</h1>`))
// > HTML skeleton with the above h1 tag in the <body> and MassCSS loaded into the <head><style> tag
```
<br>

**jquery**   
`no.jquery()`  
Let's you run jquery on a given string of HTML, useful in conjunction with no.html() for quickly hacking in some content in your HTML skeleton.  A wrapper for [cheerio.load]

```javascript
let $ = no.jquery( no.html() )
$('body').append( `<h1>hello world</h1>` )
$('h1').html( `<h1>hello flat world</h1>` )
$.html()
// <html><head></head>
// <body>
//   <h1>hello flat world</h1>
// </body></html>
```
<br>

**compile**   
`no.compile(watch, compress, clientJsName, bundleName)`  

Runs [browserify] with a preconfigured set of transforms including [stringify] and [babelify] (and [Babel] presets,plugins, and additional pre-configuration).  

Supply booleans as first params to indicate whether to `watch` or `compress` the bundle, and specify a `clientJsName` (ie- the script you want bundled) as well as output bundle name (if the latter two params are not provided will default to `client.js` and `client.bundle.js`)

This 'holy grail' build configuration *if all goes well* should allow you to use ES6 imports and Node/CommonJS require calls interchangeably.  And allows you to require `.html`, `.css`, and `.svg` extensions. 

Note: see [compiling](#compiling) for potential prerequisite

```javascript
let html = no.html(null,null, 'client.bundle.js') 
no.makeIndex(html) //< writes the html to disk
no.compile(false,true) 
//> outputs a compressed bundle of a file called `client.js` in your cwd 
```
<br>


**watch**   
`no.watch(clientJsName, bundleName)`  

Alias for `no.compile(true,false)` this will watch the optional `clientJsName` and output `bundleName` (defaults to `client.js` and `client.bundle.js`)

Note: see [compiling](#compiling) for potential prerequisite

```javascript
let html = no.html(null,null, 'client.bundle.js') 
no.watch(null, '/public/client.bundle.js')
//> creates a bundle of a file named `client.js` and ouptuts to /public/client.bundle.js
// rebundles anytime you make changes to client.js including any changes to modules within it loaded via import or require
```
<br>

Also note: this particular build configuration makes for a rather system intensive process, especially when `watch`ing. 


#### compiling/watching extra step
<a name="compiling"></a>

To use the `no.compile()` and/or `no.watch()` functions you may need to install the following dependencies (copy/paste and run in your local project): 

```bash
npm install @babel/core@7.5.5 @babel/preset-env@7.5.5 @babel/runtime@7.11.2 @babel/plugin-transform-runtime@7.11.5 @babel/plugin-external-helpers@7.10.4 babelify@10.0.0 --saveDev
```

This is necessary to give Browserify & Babel context within your project's local directory (ie- so it can watch/re-compile as any files in your project change).  

MIT


[See template]: ./no-html.js#3
[MassCSS]: https://github.com/drschwabe/masscss
[cheerio.load]: https://github.com/cheeriojs/cheerio
[browserify]: https://github.com/browserify/browserify
[stringify]: https://github.com/JohnPostlethwait/stringify
[babelify]: https://github.com/babel/babelify
[Babel]: https://github.com/babel/babel