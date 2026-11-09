# setup-wasmbed

Installs the [Wasmbed](https://wasmbed.com) CLI in a GitHub Actions job, so
your workflow can test an app, upload a version and deploy it to your
devices in waves. Wasmbed runs WebAssembly on microcontroller boards: write
apps in AssemblyScript, or bring any wasm bundle from Rust or other
languages.

```yaml
- uses: wasmbed-com/setup-wasmbed@v1
- run: wasmbed test
- run: wasmbed upload
```

## Upload a version on every push to main

The upload signs in with GitHub's OIDC token, so there is no secret to
store. Connect the app to this repository and branch once, on the app's CI
tab in the dashboard, and commit `wasmbed.json` with the app's `"app"` UUID
(`wasmbed upload` from your computer writes it).

```yaml
name: wasmbed
on:
  push:
    branches: [main]

permissions:
  contents: read

jobs:
  upload:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      id-token: write # sign in to Wasmbed as this run
    steps:
      - uses: actions/checkout@v6
      - uses: wasmbed-com/setup-wasmbed@v1
      - run: wasmbed test
      - run: wasmbed upload
```

## Deploy to a device group in waves

Deploying needs an API token with the `deployments:create`,
`deployments:read` and `devices:read` scopes, stored as the secret
`WASMBED_API_TOKEN`. Add a job after the upload:

```yaml
deploy:
  needs: upload
  runs-on: ubuntu-latest
  environment: devices # optional: GitHub can ask for an approval here
  steps:
    - uses: actions/checkout@v6
    - uses: wasmbed-com/setup-wasmbed@v1
    - run: wasmbed deploy --group lab
      env:
        WASMBED_API_TOKEN: ${{ secrets.WASMBED_API_TOKEN }}
```

It deploys the app's newest ready version (the one just uploaded; add
`--version X.Y.Z` to pick one). `wasmbed deploy` uses the project's wave plan and waits until the
deployment ends or a wave waits at a Promote gate, so the job fails if a
wave fails. `--waves plan.json` sets
the waves, `--no-wait` returns once it has started.

## Inputs

| Input     | Default                   | What it does                                                    |
| --------- | ------------------------- | --------------------------------------------------------------- |
| `version` | tested with this release  | The `@wasmbed/cli` version: exact, such as `0.1.0`, or `latest` |
| `project` | the one in `wasmbed.json` | The project commands use (sets `WASMBED_PROJECT`)               |
| `package` | `@wasmbed/cli@<version>`  | A tarball to install instead, for testing the CLI itself        |

The output `version` is the CLI version installed. The action needs Node
20 or newer, which GitHub's runners have; on your own runners, add
`actions/setup-node` first.

The full guide, with examples for releases, Rust and other languages, and
GitLab: [wasmbed.com/docs/apps/github-actions](https://wasmbed.com/docs/apps/github-actions).
