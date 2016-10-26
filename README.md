# node-notes

### To start a program using node-notes
node <program_name>

### Input via command-line in node-notes
Node uses 'process' object to get input as arguments. 'process.argv' hold all the arguments given via command-line.

`node file.js 1 2 three`   

process.argv is an array which looks like - ["node", <path_of_file>, <argument>...]

First argument is always "node", second argument is path of the file that is being run, and then arguments passed i.e., "1", "2", "three".

One thing to note here is that the arguments passed are in string form in process.argv array so to use them in any other format, we have coerce them.

For quickly coercing string to no, just prefix '+' or pass it to Number() e.g., +process.argv[3] or Number(process.argv[3]).
