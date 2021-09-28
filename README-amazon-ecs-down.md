## Halt Amazon ECS task/service for GitHub Actions

Brings down an Amazon ECS task/service utilizing the
[Docker Compose Amazon ECS integration](https://docs.docker.com/compose/cli-command/).

## Requirements

This action requires [Docker CLI v2](https://docs.docker.com/compose/cli-command/)
with [Amazon ECS integration](https://docs.docker.com/cloud/ecs-integration/) enabled.
The [docker-cli-update](README-docker-cli-update.md) action can handle the installation for you.

## Usage

1. [Add Secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets) to your repo settings for
   `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`.
2. Create a Docker Compose YAML file utilizing the [Amazon ECS integration](https://docs.docker.com/cloud/ecs-integration/).
3. Create a [GitHub Workflow](https://docs.github.com/en/actions/learn-github-actions) YAML file with whatever triggers
   apply to your situation and include the following as to your `steps`:
   ```yaml
   - name: Checkout org actions
     uses: actions/checkout@v2
     with:
       repository: CurioNFT/github-actions
       path: ./.github/actions
       ref: v1

   - name: Docker CLI update
     uses: ./.github/actions/docker-cli-update

   - name: Amazon ECS down
     uses: ./.github/actions/amazon-ecs-down
     with:
       aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
       aws-region: us-east-2
       aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
       docker-compose-path: ./docker-compose.deploy.yml
       ecr-repository: my-ecr-repository-name
   ```

See [action.yml](amazon-ecs-down/action.yml) for the full documentation for this action's inputs and outputs.
