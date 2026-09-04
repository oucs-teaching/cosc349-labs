---
title: COSC349 Lab 8—Cloud Architecture—2026
tags: [cosc349, lab]

---

## Lab 8—Amazon S3 object storage

This lab encourages you to become familiar with Amazon's Simple Storage System (S3)—both a very widely used public object storage system and an API for object storage emulated by many other systems.

## Creating a static website using S3

The aim is to demonstrate S3 hosting static web content. Note, however, that S3 used in this way will only serve content insecurely over HTTP, as opposed to HTTPS. Since the launch of S3, the web has moved to expecting secure web hosting for most purposes (a good thing!). To effect secure web hosting, though, the AWS CloudFront CDN is typically used to add HTTPS support over the top of S3's HTTP. We will not examine that functionality, but you are welcome to do so if you are interested.

- AWS Academy includes a library of shared material such as a tutorial about getting started with S3. We will be very loosely following the structure of [this PDF tutorial](https://cosc349.cspages.otago.ac.nz/cache/s3-website-tute.pdf). That is just for your information though: there are many steps in the PDF we do not need to take, some steps are certainly not going to work through AWS Academy, and the screen captures and processes represented below are more up-to-date than those in the PDF.
- Ensure that you are reading the material critically and are thinking through what is being achieved as the exercise progresses.

### Create an S3 Bucket for your website content

- You will need to access your AWS Management Console to administer S3 resources. You access your console using the same approach that you used to complete Lab 4.
- Your AWS Management Console should look similar to:

![image](https://hackmd.io/_uploads/BkmEr6wOzg.png)


:::info
:bulb:
While you are in the console, consider double-checking that you are not running any services accidentally, left over from your experimentation in earlier labs.
:::

- From the console you can search for or otherwise select S3

![image](https://hackmd.io/_uploads/rkzIr6vdzg.png)

- From the S3 Management Console you can create a bucket.
![image](https://hackmd.io/_uploads/HysjS6P_Ml.png)
- Choose a simple bucket name, but keep in mind that it will need to be globally unique.
- Do not change the region.
- I usually use a name such as `dme26-2026-test` (but please excuse the screen captures that show experiments from earlier years!) so make sure that you replace that name with yours if you are working from my screenshots or examples!
- As an additional step, turn off "block all public access". This has been added since the tutorial PDF, in order to try to ensure that developers using S3 don't accidentally release private data. (Unfortunately this has happened a lot!) 
![image](https://hackmd.io/_uploads/HJGe8awdMl.png)
    - you will also need to check the acknowledgement tick box
![image](https://hackmd.io/_uploads/Byd-8TPuzg.png)
- You should then be able to select "Create bucket" at the bottom of the web page.
![image](https://hackmd.io/_uploads/r1xm8TPdzx.png)
![image](https://hackmd.io/_uploads/HJ1NITDuMg.png)

### Create an S3 bucket for log data
- Follow the above procedure to create another bucket, this time with the name `logs.` prepended to the name you have used above. Note that the bucket containing logs should not be publicly accessible, though.
- My S3 web console's Buckets page looked like this after creating the two buckets:
![image](https://hackmd.io/_uploads/HJ7oUTw_fe.png)

### Configure your S3 buckets

- Now we will add a policy to our S3 bucket to allow it to have public access.
- Select your website content bucket, and choose the 'permissions' tab.
![image](https://hackmd.io/_uploads/Byf6IaP_fe.png)
- Scroll down and edit the Bucket policy.
![image](https://hackmd.io/_uploads/B1jCI6Ddfl.png)
- I have used the policy from the PDF tutorial linked above *but* you must change the `Resource` to match the name of your bucket!
```json
{
    "Version":"2012-10-17",
    "Statement": [{
        "Sid": "Allow Public Access to All Objects",
        "Effect": "Allow",
        "Principal": "*",
        "Action": "s3:GetObject",
        "Resource": "arn:aws:s3:::dme26-2026-test/*"
    }] 
}
```
- Click "Save changes" at the bottom of the policy editor web page and you will be returned to the page configuring your bucket.
![image](https://hackmd.io/_uploads/ByRMPTDdMg.png)
- Now under the "Properties" tab enable "Server access logging".
![image](https://hackmd.io/_uploads/SkjEvpwuzg.png)
![image](https://hackmd.io/_uploads/BJ3rPaDOGe.png)
![image](https://hackmd.io/_uploads/Sk_vDTvuMx.png)
- I used the "Browse S3" button to locate the bucket created earlier, with the `log.` name at the start.
![image](https://hackmd.io/_uploads/B1zKwpwOMg.png)
![image](https://hackmd.io/_uploads/r1KKPawdGg.png)

### Deploy website content

- Create a very simple HTML5 page and save it in a local file called`index.html`
```htmlmixed
<html>
  <body>
    <p>Hello, World!</p>
  </body>
</html>
```
- Likewise create a simple HTML5 page that indicates an error has occurred, and save that as local file `error.html`:
```htmlmixed
<html>
  <body>
    <p>This is an error page.</p>
  </body>
</html>
```
- There are many ways to upload the above two files to your S3 bucket. In my case, from the "Objects" panel of the website bucket:
![image](https://hackmd.io/_uploads/H1SRvTwuMl.png)
- I selected "Upload"
![image](https://hackmd.io/_uploads/BJyJ_TPOfl.png)
- ... and was able to use the drag-and-drop interface shown on that page.
![image](https://hackmd.io/_uploads/rymZOaDdfl.png)
- ... after which you can click the "Upload" button at the end of that page.
![image](https://hackmd.io/_uploads/H1lMdpD_Ge.png)
- The Upload status page should confirm that your files were uploaded successfully.
![image](https://hackmd.io/_uploads/S1jGOaPOGl.png)
- Navigate to the "Properties" tab of your bucket, and find the "Static website hosting" section and the end:
![image](https://hackmd.io/_uploads/BkOSupP_fg.png)
- Edit that setting and add the `index.html` and `error.html` files in the appropriate fields.
![image](https://hackmd.io/_uploads/SJ4wOaPufg.png)
- After saving changes you will be returned to the bucket configuration page, but will now see that static website details are included on the "Static website hosting" section:
![image](https://hackmd.io/_uploads/rkbcupDufg.png)
- My tests returned the expected results for the bucket URI:
![image](https://hackmd.io/_uploads/SkpodTwOGg.png)
- ... and also when I tried to request a name that didn't have a corresponding object:
![image](https://hackmd.io/_uploads/HyTa_TwdMg.png)

### Clean up

- Do not delete your buckets until you are finished with them, e.g., completing the exercises below, and accessing them from code.

:::success
:pencil: 
Exercise:
- Try uploading an image as a separate object in your bucket, and modify your HTML object to include a link to your image.
- Test that the image appears as expected, using your web browser.
:::

:::success
:pencil: 
Exercise:
- If you want to examine what Amazon S3's logging information looks like, you will likely need to wait for quite a while—possibly hours—before the log writing process deposits data into the bucket you set up for logging. In my case, I left the S3 resources online for an hour or so. I then viewed my logging bucket, which contained numerous objects:

![image](https://hackmd.io/_uploads/rJcHzTlA0.png)

- ... with details such as:

![image](https://hackmd.io/_uploads/SJE8G6gRR.png)

- ... and content (that I downloaded) such as:

```
3cc995608916b20d5e9e7ca8746195f3df1d13c5e67bab619d8a563cfa09f558 dme26-2023-test [05/Sep/2023:10:57:05 +0000] 161.65.248.210 arn:aws:sts::262408492548:assumed-role/voclabs/user2678317=_Student_View__David_Eyers 34HRHA3CNN7HQSSS REST.GET.LOGGING_STATUS - "GET /dme26-2023-test?logging= HTTP/1.1" 200 - 241 - 30 - "-" "S3Console/0.4, aws-internal/3 aws-sdk-java/1.12.488 Linux/5.10.186-157.751.amzn2int.x86_64 OpenJDK_64-Bit_Server_VM/25.372-b08 java/1.8.0_372 vendor/Oracle_Corporation cfg/retry-mode/standard" - 7eas+bV+xuvM0biIKoT7SQOOHKvLpduQVv4ciuu34zWPc3RNUJ6QP2i691cZtT1W0zWe8lHdmxY= SigV4 ECDHE-RSA-AES128-GCM-SHA256 AuthHeader s3.amazonaws.com TLSv1.2 - -
```

- ... which looks like it was me using the `aws` command line tool to access my bucket (the mention of Java leads me to assume the `aws` tool itself, or at least a library used by CLI tooling is implemented in Java).
- There will likely be next to no cost counted against your AWS Academy Learner Lab account for your S3 use, but it is safer to delete S3 buckets when you are done with them, rather than leaving them online. One reason for that is that the logging bucket will continue to accumulate log records, and these incur standard S3 charges. Your S3 buckets are, of course, Internet-accessible, although it is not likely that they will attract much web traffic.
:::

- When you have finished your experimentation, it would be safest to delete your S3 buckets, or to otherwise regularly check that you are not incurring unexpected charges from use of your S3 resources.

## Update your S3 resources from code
- These instructions focus on use of Python, but any language for which you can find S3 API documentation is acceptable.
- You will need the `aws` command line tools. These should be installed in the computers within the Ōwheo Building labs, but if the lab environment does not work directly for you, you will need to follow the next step.
- If you wish, you can use the `aws` command line tools from within a virtual machine. The software you will need is included in a Vagrant repository at https://altitude.otago.ac.nz/cosc349/lab08-s3-api that you should `git clone` and `vagrant up` as usual.
    - This repository installs an official Python3 library that facilitates you accessing AWS services from Python code.
    - The repository also installs the AWS command line interface (`aws`).

:::warning
:bomb: 
Note: if you are using an Arm Mac, you will need your VirtualBox to be set up to run the experimental Intel emulation functionality, as configured in the first lab you used VirtualBox (week 2). You can alternatively use the `docker` branch of the Multi-VM Vagrant example from earlier labs, and manually install the small number of packages seen in the `Vagrantfile` at the above `lab08-s3-api` repository.
:::

### Setting up your access credentials
- Before you can use AWS from your code or the command line, you need to set up your access credentials.
- If using AWS command line tools that you have installed yourself, consult the documentation to determine where you need to save your credentials. (On Unix systems typically `~/.aws` will be consulted.)
- If using the aforementioned virtual machine, you should first `vagrant ssh` to your VM and `mkdir ~/.aws`
- For normal AWS accounts you would acquire your command line credentials using the IAM console. However, this won't work for AWS Academy accounts.
- For AWS Academy accounts you instead need to navigate to your "Learner Lab" module page:
![image](https://hackmd.io/_uploads/S16sy0wdzx.png)
- ... and from there select the "AWS Details" option near the top right of the page, which will show an information panel on the right hand side:
![image](https://hackmd.io/_uploads/Hk6h1Rv_fl.png)
- From there you can reveal your AWS CLI credentials (these will keep changing every few hours!).
- Copy/paste your access credentials into the appropriate file, for the AWS tools you are using:
    - e.g., under `~/.aws/credentials` on the aforementioned VM, e.g., using the `nano` editor on your VM.

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
vagrant@vagrant:~$ python3 test.py
cf-templates-16qvuw4hc031r-us-east-1
cf-templates-1rbwhbg4nv0v1-us-east-1
dme26-2026-test
logs.dme26-2026-test
```
:::success
:pencil: 
Exercise:
- Write a small program that changes the content of your S3 web resources.
- Confirm by reloading content in your web browser that your code is working as desired.
:::

## Accessing AWS resources using the command line interface

- The setup you did above to install credentials to support use of Boto3 will also work for the AWS CLI.
- You can consult the Amazon documentation to discover commands, e.g., https://docs.aws.amazon.com/cli/latest/reference/s3api/list-buckets.html
- Within my VM I was able to have interactions such as the following example, with the expected results.

```
vagrant@vagrant:~$ aws s3 ls
2026-08-22 02:28:02 cf-templates-16qvuw4hc031r-us-east-1
2026-08-22 02:31:50 cf-templates-1rbwhbg4nv0v1-us-east-1
2026-09-04 04:16:30 dme26-2026-test
2026-09-04 04:17:33 logs.dme26-2026-test
```

## Cleaning up
- Remember to remove resources that you have finished with, in this case, deleting the bucket you created.## Lab 8—Amazon S3 object storage

This lab encourages you to become familiar with Amazon's Simple Storage System (S3)—both a very widely used public object storage system and an API for object storage emulated by many other systems.

## Creating a static website using S3

The aim is to demonstrate S3 hosting static web content. Note, however, that S3 used in this way will only serve content insecurely over HTTP, as opposed to HTTPS. Since the launch of S3, the web has moved to expecting secure web hosting for most purposes (a good thing!). To effect secure web hosting, though, the AWS CloudFront CDN is typically used to add HTTPS support over the top of S3's HTTP. We will not examine that functionality, but you are welcome to do so if you are interested.

- AWS Academy includes a library of shared material such as a tutorial about getting started with S3. We will be very loosely following the structure of [this PDF tutorial](https://cosc349.cspages.otago.ac.nz/cache/s3-website-tute.pdf). That is just for your information though: there are many steps in the PDF we do not need to take, some steps are certainly not going to work through AWS Academy, and the screen captures and processes represented below are more up-to-date than those in the PDF.
- Ensure that you are reading the material critically and are thinking through what is being achieved as the exercise progresses.

### Create an S3 Bucket for your website content

- You will need to access your AWS Management Console to administer S3 resources. You access your console using the same approach that you used to complete Lab 4.
- Your AWS Management Console should look similar to:

![image](https://hackmd.io/_uploads/BkmEr6wOzg.png)


:::info
:bulb:
While you are in the console, consider double-checking that you are not running any services accidentally, left over from your experimentation in earlier labs.
:::

- From the console you can search for or otherwise select S3

![image](https://hackmd.io/_uploads/rkzIr6vdzg.png)

- From the S3 Management Console you can create a bucket.
![image](https://hackmd.io/_uploads/HysjS6P_Ml.png)
- Choose a simple bucket name, but keep in mind that it will need to be globally unique.
- Do not change the region.
- I usually use a name such as `dme26-2026-test` (but please excuse the screen captures that show experiments from earlier years!) so make sure that you replace that name with yours if you are working from my screenshots or examples!
- As an additional step, turn off "block all public access". This has been added since the tutorial PDF, in order to try to ensure that developers using S3 don't accidentally release private data. (Unfortunately this has happened a lot!) 
![image](https://hackmd.io/_uploads/HJGe8awdMl.png)
    - you will also need to check the acknowledgement tick box
![image](https://hackmd.io/_uploads/Byd-8TPuzg.png)
- You should then be able to select "Create bucket" at the bottom of the web page.
![image](https://hackmd.io/_uploads/r1xm8TPdzx.png)
![image](https://hackmd.io/_uploads/HJ1NITDuMg.png)

### Create an S3 bucket for log data
- Follow the above procedure to create another bucket, this time with the name `logs.` prepended to the name you have used above. Note that the bucket containing logs should not be publicly accessible, though.
- My S3 web console's Buckets page looked like this after creating the two buckets:
![image](https://hackmd.io/_uploads/HJ7oUTw_fe.png)

### Configure your S3 buckets

- Now we will add a policy to our S3 bucket to allow it to have public access.
- Select your website content bucket, and choose the 'permissions' tab.
![image](https://hackmd.io/_uploads/Byf6IaP_fe.png)
- Scroll down and edit the Bucket policy.
![image](https://hackmd.io/_uploads/B1jCI6Ddfl.png)
- I have used the policy from the PDF tutorial linked above *but* you must change the `Resource` to match the name of your bucket!
```json
{
    "Version":"2012-10-17",
    "Statement": [{
        "Sid": "Allow Public Access to All Objects",
        "Effect": "Allow",
        "Principal": "*",
        "Action": "s3:GetObject",
        "Resource": "arn:aws:s3:::dme26-2026-test/*"
    }] 
}
```
- Click "Save changes" at the bottom of the policy editor web page and you will be returned to the page configuring your bucket.
![image](https://hackmd.io/_uploads/ByRMPTDdMg.png)
- Now under the "Properties" tab enable "Server access logging".
![image](https://hackmd.io/_uploads/SkjEvpwuzg.png)
![image](https://hackmd.io/_uploads/BJ3rPaDOGe.png)
![image](https://hackmd.io/_uploads/Sk_vDTvuMx.png)
- I used the "Browse S3" button to locate the bucket created earlier, with the `log.` name at the start.
![image](https://hackmd.io/_uploads/B1zKwpwOMg.png)
![image](https://hackmd.io/_uploads/r1KKPawdGg.png)

### Deploy website content

- Create a very simple HTML5 page and save it in a local file called`index.html`
```htmlmixed
<html>
  <body>
    <p>Hello, World!</p>
  </body>
</html>
```
- Likewise create a simple HTML5 page that indicates an error has occurred, and save that as local file `error.html`:
```htmlmixed
<html>
  <body>
    <p>This is an error page.</p>
  </body>
</html>
```
- There are many ways to upload the above two files to your S3 bucket. In my case, from the "Objects" panel of the website bucket:
![image](https://hackmd.io/_uploads/H1SRvTwuMl.png)
- I selected "Upload"
![image](https://hackmd.io/_uploads/BJyJ_TPOfl.png)
- ... and was able to use the drag-and-drop interface shown on that page.
![image](https://hackmd.io/_uploads/rymZOaDdfl.png)
- ... after which you can click the "Upload" button at the end of that page.
![image](https://hackmd.io/_uploads/H1lMdpD_Ge.png)
- The Upload status page should confirm that your files were uploaded successfully.
![image](https://hackmd.io/_uploads/S1jGOaPOGl.png)
- Navigate to the "Properties" tab of your bucket, and find the "Static website hosting" section and the end:
![image](https://hackmd.io/_uploads/BkOSupP_fg.png)
- Edit that setting and add the `index.html` and `error.html` files in the appropriate fields.
![image](https://hackmd.io/_uploads/SJ4wOaPufg.png)
- After saving changes you will be returned to the bucket configuration page, but will now see that static website details are included on the "Static website hosting" section:
![image](https://hackmd.io/_uploads/rkbcupDufg.png)
- My tests returned the expected results for the bucket URI:
![image](https://hackmd.io/_uploads/SkpodTwOGg.png)
- ... and also when I tried to request a name that didn't have a corresponding object:
![image](https://hackmd.io/_uploads/HyTa_TwdMg.png)

### Clean up

- Do not delete your buckets until you are finished with them, e.g., completing the exercises below, and accessing them from code.

:::success
:pencil: 
Exercise:
- Try uploading an image as a separate object in your bucket, and modify your HTML object to include a link to your image.
- Test that the image appears as expected, using your web browser.
:::

:::success
:pencil: 
Exercise:
- If you want to examine what Amazon S3's logging information looks like, you will likely need to wait for quite a while—possibly hours—before the log writing process deposits data into the bucket you set up for logging. In my case, I left the S3 resources online for an hour or so. I then viewed my logging bucket, which contained numerous objects:

![image](https://hackmd.io/_uploads/rJcHzTlA0.png)

- ... with details such as:

![image](https://hackmd.io/_uploads/SJE8G6gRR.png)

- ... and content (that I downloaded) such as:

```
3cc995608916b20d5e9e7ca8746195f3df1d13c5e67bab619d8a563cfa09f558 dme26-2023-test [05/Sep/2023:10:57:05 +0000] 161.65.248.210 arn:aws:sts::262408492548:assumed-role/voclabs/user2678317=_Student_View__David_Eyers 34HRHA3CNN7HQSSS REST.GET.LOGGING_STATUS - "GET /dme26-2023-test?logging= HTTP/1.1" 200 - 241 - 30 - "-" "S3Console/0.4, aws-internal/3 aws-sdk-java/1.12.488 Linux/5.10.186-157.751.amzn2int.x86_64 OpenJDK_64-Bit_Server_VM/25.372-b08 java/1.8.0_372 vendor/Oracle_Corporation cfg/retry-mode/standard" - 7eas+bV+xuvM0biIKoT7SQOOHKvLpduQVv4ciuu34zWPc3RNUJ6QP2i691cZtT1W0zWe8lHdmxY= SigV4 ECDHE-RSA-AES128-GCM-SHA256 AuthHeader s3.amazonaws.com TLSv1.2 - -
```

- ... which looks like it was me using the `aws` command line tool to access my bucket (the mention of Java leads me to assume the `aws` tool itself, or at least a library used by CLI tooling is implemented in Java).
- There will likely be next to no cost counted against your AWS Academy Learner Lab account for your S3 use, but it is safer to delete S3 buckets when you are done with them, rather than leaving them online. One reason for that is that the logging bucket will continue to accumulate log records, and these incur standard S3 charges. Your S3 buckets are, of course, Internet-accessible, although it is not likely that they will attract much web traffic.
:::

- When you have finished your experimentation, it would be safest to delete your S3 buckets, or to otherwise regularly check that you are not incurring unexpected charges from use of your S3 resources.

## Update your S3 resources from code
- These instructions focus on use of Python, but any language for which you can find S3 API documentation is acceptable.
- You will need the `aws` command line tools. These should be installed in the computers within the Ōwheo Building labs, but if the lab environment does not work directly for you, you will need to follow the next step.
- If you wish, you can use the `aws` command line tools from within a virtual machine. The software you will need is included in a Vagrant repository at https://altitude.otago.ac.nz/cosc349/lab08-s3-api that you should `git clone` and `vagrant up` as usual.
    - This repository installs an official Python3 library that facilitates you accessing AWS services from Python code.
    - The repository also installs the AWS command line interface (`aws`).

:::warning
:bomb: 
Note: if you are using an Arm Mac, you will need your VirtualBox to be set up to run the experimental Intel emulation functionality, as configured in the first lab you used VirtualBox (week 2). You can alternatively use the `docker` branch of the Multi-VM Vagrant example from earlier labs, and manually install the small number of packages seen in the `Vagrantfile` at the above `lab08-s3-api` repository.
:::

### Setting up your access credentials
- Before you can use AWS from your code or the command line, you need to set up your access credentials.
- If using AWS command line tools that you have installed yourself, consult the documentation to determine where you need to save your credentials. (On Unix systems typically `~/.aws` will be consulted.)
- If using the aforementioned virtual machine, you should first `vagrant ssh` to your VM and `mkdir ~/.aws`
- For normal AWS accounts you would acquire your command line credentials using the IAM console. However, this won't work for AWS Academy accounts.
- For AWS Academy accounts you instead need to navigate to your "Learner Lab" module page:
![image](https://hackmd.io/_uploads/S16sy0wdzx.png)
- ... and from there select the "AWS Details" option near the top right of the page, which will show an information panel on the right hand side:
![image](https://hackmd.io/_uploads/Hk6h1Rv_fl.png)
- From there you can reveal your AWS CLI credentials (these will keep changing every few hours!).
- Copy/paste your access credentials into the appropriate file, for the AWS tools you are using:
    - e.g., under `~/.aws/credentials` on the aforementioned VM, e.g., using the `nano` editor on your VM.

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
vagrant@vagrant:~$ python3 test.py
cf-templates-16qvuw4hc031r-us-east-1
cf-templates-1rbwhbg4nv0v1-us-east-1
dme26-2026-test
logs.dme26-2026-test
```
:::success
:pencil: 
Exercise:
- Write a small program that changes the content of your S3 web resources.
- Confirm by reloading content in your web browser that your code is working as desired.
:::

## Accessing AWS resources using the command line interface

- The setup you did above to install credentials to support use of Boto3 will also work for the AWS CLI.
- You can consult the Amazon documentation to discover commands, e.g., https://docs.aws.amazon.com/cli/latest/reference/s3api/list-buckets.html
- Within my VM I was able to have interactions such as the following example, with the expected results.

```
vagrant@vagrant:~$ aws s3 ls
2026-08-22 02:28:02 cf-templates-16qvuw4hc031r-us-east-1
2026-08-22 02:31:50 cf-templates-1rbwhbg4nv0v1-us-east-1
2026-09-04 04:16:30 dme26-2026-test
2026-09-04 04:17:33 logs.dme26-2026-test
```

## Cleaning up
- Remember to remove resources that you have finished with, in this case, deleting the bucket you created.