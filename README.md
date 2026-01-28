# Deploying-a-Lambda-Function
 create AWS Lambda function and configure an event notification for Amazon S3. The Amazon S3 notification invokes the CreateThumbnail Lambda function for each image file that is uploaded to an Amazon S3 bucket. The function reads the image object from the source S3 bucket and creates a thumbnail image to save in a target S3 bucket


Create a Lambda function.
Configure an S3 bucket as a Lambda event source.
Invoke a Lambda function by uploading an object to Amazon S3.
Monitor AWS Lambda S3 functions through Amazon CloudWatch Logs.



ps. you will need 2 buckets (1 source bucket and 1 resoze bucket)

Task 1: Creating Amazon S3 buckets
On the AWS Management Console,choose S3
Create bucket .
Create a bucket name.

Create a Source bucket and a target S3 bucket.

ps. Amazon S3 buckets require unique names. 

<img width="1759" height="840" alt="Screenshot 2026-01-27 at 5 04 10 PM" src="https://github.com/user-attachments/assets/5874292e-378f-4f51-ab1c-fedccaeacaf3" />


<img width="1144" height="600" alt="Screenshot 2026-01-27 at 5 04 46 PM" src="https://github.com/user-attachments/assets/dce494a0-1d75-4454-97d5-4638c3bf519f" />


Task 1.3: Uploading a picture to the source bucket
On the Amazon S3 console, choose the images- bucket (not the -resized bucket).

Choose Upload .

In the Upload window, choose Add files .

<img width="1776" height="792" alt="Screenshot 2026-01-27 at 5 11 25 PM" src="https://github.com/user-attachments/assets/c7fe35fd-9222-4960-b531-002e998022b1" />


Task 2: Creating and configuring a Lambda function
you create a Lambda function and configure an Amazon S3 trigger to invoke the Lambda function whenever a new object is created in the source S3 bucket.
On the AWS Management Console, in the search bar, search for and choose Lambda. Create Function.

<img width="1615" height="883" alt="Screenshot 2026-01-27 at 5 15 39 PM" src="https://github.com/user-attachments/assets/d19276b2-1028-4dbe-b6db-e82328ce31ad" />


Task 2.2 Adding an Amazon S3 trigger
you configure an Amazon S3 trigger to invoke a Lambda function whenever a new object is created in your source S3 bucket.
On the Create-Thumbnail page, in the Function overview section, choose  Add trigger
<img width="1758" height="881" alt="Screenshot 2026-01-27 at 5 32 58 PM" src="https://github.com/user-attachments/assets/16e79bfb-a490-4218-920e-1bb92cfe04d5" />

On the Create-Thumbnail page, in the Function overview section, choose  Add trigger
Select S3 as the source to search and select.
For Bucket, select your images- bucket (for example, images-123456789).
Acknowledge the Recursive invocation.


Task 2.3 Configuring the Lambda function

Upload Lambda function code
On the Create-Thumbnail page, choose the Code tab.
Open the Upload from menu and choose .zip file.
Choose the Upload button, then navigate to and select the deployment package you saved to your computer.
Choose Save.

General configuration
On the Create-Thumbnail page, choose the Configuration tab.

In the navigation pane to the left of the section, choose General configuration, and choose Edit .

On the Edit basic settings page, in the Basic settings section, for Description, enter
<img width="1748" height="880" alt="Screenshot 2026-01-27 at 5 41 20 PM" src="https://github.com/user-attachments/assets/545808b5-d450-4018-9046-b3a5b01aac9a" />

 The CreateThumbnail.zip file contains the following Lambda function:


import boto3
import os
import sys
import uuid
from PIL import Image
import PIL.Image

s3_client = boto3.client('s3')

def resize_image(image_path, resized_path):
    with Image.open(image_path) as image:
        image.thumbnail((128, 128))
        image.save(resized_path)

def lambda_handler(event, context):
    for record in event['Records']:
        bucket = record['s3']['bucket']['name']
        key = record['s3']['object']['key']
        download_path = '/tmp/{}{}'.format(uuid.uuid4(), key)
        upload_path = '/tmp/resized-{}'.format(key)

        s3_client.download_file(bucket, key, download_path)
        resize_image(download_path, upload_path)
        s3_client.upload_file(upload_path, '{}-resized'.format(bucket), key)

####
        

 


Task 3: Testing the Lambda Function and verifying output
you test the Lambda function. This is done by simulating an event with the same information normally sent from Amazon S3 when a new object is uploaded.

Task 3.1: Testing the Lambda function
On the Create-Thumbnail page, choose the Test tab.

In the Test event section, configure the following:

For Event name, enter Upload.
For Template - optional, select S3 Put.
replace example-bucket with the name of your image bucket (source).Be sure to replace example-bucket in both locations.
Replace test/key with the name of the picture that you uploaded.
Choose Test in the top-right corner of the section.

<img width="1620" height="663" alt="Screenshot 2026-01-27 at 5 51 17 PM" src="https://github.com/user-attachments/assets/c2125fb8-14d5-408d-9033-c8762e775a9f" />

Task 3.2: Verifying output in the target S3 bucket
On the AWS Management Console, in the search bar, search for and choose  S3
choose the name of your -resized bucket. choose OPEN.

Task 4: Monitoring and logging
you can monitor Lambda functions to identify problems and view log files to assist in debugging.
On the AWS Management Console, in the search bar, search for and choose Lambda.

In the navigation pane on the left side of the page, choose Functions.

On the Functions page, choose the Create-Thumbnail link to launch the function.

On the Create-Thumbnail page, choose the Monitor tab.


Accessing the event log
Log messages from Lambda functions are retained in Amazon CloudWatch Logs.

On the Monitor tab, choose View CloudWatch logs .



 
