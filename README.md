# GitOps Setup & Workflow
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