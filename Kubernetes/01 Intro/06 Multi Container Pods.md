- Share lifecycle
- Scale together
- Share network space (refer to each other via localhost)
- Access same storage volumes - do not need to establish volume sharing or services to enable communication

# Patterns

There are different patterns for multi container pods (note all of them use same configuration of just adding another container in the list of containers in the pod definition).

- Sidecar

	- logging agent alongside a webserver to collect logs and forward them to central server

- Adapter

	- process logs to correct format before sending to central server

- Ambassador

	- application refers to databases in Dev, Test, Prod - an ambassador can take that responsibility so you just refer to the db at localhost and it proxies to the correct one