If a script throws an error, we cannot see that error if the script comes from a different origin (domain/port/protocol triple)
To allow cross-origin access, the <script> tag needs to have the crossorigin attribute, plus the remote server must provide special headers.
There are three levels of cross-origin access:
	1. No crossorigin attribute – access prohibited.
	2. crossorigin="anonymous" – access allowed if the server responds with the header Access-Control-Allow-Origin with * or our origin. Browser does not send authorization information and cookies to remote server.
	3. crossorigin="use-credentials" – access allowed if the server sends back the header Access-Control-Allow-Origin with our origin and Access-Control-Allow-Credentials: true. Browser sends authorization information and cookies to remote server.

<script crossorigin="anonymous" src="https://cors.javascript.info/article/onload-onerror/crossorigin/error.js"></script>