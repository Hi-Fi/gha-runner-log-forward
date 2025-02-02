# ARC with logging sidecar

Local setup to check logging sidecar functionality

## Steps

### Create image with `xtail`

Very basic image (based on Ubuntu) can be built using [Dockerfile](./Dockerfile) with command:

```bash
docker image build -t xtail:test .
```

### Actions runner controller (ARC) installation

From [GitHub documentaion](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners-with-actions-runner-controller/deploying-runner-scale-sets-with-actions-runner-controller).


Install Scale set controller:
```bash
NAMESPACE="arc-systems"
helm install arc \
    --namespace "${NAMESPACE}" \
    --create-namespace \
    oci://ghcr.io/actions/actions-runner-controller-charts/gha-runner-scale-set-controller
```

Create GitHub classic personal access token: [GitHub Docs](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners-with-actions-runner-controller/authenticating-to-the-github-api#authenticating-arc-with-a-personal-access-token-classic)

Set basic configuration:
```bash
INSTALLATION_NAME="arc-runner-set"
NAMESPACE="arc-runners"
GITHUB_CONFIG_URL="https://github.com/Hi-Fi/gha-runner-log-forward"
GITHUB_PAT="<PAT>"
```

Install scale set
```bash
helm install "${INSTALLATION_NAME}" \
    --namespace "${NAMESPACE}" \
    --create-namespace \
    --set githubConfigUrl="${GITHUB_CONFIG_URL}" \
    --set githubConfigSecret.github_token="${GITHUB_PAT}" \
    -f ./values.yaml \
    oci://ghcr.io/actions/actions-runner-controller-charts/gha-runner-scale-set
```

