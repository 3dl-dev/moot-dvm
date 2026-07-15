# moot-dvm

The feed algorithm for **[moot](https://moot.pub)**, run as a Nostr DVM.

This repo is a **thin scheduler** — it holds only the cron and the signing key.
The algorithm and publisher live in [`3dl-dev/moot`](https://github.com/3dl-dev/moot)
(`lib/rank.ts`, `scripts/feed-build.ts`, `scripts/dvm-publish.ts`); the workflow
checks that repo out and runs it, so there's a single source of truth for the
ranking with no vendored copies.

## What it publishes

Every 15 minutes, one NIP-90 `kind:6300` result per sort, tagged `t=moot-<sort>`:

- **`moot-hot`** — discussion-weighted engagement + author-trust prior, age-decayed
- **`moot-top`** — best-of-window, no decay
- **`moot-rising`** — real Δengagement/hour vs. the previous run (velocity)
- **`moot-controversial`** — "the ratio": replies ≫ likes (downvotes fold in)

Plus a NIP-89 `kind:31990` announcement so any Nostr client can discover it.

Trust is **engagement-seeded**: the web of trust that filters spam is derived
from who the network actually engages with, not one curator's follows. Full
design: [moot/docs/dvm-feed.md](https://github.com/3dl-dev/moot/blob/main/docs/dvm-feed.md).

## Identity & secret

- DVM identity: `npub1null3tev8jgqpk286ztpagch07hmlxfdxc0xmhkfpqfk8emsepnqnkhn88`
  (nsec in 1Password → `3dl-ops / moot-dvm nostr identity`).
- Repo secret **`NOSTR_NSEC`** is the signing key.

Trigger a run by hand: `gh workflow run publish.yml -R 3dl-dev/moot-dvm`.
