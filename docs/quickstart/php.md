---
bodyclass: docs
layout: docs
headline: PHP Quickstart
sidenav: doc-side-quickstart-nav.html
type: markdown
---
<p class="lead">This guide gets you started with gRPC in PHP with a simple
working example.</p>

<div id="toc"></div>

### Prerequisites

* `php` 5.5 or above, 7.0 or above
* `pecl`
* `composer`
* `phpunit` (optional)

**Install PHP and PECL on Ubuntu/Debian:**

For PHP5:

```sh
$ sudo apt-get install php5 php5-dev php-pear phpunit
```

For PHP7:

```sh
$ sudo apt-get install php7.0 php7.0-dev php-pear phpunit
```
or
```sh
$ sudo apt-get install php php-dev php-pear phpunit
```

**Install PHP and PECL on CentOS/RHEL 7:**

```sh
$ sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
$ sudo rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
$ sudo yum install php56w php56w-devel php-pear phpunit gcc zlib-devel
```

**Install PHP and PECL on Mac:**

```sh
$ curl -O http://pear.php.net/go-pear.phar
$ sudo php -d detect_unicode=0 go-pear.phar
```

**Install Composer (Linux or Mac):**

```sh
$ curl -sS https://getcomposer.org/installer | php
$ sudo mv composer.phar /usr/local/bin/composer
```

**Install PHPUnit (Linux or Mac):**

```sh
$ wget https://phar.phpunit.de/phpunit-old.phar
$ chmod +x phpunit-old.phar
$ sudo mv phpunit-old.phar /usr/bin/phpunit
```

## Install the gRPC PHP extension

There are two ways to install gRPC PHP extension.
* `pecl`
* `build from source`

### Using PECL

```sh
sudo pecl install grpc
```

or specific version

```sh
sudo pecl install grpc-1.7.0
```

Note: for users on CentOS/RHEL 6, unfortunately this step won’t work. 
Please follow the instructions below to compile the PECL extension from source.

#### Install on Windows

You can download the pre-compiled gRPC extension from the PECL
[website](https://pecl.php.net/package/grpc)

### Build from Source with gRPC C core library

Clone this repository

```sh
$ git clone -b $(curl -L https://grpc.io/release) https://github.com/grpc/grpc
```

#### Build and install the gRPC C core library

```sh
$ cd grpc
$ git submodule update --init
$ make
$ sudo make install
```

#### Build and install gRPC PHP extension

Compile the gRPC PHP extension

```sh
$ cd grpc/src/php/ext/grpc
$ phpize
$ ./configure
$ make
$ sudo make install
```

This will compile and install the gRPC PHP extension into the 
standard PHP extension directory. You should be able to run 
the [unit tests](#unit-tests), with the PHP extension installed.


### Update php.ini

After installing the gRPC extension, make sure you add this line 
to your `php.ini` file, (e.g. `/etc/php5/cli/php.ini`, 
`/etc/php5/apache2/php.ini`, or `/usr/local/etc/php/5.6/php.ini`), 
depending on where your PHP installation is.

```sh
extension=grpc.so
```

**Add the gRPC PHP library as a Composer dependency**

You need to add this to your project's `composer.json` file.

```
  "require": {
    "grpc/grpc": "v1.7.0"
  }
```

To run tests with generated stub code from `.proto` files, you will also 
need the `composer` and `protoc` binaries. You can find out how to get these below.


## Install other prerequisites for both Mac OS X and Linux

* `protoc: protobuf compiler`
* `protobuf.so: protobuf runtime library`
* `grpc_php_plugin: Generates PHP gRPC service interface out of Protobuf IDL`

### Install Protobuf compiler

If you don't have it already, you need to install the protobuf compiler
`protoc`, version 3.4.0+ (the newer the better) for the current gRPC version.
If you installed already, make sure the protobuf version is compatible with the 
grpc version you installed. If you build grpc.so from source, you can check
the version of grpc inside package.xml file.

The compatibility between the grpc and protobuf version is listed as table below:

grpc | protobuf
--- | --- 
v1.0.0 | 3.0.0(GA)
v1.0.1 | 3.0.2
v1.1.0 | 3.1.0 
v1.2.0 | 3.2.0 
v1.2.0 | 3.2.0 
v1.3.4 | 3.3.0 
v1.3.5 | 3.2.0
v1.4.0 | 3.3.0 
v1.6.0 | 3.4.0

If `protoc` hasn't been installed, you can download the `protoc` binaries from
[the protocol buffers GitHub repository](https://github.com/google/protobuf/releases).
Then unzip this file and Update the environment variable `PATH` to include the path to 
the protoc binary file./protobuf/releases).
Then unzip this file and Update the environment variable `PATH` to include the path to 
the protoc binary file.

If you really must compile `protoc` from source, you can run the following
commands, but this is risky because there is no easy way to uninstall /
upgrade to a newer release.

```sh
$ cd grpc/third_party/protobuf
$ ./autogen.sh && ./configure && make
$ sudo make install
```

### Protobuf Runtime library

There are two protobuf runtime libraries to choose from. They are identical
in terms of APIs offered. The C implementation provides better performance, 
while the native implementation is easier to install. Make sure the installed 
protobuf version works with grpc version.

#### 1. C implementation (for better performance)

``` sh
$ sudo pecl install protobuf
```
or specific version

``` sh
$ sudo pecl install protobuf-3.4.0
```

After protobuf extension is installed, Update php.ini by adding this line 
to your `php.ini` file, (e.g. `/etc/php5/cli/php.ini`, 
`/etc/php5/apache2/php.ini`, or `/usr/local/etc/php/5.6/php.ini`), 
depending on where your PHP installation is.

```sh
extension=protobuf.so
```

#### 2. PHP implementation (for easier installation)

Add this to your `composer.json` file:

```
  "require": {
    "google/protobuf": "^v3.3.0"
  }
```

### PHP Protoc Plugin

You need the gRPC PHP protoc plugin to generate the client stub classes.
It can generate server and client code from .proto service definitions.

It should already been compiled when you run `make` from the root directory
of this repo. The plugin can be found in the `bins/opt` directory. We are
planning to provide a better way to download and install the plugin
in the future.

You can also just build the gRPC PHP protoc plugin by running:

```sh
$ git clone -b $(curl -L https://grpc.io/release) https://github.com/grpc/grpc
$ cd grpc
$ git submodule update --init
$ make grpc_php_plugin
```

Plugin may use the new feature of the new protobuf version, thus please also
make sure that the protobuf version installed is compatible with the grpc version 
you build this plugin.

## Download the example

You'll need a local copy of the example code to work through this quickstart.
Download the example code from our GitHub repository (the following command
clones the entire repository, but you just need the examples for this quickstart
and other tutorials):

Note that currently you can only create clients in PHP for gRPC services -
you can find out how to create gRPC servers in our other tutorials,
e.g. [Node.js](node-quickstart.md).

```sh
$ # Clone the repository to get the example code:
$ git clone -b {{ site.data.config.grpc_release_tag }} https://github.com/grpc/grpc
$ # Build grpc_php_plugin to generate proto files if not build before
$ cd grpc && git submodule update --init && make grpc_php_plugin
$ # Navigate to the "hello, world" PHP example:
$ cd examples/php
$ ./greeter_proto_gen.sh
$ composer install
```

## Run a gRPC application

From the `examples/node` directory:

1. Run the server

   ```sh
   $ npm install
   $ cd dynamic_codegen
   $ node greeter_server.js
   ```

In another terminal, from the `examples/php` directory:

1. Run the client

   ```sh
   $ ./run_greeter_client.sh
   ```

Congratulations! You've just run a client-server application with gRPC.

## Update a gRPC service

Now let's look at how to update the application with an extra method on the
server for the client to call. Our gRPC service is defined using protocol
buffers; you can find out lots more about how to define a service in a `.proto`
file in [gRPC Basics: PHP][]. For now all you need to know is that both the
server and the client "stub" have a `SayHello` RPC method that takes a
`HelloRequest` parameter from the client and returns a `HelloResponse` from
the server, and that this method is defined like this:


```
// The greeting service definition.
service Greeter {
  // Sends a greeting
  rpc SayHello (HelloRequest) returns (HelloReply) {}
}

// The request message containing the user's name.
message HelloRequest {
  string name = 1;
}

// The response message containing the greetings
message HelloReply {
  string message = 1;
}
```

Let's update this so that the `Greeter` service has two methods. Edit
`examples/protos/helloworld.proto` and update it with a new `SayHelloAgain`
method, with the same request and response types:

```
// The greeting service definition.
service Greeter {
  // Sends a greeting
  rpc SayHello (HelloRequest) returns (HelloReply) {}
  // Sends another greeting
  rpc SayHelloAgain (HelloRequest) returns (HelloReply) {}
}

// The request message containing the user's name.
message HelloRequest {
  string name = 1;
}

// The response message containing the greetings
message HelloReply {
  string message = 1;
}
```

(Don't forget to save the file!)

## Generate gRPC code

Next we need to update the gRPC code used by our application to use the new
service definition. From the `grpc` root directory:

```sh
$ protoc --proto_path=examples/protos \
  --php_out=examples/php \
  --grpc_out=examples/php \
  --plugin=protoc-gen-grpc=bins/opt/grpc_php_plugin \
  ./examples/protos/helloworld.proto
```

or running the helper script under the `grpc/example/php` directory if you build
grpc-php-plugin by source:

```sh
$ ./greeter_proto_gen.sh
```

This regenerates the protobuf files, which contain our generated client classes,
as well as classes for populating, serializing, and retrieving our request and
response types.

## Update and run the application

We now have new generated client code, but we still need to implement and call
the new method in the human-written parts of our example application.

### Update the server

In the same directory, open `greeter_server.js`. Implement the new method like
this:

```js
function sayHello(call, callback) {
  callback(null, {message: 'Hello ' + call.request.name});
}

function sayHelloAgain(call, callback) {
  callback(null, {message: 'Hello again, ' + call.request.name});
}

function main() {
  var server = new grpc.Server();
  server.addProtoService(hello_proto.Greeter.service,
                         {sayHello: sayHello, sayHelloAgain: sayHelloAgain});
  server.bind('0.0.0.0:50051', grpc.ServerCredentials.createInsecure());
  server.start();
}
...
```

### Update the client

In the same directory, open `greeter_client.php`. Call the new method like this:

```php
    $request = new Helloworld\HelloRequest();
    $request->setName($name);
    list($reply, $status) = $client->SayHello($request)->wait();
    $message = $reply->getMessage();
    list($reply, $status) = $client->SayHelloAgain($request)->wait();
    $message = $reply->getMessage();
```

### Run!

Just like we did before, from the `examples/node/dynamic_codegen` directory:

1. Run the server

   ```sh
   $ node greeter_server.js
   ```

In another terminal, from the `examples/php` directory:

2. Run the client

   ```sh
   $ ./run_greeter_client.sh
   ```

## What's next

- Read a full explanation of how gRPC works in [What is gRPC?](../guides/)
  and [gRPC Concepts](../guides/concepts.html)
- Work through a more detailed tutorial in [gRPC Basics: PHP][]
- Explore the gRPC PHP core API in its [reference
  documentation](/grpc/php/namespace-Grpc.html)

[helloworld.proto]:../protos/helloworld.proto
[gRPC Basics: PHP]:../tutorials/basic/php.html
