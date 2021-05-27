# AWS CodePipeline Demo

The `pipeline.yaml` CloudFormation template deploys a pipeline that:

* Checks directory from the GitHub repository
* Uses the cfn-nag tool as a linter
* Deploys a CloudFormation template. This template is the "cfn-template.yaml" file. It is deployed into the same account as the CodePipeline is in, however with appropriate IAM permissions, cross-account deployment can be configured.
* Runs tests. Currently the pipeline saves the outputs of the CloudFormation deployment into a file and reads it.
* Passes artifacts, such as the artifact that is passed to the final testing stage.

## Deploy with the AWS CLI

To deploy the CodePipeline template, you will need:

* the AWS CLI installed
* to deploy ahead of time the `deployment-role.yaml` file, which provides the permissions necessary for the pipeline to deploy a CloudFormation stack
* a separate JSON file stored within a `parameters` directory that provides the following parameter values:

```json
[
    "ProductName=your-product-name",
    "Environment=your-environment-such-as-dev",
    "GitHubBranchName=your-github-branch-name",
    "TargetAccountForCFnDeployment=current-account-number-to-deploy-the-cfn-template-file-into"
]
```

Deploy from the AWS CLI:

```bash
aws cloudformation deploy \
--template-file "pipeline.yaml" \
--stack-name <your-stack-name> \
--parameter-overrides file://parameters/<name-of-your-parameters-file>.json \
--profile <your-aws-profile-name> \
--capabilities CAPABILITY_NAMED_IAM
```
