---
tags: cosc349
---
# COSC349 Lab 8—Cloud Architecture—2019
## Lab 8—Amazon S3 object storage

This lab encourages you to become familiar with Amazon's Simple Storage System (S3)—both a very widely used public object storage system and an API for object storage emulated by many other systems.

## Creating a static website using S3

- Amazon Educate includes a library of shared material such as a tutorial about getting started with S3
    - [Here is a link to the PDF](https://www.cs.otago.ac.nz/cosc349/cache/s3-website-tute.pdf) that we will follow along.
    - Note, however, to my surprise the PDF above is written for an older version of the Amazon web console, so I have tried to augment the instructions with screeenshots from my working through the exercise.
    - Not all of the steps in the tutorial are relevant or necessary, so I will provide a guide that you should read in parallel, below.
    - Ensure that you are reading the material critically and are thinking through what is being achieved as the exercise progresses.

### Page 1:
- We will not be associating a custom domain with your website. This means that you can choose any bucket name that you wish to use. Otherwise the DNS domain name that you register (and pay for) would dictate the bucket name that you need to use.
- We will not be looking at Amazon CloudFront in the lab, although you are of course very welcome to return to the material and try it out yourself.

### Page 5:
- The tutorial has seven steps: we will explore 1, 3, 4, and 7, although reading through the other sections is recommended, for your own learning.

### Page 7:
- You do not need to "Sign Up for AWS": you can use your Educate account.
- The Educate accounts do not permit an IAM User, so you can skip that too.

### Page 11:
- As noted above, choose a simple name, but keep in mind that it will need to be globally unique.
- Do not change the region.
- I used the name `dme26-test` so make sure that you replace that name with yours if you are working from my screenshots or examples!

### Page 12:
- (Step 6) You do not need to create the bucket with the `www.` prefix. The purpose of that bucket if you had registered a domain is to ensure that web visitors who navigate to (for example) `www.example.com` will be redirected to the canonical name, and actual bucket with files in it, at `example.com`.
- Do create the bucket with `logs.` prefix, though.

### Page 14:
- Remember to skip step 2 entirely.

### Page 15:
- As an additional step, turn off "block all public access". This has been added since the tutorial PDF, in order to try to ensure that developers using S3 don't accidentally release private data.

### Page 19:
- For me I was able to upload files into S3 by dragging and dropping them from Finder to the upload dialogue box—handy.

### Page 21:
- Skip "Set Up a Redirect".

### Page 25:
- Remember to skip step 5 and step 6.
    - However, feel free to read those sections.

### Page 33:
- The first two "Delete the [...]" tasks are not relevant.
- Do not delete your buckets until you are finished with them, e.g., completing the exercises below, and accessing them from code.

:::success
Exercise:
- Try uploading an image as a separate object in your bucket, and modify your HTML object to include a link to your image.
- Test that the image appears as expected, using your web browser.
:::

:::success
Exercise:
- If you want to examine what Amazon S3's logging information looks like, you will likely need to wait for quite a while—possibly hours—before the log writing process deposits data into the bucket you set up for logging. In my case, I left the S3 resources online overnight.
- There will likely be next to no cost counted against your Educate account for your S3 use, but it is safer to delete S3 buckets when you are done with them, rather than leaving them online. One reason for that is that the logging bucket will continue to accumulate log records, and these incur standard S3 charges.
:::

## Update your S3 resources from code
- These instructions focus on use of Python, but any language for which you can find S3 API documentation is acceptable.
- The software you will need is included in a Vagrant repository at https://altitude.otago.ac.nz/cosc349/lab08-s3-api that you should `git clone` and `vagrant up` as usual.
    - This repository installs an official Python3 library that facilitates you accessing AWS services from Python code.
    - The repository also installs the AWS command line interface (`aws`).
- Before you can use AWS from your code or the command line, you need to set up your access credentials.
- For normal AWS accounts the IAM console facilitates doing this, however for AWS Educate accounts you instead need to navigate to your "Workbench" page.
    - A button on that page facilitates opening your Amazon web console
    - The button to the left "Account Details" opens a display that you can click on to reveal your AWS API access credentials—do not share these with anyone!
- `vagrant ssh` to your VM and `mkdir ~/.aws`
- Then copy/paste your access credentials into `~/.aws/credentials`, e.g., using the `nano` editor on your VM.
- Open up Amazon's documentation for the Boto3 library: https://boto3.amazonaws.com/v1/documentation/api/latest/guide/quickstart.html
- In my case, I created a small Python program that I named `test.py`:
```python=
import boto3

s3 = boto3.resource('s3')
for bucket in s3.buckets.all():
    print(bucket.name)
```
- Running this code produced the results I expected:
```
vagrant@ubuntu-xenial:~$ python3 test.py
dme26-test
logs.dme26-test
```
:::success
Exercise:
- Write a small program that changes the content of your S3 web resources.
- Confirm by reloading content in your web browser that your code is working as desired.
:::

## Cleaning up
- Remember to remove resources that you have finished with, in this case, deleting the bucket you created, e.g., following along from page 34 in the linked tutorial PDF.