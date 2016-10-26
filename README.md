# node-notes

## To start a program using node-notes
node <program_name>

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
