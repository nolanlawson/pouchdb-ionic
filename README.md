pouchdb-ionic
========

PouchDB runs great in the Ionic Framework. Here's how to get started.

_Note: these instructions were written for Ionic 1. For Ionic 2, you might try [this tutorial](https://gonehybrid.com/how-to-use-pouchdb-sqlite-for-local-storage-in-ionic-2/)._

## Sample app

* [PouchDB Ionic "hello world" app](https://github.com/nolanlawson/pouchdb-ionic-hello-world) (with screenshots!)

## Installing PouchDB

Install with Bower:

    bower install --save pouchdb
    
Then add `pouchdb.js` to `index.html` (after `cordova.js`):

```html
<script src="cordova.js"></script>

<script src="lib/pouchdb/dist/pouchdb.min.js"></script>
```

## Using PouchDB

PouchDB is already ready to be used within your controllers and services. Just call:

```js
var db = new PouchDB('my_database');
```

That's it!

### Service pattern

Since Angular services are singletons, and since we only need a single instance of a PouchDB database, a good practice is to create a service called `PouchService` that handles your PouchDB:

```js

function PouchService() {
  this.db = new PouchDB('my_database');
}

angular.module('myModule').service('pouchService', PouchService)
```

Now you can access your database from `pouchService.db`.

### $q.when()

PouchDB promises are compliant with the [A+ spec](https://promisesaplus.com/). So if you want to turn them into Angular `$q` promises, you just wrap them in `$q.when()`:

```js
$q.when(pouchdb.post({})).then(function () {
  /* ... */
}).catch(function () {
  /* ... */
});
```

### $rootScope.$apply()

Since PouchDB is asynchronous, you will often need to call `$scope.$apply()` or `$rootScope.$apply()` in order for the changes to be reflected in your UI. For instance, you might want to do:

```js
function PouchService($scope, $rootScope) {
  $scope.listOfStuff = [];

  $scope.db = new PouchDB('my_database');
  
  $scope.db.allDocs({include_docs: true}).then(function (result) {
    var docs = result.rows.map(function (row) { return row.doc; });
    
    $scope.listOfStuff = docs;
    
    $rootScope.$apply(); // <--- better call this!
    
  }).catch(function (error) {
    // got an error somehow
  });
}
```

If you get tired of calling `$rootScope.$apply()` all the time, you may want to look into a wrapper like [angular-pouchdb](https://github.com/angular-pouchdb/angular-pouchdb). But it's not required.

### NPM Browser

There is an open-source Angular project called [NPM Browser](http://www.npm-browser.com) that you can check out for a more detailed example of using PouchDB with Angular.

For instance, here is the [pouch-service.js](https://github.com/pouchdb/npm-browser/blob/master/scripts/services/pouch-service.js) in that project.

## See also

The [pouchdb-phonegap-cordova](https://github.com/nolanlawson/pouchdb-phonegap-cordova) guide, which has more general information about using PouchDB with Cordova/PhoneGap.
