# Examples to show how to refactor callback-based code using the async npm package to use the es6 await/await syntax supported by nodejs v8+

<table>
  <tr>
    <th><a href="https://www.npmjs.com/package/async">async package</a></th>
    <th><a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function">es6 async/await</a></th>
  </tr>
    <tr valign="top">
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
  <tr valign="top">
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
  <tr valign="top">
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
  <tr valign="top">
    <td>
      <pre>
async.waterfall(
  f1: function f1(cb) {cb(null, 1, 2)},
  f2: function f2(arg1, arg2, cb) {cb(null, 3)},
  f3: function f3(arg1, cb) {cb(null, 'done')}
), (err,result) => {
  if(err) {
    console.log('error', err);
  } else {
    console.log('info', result)
  }
});
      </pre>
    </td>
    <td>
      <pre>
try {
  const f1Res = await f1()
  const f2Res = await f2(f1Res)
  const f3Res = await f3(f2Res)
  console.log('info', f3Res)
} catch(err) {
  console.log('error', err);
}
      </pre>
    </td>
  </tr>
  <tr valign="top">
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
});
      </pre>
    </td>
    <td>
      <pre>
const [res1, res2, res3] = await Promise.all([f1, f2, f3])
      </pre>
    </td>
  </tr>
  <tr valign="top">
    <td>
      <pre>
async.eachSeries(openFiles, saveFile, function(err){
});
      </pre>
    </td>
    <td>
      <pre>
try {
  for (let f of openFiles) {
    await saveFile(f)
  }
} catch(err) {
  console.log('error', err);
}
      </pre>
    </td>
  </tr>
  <tr valign="top">
    <td>
      <pre>
async.each(openFiles, saveFile, function(err){
});
console.log('done')
      </pre>
    </td>
    <td>
      <pre>
try {
  const resArr = await Promise.all(openFiles.map(async (it) => await saveFile(it))
  console.log('done')
} catch(err) {
  console.log('error', err);
}
      </pre>
    </td>
  </tr>
</table>
