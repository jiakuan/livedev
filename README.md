livedev
=======

livedev is a development proxy server for golang that allows live reloading.  
It supports multiple server configuration.  
It uses the request's header "Host" field to determine which server the request should be routed to.  
If no match is found the request is routed the default server.

 
Compatible with: `go version go1.0.2+`

Features
========
* Cross-platform
* Unobstructive. No code change required
* Simple json configuration file
* Multiple server support
* Automated build service.
* Dependency change detection 


Installation
============

`go get -u github.com/qrtz/livedev` 

Configuration
=============
livedev is controlled by a json configuration file:

* __port__: (int, default:"80") proxy port
* __GOROOT__: (string, optional) 
* __GOPATH__: (string, optional)
* __server__: ([]Server) A list of Server object with the following options:
    * __GOROOT__: (string, optional)  Server specific GOROOT for compiling with different go version
    * __GOPATH__: ([]string, optional) Server specific GOPATH.
    * __host__: (string, default:'localhost') server hostname (must be unique)
    * __port__: (int, default: dynamically generated) server port  
 When omitted, the server must accept `addr=<hostname:port>` argument
    * __workspace__: (string, optional) The project root. It will be added to the build process GOPATH  
    If omitted, an atempt will be make to guess if from __target__
    * __target__: (string, optional) Build target. The file that contains the main function.  
 if __target__ is not in the GOPATH, livedev will attempt to add it by guessing the workspace from the filename.  
 When __target__ is ommited, the build step is skipped.
    * __resources__: (optional) A list resources such as template files. Cause a server restart when chaange is made to them.
        * __ignore__: (string, optional) filename pattern
        * __paths__: ([]string) A list of files or directories to monitor
    * __bin__: (string, optional) server executable file. When absent, livedev will act as proxy only
    * __builder__: ([]string, optional) To use a builder other that default build tool. The first element is the command and the rest its arguments
    * __startup__: ([]string, optional) server startup argument list
    * __default__: (bool, optinal) Specifies the default server.  
 Defaults to the first server in the list
 
### Example:

    {
        "port":80,
        "server":[
            {
                "host":"example.com",
                "port": 8080,
                "target":"/projects/example/src/main.go",
                "resources":{
                    "ignore":"static*",
                    "paths":["/projects/expemple/templates"]
                 },
                "bin":"/projects/example/bin/example",
                "startup":["-res", "/path/to/resource/directory"],
                "default":true
            }
        ]
    }


Usage
=====

    $ livedev -c config.json
    
Assuming you used the above configuration and added `example.com` to your host file,
point your browser to [http://example.com](http://example.com)  
livedev will start your app, compiling it if necessary and forward the request to
it on the configured port.

You can have multiple app running with different hostname and port number.
Just add another entry in the server list.



