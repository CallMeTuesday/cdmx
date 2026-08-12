# Current State

_Updated: 2026-08-12 - Branch: main_

## Task

Host a single self-contained HTML travel guide (CDMX trip, Tues and Alice, Roma Norte,
nine nights) on GitHub Pages so it can be read on a phone during the trip. The whole site
is one file, `index.html`. There is no build step, no dependencies, no framework. Editing
means editing that file, committing, and pushing.

Live URL: https://callmetuesday.github.io/cdmx/
Repo: https://github.com/CallMeTuesday/cdmx (public)

## Done

- Initialized the git repo, renamed `cdmx.html` to `index.html` so the Pages root URL
  resolves with no path to type on a phone. Commit `b434618`.
- Created the public repo, pushed, enabled Pages on `main` at root with HTTPS enforced
  via `gh api -X POST repos/CallMeTuesday/cdmx/pages`. Verified HTTP 200 and byte-for-byte
  match against the local file.
- Pushed the user's updated file adding Google Maps deep links on place names, the mode
  switch, and the Jalapa crawl routing. Commit `26bf74b`.
- Fixed the mobile horizontal overflow, removed the "Links open" label, renamed
  "Route from here" to "Directions", restyled the switch as a pill segmented control,
  removed the Heads-up / Choza alert section and its nav link, and added anchor
  scroll offset. Commit `bf6dc5f`. Deployed and confirmed live.

## In progress

Nothing uncommitted. Working tree is clean, `main` is pushed, and the deployed site
matches `bf6dc5f`.

One change is shipped but NOT yet confirmed on a real phone: the anchor scroll fix.
The user said they would test it themselves. See Next.

## Next

1. Ask the user whether jump links (Your street, Places, The plan, Notes) now land on
   the section heading on their iPhone, or still stop slightly high.
2. If they still land high on a cold load, the cause is the `--stickyh` value being read
   before the webfonts settle, which makes the sticky bar measure a few pixels short.
   The fix is to replace the JS-measured `scroll-margin-top` on the sections with
   `scroll-padding-top` on the scrolling element, which the browser resolves at scroll
   time instead of from a cached value:
   ```css
   html{scroll-padding-top:calc(var(--stickyh) + .5rem)}
   ```
   Keep the JS measurement feeding `--stickyh` either way.
3. Optional, previously offered and not taken up: inline the Archivo and DM Mono webfonts
   as base64 so the page renders correctly with no network. Adds a few hundred KB. The
   Maps links need network regardless, so the better offline answer is for the user to
   download the CDMX area in Google Maps before travelling.

## Decisions

- Public repo, not private. GitHub Pages on private repos needs a paid plan and this
  account reads as free. The user was shown the exposure (the guide is publicly readable
  and crawlable) plus a private Artifact alternative, and chose public anyway. Do not
  re-litigate this.
- Served as `index.html`, not `cdmx.html`, so the bare URL works with no path.
- Skipped the full agent-kit onboarding (hooks, constitution, `EXEMPTIONS.md`, PR flow).
  A one-file static site has no gate command to run and no protected branch worth
  guarding, so those would be ceremony with nothing behind them. The kit rules that DO
  apply here were followed: work in a git repo, one idea per commit, why-not-what commit
  messages, no em dashes in commit messages or docs, `rg` instead of `grep`.
- Committing straight to `main` rather than branch-and-PR, for the same reason. If this
  ever grows past one file, revisit and install the kit properly.
- Fixed the overflow at the flex `min-width` rather than adding `overflow-x:hidden` to
  the body. The latter would have hidden the symptom and left the document still too wide.

## Blocked

Nothing.

## Landmines

- **`grep` on this machine is shimmed to `ugrep` and silently returns wrong results.**
  A search can appear to pass by matching nothing. Always use `rg`.
- **The user edits the file outside this repo and drops it in as `cdmx.html`.** Twice now
  a new `cdmx.html` appeared next to the tracked `index.html`. Do not assume the tracked
  file is current. Check for a stray `cdmx.html`, diff it against `index.html`, and read
  the diff before overwriting, because the incoming file is hand-authored and may carry
  changes nobody described.
- **`.claude/settings.local.json` is gitignored** and is written by the Claude session,
  not by the project. Leave it untracked.
- **GitHub Pages takes roughly 30 to 60 seconds to rebuild after a push,** and Safari
  caches the page hard. Verify a deploy by polling the live URL for a marker string or
  byte count, not by assuming the push landed. Tell the user to hard-refresh.
- **`.seg button` needs `min-width:0`.** It looks redundant next to `flex:1 1 0` but it is
  load-bearing. Flex items default to `min-width:auto`, so the nowrap button text sets a
  width floor that pushed the whole document wider than the viewport and clipped the nav
  on both edges. Do not remove it.
- **`--stickyh` is set by JS from the sticky bar's measured height** and drives the anchor
  scroll offset. The CSS fallback of `7rem` is only a first guess. If the sticky bar gains
  or loses a row, nothing needs editing, but if the JS block at the bottom of the file is
  deleted the jump links quietly regress.
- **Choza still appears in the day plan and the Notes booking line.** The user removed only
  the top alert banner because they already know that information. Do not treat the
  remaining Choza mentions as leftovers to clean up; they are schedule context and were
  deliberately left.
