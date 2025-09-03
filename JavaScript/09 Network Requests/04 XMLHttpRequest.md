XMLHttpRequest is a built-in browser object that allows to make HTTP requests in JavaScript.

Despite having the word “XML” in its name, it can operate on any data, not only in XML format. We can upload/download files, track progress and much more.

Right now, there’s another, more modern method [fetch](onenote:#fetch&section-id={0326c9b2-bccc-4be5-91b3-71cb75eef69f}&page-id={b2cec9e8-b385-4f92-aa86-cc388efec152}&end), that somewhat deprecates XMLHttpRequest.

In modern web-development XMLHttpRequest is used for three reasons:

1. Historical reasons: we need to support existing scripts with XMLHttpRequest.
2. We need to support old browsers, and don’t want polyfills (e.g. to keep scripts tiny).
3. We need something that fetch can’t do yet, e.g. to track upload progress.

If you need to use XMLHttpRequest, please ses: [https://javascript.info/xmlhttprequest](https://javascript.info/xmlhttprequest)