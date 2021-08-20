# Website With Images Rewrite From S3
## 
[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

I will briefly explain the scenario for this project. There is a website with mainly focus on images content and at the same time, there were different users for the website development. Here their concern was that the images for the website should server from the S3 bucket and the user uploading the images for the website should have access to that particular purpose only. No other privileges should be provided for the user. To achieve this I have made use of S3 storage and IAM policies.
    Here I have configured the IAM user and provided them with the privilege to access the specific S3 bucket, where he will be uploading the images. Further developing side of the website will be handled by the developers who have access to the server and also provided with a rewrite to fetch the images from the S3 bucket. So at the time of browsing the website, the image content will be served from the S3.
    

## Features

- Images can be stored in the S3 and avoid space concerns in the server
- Separate user privilege can be provided for the user and  restrictions, unauthorized users, from accessing the data
- Free to use, so as many IAM users can be created according to the requirements

## Prerequisites
- Basic Knowledge in AWS IAM, S3 policy
- IAM user account with necessary privileges.

## Architecture

![
alt_txt
](https://i.ibb.co/JKLxz8W/Untitled-Diagram-1.jpg)


## Steps Followed

- Initially created an IAM user and configured a policy for the user to access the specific S3 bucket only. For creating the policy follow the screenshots given below
![
alt_txt
](https://i.ibb.co/Vmq7303/policy-1.png)

Update the policy with the required access details
![
alt_txt
](https://i.ibb.co/PxYYFm5/org-policy-2.png)
The policy I have created for accessing a particular bucket is provided below.
```sh
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket"
            ],
            "Resource": "arn:aws:s3:::BUCKET_NAME"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:GetObject"
            ],
            "Resource": "arn:aws:s3:::BUCKET_NAME/*"
        }
    ]
}
```
Update the details of the policy with the appropriate comments and create the policy.
![
alt_txt
](https://i.ibb.co/f4p9rzt/Screenshot-5.png)
-  Currently, the configuration part of the user is completed. Further, I have configured the bucket policy to update all the contents uploaded in the bucket to be public. Also enabled public access for the bucket. Review the screenshot and the policy I have used.

![
alt_txt
](https://i.ibb.co/RYDTcmX/Screenshot-3.png)
```sh
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::BUCKET_NAME/*"
        }
    ]
}
```
Next, the most important part is that for redirecting all the images in the website to fetch from the S3 bucket. For that, I have configured with a rewrite rule in httpd.conf.

```sh

# vim /etc/httpd/conf/httpd.conf
RewriteEngine On
RewriteRule ^/img/(.*)$  https://s3.ap-south-1.amazonaws.com//app.ajishantony.tech/$1 [L]
```

## Website_user uploading the images. 

For uploading the images for the user, provide the user with the specific URL for accessing the S3 bucket as like: https://s3.console.aws.amazon.com/s3/buckets/website-image-devops. Then the user can choose the upload option for uploading the images.

![
alt_txt
](https://i.ibb.co/w4jMM4z/Screenshot-9.png)




## Output be like

As provided in the screenshots below. The image for the website has been fetched from the S3 bucket. 

![
alt_txt
](https://i.ibb.co/vw3G0MC/Screenshot-1.png)

![
alt_txt
](https://i.ibb.co/FBFQRrG/Screenshot-2.png)
