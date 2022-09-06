---
tags: cosc349
---
# COSC349 Lab 8—Cloud Architecture—2022
## Lab 8—Amazon S3 object storage

This lab encourages you to become familiar with Amazon's Simple Storage System (S3)—both a very widely used public object storage system and an API for object storage emulated by many other systems.

## Creating a static website using S3

The aim is to demonstrate S3 hosting static web content. Note, however, that S3 used in this way will only serve content insecurely over HTTP, as opposed to HTTPS. Since the launch of S3, the web has moved to expecting secure web hosting for most purposes (a good thing!). To effect secure web hosting, though, the AWS CloudFront CDN is typically used to add HTTPS support over the top of S3's HTTP. We will not examine that functionality, but you are welcome to do so if you are interested.

- AWS Academy includes a library of shared material such as a tutorial about getting started with S3. We will be very loosely following the structure of [this PDF tutorial](https://cosc349.cspages.otago.ac.nz/cache/s3-website-tute.pdf). That is just for your information: there are many steps in the PDF we do not need to take, and the screen captures below should be more up-to-date than those in the PDF.
- Ensure that you are reading the material critically and are thinking through what is being achieved as the exercise progresses.

### Create an S3 Bucket for your website content

- You will need to access your AWS Management Console to administer S3 resources. You access your console using the same approach that you used to complete Lab 4.
- Your AWS Management Console should look similar to:

![](https://i.imgur.com/uoTiQOC.png)

:::info
:bulb:
While you are in the console, consider double-checking that you are not running any services accidentally, left over from your experimentation in lab 4.
:::

- From the console you can search for or otherwise select S3

![](https://i.imgur.com/DH1Dhsq.png)
![](https://i.imgur.com/NG1Nrsj.png)

- From the S3 Management Console you can create a bucket.
![](https://i.imgur.com/knPq0El.png)
- Choose a simple bucket name, but keep in mind that it will need to be globally unique.
- Do not change the region.
- I used the name `dme26-2022-test` so make sure that you replace that name with yours if you are working from my screenshots or examples!
- As an additional step, turn off "block all public access". This has been added since the tutorial PDF, in order to try to ensure that developers using S3 don't accidentally release private data. (Unfortunately this has happened a lot!) ![](https://i.imgur.com/QPPLguW.png)
    - you will also need to check the acknowledgement tick box ![](https://i.imgur.com/uxc2RFb.png)
- You should then be able to select "Create bucket" at the bottom of the web page.
![](https://i.imgur.com/gm3xOpM.png)
![](https://i.imgur.com/16fQIK0.png)
### Create an S3 bucket for log data
- Follow the above procedure to create another bucket, this time with the name `logs.` prepended to the name you have used above. Note that the bucket containing logs should not be publicly accessible, though.
- My S3 web console looked like this after creating the two buckets:
![](https://i.imgur.com/b7x3xY9.png)

### Configure your S3 buckets

- Now we will add a policy to our S3 bucket to allow it to have public access.
- Select your website content bucket, and choose the 'permissions' tab.
![](https://i.imgur.com/4BAsAjk.png)
- Scroll down and edit the Bucket policy.
![](https://i.imgur.com/oiCCS36.png)
- I have used the policy from the PDF tutorial linked above *but* you must change the `Resource` to match the name of your bucket!
```json
{
    "Version":"2012-10-17",
    "Statement": [{
        "Sid": "Allow Public Access to All Objects",
        "Effect": "Allow",
        "Principal": "*",
        "Action": "s3:GetObject",
        "Resource": "arn:aws:s3:::dme26-2022-test/*"
    }] 
}
```
- Click "Save changes" at the bottom of the policy editor web page and you will be returned to the page configuring your bucket.
![](https://i.imgur.com/QUXRIhE.png)
- Now under the "Properties" tab enable "Server access logging".
![](https://i.imgur.com/zJueWhd.png)
![](https://i.imgur.com/52risYJ.png)
![](https://i.imgur.com/647hjD6.png)
- I used the "Browse S3" button to locate the bucket created earlier, with the `log.` name at the start.
![](https://i.imgur.com/vhHrxJx.png)
![](https://i.imgur.com/AF6CLak.png)

### N(s4) Deploy website content

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
- There are many ways to upload the above two files to your S3 bucket. From the "Objects" panel:
![](https://i.imgur.com/Qi51os4.png)
- I selected "Upload"
![](https://i.imgur.com/Vt1Fn0T.png)
- ... and was able to use the drag-and-drop interface shown on that page.
![](https://i.imgur.com/Qhw2KlE.png)
- ... after which you can click the "Upload" button at the end of that page.
![](https://i.imgur.com/dmawIbo.png)
- The Upload status page should confirm that your files were uploaded successfully.
![](https://i.imgur.com/3eCiIWW.png)
- Navigate to the "Properties" tab of your bucket, and find the "Static website hosting" section and the end:
![](https://i.imgur.com/WuyN5Mk.png)
- Edit that setting and add the `index.html` and `error.html` files in the appropriate fields.
![](https://i.imgur.com/57EewUA.png)
- After saving changes you will be returned to the bucket configuration page, but will now see that static website details are included on the "Static website hosting" section:
![](https://i.imgur.com/lbKiyhi.png)
- My tests returned the expected results for the bucket URI:
![](https://i.imgur.com/dnGnfJv.png)
- ... and also when I tried to request a name that didn't have a corresponding object:
![](https://i.imgur.com/ZrQBz3f.png)

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
- If you want to examine what Amazon S3's logging information looks like, you will likely need to wait for quite a while—possibly hours—before the log writing process deposits data into the bucket you set up for logging. In my case, I left the S3 resources online overnight. I then viewed my logging bucket: (Apologies for the screen captures coming from a previous version of the user interface!)

![](https://i.imgur.com/qsOvRSE.png)

- ... which contained numerous objects:

![](https://i.imgur.com/J31G0Pw.png)

- ... with details such as:

![](https://i.imgur.com/59gdYls.png)

- ... and content (clicking the Object URL) such as:

![](https://i.imgur.com/7EqcuFE.png)

- There will likely be next to no cost counted against your AWS Academy Learner Lab account for your S3 use, but it is safer to delete S3 buckets when you are done with them, rather than leaving them online. One reason for that is that the logging bucket will continue to accumulate log records, and these incur standard S3 charges. Your S3 buckets are, of course, Internet-accessible, although it is not likely that they will attract much web traffic.
:::

- When you have finished your experimentation, it would be safest to delete your S3 buckets, or to otherwise regularly check that you are not incurring unexpected charges from use of your S3 resources.

## Update your S3 resources from code
- These instructions focus on use of Python, but any language for which you can find S3 API documentation is acceptable.
- You will need the `aws` command line tools. These should be installed in the CS Lab macOS environment already.
- If you wish, you can use the `aws` command line tools from within a virtual machine. The software you will need is included in a Vagrant repository at https://altitude.otago.ac.nz/cosc349/lab08-s3-api that you should `git clone` and `vagrant up` as usual.
    - This repository installs an official Python3 library that facilitates you accessing AWS services from Python code.
    - The repository also installs the AWS command line interface (`aws`).

### Setting up your access credentials
- Before you can use AWS from your code or the command line, you need to set up your access credentials.
- If using AWS command line tools that you have installed yourself, consult the documentation to determine where you need to save your credentials.
- If using the CS Labs running macOS, you should save your credentials within `~/.aws`.
- If using the aforementioned virtual machine, you should first `vagrant ssh` to your VM and `mkdir ~/.aws`
- For normal AWS accounts you would acquire your command line credentials using the IAM console. However, this won't work for AWS Academy accounts.
- For AWS Academy accounts you instead need to navigate to your "Learner Lab" module page:
![](https://i.imgur.com/QS6QbRN.png)
- ... and from there select the "AWS Details" option near the top right of the page, which will show an information panel on the right hand side:
![](https://i.imgur.com/iuqmCbn.png)
- From there you can reveal your AWS CLI credentials (these will keep changing every few hours!).
- Copy/paste your access credentials into the appropriate file, for the AWS tools you are using:
    - e.g. under `~/.aws/credentials` on your CS Lab macOS environment, or the aforementioned VM, e.g., using the `nano` editor on your VM.
    - In the VM I needed to run `pip3 install --upgrade awscli` to fix the AWS CLI

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
dme26-2022-test
logs.dme26-2022-test
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
vagrant@ubuntu-focal:~$ aws s3 ls
2022-09-05 23:32:22 dme26-2022-test
2022-09-05 23:36:57 logs.dme26-2022-test
```

## Cleaning up
- Remember to remove resources that you have finished with, in this case, deleting the bucket you created.