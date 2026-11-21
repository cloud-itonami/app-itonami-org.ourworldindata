# Proof of concept: a git-annex reference whose bytes live on Yataverse (AWAI-3666)

`electricity-prod-source-stacked.2026-11-21.csv` in this directory is a git-annex reference, not a
file stored in git. Its bytes are the same as `raw/grapher/2026-11-21/electricity-prod-source-stacked.csv`,
which stays in git. Nothing under `raw/` was changed or removed, and nothing new was written to
Yataverse for this test.

## What is recorded where

- In git (`main`): a symbolic link whose target names the key
  `SHA256E-s544028--3c1b33823486dfcb8f661ec3f64bb64a741ff460620f0efbb55545ed34fea97f.csv`.
  The sha256 in the key equals the row for the `raw/` file in `storage/cid-manifest.edn`.
- In the `git-annex` branch: the URL the bytes can be fetched from,
  `https://yataverse.com/ipfs/bafkreib4dmzyeneg37fy6zq6yp3exnskoqp7iydcb4hpxnkvixwtj7vjp4`.
  The CID is the same sha256 in CIDv1 raw form, so one hash serves both systems.
- `.gitattributes` got one rule, for `annex-poc/*.csv` only (`annex.largefiles=anything`,
  `annex.backend=SHA256E`). The existing rules for the rest of the repository are unchanged.

## Fetching the bytes

    git clone https://github.com/cloud-itonami/app-itonami-org.ourworldindata.git
    cd app-itonami-org.ourworldindata
    datalad get annex-poc/electricity-prod-source-stacked.2026-11-21.csv    # or: git annex get <path>

git-annex downloads from the URL and accepts the content only if its sha256 matches the key.

## Result on 2026-11-21

Tools: git-annex 10.20260525, datalad from Homebrew, macOS.

- Added with `git annex addurl --file=<path> <url>`; the downloaded content was byte-identical to the
  `raw/` file.
- In a fresh clone the path was a dangling link before `datalad get` and the file after it; `cmp`
  against the `raw/` file reported no difference and `git annex fsck` passed. The read needed no
  credential.
- Negative control, in a clone that could not push: the key was pointed at the URL of a different
  block (the older `raw/grapher/electricity-prod-source-stacked.csv`). `git annex get` downloaded it,
  reported `verification of content failed`, and left the file absent.

## What this does and does not show

- It shows that a repository can carry only a reference and that a reader with git-annex gets
  verified bytes from Yataverse.
- A plain `git clone` without git-annex leaves a dangling link, and GitHub shows the link target
  instead of the data. Every reader (build scripts, CI) needs the `get` step.
- The URL list lives in the `git-annex` branch, which is pushed directly and not through a pull
  request.
- It does not make Yataverse a durable store. The storage receipts for these blocks have
  `retentionUntil` 0. If the block stopped being served, this reference could not be resolved from
  Yataverse; today the `raw/` copy in git covers that. Do not remove data from git on the strength of
  this test.
- Removing a file from the current tree does not shrink the repository's history.
