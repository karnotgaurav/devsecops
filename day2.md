getting the report in json format form semgrep 
semgrep --config=p/owasp-top-ten . --json > ~/semgrep-report.json

to use to find the number of finding
cat ~/semgrep-report.json | python3 -c "
import json, sys
data = json.load(sys.stdin)
print('Total findings:', len(data['results']))
"

Extract the important fields from each finding cleanly
cat ~/semgrep-report.json | python3 -c "
import json, sys
data = json.load(sys.stdin)
for i, r in enumerate(data['results'], 1):
    print(f'--- Finding {i} ---')
    print('Rule:   ', r['check_id'].split('.')[-1])
    print('File:   ', r['path'])
    print('Line:   ', r['start']['line'])
    print('Message:', r['extra']['message'][:120])
    print()
"

## What JSON Output Means
- JSON = standard format for security tool output
- Every tool (Semgrep, Bandit, ZAP, Trivy) outputs JSON
- Four fields that matter: Rule, File, Line, Message

## All 7 Findings — Analyzed

### Finding 1 & 2 — Missing USER in Dockerfile (Line 13, 15)
- Severity: High
- Risk: Container runs as root. If attacker escapes container = root on host machine
- Fix: Add USER non-root in Dockerfile before ENTRYPOINT

### Finding 3 & 4 — Raw HTML Input (app/app.py Line 103)
- Severity: High  
- Risk: User input flows directly into HTML = XSS attack possible
- Fix: Use templating engine with auto-escaping (e.g. Jinja2 with escape())

### Finding 5 — MD5 Used as Hash (app/app.py Line 141)
- Severity: Medium
- Risk: MD5 is a broken algorithm — not safe for any security purpose
- Code: hash_pass = hashlib.md5(password).hexdigest()
- Fix: Replace with SHA-256 minimum, bcrypt preferred

### Finding 6 — MD5 Used as Password Hash (app/app.py Line 141)
- Severity: Critical
- Risk: Passwords hashed with MD5 can be cracked in minutes
- Same line as Finding 5 — two rules fired because MD5 is both weak generally AND dangerous for passwords specifically
- Fix: Use bcrypt or Argon2 — purpose-built slow password hashing algorithms

### Finding 7 — SQL Injection (app/app.py Line 261)
- Severity: Critical
- Risk: User input goes directly into SQL query string
- Code: "SELECT ... WHERE username = '%s';" % search_term
- Fix: Parameterized queries — cursor.execute(query, (search_term,))

## What I Learned Today
learned to get the json format and saw how to convert it into nice visble view with python
## What Confused Me
the python confused me but okie we can work with it 