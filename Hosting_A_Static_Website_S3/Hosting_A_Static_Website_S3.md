# Hosting a static website on S3 bucket

## Introduction
AWS S3 is great place to host a static website.If you want the DNS domain of your choice then use [Amazon Route 53](https://aws.amazon.com/route53/) service.I will discuss about Amazon Route 53 in another tutorial.In this tutorial my personal website page is going to have DNS domain name in the the following format.<br>
NAME_OF_THE_WEBSITE.s3.AWS_REGION.amazonaws.com/index.html


## Best Practice
It is a efficient practice to create AWS resources using AWS CLI(Commmand Line Interface).You will need to install AWs CLI on your local computer.The administrator of the account should create a new user with the necessary permission and provide the user with <b>Access key Id,Secret Access key</b>.<br>
Maintaining you source code in version control repository is great when you are working in a team and also useful to track your changes and history.

## Intial Setup
To make sure that AWS CLI is installed on the local machine enter the following command on Command prompt `aws --version`.Please note my local computer operating system is Windows 10.The commands could differ for Mac or Linux.<br>
<br>
![Images](./images/aws_version.PNG)
<br>
Now we need to enter `aws configure` command<br>
- User will prompted to enter the Access key Id and Secret Access key which you can copy paste.Access key Id and Secret - Access key is important for information which you should keep in safe and secure place and should not be shared.
- Enter the region where you want to deploy the AWS resourse.For example ap-southeast-2 which is the Sydney region.
- format for output.Enter json in the prompt.<br>
Please find screen shot below<br>
![Images](./images/aws_config.PNG)
<br>After entering all the information above the prompt is retured.Note that prompt does not display any message that login sucessful.So to test it we need to enter the following command `aws sts get-caller-identity`<br>
If the details enter after the `aws configure` command are not correct you will get error message as below.<br>
![Images](./images/aws_conn_err.PNG)

<br>If the details enter after the `aws configure` command are correct it will display the identity in json format.<br>
![Images](./images/aws_conn_sucess.PNG)

## Step 1 Creating a S3 bucket 
S3 (Simple Storage Service)bucket is a object storage service provided by Amazon Corp.In this session I will go through all the commmand relating to creation /deletion,listing and sychronisation of s3 bucket with your local copy of the s3 bucket.
- `aws s3 ls` command will list all the present for this account.If nothing get displayed means there are no s3 bucket created yet.
- `aws s3 ls s3://NAMEOF_OF_THE_BUCKET --recursive --summarize --human-readable` command will list all the files and folder in the s3 bucket.
- `aws s3api list-objects --bucket NAMEOF_OF_THE_BUCKET --output text --query "Contents[].{Key:Key}"` command  will only list only file/foldername nothing else.
- `aws s3 ls s3://NAMEOF_OF_THE_BUCKET/FOLDER_NAME --recursive`
list only the object in a particular folder use this command
- `aws s3 mb s3://NAMEOF_OF_THE_BUCKET ` Note the bucket name should be unique globally else it will throw an error.Note that mb in the command stands for make bucket.
- `aws s3 rb s3://NAMEOF_OF_THE_BUCKET` This command will delete the bucket if not empty.Here rb stands for remove bucket.If the bucket has some content it will throw an error.
- `aws s3 rb s3://NAMEOF_OF_THE_BUCKET --force` This command will delete the bucket even if is not empty.--force flag are used for that.Note one a bucket is deleted it cannot be restored.So be mindful of the operation.
- `aws s3 sync C:\PATH_TO_THE_FOLDER S3://NAMEOF_OF_THE_BUCKET` Uploads from local machine and tranfers the file folder to s3 bucket which are not modified.
- `aws s3 sync S3://NAMEOF_OF_THE_BUCKET C:\PATH_TO_THE_FOLDER` Dowloads from s3 bucket the file/folders to local machine which are not modified.
- `aws s3api get-bucket-policy --bucket NAMEOF_OF_THE_BUCKET` command will display the current bucket policy.If no bucket policy exsits then an error is thrown.

# Step 2 Enabling static website in s3 bucket
- `aws s3 website s3://my-bucket --index-document index.html` This command configures a bucket named my-bucket as a static website

# Step 3 Edit Block Public Access settings
By default, all Amazon S3 buckets and objects are private. Only the resource owner which is the AWS account that created the bucket can access that bucket.This command below allows you to configure the public access to the bucket. We???re setting all of the blocks to false to enable public access.<br>
`aws s3api put-public-access-block --bucket NAME_OF_THE_BUCKET --public-access-block-configuration "BlockPublicAcls=false,IgnorePublicAcls=false,BlockPublicPolicy=false,RestrictPublicBuckets=false"`
<br>

# Step 4 Update the bucket policy for public read access:
 
The resource owner can, however, choose to grant access permissions to other resources and users. One way to do this is to write an access policy.<br>
Use the command below to upload implement a read only bucket policy.<br>
`aws s3api put-bucket-policy --bucket NAMEOF_OF_THE_BUCKET --policy file://C:\PATH_OF_FILE\policy.json`<br>
Below is content of the policy.json below<br>
![Images](./images/aws_policy_json.PNG)          
<br>
Read only access to the the bucket objects is provided by implementing the policy.json.

# Step 5 Create a static website using HTML,CSS and upload it to s3 bucket.
Below is simple example of a webpage written in HTML.Copy the contents in the code block and save it in a file called index.html<br>

    <!DOCTYPE html>
    <html lang="en">
    <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="./style.css">
    <title>MY TEST WEBSITE</title>
    </head>
    <body>
    <h1>Hello and Welcome to HTML world !!</h1>
    </body>
    </html>
<br>Similary copy contents in the code block and save it in a file called style.css

    body
    {
    background-color:azure;
    }

<br>Open the index.html in a Chrome browser.It should look similar to the example below.

![Images](./images/aws_website_ouput.PNG)

<br>You can edit the basic template of HTML and CSS file provided and create your own personal website.We will not go in the details of HTML and CSS in this tutorial.<br>

Finally we can upload the index.html and style.css to the s3 bucket.This can be done by using the following command in the command line.<br>
`aws s3 cp PATH_OF_FILE s3://NAME_OF_BUCKET/filename`
<br>
Type the url in browser `NAME_OF_THE_WEBSITE.s3.AWS_REGION.amazonaws.com/index.html`.The website similar to screenshot should be displayed.

# Final notes
If you any suggestion/improvements or doubts about this document feel free to reach out to me.

 

                















