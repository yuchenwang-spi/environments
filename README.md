# OSDU SPI development environments

Non-secret declarations for development environments operated with the official
[`Azure/osdu-spi-stack`](https://github.com/Azure/osdu-spi-stack) CLI and manifests.
This repository does not publish a custom stack or run deployment workflows.

## ywspi-dev

[`ywspi-dev.yaml`](ywspi-dev.yaml) pins a core stack in West US 3. Its resource
group and cluster are named `spi-stack-ywspi-dev`. The initial data partition is
`opendes`; all services initially use community images.

Use the official CLI release matching `stackVersion`. Confirm that Azure CLI is
signed in to the intended tenant and subscription before running:

```powershell
az account show --query '{subscription:id,tenant:tenantId}' --output json
spi check
spi up --env ywspi-dev --declaration yuchenwang-spi/environments:ywspi-dev.yaml --dry-run
spi up --env ywspi-dev --declaration yuchenwang-spi/environments:ywspi-dev.yaml
```

Even the dry run creates or updates the resource group and naming tag. A real
deployment creates billable resources and may return before workloads are ready.
Check `spi status` and gateway/API readiness before clearing maintenance on a
version-pinned deployment.

## Changes and onboarding

The CLI reads declarations from `main`. Use pull requests for changes. Keep
`nameSuffix` stable across retries and upgrades; do not change region on an
existing environment as though it were an in-place upgrade.

Each `forks` entry authorizes the named repository's CI against this environment.
Only list our own service forks. Establish the repository's `spi-stack` GitHub
environment, add the matching declaration entry through a pull request, and run
the `spi onboard` plan before applying it with `--write`. Start with
`canonicalSource: community`; promotion to a fork's published `main` is separate.

An empty `forks` list means no fork trust. Declaration reconciliation revokes
trust for repositories removed from the list; it is not an additive registry.

Never commit credentials, tokens, private keys, kubeconfigs, or test environment
files. GitHub Actions secrets and Azure workload identities hold credentials.
