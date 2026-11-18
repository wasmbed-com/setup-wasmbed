# setup-wasmbed

A GitHub Action that installs the Wasmbed CLI.

## Standards

Read the Wasmbed engineering standards before writing or reviewing code here: `STANDARDS.md` in the wasmbed-com/docs repo (https://github.com/wasmbed-com/docs/blob/main/STANDARDS.md; in a session with every repo checked out it is at `../docs/STANDARDS.md`). Above all, follow "Reuse and boundaries": one source for every fact, vendored files (never retyped copies) across repos, and a shared module before a third copy. Customer-facing words follow `COPY.md` in the same repo.

## Notes for this repo

- The source of `action.yml` is `actions/setup-wasmbed/action.yml` in wasmbed-platform: change it there first, then bring this copy in step
- This repo is public: commits are authored as Joel, and nothing here points at a dev environment
