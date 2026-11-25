## Kind: design

## What I was doing
Removing the `security-audit` and `dependabot-cant-fix` entries from `chores` in `.claude/agent-fleet/agent-fleet.json` (the one-line edit, with `secret-scan` left off). I made it, ran `agent-fleet gates check` (clean) and a self-review, then dropped the local branch without pushing.

## Why I stopped
On #150 (2026-11-23) e-gineer wrote that the security audit stays off until it can file findings in this public repo without spelling out how to use them, and that "Dependabot can't fix" waits with it. The engine in 0.22.0 still files a security finding about a public repo's own code as a public issue with a "**How it is exploited:**" line (`scripts/sweep-file-render.cjs:292`). On 2026-11-25 MichaelBurgess removed `agent:skip` and re-armed the issue with no comment, which neither meets nor waives that condition. Turning the audit on would publish exploit write-ups, which a later revert cannot unpublish.

## Question
Should the security audit and "Dependabot can't fix" run now, with exploit text in public issues, or wait for the fleet to withhold it? If you want them on now, say so on #150 and re-arm; the change is the two deletions above.

## Next step
On a go-ahead: delete the `security-audit` and `dependabot-cant-fix` objects from `chores`, leave `secret-scan`, run `agent-fleet gates check`, open the PR with `Closes #150`.
