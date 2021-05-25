# AWS-CodePipeline-Demo

Template deploys a pipeline that:

* Uses CloudFormation Nag-Scan



## Deploy with the AWS CLI

aws cloudformation deploy \
--template-file "pipeline.yaml" \
--stack-name sharina-codepipeline-demo \
--parameter-overrides file://parameters/us-west-2.json \
--profile 1s-sandbox \
--capabilities CAPABILITY_NAMED_IAM
