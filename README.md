# aws-codedeploy-app

Download this directory / Clone this repo

cd aws-codedeploy-app

1. Create an S3 bucket
Command:- aws s3 mb s3://<s3 bucket name>

Example:- aws s3 mb s3://cd-sample-bucket-01 

2. Upload the Sample app zip file
Command:- aws s3 cp SampleApp_Linux.zip s3://cd-sample-bucket-01 
Example:- aws s3 cp SampleApp_Linux.zip s3://cd-sample-bucket-01 


3. Create the Infrastructure includin EC2 auto scalling group with VPC, subnets, Security Groups and code deploy components like Deployment group and application

Command:-

aws cloudformation create-stack --stack-name AWSInfraStack --capabilities CAPABILITY_NAMED_IAM --template-body file://aws_infrastructure.yaml --parameters 'ParameterKey=KeyName,ParameterValue=<Your AWS key name>' 'ParameterKey=LaptopIP,ParameterValue=<Your publicIP/32>' 'ParameterKey=DeploymentBucket,ParameterValue=<S3 bucket name> '
aws cloudformation create-stack --stack-name codedeploy --capabilities CAPABILITY_NAMED_IAM --template-body file://code_deploy.yaml

Example:- 

aws cloudformation create-stack --stack-name AWSInfraStack --capabilities CAPABILITY_NAMED_IAM --template-body file://aws_infrastructure.yaml --parameters 'ParameterKey=KeyName,ParameterValue=dhanesh_aws' 'ParameterKey=LaptopIP,ParameterValue=92.24.216.44/32' 'ParameterKey=DeploymentBucket,ParameterValue=cd-sample-bucket-01 '
aws cloudformation create-stack --stack-name codedeploy --capabilities CAPABILITY_NAMED_IAM --template-body file://code_deploy.yaml



4. Deploy the application via Clouddeploy

Command:-

aws deploy create-deployment --application-name AWSInfraStack --deployment-group-name codedeploy-DeploymentGroup --description "Sample deployment" --s3-location bucket=<S3 Bucket name>,bundleType=zip,key=<App zip file>

Example:- 

aws deploy create-deployment --application-name AWSInfraStack --deployment-group-name codedeploy-DeploymentGroup --description "Sample deployment" --s3-location bucket=cd-sample-bucket-01,bundleType=zip,key=SampleApp_Linux.zip



###Get the URL of the load balancer with the below command
aws cloudformation describe-stacks --stack-name codedeploy --output table