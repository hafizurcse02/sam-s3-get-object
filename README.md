# sam-app


## Requirements

* AWS CLI already configured with Administrator permission
* [NodeJS 6.10 installed](https://nodejs.org/en/download/releases/)
* [Docker installed](https://www.docker.com/community-edition)

## Setup process

### Local development

**`template.yaml`**
```yaml
...
Resources:
    s3getobject:
        ...
```

**Generating event.json as test payload when invoking `s3getobject` function locally**
```
sam local generate-event s3 put --region ap-southeast-2 --bucket alicebob-predictorcloudformation --key 25d068740afd38d9161f6fa5bc54e225.template > event.json
```

**Invoking `s3getobject` function locally**
```
sam local invoke -e event.json s3getobject
```

## Packaging and deployment

Firstly, we need a `S3 bucket` where we can upload our Lambda functions packaged as ZIP before we deploy anything - If you don't have a S3 bucket to store code artifacts then this is a good time to create one:

```bash
aws s3 mb s3://alicebobbucket-template
```

Next, run the following command to package our Lambda function to S3:

```bash
sam package \
    --template-file template.yaml \
    --output-template-file packaged.yaml \
    --s3-bucket alicebobbucket-template
```

Next, the following command will create a Cloudformation Stack and deploy your SAM resources.

```bash
sam deploy \
    --template-file packaged.yaml \
    --stack-name samS3GetObject \
    --capabilities CAPABILITY_IAM
    --parameter-overrides \
    BucketNameParameter=alicebobbucket-samdemo
```

> **See [Serverless Application Model (SAM) HOWTO Guide](https://github.com/awslabs/serverless-application-model/blob/master/HOWTO.md) for more details in how to get started.**

After deployment is complete you can run the following command to retrieve the API Gateway Endpoint URL:

```bash
aws cloudformation describe-stacks \
    --stack-name samS3GetObject \
    --query 'Stacks[].Outputs'
``` 
