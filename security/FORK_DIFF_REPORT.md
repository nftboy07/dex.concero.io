# nftboy07/dex.concero.io — fork diff result: honest kill

## Verdict
No exploitable bug planted or introduced. The fork is byte-identical to its upstream at commit `b76e7ba`, so no fork-specific code exists to attack.

## Upstream
- GitHub lists this repo as **forked from `concero/app.concero.io`**.
- The fork has one ref, `master` @ `b76e7ba49c93e7c5781a0592579ac5738e768e07` ("refactor", lufaque, 2023-10-27).
- That commit is in upstream history (it is an ancestor of `concero/app.concero.io` `master`, `release` and the `feature/*` branches). Upstream `master` (`692c8539`, 2025-09-18) is 1191 commits ahead.

## Diff evidence (reproduce)
```
git remote add upstream https://github.com/concero/app.concero.io.git
git fetch upstream
git log --oneline origin/master --not $(git for-each-ref --format='%(refname)' refs/remotes/upstream)
# -> (no output): 0 fork-only commits
git merge-base origin/master upstream/master
# -> b76e7ba49c93e7c5781a0592579ac5738e768e07  (fork HEAD itself)
git archive b76e7ba | tar -x -C /tmp/up && diff -rq --exclude=.git --exclude=node_modules /tmp/up .
# -> no output, exit 0 (working tree byte-identical to upstream b76e7ba)
```
All commits in the fork are authored by the upstream developer (lufaque); none differ from upstream.

## What was also looked at
A quick pass over the parts of upstream's frontend where a planted bug would cost users funds, in case the history was rewritten: swap route fetching (LI.FI, Rango), Rango tx building (`prepareEvmTransaction`, `executeRangoRoute`), Enso staking approvals and execution (`handleExecuteSwap`, `approveToken`), hardcoded addresses, and HTML sinks (`innerHTML` in the chart tooltip, which renders only numbers and dates). No attacker-controlled recipient, spender or calldata was found. Spenders and recipients come from the LI.FI, Rango and Enso APIs, as in upstream.

Pre-existing upstream items (not fork changes, no fund impact for an unprivileged attacker): a CryptoPanic API key hardcoded in `src/constants/config.ts`, and debug `console.log` calls. Neither has a PoC that shows impact today.

## Submission venue
None. There is no finding to submit.
