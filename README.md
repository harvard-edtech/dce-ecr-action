# DCE ECR Action

This action is designed to create docker images for the various
[harvard-edtech](https://github.com/harvard-edtech) nodejs apps.


## Parameters
| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `access_key_id` | `string` | | Your AWS access key id |
| `secret_access_key` | `string` | | Your AWS secret access key |
| `account_id` | `string` | | Your AWS Account ID |
| `repo` | `string` | | Name of your ECR repository |
| `region` | `string` | | Your AWS region |
| `create_repo` | `boolean` | `true` | Set this to true to create the repository if it does not already exist |
| `tags` | `string` | `latest` | Comma-separated string of ECR image tags (ex latest,1.0.0,) |
| `add_branch_tag` | `boolean` | `true` | Add an additional image tag based on the branch/revision name |
| `add_package_version_tag_for_branch` | `string` | `master` | When building the specified branch, add an additional image tag based on the app's package.json version. Set this to an empty string to disable. |
| `dockerfile` | `string` | `Dockerfile` | Name of Dockerfile to use |
| `extra_build_args` | `string` | | Extra flags to pass to docker build (see docs.docker.com/engine/reference/commandline/build) |
| `path` | `string` | `.` | Path to Dockerfile, defaults to the working directory |
| `slack_webhook_url` | `string` | | Slack webhook url for posting notifications |

### AWS Access Key/Id

It is recommended that the access key id/secret belong to an IAM user with only the bare minimum
of rights needed to create ECR repos and push images to them.

### Slack Notifications

If a webhook url is provided the target channel(s) will recieve one notification when a build is triggered,
and additional for each image:tag combo that is pushed.

### A note about the Dockerfile

If the project using this action does not have a `Dockerfile` in the project root directory
a generic `Dockerfile`, which is suitable for the majority of the DCE nodejs apps, will
be used instead.

### Example Usage

This is how we roll at DCE. Put this in your `./github/workflow/whatever.yml`

```yaml
env:
  REPOSITORY_NAME: hdce/my-cool-node-app

jobs:
  build_and_push:
    runs-on: ubuntu-latest
    steps:
      - name: Check out the repo
        uses: actions/checkout@v2

      - name: Build + Push
        uses: harvard-edtech/dce-ecr-action@v1
        with:
          access_key_id:  ${{ secrets.PUSH_TO_ECR_AWS_ACCESS_KEY_ID }}
          secret_access_key: ${{ secrets.PUSH_TO_ECR_AWS_SECRET_ACCESS_KEY }}
          account_id: ${{ secrets.AWS_ACCOUNT_ID }}
          repo: ${{ env.REPOSITORY_NAME }}
          region: ${{ secrets.AWS_DEFAULT_REGION }}
          tags: ${{ github.sha }}
          add_branch_tag: true
          slack_webhook_url: https://hooks.slack.com/services/asdfklajs/f0q9p384uroi8asuerp9r84u
```

## License
The MIT License (MIT)
