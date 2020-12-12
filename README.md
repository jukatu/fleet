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
* configure app deployments on all clusters (pre-releases on staging, semver releases on production
  * TODO: complete actual git refs

## Boostrap
We will create Ingress ready clusters from yaml configurations.

The only difference between the configuration are the hosted ports to allow to run a full workflow demo on a single host.
    
On actual usage, we will use, instead the same default host ports (80 and 443) for all clusters.


### Prepare bootstrap repo
```bash
# Go to the fleet repo
git clone https://github.com/jukatu/fleet.git
cd fleet
```

### Dev bootstrap
```bash
kind create cluster --name dev --config=clusters/cluster-dev.yaml

kubectl cluster-info --context kind-dev

flux check --pre --context kind-dev

flux bootstrap github \
  --owner=$GITHUB_USER \
  --repository=fleet \
  --branch=main \
  --path=dev-cluster \
  --personal \
  --context kind-dev

# Since `flux bootstrap` updated the fleet remote repository, we ensure to update the changes locally.
git pull
```

### Dev workflow
#### Create a git source pointing to our application source code repository main branch
```bash
flux create source git webapp \
  --url=https://github.com/jukatu/podinfo \
  --branch=dev \
  --interval=30s \
  --export > ./dev-cluster/webapp-source.yaml
```
