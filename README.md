# GitOps setup & workflow
## Prerequisites
Install [Kubernetes kind](https://kind.sigs.k8s.io/docs/user/quick-start/)

Export your GitHub personal access token and username:
```bash
export GITHUB_TOKEN=<your-token>
export GITHUB_USER=<your-username>
```

## Install the Flux CLI
```bash
curl -s https://toolkit.fluxcd.io/install.sh | sudo bash
```

To configure your shell to load flux completions add to your Bash profile:
```bash
# ~/.bashrc or ~/.bash_profile
. <(flux completion bash)
```

## GitOps workflow

This GitHub repository will be used to manage 3 Kubernetes clusters:
* dev
* staging
* production

Using the Flux CLI we'll do the following:
* configure each cluster to synchronise with a directory inside the fleet repository
* register app sources (git repositories) that contain plain Kubernetes manifests or Kustomize overlays
* configure app deployments on all clusters (pre-releases on staging, semver releases on production -- TODO: complete actual git refs)
