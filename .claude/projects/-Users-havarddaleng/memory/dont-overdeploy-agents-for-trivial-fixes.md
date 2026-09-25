---
name: dont-overdeploy-agents-for-trivial-fixes
description: "For obviously-scoped, small fixes (e.g. a wrong expected status code in one test), just read/edit/run it directly — don't dispatch Explore/tester/augmentor agents"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 177baf30-3a25-4447-8539-1596957daedf
  modified: 2026-08-05T12:19:30.185Z
---

Don't reach for the Agent tool (Explore, tester, augmentor, etc.) when the fix is small enough to diagnose and apply directly — e.g. a single test asserting a stale expected value, a one-line config change, a typo. Read the relevant file(s) yourself, make the edit with Edit, and run the specific test/command yourself with Bash.

**Why:** the user was annoyed that a CI failure traced to "test expects 404, should expect 403" turned into an Explore agent dispatch (for root-cause investigation, arguably justified) followed by ALSO dispatching a tester agent just to run one `lein test :only ...` command — pure overhead for something a single Bash call handles.

**How to apply:** reserve Agent dispatches for genuinely open-ended investigation (unclear root cause spanning multiple files/services) or non-trivial implementation work ([[augmentor-efficiency]]). Once the root cause and fix are already clear — even if an agent helped establish that — do the mechanical edit and verification yourself inline. This applies especially to "run this one test/command and tell me the result" — that's a direct Bash call, never a tester dispatch.
