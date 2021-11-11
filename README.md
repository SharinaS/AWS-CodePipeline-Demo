# AWS CodePipeline Demo

The `pipeline.yaml` CloudFormation template deploys a pipeline that:

* Reads the contents of the directory in the GitHub repository
* Uses the cfn-nag tool as a linter
* Deploys a CloudFormation template. This template is the `cfn-template.yaml` file, which upon deployment creates a simple S3 bucket. The template is deployed into the same account as the CodePipeline is in, however with appropriate IAM permissions, cross-account deployment can be configured. The `DeploymentRole` is deployed from a separate file, `deployment-role.yaml`, to allow for this possibility.
* Runs tests. Currently the pipeline saves the outputs of the CloudFormation deployment into a file and reads it.
* Passes artifacts, such as the artifact that is passed to the final testing stage in the pipeline.

## Deploy with the AWS CLI

### Prerequisites

To deploy the CodePipeline template, you will need:

* the AWS CLI installed and configured (how to do this is [here](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) in AWS documentation)
* to deploy the `deployment-role.yaml` file, which provides the permissions necessary for the pipeline to deploy a CloudFormation stack
* A CodeStart connection established in the account you want to create a CodePipeline in. More info available on AWS, [here](https://docs.aws.amazon.com/codepipeline/latest/userguide/action-reference-CodestarConnectionSource.html).
* a separate JSON file stored within a `parameters` directory and updated to provide the following parameters:

```json
[
    "ProductName=your-product-name",
    "Environment=your-environment-such-as-dev",
    "GitHubBranchName=your-github-branch-name",
    "TargetAccountForCFnDeployment=current-account-number-to-deploy-the-s3-bucket-yaml-file-into",
    "GitHubOwner=your-GitHub-name",
    "GitHubRepoName=your-GitHub-repo-name",
    "GitHubConnectionArn=arn:aws:codestar-connections:<region>:<account-number>:connection/<connection-id>"
]
```

### Deploy the Deployment-Role First

This role is used by the pipeline to deploy a CloudFormation template of an S3 bucket with the properties specified in the `s3-bucket.yaml` file.

Deploy from the AWS CLI:

```bash
aws cloudformation deploy \
--template-file "deployment-role.yaml" \
--stack-name <your-stack-name> \
--parameter-overrides file://role-parameters/<parameter-file-name>.json \
--profile <your-aws-profile-name>
--capabilities CAPABILITY_NAMED_IAM
```

### Deploy the Pipeline Once the Above Role has been Deployed

Deploy from the AWS CLI:

```bash
aws cloudformation deploy \
--template-file "pipeline.yaml" \
--stack-name <your-stack-name> \
--parameter-overrides file://parameters/<parameter-file-name>.json \
--profile <your-aws-profile-name> \
--capabilities CAPABILITY_NAMED_IAM
```
