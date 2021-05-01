# How to deploy this stack

Run the following commands order to 


1. validate each individual templates stack locally

```
aws cloudformation validate-template \
--template-body file://ecs-three-tier-architecture-base.yaml \
--profile bootcamp
```

the `--profile` parameter is used by aws cli to pick up correct configuration for the target AWS account I am using, from available preconfigured cli profiles. 


2. upload the template files to S3

Create a target bucket in S3. You must chose an unique bucket name. 

```
aws s3 mb s3://cloud-formation-template --profile bootcamp
```

Sync the local templates to the target S3 bucket just created.

You can try a dryrun first to make sure unwanted local files are excluded. 

```
aws s3 sync --dryrun . s3://cloud-formation-template --exclude ".*" --profile bootcamp
```

Then repeat the command without dry run

```
aws s3 sync . s3://cloud-formation-template --exclude ".*" --profile bootcamp
```

Make the templates public 
```
aws s3api put-object-acl --bucket cloud-formation-template-630104356725 --key dependencies/networking-template.yaml --acl public-read --profile bootcamp
```

3. Now you can deploy the stack

```
aws cloudformation create-stack \
--stack-name ecs-three-tier-stack \
--template-body file://ecs-three-tier-architecture-base.yaml  \
--parameters ParameterKey=ResourceBucket,ParameterValue=s3://cloud-formation-template-630104356725 \
--profile bootcamp
```
```

```
 aws cloudformation create-stack \
--stack-name ecs-three-tier-stack \
--template-body file://ecs-three-tier-architecture-base.yaml  \
--parameters ParameterKey=ResourceBucket,ParameterValue=cloud-formation-template \
--profile bootcamp
```




4. check the status of the stack

```
aws cloudformation describe-stacks --stack-name ecs-three-tier-stack \
--profile bootcamp
```

Full list of events 
```
aws cloudformation describe-stack-events --stack-name ecs-three-tier-stack \
--profile bootcamp
```

4. delete the stack 

```
aws cloudformation delete-stack --stack-name ecs-three-tier-stack --profile bootcamp
```