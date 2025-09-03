Before we get to keyboard, please note that on modern devices there are other ways to “input something”. For instance, people use speech recognition (especially on mobile devices) or copy/paste with the mouse.

So if we want to track any input into an <input> field, then keyboard events are not enough. There’s another event named input to track changes of an <input> field, by any means. And it may be a better choice for such task. We’ll cover it later in the chapter Events: change, input, cut, copy, paste. Link to OneNote section when done.

Legacy

In the past, there was a keypress event, and also keyCode, charCode, which properties of the event object.
There were so many browser incompatibilities while working with them, that developers of the specification had no way, other than deprecating all of them and creating new, modern events described next.

keydown and keyup

The keydown events happens when a key is pressed down, and then keyup – when it’s released.

event.code and event.key

The key property of the event object allows to get the character, while the code property of the event object allows to get the “physical key code”.

For instance, the same key Z can be pressed with or without Shift. That gives us two different characters: lowercase z and uppercase Z.

The event.key is exactly the character, and it will be different. But event.code is the same:

|         |               |            |
| ------- | ------------- | ---------- |
| Key     | event.key     | event.code |
| Z       | z (lowercase) | KeyZ       |
| Shift+Z | Z (uppercase) | KeyZ       |

If a user works with different languages, then switching to another language would make a totally different character instead of "Z". That will become the value of event.key, while event.code is always the same: "KeyZ".

Every key has the code that depends on its location on the keyboard. Key codes described in the UI Events code specification.
For instance:
	• Letter keys have codes "Key<letter>": "KeyA", "KeyB" etc.
	• Digit keys have codes: "Digit<number>": "Digit0", "Digit1" etc.
	• Special keys are coded by their names: "Enter", "Backspace", "Tab" etc.
There are several widespread keyboard layouts, and the specification gives key codes for each of them.
Read the alphanumeric section of the spec for more codes.
See https://javascript.info/keyboard-events#event-code-and-event-key for more info on different keyboard layouts and languages, and choosing between event.key and event.code.


Auto-repeat

If a key is being pressed for a long enough time, it starts to “auto-repeat”: the keydown triggers again and again, and then when it’s released we finally get keyup. So it’s kind of normal to have many keydown and a single keyup.

For events triggered by auto-repeat, the event object has event.repeat property set to true.







