# docker-image-publisher

Builds Docker images from private repositories and publishes them to Docker Hub and GHCR.
It is public only so that GitHub Actions runners (including native arm64) are free. The source
repositories keep Actions switched off.

**Everything here is public:** workflow runs, run names and logs. Builds run with quiet output
by default, and no caches or artifacts are kept.

## Publishing

Run from the Actions tab (**Publish Docker image → Run workflow**), or:

```sh
gh workflow run publish.yml -R liveinaus/docker-image-publisher \
  -f repo=msOauth2api -f ref=v0.8.4 -f image=msoauth2api
```

| Input        | Required | Meaning                                                           |
| ------------ | -------- | ----------------------------------------------------------------- |
| `repo`       | yes      | Source repository name under `liveinaus`                          |
| `ref`        | yes      | Branch, tag or SHA to build                                       |
| `image`      | yes      | Image name, lowercase                                             |
| `tag`        | no       | Image tag; defaults to `ref`. Must be `vX.Y.Z`, `vX.Y.Z-pre` or `dev-*` |
| `context`    | no       | Build context (default `.`)                                       |
| `dockerfile` | no       | Dockerfile path (default `Dockerfile`)                            |
| `verbose`    | no       | Full build log. Public -- only for debugging a failure            |

Tags: `vX.Y.Z` pushes `X.Y.Z` and `latest`; any prerelease pushes `X.Y.Z-pre` and `beta`;
`dev-*` pushes itself and `dev`. Images are built natively for `linux/amd64` and `linux/arm64`.

## Secrets

| Secret               | Value                                                                  |
| -------------------- | ---------------------------------------------------------------------- |
| `SOURCE_REPO_TOKEN`  | Fine-grained PAT, **read-only Contents** on the source repositories    |
| `DOCKERHUB_USERNAME` | Docker Hub user                                                        |
| `DOCKERHUB_TOKEN`    | Docker Hub access token with read/write                                |

GHCR uses the built-in `GITHUB_TOKEN`. For a GHCR package that already exists (created by
another repository), grant this repository **Write** under the package's
*Settings → Manage Actions access*.

## Adding a repository

Add it to the `SOURCE_REPO_TOKEN` PAT's repository list. Nothing in this repository changes.
