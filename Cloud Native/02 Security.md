# Challenge of Minimal Change

There has been a belief that if enterprises deliver software with velocity, the trade-off is they must reduce their security posture and increase risk. Therefore, traditionally, many enterprises have relied on a concept of minimal change to mitigate risk and reduce velocity. Security teams establish strict and restrictive policies in an attempt to minimize the injection of new vulnerabilities.

This is evident by ticketing systems to make basic configuration changes to middleware and databases, long-lived transport layer security (TLS) credentials, static firewall rules, and numerous security policies to which applications must adhere.

Minimal change becomes compounded by complexity of the environment. Because machines are difficult to patch and maintain, environmental complexity introduces a significant lag between the time a vulnerability is discovered and the time a machine is patched, be it months, or worse, even years in some production enterprise environments.

# The Three Rs of Enterprise Security

These combined risk factors provide a perfect ecosystem in which APTs can flourish, and minimal change creates an environment in which all three factors are likely to occur.

Platforms like Cloud Foundry inverts the traditional enterprise security model by focusing on the three Rs of enterprise security: rotate, repave, repair.

[Cloud Native Security: Rotate, Repair, Repave](https://www.infoq.com/presentations/cloud-native-security/)

The three pillars for malware to succeed:

![[Pasted image 20250903162738.png]]
Mitigating these:

- Repair: Repair vulnerable software as soon as updates are available. Do this for all software updates, don't triage and pick which are more urgent and leave others on a backlog.

- Do not give the malware author time
- Cloud Foundry pushes patches as soon as they are available

- Repave: Repave servers and applications from a known good state. Do this often.

- This avoids configuration drift

- BOSH is always running on a feedback loop, validating the state of real-world infrastructure with the expected configuration. If there is drift, BOSH eliminates that bad apple and promptly replaces it with one that meets the exact specification of the configuration.

- This also removes any malware frequently – redcing malware author's time of attack

- BOSH recreate can recreate VMs on an interval

- Rotate: Rotate user credentials frequently, so they are only useful for short periods of time.

- e.g. OAuth2 issues bearer tokens with limited TTL
- Again this minimizing the attack vector of time.

# Other Notes from "Cloud Foundry – The Definitive Guide"

- How much effort is required to automatically establish and apply network traffic rules to isolate components?
- What policies should be applied to automatically limit resources in order to defend against denial-of-service (DoS) attacks?
- How do you implement role-based access controls (RBAC) with in-built auditing of system access and actions?
- How do you know which components are potentially affected by a specific vulnerability and require patching?
- How do you safely patch the underlying OS without incurring application downtime?