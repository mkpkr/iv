When a promise rejects, the control jumps to the closest rejection handler.
 
fetch('/article/promise-chaining/user.json')  //could reject in any executing code / handler
  .then(response => response.json())
  .then(user => fetch(`https://api.github.com/users/${user.name}`))
  .then(response => response.json())
  .then(githubUser => new Promise((resolve, reject) => {
    let img = document.createElement('img');
    img.src = githubUser.avatar_url;
    img.className = "promise-avatar-example";
    document.body.append(img);
 
    setTimeout(() => {
      img.remove();
      resolve(githubUser);
    }, 3000);
  }))
  .catch(error => alert(error.message));
  
  
Implicit try…catch
 
The code of a promise executor and promise handlers has an "invisible try..catch" around it. If an exception happens, it gets caught and treated as a rejection.
 
For instance, this code:
 
new Promise((resolve, reject) => {
  throw new Error("Whoops!");
}).catch(alert); // Error: Whoops!


…Works exactly the same as this:
 
new Promise((resolve, reject) => {
  reject(new Error("Whoops!"));
}).catch(alert); // Error: Whoops!
 
This happens not only in the executor function, but in its handlers as well. If we throw inside a then handler, that means a rejected promise, so the control jumps to the nearest error handler.
 
 
Rethrowing
 
If we throw inside catch, then the control goes to the next closest error handler. And if we handle the error and finish normally, then it continues to the next closest successful then handler.


Unhandled Rejections - Global Catch
 
In case of an error, the promise becomes rejected, and the execution should jump to the closest rejection handler. But there is none. So the error gets “stuck”. There’s no code to handle it.
 
The JavaScript engine tracks such rejections and generates a global error in that case. You can see it in the console.
 
In the browser we can catch such errors using the event unhandledrejection:
 
window.addEventListener('unhandledrejection', function(event) {
  // the event object has two special properties:
  alert(event.promise); // [object Promise] - the promise that generated the error
  alert(event.reason); // Error: Whoops! - the unhandled error object
});
 
The event is the part of the HTML standard.
 
Usually such errors are unrecoverable, so our best way out is to inform the user about the problem and probably report the incident to the server.
 
In non-browser environments like Node.js there are other ways to track unhandled errors.
