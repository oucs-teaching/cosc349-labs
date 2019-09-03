---
tags: cosc349
---
# COSC349 Lab 8—Cloud Architecture—2019
## Lab 8—Amazon S3 object storage

This lab encourages you to become familiar with Amazon's Simple Storage System (S3)—both a very widely used public object storage system and an API for object storage emulated by many other systems.

## Creating a static website using S3

- Amazon Educate includes a library of shared material such as a tutorial about getting started with S3
    - [Here is a link to the PDF](https://www.cs.otago.ac.nz/cosc349/cache/s3-website-tute.pdf) that we will follow along.
    - However, note that—to my surprise—the PDF above is written for an older version of the Amazon web console, so I have tried to augment the instructions with screenshots from my working through the exercise.
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
- Your AWS Management Console should look similar to:

![](https://i.imgur.com/3dPT8qe.png)

- From the console you can search for or otherwise select S3

![](https://i.imgur.com/EGU2hcs.png)

- From the S3 console you can create a bucket:

![](https://i.imgur.com/zzYJfEg.png)

- As noted above, choose a simple name, but keep in mind that it will need to be globally unique. Click "Create" on the bottom left to accept the default choices for the other steps otherwise reached using the "next" button.
- Do not change the region.
- I used the name `dme26-test` so make sure that you replace that name with yours if you are working from my screenshots or examples!

![](https://i.imgur.com/X3LNIK6.png)


### Page 12:
- (Step 6) You do not need to create the bucket with the `www.` prefix. The purpose of that bucket if you had registered a domain is to ensure that web visitors who navigate to (for example) `www.example.com` will be redirected to the canonical name, and actual bucket with files in it, at `example.com`.
- Do create the bucket with `logs.` prefix, though.
- My S3 web console looked like this after creating the two buckets:

![](https://i.imgur.com/fxZNiN6.png)

### Page 14:
- Remember to skip step 2 entirely.

### Page 15:
- As an additional step, turn off "block all public access". This has been added since the tutorial PDF, in order to try to ensure that developers using S3 don't accidentally release private data.

![](https://i.imgur.com/jsPt3by.png)
![](https://i.imgur.com/xvOgobH.png)
![](https://i.imgur.com/Sylzrjt.png)

- The policy editor looks different from the PDF tutorial, but the same policy works: (interestingly the warning displayed about public access being blocked was not actually correct for me—possibly some stale data getting to the AWS web interface...)

![](https://i.imgur.com/0fHHbVJ.png)

- (The inconsistent data here that I saw is even more clear, with simultaneous warnings that the data was public and that it wasn't public. However we want the data to be public, and that worked for me.)

![](https://i.imgur.com/7y3L67J.png)

- Enabling logging is done with a different-looking web user interface, but the idea is the same as the PDF tutorial:

![](https://i.imgur.com/UFa2Oex.png)
![](https://i.imgur.com/qGMwyhm.png)
![](https://i.imgur.com/bNEyz78.png)

### Page 19:
- For me I was able to upload files into S3 by dragging and dropping them from Finder to the upload dialogue box—handy.

![](https://i.imgur.com/6tKLuct.png)
![](https://i.imgur.com/funf7LN.png)

### Page 20:
- Configuring your bucket as a website again has a different user interface, but hopefully you are feeling increasingly comfortable with the user interface:

![](https://i.imgur.com/yflM2qs.png)
![](https://i.imgur.com/Uw4B3v9.png)
![](https://i.imgur.com/zwBdh27.png)


### Page 21:
- Skip "Set Up a Redirect".

### Page 23:
- My tests returned the expected results for the bucket URI:

![](https://i.imgur.com/ammyvqM.png)

- ... and also when I tried to request a name that didn't have a corresponding object:

![](https://i.imgur.com/uPqP9UA.png)


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
- If you want to examine what Amazon S3's logging information looks like, you will likely need to wait for quite a while—possibly hours—before the log writing process deposits data into the bucket you set up for logging. In my case, I left the S3 resources online overnight. I then viewed by logging bucket:

![](https://i.imgur.com/qsOvRSE.png)

- ... which contained numerous objects:

![](https://i.imgur.com/J31G0Pw.png)

- ... with details such as:

![](https://i.imgur.com/59gdYls.png)

- ... and content (clicking the Object URL) such as:

![](https://i.imgur.com/7EqcuFE.png)

- There will likely be next to no cost counted against your Educate account for your S3 use, but it is safer to delete S3 buckets when you are done with them, rather than leaving them online. One reason for that is that the logging bucket will continue to accumulate log records, and these incur standard S3 charges.
:::

## Update your S3 resources from code
- These instructions focus on use of Python, but any language for which you can find S3 API documentation is acceptable.
- The software you will need is included in a Vagrant repository at https://altitude.otago.ac.nz/cosc349/lab08-s3-api that you should `git clone` and `vagrant up` as usual.
    - This repository installs an official Python3 library that facilitates you accessing AWS services from Python code.
    - The repository also installs the AWS command line interface (`aws`).

### Setting up your access credentials
- Before you can use AWS from your code or the command line, you need to set up your access credentials.
- `vagrant ssh` to your VM and `mkdir ~/.aws`
- For normal AWS accounts the IAM console facilitates doing this, but this won't work for an AWS Educate account, as evidenced by the warning messages all over this screenshot:

![](https://i.imgur.com/LpsFtho.png)


- For AWS Educate accounts you instead need to navigate to your "Workbench" page. Mine looks like:

![](https://i.imgur.com/0v534ez.jpg)

- Just to the left of the "AWS Console" button is a button entitled "Account Details" that opens a display such as:

![](https://i.imgur.com/SFjROyT.png)

- If you click on "Show" your AWS API access credentials will be displayed—do not share these with anyone!
- Copy/paste your access credentials into the file `~/.aws/credentials` on your VM, e.g., using the `nano` editor on your VM.

### Using the Boto3 library to access AWS from Python

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

## Accessing AWS resources using the command line interface

- As noted above, the VM for this lab also installs the AWS Command Line Interface (CLI)
- The setup you did above to install credentials to support use of Boto3 will also work for the AWS CLI.
- You can consult the Amazon documentation to discover commands, e.g., https://docs.aws.amazon.com/cli/latest/reference/s3api/list-buckets.html
- Within my VM I was able to have interactions such as the following example, with the expected results.

```
vagrant@ubuntu-xenial:~$ aws s3 ls
2019-09-02 11:59:35 dme26-test
2019-09-02 12:01:36 logs.dme26-test
```

## Cleaning up
- Remember to remove resources that you have finished with, in this case, deleting the bucket you created, e.g., following along from page 34 in the linked tutorial PDF.