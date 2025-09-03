# Access Control

- Authentication is enforced at every entry point into the application (e.g. every URL for a web application) and is performed before any other processing.
- Authorization is enforced at every entry point into the application.

	- For all user actions, the application verifies that the user is authorized to perform the requested action on the requested object.
	- An object might be coarse grained (e.g. screen level) or fine grained (e.g. field level).

- On web pages that change state, a control to prevent Cross Site Request Forgery (CSRF) is implemented.
- The client-side code comments do not contain any sensitive data (e.g., code flow, business logic, system information, username/passwords).
- Extraneous test and debug code has been removed before move-to-production.
- Ensure error messages are generic and do not leak non-public information.

# Input Validation

- User-supplied data is properly encoded or escaped before it is interpreted or parsed by another application, middleware component, or system (e.g., Browser (HTML, JavaScript, etc.), OS command line, LDAP, etc.)
- Bind variables are used in database queries. (e.g. Prepared Statements for Java, PLANS for db2, etc.)
- Inputs with well a defined set of expected values (e.g., dropdowns, radio buttons, checkboxes, text fields with an expected range of values, etc.) are validated using a white list on the server side.
- For technologies susceptible to buffer overflow, ensure bounds checking is done for all inputs and appropriate action is taken where necessary to prevent overflow (e.g. truncate data, reject data, etc.).
- For web applications, when user input is used to redirect or forward the user to other URLs, the input is validated to contain only an allowed destination.

# Data Protection

- Ensure no credentials are hardcoded or present in the code repository.
- Cookies do not store any sensitive data.
- The ‘secure’ flag is set on session cookies that represent an authenticated user.
- Data is encrypted in transit when required by and according to InfoSec Standards.
- Sensitive data is stored using approved algorithms techniques and methodology.
- For web applications, sensitive data is only sent via the POST method to ensure that it is not logged anywhere.
- For web applications, caching and auto-complete are turned off on pages handling sensitive data.
- After processing a specific sensitive data element, it is overwritten and set to null, effectively removing it from memory.
- Sensitive data is masked and/or obfuscated as required by InfoSec Standards.
- Sensitive Production data must not be used for testing or development.

# Malicious Code

- The application code (or database triggers/procedures/functions) does not contain any code that does not support a business, software, or design requirement, including backdoors, logic bombs, data leaks, and all other forms of malicious code.

	- Backdoors – Malicious code that enables a user to bypass normal authentication. To locate backdoors, for example, search and review for:
		- Hardcoded user IDs or employee IDs
		- Open ports
	
	- Logic Bombs – Malicious code that is intentionally inserted to execute when certain conditions (such as pre-defined time, a certain command is executed, or a certain parameter value is received, etc.) are met. To locate logic bombs, for example, search and review for:
		- Hardcoded dates
		- Objects such as Date, Time, Calendar, etc.
		- Counters
	
	- Data Leaks – Malicious code that intentionally transmitssensitive or Internal data to an unauthorized system or user. To locate data leaks, for example, search and review for:
		- Open sockets
		- URL connections
		- Proxy addresses or ports
		- File access
		- Code sending emails