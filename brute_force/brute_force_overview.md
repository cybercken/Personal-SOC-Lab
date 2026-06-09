## Detection Logic

The detection logic for brute force attacks consists of the following:
- **Windows Security Event ID 4625**: Failed authentication attempts.
- **Threshold of 5 failed authentication attempts within 60 seconds**: Obviously, static thresholds have inherent limitations. While it is possible for a legitimate user to mistype a password several times, it is relatively uncommon for a human user to generate five failed logon attempts within such a short period. As a result, this threshold serves as a reasonable heuristic for identifying suspicious authentication behavior in a small lab environment. An attacker who is aware of the threshold could potentially evade detection by slowing the rate of authentication attempts. In an enterprise environment, the ideal scenario would be to establish a baseline of normal authentication behavior for users, systems, or departments and alert on deviations from that baseline. Behavioral baselining is significantly more difficult for an attacker to evade than a simple static threshold.
- **Same destination IP address**: For this lab though, I tailored this to the target domain name, due to quirks with how Wazuh logs destionation IP's in this context. The idea is to ensure that all failed attempts are against the same user, so this workaround suffices as a placeholder.
- **Logon types (2, 3, 10)**: These provided additional context to the event, which could be vital in an enterprise environment. For example, if there's a company that's 100% onsite, and begins to detect an unusually high volume of failed type 10 logons (RDP), that'd be extremely suspicious.
- **Successful logon after multiple failed attempts**: The correlation of multiple failed logins and a successful logon (especially type 3 or 10) should raise a red flag.

## Tests & Automated Response
Used CrackMapExec from a separate Kali Linux machine to simulate brute-force login attempts against the Windows endpoint.

Utilized Wazuh's built-in IP blocker to temporarily block the source IP in the case of a successful logon after multiple failed attempts.

## Triage Process

If a brute-force alert is generated, the first step would be to build context around the event.

The investigation begins by reviewing authentication activity surrounding the alert:

* How many failed logons occurred?
* Over what time period did they occur?
* Which accounts were targeted?
* What logon types were involved?

Next, the source IP address should be examined. If it's an unknown IP, external threat intelligence sources can be consulted to determine whether it has been associated with malicious activity. Historical log data should also be reviewed to determine whether the source IP has appeared previously and whether it has been associated with other suspicious events. 

The pattern of authentication attempts is also important. Repeated failures from a single source is one thing, while failures originating from multiple IP addresses could indicate a distributed password-spraying campaign, or an attacker deliberatly attempting to evade defenses by rotating IP's.

At this stage, the activity may warrant escalation for further investigation. Depending on the organization's response procedures, a temporary IP block may be implemented while the event is reviewed.

If a successful authentication occurs following repeated failed attempts, the incident becomes significantly more severe. A successful authentication following a pattern of repeated failures may indicate that the attacker has obtained valid credentials and achieved initial access to the environment.
In that situation, immediate containment actions may include:

* Terminating active sessions
* Blocking the source IP address
* Isolating the affected endpoint
* Disabling or resetting the affected account
* Escalating the incident for further investigation

