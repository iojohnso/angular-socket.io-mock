angular-socket.io-mock
======================

Mock Socket.io bindings for AngularJS, useful for testing as a drop in replacement for
https://github.com/btford/angular-socket-io which allows testing against a fake server.

## Usage

### Standard usage

* Make sure AngularJS is loaded prior to inclusion.

karma.conf.js
```js
module.exports = function(config) {
  config.set({
    files: [
      {pattern: "bower_components/angular-socket.io-mock.js", included: false}
    ]
  })
}
```

## With RequireJS

In all our projects we utilize RequireJS to use this test library with RequireJS is very simple. Just replace the entry
that points to the standard angular-socket-io with this library. Below is an example.

main.js
```js
require.config({
  baseUrl: "/base/js",
  shim: {
    "socketio": {exports: "io"},
    "angular": {exports: "angular"},
    "ng-socket-io": ["angular","socketio"],
    "ng-mocks": ["angular"]
  },
  paths: {
    "angular": "/base/bower_components/angular/angular",
    "socketio": "/base/bower_components/socket.io-client/dist/socket.io",
    "ng-socket-io": "/base/bower_components/angular-socket.io-mock/angular-socket.io-mock",
    "ng-mocks": "/base/bower_components/angular-mocks/angular-mocks"
  },
  callback: window.__karma__.start
})
```

Your bower.json should look something like this.

bower.json
```json
{
  "name": "my-app",
  "version": "0.0.1",
  "dependencies": {
    "angular": "latest",
    "angular-socket-io": "latest"
  },
  "devDependencies": {
    "socket.io-client": "latest",
    "angular-mocks": "latest",
    "angular-socket.io-mock": "latest"
  }
}
```

### Example Test

This example uses Mocha Test as the testing framework and ChaiJS for the assertion library. It also uses RequireJS to
load the environment.

test.js
```js
define(["chai","angular","angular-mocks","angular-socket-io"],function(chai){
  describe("Test Suite",function(){
    beforeEach(module("app"))
    it("should be using the mock ng-socket.io",inject(function($rootScope,$controller){
      var expect = chai.expect
        , scope = $rootScope.$new()
        , ctrl = $controller("MyController",{$scope: scope})
      expect(scope.save).to.be.a("function")
      expect(scope.items).to.be.a("array")
      expect(scope.items.length).to.equal(3)
    }))
  })
})
```

MyController.js
```js
define(["angular","ng-socket-io"],function(ng){
  var app = ng.module("app",["btford.socket-io"])
  app.controller("MyController",["$scope","socket",function($scope,socket){
    //load items now
    $scope.items = []
    socket.emit("loadItems",{},function(res){
      $scope.items = res.res
    })
    //setup functions
    $scope.save = function(){}
  }])
})
```

### Todo

* Usage with sinon.js for spies
* Mocking responses

*Note* Please see https://github.com/btford/angular-socket-io for library usage.