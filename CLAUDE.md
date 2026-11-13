<!-- deploy-notice:start v1 (from andhika-sudarman/infra gates/deploy-notice/section.md; change it there, not here) -->
## Before you deploy or release: Claude (F) deploys this repo too

People and the owner's Claude workers (F) both deploy and release `Sejutacita/alice`. Before any staging or production
step (a release branch cut, a cherry-pick onto a release branch, a tag or GitHub release, an image tag change, a
production deploy, a migration, an Unleash flag switch), check what is already in flight, so neither side overwrites
the other:

1. **This repo:** the open PRs into `dev`, the staging build branches (`dev-*`, `dev_*`) and any release or hotfix branch, the newest merges and pushes there, and the newest tag or GitHub
   release.
2. **What runs now:** the newest Firebase App Distribution and TestFlight builds, and the store release in progress (Google Play Console, App Store Connect). Never upload a build number at or below one already uploaded.
3. **F's deploys:** the Board log (tab `Board log` of the CDA sheet, https://docs.google.com/spreadsheets/d/1rBHMmdFGxjw-hKN5PtktbhCMmO_OL8-rQ5tqHAQ5Pzw): the lines that start with
   `Deploy: Sejutacita/alice` or `Release train: alice` in the last 24 hours. No access to the sheet: ask in your PR,
   and a Claude session can read it for you.

Then:

- The commit you deploy must contain every change already live or queued for that environment: the commit live now,
  the commit of any deploy in flight and the head of the release branch it goes out from.
  `git merge-base --is-ancestor <that sha> <your sha>` must succeed for each; a newer build that lacks one of them
  drops that change. If it fails, merge that sha into yours (not a cherry-pick: a cherry-pick makes a new sha, so the
  check would still fail). A change that came in by cherry-pick or squash already passes when
  `git cherry <your sha> <that sha>` prints no `+` line; a squash of several commits, which `git cherry` cannot match:
  name in the PR each `+` commit and the commit of yours that holds its change. Two exceptions, each said in the PR:
  a planned rollback, and a hotfix. A hotfix contains the commit production runs, not a release in flight (it
  branches from that commit and carries only its fix); on staging it may go over a release line in flight, whose
  owner is told in the hotfix PR and deploys that line again after.
- Never skip or reorder a migration. If a release in flight adds one, yours goes after it, with it applied.
- Never switch an Unleash flag that another release in flight depends on.
- Another deploy of this repo in flight (started, not yet live and checked): start yours after it is live, or agree
  the order in its PR.
- Say in your PR who deploys it, with one line: `Deploys: <name, or F (Claude)> · <staging or production> · <tag or
  sha>`.

F writes one Board log line for each deploy it makes:
`Deploy: Sejutacita/alice <env> <tag> sha <commit link> at <yyyy-mm-dd hh:mm> WIB`.
<!-- deploy-notice:end -->
