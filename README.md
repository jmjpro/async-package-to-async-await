# Examples to show how to refactor callback-based code using the async npm package to use the es6 await/await syntax supported by nodejs v8+

<table>
  <tr><th>Async package</th><th>es6 async/await</th></tr>
  <tr>
    <td>
      <pre>
async.auto({
  f1: (callback) => {...},
  f2: 'f1', (res, callback) => {...},
  f3: 'f2', (res, callback) => {...},
}), (err, results) => {
  callback(err, { k1: results.f1, k2: results.f2 });
});
      </pre>
    </td>
    <td>
      <pre>
const results = {}
results.f1 = await f1(...)
results.f2 = await f2(...)
results.f3 = await f3(...)
return results
      </pre>
    </td>
  </tr>
  <tr>
    <td>
      <pre>
var isCompleted = 0;
async.whilst(
    function() {
      !isCompleted;
    },
    function(callback) {
        callbackFn((err, res) => {
          if (res.isCompleted) {
            isCompleted = res.isCompleted;
          }
          else {
            setTimeout(callback, 5000);
          }
        })
    },
    function (err) {
        callback(err)
    }
);
      </pre>
    </td>
    <td>
      <pre>
const isCompleted = false
while( !isCompleted ) {
  const res = await util.promisify(callbackFn)
  if (res.isCompleted) {
    isCompleted = res.isCompleted
  } else {
    await util.promisify(setTimeout).call(5000)
  }
}
      </pre>
    </td>
  </tr>
  <tr>
    <td>
      <pre>
async.series(
    f1: function f1(cb) {...},
    f2: function f2(cb) {...},
    f3: function f3(cb) {...}
), err => {
  if(err) {
    console.log('error', err);
  } else {
    console.log('info', 'success')
  }
}
);
      </pre>
    </td>
    <td>
      <pre>
try {
  await f1()
  await f2()
  await f3()
  console.log('info', 'success')
} catch(err) {
  console.log('error', err);
}
      </pre>
    </td>
  </tr>
  <tr>
    <td>
      <pre>
async.parallel(
  f1: function f1(cb) {...},
  f2: function f2(cb) {...},
  f3: function f3(cb) {...}
), err => {
  if(err) {
    console.log('error', err);
  } else {
    console.log('info', 'success')
  }
}
);
      </pre>
    </td>
    <td>
      <pre>
const res = await Promise.all([f1, f2, f3])
      </pre>
    </td>
  </tr>
</table>
