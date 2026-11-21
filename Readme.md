# NovaCharts

Helm chart for installing Nova components.

## Build and release

GitHub Actions lints, renders, and packages `Nova/` on pull requests, pushes to
`main`, and manual workflow runs. The packaged chart is saved as the
`nova-chart` workflow artifact.

To publish a release:

1. Update `version` in `Nova/Chart.yaml` to a new semantic version and merge the change.
2. Tag that commit with the same version prefixed by `v`, then push the tag:

   ```sh
   git tag v0.1.0
   git push origin v0.1.0
   ```

The tag must match the chart version. The workflow publishes the package to
`oci://ghcr.io/proximatechnologies/novacharts/nova` using GitHub's automatic
`GITHUB_TOKEN` with `packages: write`; no additional repository secret is needed.
The organization must allow Actions to create packages. If the package already
exists, grant this repository Actions access in the package settings.

For anonymous installation, change the GHCR package visibility to public after
the first release. Private packages require `helm registry login ghcr.io` before
pulling or installing.

## Install a published chart

```sh
helm upgrade --install nova \
  oci://ghcr.io/proximatechnologies/novacharts/nova \
  --version 0.1.0 \
  --namespace nova --create-namespace \
  --values my-values.yaml
```

Provide your deployment settings and credentials in `my-values.yaml`.

## Validate locally

The workflow uses Helm `v4.3.0`.

```sh
helm lint Nova --strict
helm template nova Nova > /tmp/nova-rendered.yaml
helm package Nova --destination /tmp/novacharts-dist
```

See the [Helm OCI registry documentation](https://helm.sh/docs/topics/registries/)
and [GitHub Packages authentication documentation](https://docs.github.com/en/packages/managing-github-packages-using-github-actions-workflows/publishing-and-installing-a-package-with-github-actions)
for registry behavior and access configuration.
