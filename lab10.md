---
tags: cosc349
---
# COSC349 Lab 10—Cloud Architecture—2021
## Lab 10—AWS Lambda (Serverless computing)

[Lab 8]: /a8wHsmkVTh6-ud_vNAe2Mw

In this lab we will explore AWS Lambda, which is Amazon's Function as a Service (FaaS) platform.

## Navigate to AWS Lambda

- In the usual (unusual) AWS Educate way, reach your AWS Management Console.

![](https://i.imgur.com/wCg14ce.png)

- Using "Find Services" or otherwise, click through to AWS Lambda.

![](https://i.imgur.com/8JfdLbO.png)

- Click the "Create a function" button that should be near the top right of the web interface you see. You should reach a form from which you can select "Author from scratch" ...

![](https://i.imgur.com/pMfLNen.png)

- ... and scrolling further down that page, other options that you should set up are shown. In particular
    - You need to name your function
    - In the walk-through here Python 3.9 is used—although of course in your own experimentation feel free to use whatever language you want to.
    - Note that the "Execution role" must be set to the existing role: the AWS Educate accounts do not have sufficient IAM privileges to be able to create roles.

![](https://i.imgur.com/Y1QBPYl.png)

- Click "Create function" and you should be taken to a page dedicated to your function. 

![](https://i.imgur.com/LZ5loJs.png)

- The "Designer" pane shows your AWS Lambda function, and the resources it could interact with. The test function currently does not interact with other AWS services—we will later interact with AWS S3.
- Scroll down and you will find the "Code source" pane.
- In this case the "hello world" type of message will be sufficient for testing.

![](https://i.imgur.com/zib7ZeV.png)

- From the button bar at 
the top of the page, click the "Test" button, and you will be taken to a page on which you should configure a test event.

![](https://i.imgur.com/QwQgkgD.png)

- In this case there is no need to fine-tune the test event that you are defining.
- Lambda maintains the set of test events that you define for each function, so that you can reuse them.
- Complete the definition of your test event (likely this just involves giving it a name), and you will be returned to the function designer page.

![](https://i.imgur.com/oiSNJnZ.png)

- With the event that you defined selected in the pull-down menu to the left of the "Test" button, click the "Test" button.
- You should see the following "Execution result" pane appear.

![](https://i.imgur.com/Y7SPtoh.png)

- The above execution success includes the data returned from the function, using the HTTP status code 200, to indicate everything is OK, and the body of the request containing the text that was returned by the test function's code.
- Now, using the "breadcrumbs" in the top-left of the button bar or otherwise, return to the Lambda page and define a new function.
- This time select "Use a blueprint".

![](https://i.imgur.com/7tIVyhP.png)

- Search the "Blueprints" for "s3-get-object-python" and select that blueprint. (Click "Configure".)
- In the "Basic information" pane can can name your function.
- Ensure that you select the AWS Educate role, or you will likely encounter missing permission errors.

![](https://i.imgur.com/zT2xy65.png)

- Note that the above blueprint includes an S3 trigger. Scroll down to see the configuration that I used, as shown below.
- You will need to specify the S3 bucket to use, and you will not be able to access `dme26-test`. You can use the instructions in [Lab 8] to create an S3 bucket again, noting that it does not need to contain any files.

![](https://i.imgur.com/kPx2Sdm.png)

- Scroll down further to see the function's code.
- Note that the Python function is using the boto3 library, just as we did, in [Lab 8].

![](https://i.imgur.com/IYYEvz3.png)

- When you have read through the Python code, click "Create function" button below the listing.
- You will reach the configuration page of your new test function, just as you did for the hello world example, shown previously.
- Note that in this case, the S3 trigger is shown.

![](https://i.imgur.com/sqozH5B.png)

- First, let's try a test event that will fail.
- Click "Test" to configure a new test event.
- Create your event from the "Amazon S3 Put" template, and give your event a name.

![](https://i.imgur.com/unaEhTG.png)

(The screenshot below is scrolled down from the screenshot shown above.)
- Click the "Create" button to define your test event.

![](https://i.imgur.com/ejsi3u5.png)

- Now, back at the function configuration page, select your test event and click the "Test" button.

![](https://i.imgur.com/NPhonkN.png)

- If you scroll down to the source code pane, you should see an "Execution Result" sub-panel.
- Note that the stack trace indicates where the problem occurred in the code.
- The cause is that an invalid S3 bucket was specified in the test event's template, and we did not change it.

![](https://i.imgur.com/cb45per.png)

- The test selection pull-down menu allows you to "Configure test events" to fix the bucket being referred to.

![](https://i.imgur.com/Sa329Uv.png)

- In a separate web browser tab or window, navigate to the S3 bucket that you intend to use.
- Selecting the bucket from the S3 console should show a pop-up information panel.
- Click the "Copy Bucket ARN" button, to place the specific Amazon Resource Name into the clipboard.

![](https://i.imgur.com/zjvuPd1.png)

- In your other browser tab, where you are editing your test event, paste in your ARN as the value of the "arn" key in the test event's JSON.
- Also change the "name" of your "bucket" to the value that you are using.
- Note that in the "object" section, the "key" of the object that will be retrieved defaults to `test/key` (URI encoded as `test%2fkey`). 
    - Recall that S3 keys are the names of S3 objects.
    - You need to change the value `test/key` to an S3 key of an object that is actually in your bucket.
    - (Also, if you have no objects in your S3 bucket, you will first need to upload one.)

![](https://i.imgur.com/G03XFXb.png)

- Save your updated test event.

:::info
:bulb:
Note that we are using fields for the S3 "put" template, despite the function's code reading from an S3 bucket rather than writing to it. So much of the JSON structure is the same, though, that this approach is still faster than setting up the test event from scratch. Irrelevant fields will be ignored.
:::

- Now re-run your test event, and if your S3 bucket is set up to host a website (and thus serves an HTML page when you visit the bucket address in a web browser), then hopefully you will see an execution success.
- The execution response should indicate the type of the content that the function retrieved from your S3 bucket.

:::info
:bulb: 
Recall that [Lab 8] steps through activating website hosting features such as specifying an index file (you will need to first upload such an index file to your S3 bucket).
:::

![](https://i.imgur.com/GyHqQtU.png)

- For more information, you can click on the "Monitoring" tab that's just above the "Code source" panel.
- This will display CloudWatch metrics related to the operation of your function. (Note that there is sometimes a delay between a function executing and the CloudWatch metrics updating.)

![](https://i.imgur.com/l1h8eig.png)

- If you instead select the "Logs" tab that's to the right of the Cloud Watch "Metrics" tab that is highlighted by default, you can see your "Recent invocations" list, and information about the cost of the function. (Note that at least one of the recent invocations in my list was an accident, so you'll likely see a different number of invocations!)

![](https://i.imgur.com/PTd9erz.png)

- So far we have been manually testing the Lambda function.
- An S3 "trigger" is the mechanism that reacts to S3 events.
- To view your S3 trigger, select the "Configuration" tab of your function (to the right of the "Monitor" tab you were just using), and click on the "Triggers" left menu item.

![](https://i.imgur.com/JJFBSsr.png)

- In a browser window focused on your S3 console, click the "Upload" button.

![](https://i.imgur.com/mpGXyhw.png)

- You should see a panel into which you can drag-and-drop (or otherwise) upload a file.
- Select a non-sensitive file with a known file-type and drag-and-drop this into the upload panel.

![](https://i.imgur.com/kNN58Fs.png)

- You can then click the "Upload" button in the bottom-left.

![](https://i.imgur.com/tLqwVbg.png)

- You should be returned to the S3 overview page, and see your new object (`mascot-cosc349.jp2` in this screen-shot).

![](https://i.imgur.com/dDtObh8.png)

- Back in your browser window examining your function's logs, you should now see another invocation on your "Recent invocations". (Note again that CloudWatch may not update the Recent invocations immediately.)

![](https://i.imgur.com/vWqrX00.png)

- Near the top of the "CloudWatch metrics" tab, there is a button labeled "View logs in CloudWatch". Click this button.

![](https://i.imgur.com/jIrKPLV.png)

- You will be taken to the CloudWatch service web pages.
- You should see some Log Streams.

![](https://i.imgur.com/FOU3aLr.png)

- Click on the Log Stream related to the event around the time you modified your S3 bucket.
- Within that Log Stream, you should see displayed the console output of your Lambda function.

![](https://i.imgur.com/1UXXPYC.png)

- In the case shown above, the content type of the uploaded file is correct—an `image/jp2` is was what I had uploaded.

:::info
:confused:
**Irrelevant aside**: the `jp2` extension is normally used by JPEG-2000 images. As the name suggests JPEG-2000 is a newer file format inspired by JPEG. In general JPEG-2000 files do a much better job than JPEG in creating small file sizes that retain high quality... however the format has not gained widespread adoption... although it can be used within PDF documents. So there you go.
:::

- When you reach this point in the lab exercise, you should have convinced yourself that you can attach code that gets run in response to changes to a particular S3 bucket.

:::success
:pencil: Exercises: 
- Develop an AWS Lambda function of your own design that notices when objects with names that have a particular prefix (e.g., `images/`) have been uploaded to an S3 bucket and updates content within that bucket.
- A first step might be to use S3 put to deposit an object with a related name that contains information about objects you uploaded, e.g., uploading `images/pic1.jpg` might trigger your function, which uploads an information file `info/pic1.jpg.txt`.
- Going further, you could develop code that on seeing upload of `images/pic1.jpg` creates a thumbnail, which is stored into `thumb/pic1.jpg`.
- There is an ImageMagick example in the function templates, but note that it uses Node.js rather than Python, so may not be intuitive to work with, if you are not familiar with JavaScript.
- It is expected that you will need to read documentation of the API functions used to achieve this task.
:::

