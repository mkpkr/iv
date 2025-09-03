As scripts became more and more complex, so the community invented a variety of ways to organize code into modules, special libraries to load modules on demand.
 
To name some (for historical reasons):
 
	• AMD – one of the most ancient module systems, initially implemented by the library require.js.
	• CommonJS – the module system created for Node.js server.
	• UMD – one more module system, suggested as a universal one, compatible with AMD and CommonJS.

Now these all slowly became a part of history, but we still can find them in old scripts.
 
The language-level module system appeared in the standard in 2015, gradually evolved since then, and is now supported by all major browsers and in Node.js. So we’ll study the modern JavaScript modules from now on.
 
What is a module?

A module is just a file. One script is one module. As simple as that.
 
Modules can load each other and use special directives export and import to interchange functionality, call functions of one module from another one:
 
// 📁 sayHi.js
export function sayHi(user) {
  alert(`Hello, ${user}!`);
}

// 📁 main.js
import {sayHi} from './sayHi.js';
 
alert(sayHi); // function...
sayHi('John'); // Hello, John!

As modules support special keywords and features, we must tell the browser that a script should be treated as a module, by using the attribute <script type="module">.
 
<!doctype html>
<script type="module">
  import {sayHi} from './say.js';
 
  document.body.innerHTML = sayHi('John');
</script>

Modules work only via HTTP(s), not locally

If you try to open a web-page locally, via file:// protocol, you’ll find that import/export directives don’t work. Use a local web-server, such as static-server or use the “live server” capability of your editor, such as VS Code Live Server Extension to test modules.

# Import/Export

See [https://javascript.info/import-export](https://javascript.info/import-export) for details on the syntax varieties.
