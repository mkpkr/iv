Let’s say we want many promises to execute in parallel and wait until all of them are ready.
 
Promise.all takes an iterable (usually, an array of promises) and returns a new promise.
 
The new promise resolves when all listed promises are resolved, and the array of their results becomes its result.
 
let promise = Promise.all(iterable);
 
Promise.all([
  new Promise(resolve => setTimeout(() => resolve(1), 3000)), // 1
  new Promise(resolve => setTimeout(() => resolve(2), 2000)), // 2
  new Promise(resolve => setTimeout(() => resolve(3), 1000))  // 3
]).then(alert); // 1,2,3 when promises are ready: each promise contributes an array member
 
Please note that the order of the resulting array members is the same as in its source promises. Even though the first promise takes the longest time to resolve, it’s still first in the array of results.
 
A common trick is to map an array of job data into an array of promises, and then wrap that into Promise.all
 
let urls = [
  'https://api.github.com/users/iliakan',
  'https://api.github.com/users/remy',
  'https://api.github.com/users/jeresig'
];
 
// map every url to the promise of the fetch
let requests = urls.map(url => fetch(url));
 
// Promise.all waits until all jobs are resolved
Promise.all(requests)
  .then(responses => responses.forEach(
    response => alert(`${response.url}: ${response.status}`)
  ));


If any of the promises is rejected, the promise returned by Promise.all immediately rejects with that error. 
Other promises will be executed but there results are ignored.

Promise.all allows non-promise “regular” values in iterable

Normally, Promise.all accepts an iterable (in most cases an array) of promises. 

But if any of those objects is not a promise, it’s passed to the resulting array “as is”.
