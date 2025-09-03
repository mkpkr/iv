let now = new Date(); //current date/time

Date.now() //current time in millis

Date Constructors
 
	• new Date(milliseconds)

let Jan01_1970 = new Date(0);

	• new Date(datestring)
		○ This algorithm is the same as Date.parse() (see below)
		○ assumed to be midnight GMT and is adjusted according to the timezone the code is run in
		○ So the following could be 
			▪ Thu Jan 26 2017 11:00:00 GMT+1100 (Australian Eastern Daylight Time)
			▪ or
			▪  Wed Jan 25 2017 16:00:00 GMT-0800 (Pacific Standard Time)
		
let date = new Date("2017-01-26");
 
	• new Date(year, month, date, hours, minutes, seconds, ms)
		○ Create the date with the given components in the local time zone. 
		○ Only the first two arguments are obligatory.
		○ The year should have 4 digits. For compatibility, 2 digits are also accepted and considered 19xx, e.g. 98 is the same as 1998 here, but always using 4 digits is strongly encouraged.
		○ The month count starts with 0 (Jan), up to 11 (Dec).
		○ The date parameter is day of month, if absent then 1 is assumed.
		○ If hours/minutes/seconds/ms is absent, they are assumed to be equal 0.
 
new Date(2011, 0, 1, 0, 0, 0, 0); // 1 Jan 2011, 00:00:00
new Date(2011, 0, 1); // the same, hours etc are 0 by default
new Date(2011, 0, 1, 2, 3, 4, 567); // 1 Jan 2011, 02:03:04.567


Date.parse(string)

Returns ms value since epoch.

	• YYYY-MM-DDTHH:mm:ss.sssZ
		○ YYYY-MM-DD – is the date: year-month-day.
		○ The character "T" is used as the delimiter.
		○ HH:mm:ss.sss – is the time: hours, minutes, seconds and milliseconds.
		○ The optional 'Z' part denotes the time zone in the format +-hh:mm. A single letter Z would mean UTC+0.
 
let ms = Date.parse('2012-01-26T13:51:50.417-07:00');


Access Date Ccomponents

All the methods return the components relative to the local time zone.

There are also their UTC-counterparts, that return day, month, year and so on for the time zone UTC+0: 
getUTCFullYear(), getUTCMonth(), getUTCDay(). Just insert the "UTC" right after "get".

	• getFullYear()
		○ Get the year (4 digits)
		○ Many JavaScript engines implement a non-standard method getYear(). This method is deprecated. It returns 2-digit year sometimes. Use getFullYear() for the year.
	• getMonth()
		○ Get the month, from 0 to 11.
	• getDate()
		○ Get the day of month, from 1 to 31, the name of the method does look a little bit strange.
	• getHours()
	• getMinutes()
	• getSeconds()
	• getMilliseconds()
	• getDay()
		○ Get the day of week, from 0 (Sunday) to 6 (Saturday). The first day is always Sunday, in some countries that’s not so, but can’t be changed.
	• getTime()
		○ Returns the timestamp for the date – a number of milliseconds passed from the January 1st of 1970 UTC+0.
	• getTimezoneOffset()
		○ Returns the difference between UTC and the local time zone, in minutes:

Set Date Components

Most of the above have setter components, and all but setTime has a UTC variant.

Autocorrection

Dates autocorrect, so 32nd Jan will be 1st Feb.

This is often used to get the date after the given period of time. For instance, let’s get the date for “70 seconds after now”:
 
let date = new Date();
date.setSeconds(date.getSeconds() + 70);
