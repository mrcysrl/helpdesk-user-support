# Help Desk / IT Support Practice Portfolio

Simulated L1 Help Desk tickets, built as self-directed practice to develop
practical troubleshooting, documentation, and Help Desk process skills.

**Context:** This is a single-technician lab environment using
[Spiceworks Cloud Help Desk](https://www.spiceworks.com/free-it-help-desk/)
as the ticketing system. All tickets are based on simulated phone-call
scenarios rather than a live employer, since the goal is to build and
demonstrate the full L1 workflow — intake, information gathering,
troubleshooting, resolution, verification, and documentation — end to end.

All users, emails, and organization names are fictional. No real company,
customer, or personal data is used anywhere in this repository.

## Workflow followed for every ticket

```
User report → Information gathering → Troubleshooting → Resolution/Escalation
→ Verification → Documentation → Waiting/Closure
```

## Tickets

| Issue | Root Cause | Category | Outcome |
|---|---|---|---|
| [Computer Running Slow](#ticket--computer-running-slow) | Windows Defender full background scan (`MsMpEng.exe`) consuming disk I/O | Software | Resolved, verified live; Waiting for overnight scan completion |
| [Connected to Wi-Fi but No Internet Access](#ticket--connected-to-wi-fi-but-no-internet-access) | Failed DHCP lease (APIPA `169.254.x.x` address) | Software | Resolved via forced DHCP renewal, verified with `ping`; Waiting for recurrence monitoring |

---

## Ticket — Computer Running Slow

**Environment:** Spiceworks Cloud Help Desk (lab/practice instance)
**Role:** L1 Help Desk Agent
**Contact:** dkim@labitsupport.example (simulated user — Dana Kim, Accounting)
**Priority:** Medium
**Category:** Software
**Status:** Waiting (pending user confirmation after overnight scan completion)

### 1. Initial Report (Phone Call)

Simulated intake call, logged as the ticket description.

> **Agent:** "IT Help Desk, this is Marc, how can I help you?"
>
> **Caller (Dana Kim, Accounting):** "Hi, yeah, this is Dana Kim over in Accounting.
> My computer has been really slow all morning — like, painfully slow. It took
> almost five minutes just to open Outlook. I haven't installed anything new or
> changed anything. It was working fine yesterday."
>
> **Agent:** "Okay, I can help with that. Can I get your email to pull up your account?"
>
> **Caller:** "Sure, it's dkim@labitsupport.example."
>
> **Agent:** "Is it just Outlook that's slow, or is everything on the computer slow?"
>
> **Caller:** "Honestly everything. Even clicking on the Start menu takes a few seconds."
>
> **Agent:** "Are you able to keep working right now, just slowly, or is it basically unusable?"
>
> **Caller:** "I can still work, it's just really frustrating and slowing me down a lot."

**Ticket logged with:**
- Summary: *Computer is running slow.*
- Description: User's report, labeled and cleaned up for the ticket record.
- Priority: **Medium** — user can still work, but productivity is significantly impacted (not a full outage, not a minor annoyance).
- Category: **Unspecified** — no root cause confirmed yet at intake; category should reflect only what's known, not assumed.

![Ticket creation form](screenshots/00%20-%20slowcomputer.png)

### 2. Information Gathering

Before touching the machine, follow-up questions were asked to narrow down the cause:

| Key Factor | Question |
|---|---|
| Timing & Progression | When exactly did it start? Has it gotten worse since then, or stayed the same? |
| Live Observation | Can you try opening the application again right now while we're on the call, and tell me what happens? |
| Physical Symptoms | Besides being slow, have you noticed anything else — increased fan noise, screen freezing, unusual heat? |
| Recent Changes | Did you plug in any new devices recently — USB drive, external monitor? Has the computer been moved or bumped? |
| Scope of Impact | Does this happen with every application, or only certain ones like Outlook? |

**User's responses:**
- Started ~9 AM, has gotten worse since (Outlook slow → now everything sluggish)
- Excel took ~10 seconds to open on retest during the call
- Fan noticeably louder than usual; no freezing
- No new devices, no physical changes to the machine
- Affects every application, including File Explorer

### 3. Troubleshooting

**Hypothesis formed from symptoms:** Fan working harder + gradual slowdown across
*all* applications (not one specific app) pointed toward a background process
consuming system resources, rather than a hardware fault (which would more
likely show physical symptoms like overheating/shutdowns rather than pure
performance decline) or a one-off app issue.

**Step 1 — Check resource usage at idle.**
Walked the user through opening Task Manager (`Ctrl+Shift+Esc`) and checking the
Performance tab with no applications open. Disk utilization was reported at
**98% while completely idle** — abnormal, and the key signal that ruled out
"normal app loading" as the explanation.

**Step 2 — Identify the responsible process.**
Had the user open the Processes tab and sort by Disk usage. Identified
`MsMpEng.exe` (Windows Defender Antivirus Service) as the top consumer,
indicating an active full background virus scan.

**Step 3 — Rule out alternate causes.**
Confirmed no recent software/hardware changes and no new peripherals — ruling out
a triggered install or device conflict as the cause.

### 4. Resolution

User had an immediate project deadline. Rather than waiting out the scan:
- Guided the user to **Windows Security → Virus & threat protection → Current threats**
- Had the user **cancel the active scan** (not disable protection — real-time
  protection remained on throughout)
- Advised the user to leave the PC powered on and connected overnight so the
  full scan could complete during off-hours, minimizing repeat disruption

This decision was documented with its business justification (deadline) so the
override of a routine security process is clearly explained for anyone
auditing the ticket later.

### 5. Verification

Did not assume the fix worked — had the user actively re-check before closing
out the diagnostic work:
- Disk utilization confirmed dropped from **98% to 2%** (Task Manager, live)
- Excel re-opened **instantly** on retest (vs. ~10 seconds before)

### 6. Ticket Documentation (Internal Note)

```
Diagnostic: Task Manager indicated that disk utilization pinned at near 100%
during idle. Had user sort Task Manager Processes by disk utilization.
Identified MsMpEng.exe (Windows Defender Antivirus Service) as the primary
resource consumer, indicating an active full background scan. No recent
hardware or software changes noted.

Resolution: Due to user facing an immediate project deadline, guided the user
to Windows Security and safely canceled the active scan. Real-time protection
remains enabled. Advised user to leave the PC on overnight to let the scan
complete outside of working hours.

Verification: Confirmed with user via Task Manager that disk utilization
dropped from 98% to 2%. User verified system responsiveness has returned to
normal (Excel opened instantly on retest).
```

Category updated from **Unspecified → Software** once root cause was confirmed
(the cause was software/OS-driven behavior, not IT-scheduled maintenance work
and not a hardware fault).

![Ticket detail with troubleshooting history](screenshots/01%20-%20slowcomputer_resolved.png)

### 7. Status & Follow-Up

- **Status:** Waiting — fix applied and verified live, but held open pending
  confirmation that the overnight scan completed without further issues.
- **Due date:** Set for follow-up check-in.
- **Escalation:** Not required — resolved within L1 scope.
- **Closure plan:** If no further report from the user by the due date, ticket
  will be closed automatically as resolved.

### Key Takeaways

- Don't jump to a category or root cause before gathering evidence — "Unspecified"
  at intake is the correct, honest choice.
- High disk usage *during* an app launch is normal; high disk usage *at idle*
  is the actual red flag.
- A live retest (having the user act while on the call) beats guesswork —
  it turns a theory into evidence.
- Never document a result as "confirmed" unless it was actually verified with
  the user, not assumed.

---

## Ticket — Connected to Wi-Fi but No Internet Access

**Environment:** Spiceworks Cloud Help Desk (lab/practice instance, single-technician setup)
**Role:** L1 Help Desk Agent
**Contact:** tmarshall@labitsupport.example (simulated user — Tom, Marketing)
**Priority:** High
**Category:** Software
**Status:** Waiting (7-day monitoring period for recurrence)

### 1. Initial Report (Phone Call)

> **Agent:** "IT Help Desk, this is Marc, how can I help you?"
>
> **Caller (Tom, Marketing):** "Hi, this is Tom from Marketing. My computer says
> I'm connected to Wi-Fi, but nothing loads — no websites, no email, nothing."
>
> **Agent:** "Okay, can I get your email to pull up your account?"
>
> **Caller:** "tmarshall@labitsupport.example."
>
> **Agent:** "Let me check a couple things first. Are any of your teammates in
> Marketing having the same issue, or are their computers connecting normally?"
>
> **Caller:** "Let me check with the guy next to me... no, he says his internet's
> fine. And I've got my phone here too — it connects fine on the Wi-Fi, I can
> load a website on it right now."
>
> **Agent:** "Good to know. Can you do your work for now on your phone or another
> device, or do you need your computer specifically right now?"
>
> **Caller:** "I mean, I could use my phone for email, but I've got spreadsheets
> I need to update for a report due this afternoon — that's not happening on my phone."

**Ticket logged with:**
- Summary: *Computer is connected to Wi-Fi but No Internet Access*
- Description: labeled user report, noting the issue is isolated to Tom's device.
- Priority: **High** — isolated to one device, but actively blocking a
  same-day deliverable. Business impact (deadline), not just symptom severity,
  drove this decision.
- Category: **Unspecified** — symptom ("no internet") can map to several
  different root causes (network, hardware, or software/client-side), so no
  category was assumed before diagnosis.

![Ticket creation form](screenshots/03%20-%20nointernetaccess.png)

### 2. Scope Check

Before any technical diagnostics, confirmed whether this was isolated or
network-wide, since that changes both priority and where to look for the cause:
- Coworker on the same network: internet working normally
- Tom's own phone on the same Wi-Fi: internet working normally

**Conclusion:** issue isolated to Tom's laptop specifically, not the office
network or Wi-Fi infrastructure.

### 3. Information Gathering

| Key Factor | Question |
|---|---|
| Timing/Onset | When did it start? Was it working before? |
| Scope | Are all sites/apps affected, or only some? |
| Recent Changes | Any changes made to the machine recently? |
| Technical State | What does `ipconfig /all` show for IP address, subnet, gateway, and DNS? |

**User's responses:**
- Started ~20 minutes ago, right after returning from a meeting; working fine that morning
- Every site/app affected (Gmail, internal CRM, Google.com — nothing loads)
- No known changes made
- `ipconfig /all` output:
  - IPv4 Address: `169.254.83.12`
  - Subnet Mask: `255.255.0.0`
  - Default Gateway: *(blank)*
  - DNS Servers: *(blank)*

### 4. Diagnosis

**Key evidence:** `169.254.x.x` is an **APIPA (Automatic Private IP Addressing)**
/ link-local address — Windows self-assigns this only when it **fails to
receive an address from DHCP**. No gateway and no DNS server were listed,
consistent with a failed DHCP handshake (Wi-Fi *link* was fine; IP assignment
was not).

**Isolating the fault:** since other devices on the same Wi-Fi (coworker's PC,
Tom's own phone) received valid IPs and working internet, the network's DHCP
service was confirmed functioning — ruling out a network-wide outage and
pointing to Tom's laptop specifically.

### 5. Troubleshooting & Resolution

Rather than blindly restarting the machine or toggling Wi-Fi (which could
mask the cause and cost time against Tom's deadline), forced a clean DHCP
renegotiation directly:

```
ipconfig /release
ipconfig /renew
```

This explicitly drops the failed APIPA lease and requests a new one from DHCP,
rather than hoping a reconnect triggers it.

### 6. Verification

Re-ran `ipconfig /all` and an internet reachability test rather than assuming
success:

- IPv4 Address: `192.168.1.47`
- Subnet Mask: `255.255.255.0`
- Default Gateway: `192.168.1.1`
- DNS Servers: `192.168.1.1`
- `ping 8.8.8.8`:
  ```
  Reply from 8.8.8.8: bytes=32 time=14ms TTL=118
  Reply from 8.8.8.8: bytes=32 time=12ms TTL=118
  Reply from 8.8.8.8: bytes=32 time=13ms TTL=118
  Reply from 8.8.8.8: bytes=32 time=15ms TTL=118
  ```
- Outlook, Gmail, and the internal CRM all loaded successfully on retest.

### 7. Ticket Documentation (Internal Note)

```
Diagnostic: "ipconfig /all" showed a "169.254.x.x" APIPA address with no
default gateway or DNS server, indicating DHCP lease failure. Other devices
on the same Wi-Fi successfully received valid IP addresses, confirming
DHCP/network connectivity was functioning and isolating the issue to Tom's
laptop.

Resolution: Ran ipconfig /release followed by ipconfig /renew to obtain a
new DHCP lease.

Verification: After renewing the lease, "ipconfig /all" showed "192.168.1.47"
with gateway "192.168.1.1" and DNS "192.168.1.1". "ping 8.8.8.8" was
successful, confirming Internet reachability. Outlook also refreshed
successfully.
```

**Category** set to **Software** — the fault was in the client's network
stack/lease state, not the network infrastructure (DHCP was proven functional
for other devices) and not a hardware fault.

![Ticket detail with troubleshooting history](screenshots/04%20-%20nointernetaccess_resolved.png)

### 8. Status & Follow-Up

- **Status:** Waiting — connectivity was restored and verified, but the
  underlying cause of the initial lease failure was not identified. This was
  a corrective action (forced renewal), not a confirmed root-cause fix, so a
  short monitoring window is kept open in case of recurrence.
- **Due date:** 7 days out.
- **Escalation:** Not required — resolved within L1 scope.
- **Closure plan:** If no recurrence is reported within the week, ticket
  closes as resolved.

### Key Takeaways

- A symptom like "no internet" can map to Network, Hardware, or Software
  causes — never assume the category before diagnosing.
- `169.254.x.x` is a strong, specific signal of DHCP failure — recognizing it
  instantly narrows the investigation.
- Test other devices on the same network before touching anything — it's the
  fastest way to isolate "this device" vs. "this network."
- Prefer a deliberate diagnostic command (`ipconfig /release` + `/renew`) over
  a blind restart when time is limited — you get evidence either way, not just a guess.
- A temporary fix (forcing a new lease) is not the same as a root-cause fix —
  document that distinction and keep the ticket open for monitoring rather
  than closing prematurely.

## Skills demonstrated across these tickets

- Ticket intake and structured documentation (Summary, Description, Priority, Category)
- Distinguishing symptoms from confirmed root cause before categorizing
- Priority setting based on business impact, not just technical severity
- Structured information gathering (separating diagnostic questions from fix-attempts)
- Step-by-step remote troubleshooting guidance for non-technical users
- Evidence-based verification (never marking "resolved" without confirming with the user)
- Correct use of ticket statuses (Open / Waiting / Closed) tied to actual
  confidence in the fix, not just "problem seems gone"
