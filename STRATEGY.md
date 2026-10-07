# Strategy (spoilers)

> This gives away the quiet route. Play a few runs first; the fun is discovering why the loud route gets you caught.

## The credential ladder

The DC requires **domainAdmin** creds to touch. You only get those by dumping **ADMIN-WS-07** (it has a Domain Admin session). To reach ADMIN-WS-07 you need **serverAdmin**. To get onto the server segment at all you need **user** creds. So the whole game is climbing user → serverAdmin → domainAdmin, and the only question is how loudly you climb.

## The quiet route (around rank B, the base for an S-run)

1. **Enumerate INTRANET-WEB** (T1046) from your HR-WS-01 foothold. It has a vulnerable service.
2. **Exploit INTRANET-WEB** (T1190) → foothold.
3. **Enumerate FILE-SRV-01** (T1087) from WEB. It runs a backup **service account** (user tier).
4. **Kerberoast FILE-SRV-01** (T1558.003) → **user** creds. Quiet, because you crack it offline. (The loud alternative here is privesc + Credential Dump, which costs far more detection.)
5. **Pass-the-Hash to FILE-SRV-01**, then **enumerate and PtH to JUMP-01** → you are now bridged to the server segment.
6. **Enumerate SQL-PROD-01**. It runs a SQL **service account** (serverAdmin tier) and has **EDR**.
7. **Kerberoast SQL-PROD-01** (T1558.003) → **serverAdmin** creds. Still offline, so the EDR surcharge is small. (Dumping SQL instead means privesc + dump *on an EDR host*: very loud.)
8. **Enumerate and PtH to ADMIN-WS-07** (serverAdmin opens it).
9. **Privilege Escalation** on ADMIN-WS-07, then **Credential Dump** (T1003) → **domainAdmin**. This dump is unavoidable (no Domain Admin service account to roast), so it is the one loud move in the run. ADMIN-WS-07 has no EDR, which is why it is the right place to be loud.
10. **Enumerate and PtH to DC-01** (domainAdmin opens it).
11. **DCSync** (T1003.006). Domain owned.

## Pushing for S-rank

- Spend **Clear Logs** once *before* you cross 50%, so you never trigger SOC triage and its 25% noise surcharge.
- Never privilege-escalate or dump on an **EDR** host (SQL-PROD-01, DC-01). Roast or pass-the-hash past them.
- Skip enumerations you do not need; each one is cheap but they add up across a run.
- The only mandatory loud action is the ADMIN-WS-07 dump for Domain Admin. Everything else can be quiet.

## The lesson

The loud route (exploit → privesc → dump at every hop) reaches Domain Admin in fewer distinct techniques but drowns the run in detection and gets contained. The quiet route is longer but survives. That is the real trade-off red teams make, and the one blue teams exploit: force the attacker to be loud, and detection does the rest.
