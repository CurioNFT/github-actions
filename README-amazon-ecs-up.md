## Amazon ECR build, tag, push + ECS up for GitHub Actions

Builds, tags, and pushes a container image URI into an Amazon ECR, then brings up built container within Amazon ECS.

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

   - name: Amazon ECS up
     uses: ./.github/actions/amazon-ecs-up
     with:
       aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
       aws-region: us-east-2
       aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
       docker-compose-path: ./docker-compose.deploy.yml
       ecr-repository: my-ecr-repository-name
       ecr-tag: my-tag-name
   ```

See [action.yml](amazon-ecs-up/action.yml) for the full documentation for this action's inputs and outputs.

## Tips

### Tag as branch name

If you wish to tag your image within Amazon ECR with the branch name, prepend to the `steps` something like this:

```yaml
- name: Git branch name
  id: branch-name
  uses: EthanSK/git-branch-name-action@v1
...
- name: Docker ECS up
  uses: ./.github/actions/amazon-ecs-up
  with:
    ...
    ecr-tag: ${{ format('branch-{0}', env.GIT_BRANCH_NAME) }}
```

The resulting tag with this setup will be `branch-main` on the `main` branch, or `branch-feature` on the `feature` branch.
Feel free to adjust the first [`format()` function](https://docs.github.com/en/actions/learn-github-actions/expressions#format)
arg to your liking (`{0}` is replaced by the branch name).

### Tag as git tag

If you wish to tag your image within Amazon ECR with the git tag name, prepend to the `steps` something like this:

```yaml
- name: Docker ECS up
  uses: ./.github/actions/amazon-ecs-up
  with:
    ...
    ecr-tag: ${GITHUB_REF#refs/tags/}
```

The resulting tag with this setup will be `v1.0` for a `v1.0` tag. See the [Tag as branch name tip](#tag-as-branch-name)
for a reference on how to use the [`format()` function](https://docs.github.com/en/actions/learn-github-actions/expressions#format)
to perform any kind of interpolation.
