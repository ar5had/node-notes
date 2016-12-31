# node-notes
Reading this along with doing learnyounode exercises will benifit you the most

## Variable naming system
In normal js, we can't add `-` or `dash` between words for valid name so we use camel case or any other case. But in node js, the naming system of module follows a convention to add `-` or `dash` between words e.g., `var LiveServer = require('gulp-live-server');` 

## To start a program using node-notes
node \<program_name>

## Input via command-line in node-notes
Node uses 'process' object to get input as arguments. 'process.argv' hold all the arguments given via command-line.

`node file.js 1 2 three`   

process.argv is an array which looks like - ["node", \<path_of_file>, \<argument>...]

First argument is always "node", second argument is path of the file that is being run, and then arguments passed i.e., "1", "2", "three".

One thing to note here is that the arguments passed are in string form in process.argv array so to use them in any other format, we have coerce them.

For quickly coercing string to no, just prefix '+' or pass it to Number() e.g., +process.argv[3] or Number(process.argv[3]).

## Synchronous file reading in node

To perform a filesystem operation we need the fs module from the Node core library. To load this kind of module, or any other "global" module, use the following incantation:  

`var fs = require('fs')`  

Now you have the full fs module available in a variable named fs.  

All synchronous (or blocking) filesystem methods in the fs module end with  'Sync'. To read a file, you'll need to use fs.readFileSync('/path/to/file'). This method will return a Buffer object containing the complete contents of the file.

`var bfr = fs.readFileSync('path')`

It is not necessary that buffer object contains content of file in string format. Without specifying the encoding type, we will get content in binary or ascii format but we can easily convert it using toString() method. Another way of getting string format content is to pass another variable that tells about the type of encoding that we want our content variable to have.

`var bfr = fs.readFileSync('path', 'utf8')`

Program to read file synchronously when second argument on command-line is file-path:
``` js
var fs = require("fs");

var bfr = fs.readFileSync(process.argv[2], 'utf8');

console.log(bfr.split("\n").length - 1);
```

## Asynchronous file reading in node
For async file reading, use fs.readFile() and instead of using the return value of this method we need to collect the value from a callback function that we pass in as the second argument.

`function callback (err, data) { /* ... */ }`

We can check if an error occurred by checking whether the first  
argument is truthy. If there is no error, we should have your Buffer  
object as the second argument. As with readFileSync(), we can supply  
'utf8' as the second argument and put the callback as the third argument  
and we will get a String instead of a Buffer.

`fs.fileRead('path', 'encoding', function(err, data) { ... });`

Program to read file in async way when second argument on command-line is file-path:
``` js
  var fs = require('fs')  
  var file = process.argv[2]  

  fs.readFile(file, function (err, contents) {  
    if (err) {  
      return console.log(err)  
    }  
    // fs.readFile(file, 'utf8', callback) can also be used  
    var lines = contents.toString().split('\n').length - 1  
    console.log(lines)  
  })  
```

## Reading directories asynchronously

`fs.readdir('path', callback)`
readdir can be used to read files inside any directory. First arg to this method is path and other is callback similar to readFile method which have two params, first one holds error (if any in case of failure while reading) and other is an array of file names that this directory contains.

Path module is very useful for handling and transforming file paths.

`path.extname(p)`

Return the extension of the path, from the last '.' to end of string in the last portion of the path. If there is no '.' in the last portion of the path or the first character of it is '.', then it returns an empty string.

Program to display all the files having extension that user gives as second arg:

``` js
var fs = require("fs");
var path = require("path");
fs.readdir(process.argv[2], function(err, list) {
    if (err) throw new Error(err);
    list.forEach(function(name) {
        // We can use path module here as well to get extension out of filenames
        // if ( path.extname(name) === ("." + process.argv[3]) )
        if(name.split(".")[1] === process.argv[3])
            console.log(name);
    });
});
```

##  Making modules

For exporting a function, assign 'module.exports' that function.

`module.exports = myFunkyFunction;`

For importing a module, use 'require' method to load it into a variable.

`var myFunkyFunctionModule = require(./myFunkyFunction)`

Program that displays file names with desired ext in a directory using modules :

myModule.js
``` js
function getNames(dir, ext, callback) {
    var fs = require("fs");
    var res = [];
    fs.readdir(dir, function(err, list) {
        if (err) return callback(err);

        list.forEach(function(name) {
            if(name.split(".")[1] === ext) res.push(name);

        });
        callback(null, res);
    });
    // if 'callback(null, res);' statement were here here then this module doesn't work.
    // Figure out why ??
}
module.exports = getNames;
```

displayFilesInDir.js
``` js
var myMod = require("./myModules");
myMod(process.argv[2], process.argv[3], function(err, names) {
    if(err) throw new Error(err);
    names.forEach(function(name){console.log(name)});
});
```

## Making GET request

For making a GET request, http module is used. The http.get() method is a
shortcut for simple GET requests, use it to simplify your solution. 
The first argument to http.get() can be the URL you want to GET; 
provide a callback as the second argument.  

Unlike other callback functions, this one has the signature:  

   `function callback (response) { /* ... */ }`  

Where the response object is a Node Stream object. You can treat Node  
Streams as objects that emit events. The three events that are of most  
interest are: "data", "error" and "end". You listen to an event like so:  

   `response.on("data", function (data) { /* ... */ })`  

The "data" event is emitted when a chunk of data is available and can be  
processed. The size of the chunk depends upon the underlying data source.  

The response object / Stream that you get from http.get() also has a  
setEncoding() method. If you call this method with "utf8", the "data"  
events will emit Strings rather than the standard Node Buffer objects  
which you have to explicitly convert to Strings.  

Program to print result obtained from a GET request:

```js
 var http = require('http')  
       
 http.get(process.argv[2], function (response) {  
   response.setEncoding('utf8')  
   response.on('data', console.log)  
   response.on('error', console.error)  
 }).on('error', console.error)  
```

## Piping in streams

Piping is a great mechanism in which you can read data from the source and write to destination without managing the flow yourself. Take a look at the following snippet:

```js
var fs = require('fs');
var readableStream = fs.createReadStream('file1.txt');
var writableStream = fs.createWriteStream('file2.txt');
```

`readableStream.pipe(writableStream);`

The above snippet makes use of the pipe() function to write the content of file1 to file2. As pipe() manages the data flow for you, you should not worry about slow or fast data flow. This makes pipe() a neat tool to read and write data. You should also note that pipe() returns the destination stream. So, you can easily utilize this to chain multiple streams together. 

Imagine there was no .pipe command. You would need to do piping like this:

```js
function pipe(in, out) {
  in.on("error", function(error) {
    out.emit("error", error);
  });
  in.on("data", function(data) {
   out.write(data);
  });
  in.on("end", function() {
     out.emit("end");
  });
}
```

This is an example code but what pipe does is essentially this: Instead of you needing to write event listeners on the input and passing the data to the output you can use the method pipe for your convenience.

**NOTE: Do piping before handling events, dont worry about errors to get piped as while piping error can be handled**

## Collecting all the data from a GET request

Http.get DOES finish execution (by creating the stream) before the callback is called. It is the stream that is still being processed in the callback. The end result of http.get is the stream object that is still updating until the complete response is received.

The way to think about callbacks is not as a function that executes when the parent is done executing, but as a function that is an argument of another function. Theoretically, there is nothing to stop the parent from executing the callback at any time in its execution cycle. The convention in node.js just happens to be that callbacks get executed after the parent is done.

Function callback is passed as a parameter into function http.get and so it can have access to the response object created during the execution of http.get. However, what is also happening is that the response is a stream, meaning that it is continually updated until it is complete.

Here is the order of operation

1- http.get calls the external resource.

2- http.get creates the response object as a stream and updates it as the data comes in from the external resource

3- Upon each update of the response object, it emits a "data" event.

4- function callback contains a listener (response.on) that is activated whenever the "data" event is thrown.

Program that performs an HTTP GET request to a URL provided to you  
as the first command-line argument. Collect all data from the server (not  
just the first "data" event) and then write two lines to the console  
(stdout).  

The first line you write should just be an integer representing the number  
of characters received from the server. The second line should contain the  
complete String of characters sent by the server.

Without third-party packages:

```js
var http = require('http');
http.get(process.argv[2], function(res) {
    res.setEncoding('utf8');
    var lines = "";
    res.on("error", console.error);
    res.on("data", function(data){lines = lines.concat(data)});
    res.on("end", function(){ 
        console.log(lines.length);
        console.log(lines);
    });
}).on("error", console.error);
```

Using bl package:

```js
var http = require('http');
var bl = require("bl");
http.get(process.argv[2], function(res) {
    res.setEncoding('utf8');
    res.pipe(bl(function(err, data) {
        if (err) console.log(err);
        console.log(data.length);
        console.log(data.toString());
    }));
    res.on("error", console.error);
}).on("error", console.error);
```

Using concatStream package: 

```js
var http = require('http');
var cs = require("concat-stream");
http.get(process.argv[2], function(res) {
    res.setEncoding('utf8');
    res.pipe(cs(function(data) {
        console.log(data.length);
        console.log(data);
    }));
    res.on("error", console.error);
}).on("error", console.error);
```

Both bl and concat-stream can have a stream piped in to them and they will
collect the data for you. Once the stream has ended, a callback will be
fired with the data:

   response.pipe(bl(function (err, data) { /* ... */ }))
   // or
   response.pipe(concatStream(function (data) { /* ... */ }))

Note that you will probably need to data.toString() to convert from a
Buffer.

**NOTE:**

  * In case of concatStream we dont have "error" argument in callback so error might be handled by res.on("error", ...)

  * In case of bl we have used toString even when we have setEncoding for response stream because bl doesnt have a setEncoding method.
  
## Making multiple GET requests in an predefined order
Don't expect servers to play nicely! They are not going to  
give you complete responses in the order you hope. 

Counting callbacks is one of the fundamental ways of managing async in  
Node. Rather than doing it yourself, you may find it more convenient to  
rely on a third-party library such as [async](http://npm.im/async) or  
[after](http://npm.im/after). 

Program for multiple request and printing in order:

```js
var http = require("http");

function multipleGet(start, end) {
    if (start === end) return;
    http.get(process.argv[start], function(res) {
        res.setEncoding('utf8');
        res.on("error", console.error);
        var data = "";
        res.on("data", function(newData) {
            data += newData;
        });
        res.on("end", function() {
            console.log(data);
           multipleGet(++start, end); 
        });
    }).on("error", console.error);
    
}

multipleGet(2, process.argv.length, "");
```

**NOTE:** Dont put async function in console.log because async function will take time but console.log will get executed
instantly so better put it in callback. For error catching use promises.

## TCP time server

No HTTP involved here so we need to use the net module from Node core which has
all the basic networking functions.

The net module has a method named net.createServer() that takes a
function. The function that you need to pass to net.createServer() is a
connection listener that is called more than once. Every connection
received by your server triggers another call to the listener. The
listener function has the signature:

   `function listener(socket) { /* ... */ }`

net.createServer() also returns an instance of your server. You must call
server.listen(portNumber) to start listening on a particular port.

A typical Node TCP server looks like this:
```js
   var net = require('net')
   var server = net.createServer(function (socket) {
     // socket handling logic
   })
   server.listen(8000)
```
Remember to use the port number supplied to you as the first command-line
argument.

The socket object contains a lot of meta-data regarding the connection,
but it is also a Node duplex Stream, in that it can be both read from, and
written to.

Use socket.write(data) to write data to the socket and socket.end() to
close the socket. Alternatively, the .end() method also takes a data  
object so you can simplify to just: socket.end(data). 


Program for making tcp time server using net module:

``` js
var net = require("net");
var date = require("date-and-time");
var server = net.createServer(function(socket) {
    var now = new Date();
    var data = date.format(now, 'YYYY/MM/DD HH:mm:ss').replace(/\//g, "-").slice(0,-3); 
    socket.end(data+"\n");
}).on("error", console.error);
server.listen(process.argv[2]);
```
## Creating a http server for file stream

``` js
var http = require("http");
var fs = require("fs");
var server = http.createServer(function(req, res) {
    var src = fs.createReadStream(process.argv[3]);
    src.pipe(res);
    src.on("error", console.error);
});

server.listen(Number(process.argv[2]));
```

## Handling POST request and modifying POST request data

While we're not restricted to using the streaming capabilities of the  
request and response objects, it will be much easier if you do.  

There are a number of different packages in npm that you can use to  
"transform" stream data as it's passing through. For this exercise the  
through2-map package offers the simplest API.  

through2-map allows you to create a transform stream using only a single  
function that takes a chunk of data and returns a chunk of data. It's  
designed to work much like Array#map() but for streams:  

```js
   var map = require('through2-map')  
   inStream.pipe(map(function (chunk) {  
     return chunk.toString().split('').reverse().join('')  
   })).pipe(outStream)  
```

In the above example, the incoming data from inStream is converted to a  
String (if it isn't already), the characters are reversed and the result  
is passed through to outStream. So we've made a chunk character reverser!  
Remember though that the chunk size is determined up-stream and you have  
little control over it for incoming data.  

Program that creates a HTTP server that receives only POST requests and converts incoming POST body characters to upper-case and returns it to the client:

``` js
var http = require("http");
var map = require("through2-map");
var server = http.createServer(function(req, res){
   if(req.method === "POST") 
       req.pipe(map(function(chunk){return chunk.toString().toUpperCase();})).pipe(res);
   
}).on("error", console.error);
server.listen(process.argv[2]);
```

Without using through2-map module:

``` js
var http = require("http");
var str = "";
var server = http.createServer(function(req, res){
   if(req.method === "POST") {
       req.on('data',(function(data){str += data.toString(); }));
   
       req.on('end', function(){res.write(str.toUpperCase());});
   }
}).on("error", console.error);
server.listen(process.argv[2]);
```

## Handling JSON api using http server

The request object from an HTTP server has a url property that you will
need to use to "route" your requests for the two endpoints.

You can parse the URL and query string using the Node core 'url' module.
url.parse(request.url, true) will parse content of request.url and provide
you with an object with helpful properties.

For example, on the command prompt, type:

`   $ node -pe "require('url').parse('/test?q=1', true)"`

Your response should be in a JSON string format. Look at JSON.stringify()
for more information.

You should also be a good web citizen and set the Content-Type properly:

`   res.writeHead(200, { 'Content-Type': 'application/json' })`

The JavaScript Date object can print dates in ISO format, e.g. new
Date().toISOString(). It can also parse this format if you pass the string
into the Date constructor. Date#getTime() will also come in handy.


Program that creates a HTTP server that serves JSON data when it receives a GET request
to the path '/api/parsetime'. Expect the request to contain a query string
with a key 'iso' and an ISO-format time as the value.

For example:

/api/parsetime?iso=2013-08-10T12:10:15.474Z

The JSON response should contain only 'hour', 'minute' and 'second'
properties. For example:

```js
   {
     "hour": 14,
     "minute": 23,
     "second": 15
   }
```

Add second endpoint for the path '/api/unixtime' which accepts the same
query string but returns UNIX epoch time in milliseconds (the number of
milliseconds since 1 Jan 1970 00:00:00 UTC) under the property 'unixtime'.
For example:

```js
   { "unixtime": 1376136615474 }
```

Server should listen on the port provided by the first argument to program.

Code:

```js
var http = require("http");
var url = require("url");
var server = http.createServer(function(req, res){
   var urlProps = url.parse(req.url, true);
   var date = new Date(urlProps.query.iso), json = {};
   if(urlProps.pathname === "/api/unixtime") {
        res.writeHead(200, {'Content-Type': 'application/json'}); 
        json = {"unixtime": date.getTime()};  
        res.end(JSON.stringify(json));
   }
   else if (urlProps.pathname === "/api/parsetime") {
        res.writeHead(200, {'Content-Type': 'application/json'}); 
        json = {"hour": date.getHours(), "minute": date.getMinutes(), "second": date.getSeconds()};
        res.end(JSON.stringify(json));
   }
   else{
      res.writeHead(404);
      res.end();
  }
}).on("error", console.error);
server.listen(process.argv[2]);
```

**NOTE:** 
  * response.send() === response.write(...) and response.end();
  * response.write can be called many times but end is called only one time

