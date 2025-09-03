There’s an umbrella term “AJAX” (abbreviated Asynchronous JavaScript And XML) for network requests from JavaScript. We don’t have to use XML though: the term comes from old times, that’s why that word is there. You may have heard that term already.

There are multiple ways to send a network request and get information from the server.

The fetch() method is modern and versatile. It’s not supported by old browsers (can be polyfilled), but very well supported among the modern ones.

Request

let promise = fetch(url, [options])

	• url – the URL to access.
	• options – optional parameters: method, headers etc.


Without options, this is a simple GET request, downloading the contents of the url.


Headers

let response = fetch(protectedUrl, {
  headers: {
    Authentication: 'secret'
  }
});


POST

To make a POST request, or a request with another method, we need to use fetch options:
 
	• method – HTTP-method, e.g. POST,
	• body – the request body, one of:
		○ a string (e.g. JSON-encoded),
		○ FormData object, to submit the data as multipart/form-data,
		○ Blob/BufferSource to send binary data,
		○ URLSearchParams, to submit the data in x-www-form-urlencoded encoding, rarely used.

let response = await fetch('/article/fetch/post/user', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json;charset=utf-8'
  },
  body: JSON.stringify(user)
});

Please note, if the request body is a string, then Content-Type header is set to text/plain;charset=UTF-8 by default.
 
But, as we’re going to send JSON, we use headers option to send application/json instead, the correct Content-Type for JSON-encoded data.


Response

The browser starts the request right away and returns a promise that the calling code should use to get the result.

Getting a response is usually a two-stage process.


Response Headers

Note we will use the Async/await format here – see the section below "Without await" for doing this in a pure promise way.

First, the promise, returned by fetch, resolves with an object of the built-in Response class as soon as the server responds with headers.

At this stage we can check HTTP status, to see whether it is successful or not, check headers, but don’t have the body yet.

The promise rejects if the fetch was unable to make HTTP-request, e.g. network problems, or there’s no such site. Abnormal HTTP-statuses, such as 404 or 500 do not cause an error.

We can see HTTP-status in response properties:
	• status – HTTP status code, e.g. 200.
	• ok – boolean, true if the HTTP status code is 200-299.

The response headers are available in a Map-like headers object in response.headers. It’s not exactly a Map, but it has similar methods to get individual headers by name or iterate over them.

let response = await fetch(url);
 
if (response.ok) { // if HTTP-status is 200-299

  // get one header
  alert(response.headers.get('Content-Type')); // application/json; charset=utf-8
 
  // iterate over all headers
  for (let [key, value] of response.headers) {
    alert(`${key} = ${value}`);
  }

  // get the response body (the method explained below)
  let json = await response.json();
} else {
  alert("HTTP-Error: " + response.status);
}


Response Body

Second, to get the response body, we need to use an additional method call.

Response provides multiple promise-based methods to access the body in various formats:

	• response.text() – read the response and return as text,
	• response.json() – parse the response as JSON,
	• response.formData() – return the response as FormData object (explained in the next chapter),
	• response.blob() – return the response as Blob (binary data with type),
	• response.arrayBuffer() – return the response as ArrayBuffer (low-level representation of binary data),
	• additionally, response.body is a ReadableStream object, it allows you to read the body chunk-by-chunk, we’ll see an example later.


For instance, let’s get a JSON-object with latest commits from GitHub:

let url = 'https://api.github.com/repos/javascript-tutorial/en.javascript.info/commits';

let response = await fetch(url);



let commits = await response.json(); // read response body and parse as JSON


alert(commits[0].author.login);


Without await

The same without await, using pure promises syntax:
 
fetch('https://api.github.com/repos/javascript-tutorial/en.javascript.info/commits')
  .then(response => response.json())
  .then(commits => alert(commits[0].author.login));


Response Text

To get the response text, await response.text() instead of .json():
 
let response = await fetch('https://api.github.com/repos/javascript-tutorial/en.javascript.info/commits');
let text = await response.text(); // read response body as text
alert(text.slice(0, 80) + '...');


