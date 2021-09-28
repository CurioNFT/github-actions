# Docker Compose CLI v2 for GitHub Actions
Updates to [Docker Compose CLI v2](https://docs.docker.com/compose/cli-command/) which enables
[Amazon ECS integration](https://docs.docker.com/cloud/ecs-integration/).

## Usage

Add the following to your [GitHub Workflow](https://docs.github.com/en/actions/learn-github-actions) YAML file under
`steps` before the ECS integration is needed:

```yaml
- name: Checkout org actions
  uses: actions/checkout@v2
  with:
    repository: CurioNFT/github-actions
    path: ./.github/actions
    ref: v1

- name: Docker CLI update
  uses: ./.github/actions/docker-cli-update
```
