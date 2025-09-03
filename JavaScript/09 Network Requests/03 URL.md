The built-in URL class provides a convenient interface for creating and parsing URLs.

Generally, the URL object can be passed to any method instead of a string, as most methods will perform the string conversion, that turns a URL object into a string with full URL.

There are no networking methods that require exactly a URL object, strings are good enough. So technically we don’t have to use URL. 

But sometimes it can be really helpful for example building urls with search parameters, encoding spaces and non-ascii characters.

Note we can encode strings without using URL object, there are some methods and differences described here: https://javascript.info/url#encoding-strings

Creating a URL

new URL(url, [base])

	• url – the full URL or only path (if base is set, see below),
	• base – an optional base URL: if set and url argument has only path, then the URL is generated relative to base.


let url1 = new URL('https://javascript.info/profile/admin');
let url2 = new URL('/profile/admin', 'https://javascript.info');
 
alert(url1); // https://javascript.info/profile/admin
alert(url2); // https://javascript.info/profile/admin


The URL object immediately allows us to access its components, so it’s a nice way to parse the url, e.g.:
 
let url = new URL('https://javascript.info/url');
 
alert(url.protocol); // https:
alert(url.host);     // javascript.info
alert(url.pathname); // /url

![[Pasted image 20250903155656.png]]
searchParams “?…”

There’s a URL property for adding search params (encoded if they contain spaces, non-latin letters): 

url.searchParams, an object of type URLSearchParams.
 
It provides convenient methods for search parameters:
 
	• append(name, value) – add the parameter by name,
	• delete(name) – remove the parameter by name,
	• get(name) – get the parameter by name,
	• getAll(name) – get all parameters with the same name (that’s possible, e.g. ?user=John&user=Pete),
	• has(name) – check for the existence of the parameter by name,
	• set(name, value) – set/replace the parameter,
	• sort() – sort parameters by name, rarely needed,
	• …and it’s also iterable, similar to Map.

let url = new URL('https://google.com/search');

// parameters are automatically encoded
url.searchParams.set('q', 'test me!');
alert(url); // https://google.com/search?q=test+me%21
url.searchParams.set('tbs', 'qdr:y');
alert(url); // https://google.com/search?q=test+me%21&tbs=qdr%3Ay

// iterate over search parameters (decoded)
for(let [name, value] of url.searchParams) {
  alert(`${name}=${value}`); // q=test me!, then tbs=qdr:y
}
