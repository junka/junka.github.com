---
layout: post
title: "NodeJS_tutorial"
description: ""
category: ""
tags: [Node,JavaScript]
---
{% include JB/setup %}

##Step into NodeJS

Skip the instruction and Installation. Some features keep in mind.

	*Single thread
	*Event driven programming
	*Asynchronous IO

It differs from Ruby/Rails,Java or other server technology.

##Sample code for a http server

	var http = require('http');
	http.createServer(function (req, res) {
		res.writeHead(200, {'Content-Type': 'text/plain'});
		res.end('Hello World\n');
	}).listen(1337, '127.0.0.1');
	console.log('Server running at http://127.0.0.1:1337/');
	
1,require http module from Node.js

2,use function fo http module to create server

###Asynchronous IO & Event driven programming

Most feature of Node. Non-blocking I/O just send require to the thread and continue to next instruction. The result of the require will be notified by the thread as an event. The thread must have a **Loop**.

Event is sent to a queue. *EventEmitter* implements the callback function of all event.

##Core module

###Global Object

*window* is a global object for a browser. *console*,*process* in Nodejs are both attributes of *global*, *global* is the global object.
Some important methods

	process.nextTick(callback);
	process.stdin.resume();	process.stdin.on();	process.stdin.write();
	process.platform	process.pid	process.execPath	process.memoryUsage();
	
	console.log();
	console.error();	console.trace();

###Tools

util is a core module of Nodejs, it provides many functions.

	util.inherits(sub,base);	----inherit for objects **ONLY** in *base.protoype.*\
	util.inspect(object,[showHidden],[depth],[color]);	----any object to string, use for debug
	util.isArray();	util.isRegExp();	util.isDate();	util.isError(); util.debug(); util.format();
	
###event-driven

events is the most important module in Nodejs. It provides only one object *event.EventEmitter*
	
	var events = require('events');
	var emitter = new events.EventEmitter();
	emitter.on('someEvent', function(arg1, arg2) {
		console.log('listener1', arg1, arg2);
	});
	emitter.on('someEvent', function(arg1, arg2) {
		console.log('listener2', arg1, arg2);
	});
	emitter.emit('someEvent', 'byvoid', 1991);
	
the results of the codes above:
	
	listener1 byvoid 1991
	listener2 byvoid 1991
	
the API of the EventEmitter

	EventEmitter.on(event,listener);
	EventEmitter.emit(event,[arg1],[arg2],[...]);
	EventEmitter.once(event,listener);
	EventEmitter.removeListener(event,listener);
	EventEmitter.removeAllListeners([event]);
	...
	
Now see the **error** of the EventEmitter

	emitter.emit('error');//will throw error if there is no listener for it
	
Note:we used NOT to use EventEmitter directly, otherwise we do inherit operation for object. Like fs , net, http...

###fs file system

fs provides read, write, rename, delete, link interface for POSIX file system. All the operations have two versions of the Sync or Async ways.

	fs.readFile(filename,[encoding],[callback(err,data)]);
	fs.readFileSync(filename,[encoding]);
	fs.open(path,flags,[mode],[callback(err,fd)]);
	fs.read(fd,buffer,offset,length,position,[callback(err,bytesRead,buffer)]);
	fs.write(fd,buffer,offset,length,position,[callback(err, bytesWritten, buffer)]);
	fs.writeFile(filename, data,[encoding],[callback(err)]);
	fs.unlink(path, [callback(err)]);
	fs.mkdir(path, [mode], [callback(err)]);
	fs.rmdir(path, [callback(err)]);
	fs.readdir(path, [callback(err, files)]);
	fs.rename(path1, path2, [callback(err)]);
	fs.truncate(fd, len, [callback(err)]);
	fs.chown(path, uid, gid, [callback(err)]);
	...
	
###HTTP server and client

see the Sample code for a http server above. http.createServer creates an instance of http.Server.

http.Server inherits EventEmitter, it has the events:

	request: emit when client sends a request, has *req* and *res* parameters for http.ServerRequest and http.ServerResponse
	connection: emit when TCP connects, one parameter socket for net.Socket
	close: emit when server closes, not user closes

http.ServerRequest sends the HTTP request information, what matters for backend developers. events :

	data: emit when request data comes,provide one parameter *chunk*
	end: emit when data sending finishes.
	close: emit when user close the sending.
	
PHP has $_GET and $_POST for requests. Nodejs do things like this:

	for GET: Nodejs do these in *url* module url.parse(req.url,true);
	for POST:Nodejs will ignore the requests except explaining in querystring.parse(post);
	
http.ServerResponse response message to clients, it has three functions:

	response.writeHead(statusCode,[header]): for 200 or 404 code
	response.write(data,[encoding]): data 
	response.end([data],[encoding]): tell the client all the sending is over
	
Now see the HTTP client, http has two functions **http.request** and **http.get** for clients sending messages.

	http.request(options, callback): client sends request to server
	http.get(options, callback): another way for GET method

http.ClientRequest is the object from the above two functions.Like http.ServerResponse, it has write and end function and also has:
	
	request.abort():abort the request
	request.setTimeout(timeout,[callback]): after timeout, do callback
	request.setNoDelay();	request.setSocketKeepAlive([enable],[initialDelay]);
	...

http.ClientResponse is like http.ServerRequest, has data ,end and close events, and functions below:

	response.setEncoding([encoding]):if not encoding, store in buffer
	response.pause(): pause receiving and sending events for download
	response.resume():resume from pause status
	
##Practical Tutorial

As mentioned above, http POST process is not implemented in one phrase like PHP. But nodejs has third party's framework such as Express help us build web applications.

	var express = require('express');
	var app = express.createServer();
	app.use(express.bodyParser());
	app.all('/', function(req, res) {
		res.send(req.body.title + req.body.text);
	});
	app.listen(3000);

It's a express way to do http server.

Use the express tool and template to build a project quickly.

	express -t ejs NjBlog

Then get messages like:

	create : NjBlog
	create : NjBlog/package.json
	create : NjBlog/app.js
	create : NjBlog/public
	create : NjBlog/public/javascripts
	create : NjBlog/public/images
	create : NjBlog/public/stylesheets
	create : NjBlog/public/stylesheets/style.css
	create : NjBlog/routes
	create : NjBlog/routes/index.js
	create : NjBlog/routes/user.js
	create : NjBlog/views
	create : NjBlog/views/layout.jade
	create : NjBlog/views/index.jade

	install dependencies:
		$ cd NjBlog && npm install

	run the app:
		$ node app
	
Follow the instructions, then open http://127.0.0.1:3000 you will see the welcome page. 

In fact, jade replace ejs module as view engine in the dependency.




	