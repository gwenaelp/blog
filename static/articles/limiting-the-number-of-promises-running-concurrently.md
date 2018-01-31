#### How-to

You can solve this problem easily using [ES6 Promise Pool](https://github.com/timdp/es6-promise-pool). Here is a basic example :

```javascript
var maxParallelRequests = 3;
var count = 0;
var total = 100;

var promiseProducer = function () {
  if (count < total) {
    count++;

    promise = getResultsAsync(`http://my-api.com/${count}`);

    return promise;
  } else {
    return null;
  }
};

var pool = new PromisePool(promiseProducer, maxParallelRequests);
```

This example will request ```http://my-api.com/1```, ```http://my-api.com/2```, and so on, until ```http://my-api.com/100```, limiting the number of parallel to 3.

It works by using a promise producer function (```promiseProducer```). This function will be called by PromisePool and should returns a Promise each time, unless it returns ```null``` which stops the Promise generation logic. PromisePool will gather all the Promises it has, and execute them limiting parallel calls.
