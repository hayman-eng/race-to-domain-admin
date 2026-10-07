# Race to Domain Admin

A single-file, browser strategy game about attack paths. You are red team: starting from one phished workstation, chain real MITRE ATT&CK techniques across a fictional enterprise network to reach the domain controller and run DCSync, all while keeping the SOC's detection meter below 100.

The whole game is the tension between **stealth and speed**. Every technique adds detection; loud techniques get you there faster but get you caught. The lesson is the shape of an attack path and why each step is noisy or quiet.

## Run it

No build, no dependencies. Open `index.html`, or serve the folder:

```bash
python3 -m http.server 8000
# → http://localhost:8000
```

Drops straight onto GitHub Pages (serve from the repo root).

## How it plays

- Click a host to select it; the techniques available against it appear on the right, each showing its ATT&CK ID and how much detection it adds.
- **Enumerate** (T1087 / T1046) reveals a host's weaknesses so you know how to attack it.
- **Exploit Public Service** (T1190) takes a vulnerable host.
- **Privilege Escalation** (T1068) turns a foothold into local admin.
- **Credential Dump** (T1003) harvests creds from a host you own: loud, but works anywhere you have admin.
- **Kerberoasting** (T1558.003) harvests a service account's creds: quiet, because the crack is offline, but only works on hosts running a service account.
- **Pass-the-Hash** (T1550.002) moves laterally using stolen creds of the right tier.
- **Clear Logs** (T1070) buys detection back down, on a cooldown.
- **DCSync** (T1003.006) against the DC is the win.

Credentials come in tiers (user → serverAdmin → domainAdmin). Each host requires a tier to access, so the game is really about climbing the credential ladder to the one that opens the DC.

## The blue team

The SOC is a rising detection meter, not a passive backdrop:

- **50%**, triage: analysts start hunting and every action gets 25% noisier.
- **80%**, containment: a compromised host is isolated and your session there is killed.
- **100%**, caught. Game over.

EDR hosts (marked on the map) make everything you do on them noisier, which is why the quiet route avoids privilege-escalating and dumping on them.

## Scoring

Win and you are ranked S / A / B / C on turns used and peak detection, with a stealth bonus for staying quiet. A competent kerberoasting run lands around B; an S-rank needs a near-silent path and disciplined use of Clear Logs. `STRATEGY.md` has a worked route (spoilers).

## Why a game

Attack paths are the thing junior red-teamers and blue-teamers most need to *feel*: that credential tiers gate movement, that the loud shortcut (dump everything) is a trap, that EDR placement shapes the route, that detection is a budget you spend. A deterministic game model teaches that intuition without any real tooling or target. The techniques are mapped to ATT&CK so the vocabulary carries over to the real work you are authorized to do.

## License

MIT
