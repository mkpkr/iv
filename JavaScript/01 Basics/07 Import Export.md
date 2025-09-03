a.js

export const apiKey= "abcdefg";
export let another= "123";

b.js

import { apiKey } from "./a.js";
//or
import { apiKey, another } from "./a.js";
//or
import { apiKey, another as someOther } from "./a.js";

import all into object

import * as things from "./a.js";
console.log(things.apiKey);

note in React etc when importing modules you won't need to specify the file ending as the build process will add it for you - is this true though because he said in one lecture sometimes you do depencding on the build tool?


export default

We can also export a nameless default:

a.js

export default "abcdefg";

b.js

import whateverYouWantToCallIt from "./a.js";

We can only have one default export per file