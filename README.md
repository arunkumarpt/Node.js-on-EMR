# Node.js-on-EMR

In this lab, you’ll learn how to install Node.js on Amazon Elastic MapReduce (Amazon EMR); how to build and integrate a Node.js application with the Hadoop Streaming parallel processing architecture; and finally, how to deploy and run our Node.js MapReduce application on AWS
Prerequisites
• AWS CLI configured in your laptop/test machine to execute AWS commands More: http://docs.aws.amazon.com/cli/latest/userguide/installing.html
• IAM user with permissions to spin up EMR cluster
• A keypair to access the EMR hosts
• An S3 bucket for keeping the logs and output files
• A JSON file with the following content in your laptop/test machine
multiplefiles.json
[ {
"s3://github-aws-big-data-blog/aws-blog-nodejs-on-emr/src/sample- mapper.js,s3://github-aws-big-data-blog/aws-blog-nodejs-on- emr/src/sample-reducer.js",
"-mapper",
"sample-mapper.js",
"-reducer",
"sample-reducer.js",
"-input", "s3://github-aws-big-data-blog/aws-blog-nodejs-on-
emr/sample/tweets", "-output",
"s3://nodejunipersample/EMR/test4"] }
]
1 https://aws.amazon.com/blogs/big-data/node-js-streaming-mapreduce-with-amazon-emr/
 "Name": "JSON Streaming Step", "Type": "STREAMING", "ActionOnFailure": "CONTINUE", "Args": [
"-files",
 
Use case
The MapReduce program outputs the tweet count by day for complex JSON-structured data; in this case, Twitter data. It also escapes special characters such as newlines and tabs, and outputs the tweet created at field as the key. The reducer then rolls up this data by date, outputting the total number of tweets per day.
Step 1: Create EMR Cluster
aws emr create-cluster --ami-version 3.9.0 --instance-groups InstanceGroupType=MASTER,InstanceCount=1,InstanceType=m3.xlarge InstanceGroupType=CORE,InstanceCount=2,InstanceType=m3.xlarge --no-auto- terminate --log-uri s3://nodejunipersample/EMR/logs --bootstrap-actions Path=s3://github-emr-bootstrap-actions/node/install-nodejs.sh,Name=InstallNode.js -- ec2-attributes KeyName=emrjunipertest,InstanceProfile=EMR_EC2_DefaultRole -- service-role EMR_DefaultRole --region us-west-1 --name MyNodeJsMapReduceCluster
Step 2: Submit a step with a node.js application
aws emr add-steps --cluster-id j-37UWSGDKZC6VU --steps file://./multiplefiles.json Step 3: Check the output
In this example, the output is available at s3://nodejunipersample/EMR/test4
Step 4: Terminate the cluster
aws emr terminate-clusters --cluster-ids j-37UWSGDKZC6VU
Reference:
Sample input file : https://raw.githubusercontent.com/awslabs/aws-big-data- blog/master/aws-blog-nodejs-on-emr/sample/tweets/tweets-0.json
Mapper: https://github.com/awslabs/aws-big-data-blog/blob/master/aws-blog-nodejs- on-emr/src/sample-mapper.js
Reduer: https://github.com/awslabs/aws-big-data-blog/blob/master/aws-blog-nodejs- on-emr/src/sample-reducer.js
