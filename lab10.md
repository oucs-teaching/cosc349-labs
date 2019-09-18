---
tags: cosc349
---
# COSC349 Lab 10—Cloud Architecture—2019
## Lab 10—AWS Lambda (Serverless computing)

[Lab 8]: /a8wHsmkVTh6-ud_vNAe2Mw

In this lab we will explore AWS Lambda, which is Amazon's Function as a Service (FaaS) platform.

## Navigate to AWS Lambda

- In the usual (unusual) AWS Educate way, reach your AWS Management Console.

![](https://i.imgur.com/0kmimS1.png)

- Using "Find Services" or otherwise, click through to AWS Lambda.

![](https://i.imgur.com/b3x26Fs.png)

- As suggested "Get started" by clicking the "Create a function" button. You should reach a form from which you can select "Author from scratch" ...

![](https://i.imgur.com/6C6BOiX.png)

- ... and scrolling further down that page, other options that you should set up are shown. In particular
    - You need to name your function
    - In the walk-through here Python 3.7 is used—although of course in your own experimentation feel free to use whatever language you want to.
    - Note that the "Execution role" must be set to the existing role: the AWS Educate accounts do not have sufficient IAM privileges to be able to create roles.

![](https://i.imgur.com/iUVTJZG.png)

- Click "Create function" and you should be taken to a page dedicated to your function. 

![](https://i.imgur.com/tGWvEKO.png)

- The "Designer" pane shows your AWS Lambda function, and the resources it could iteract with. The test function currently does not interact with other AWS services—we will later interact AWS S3.
- Scroll down and you will find the "Function code" pane.
- In this case the "hello world" type of message will be sufficient for testing.

![](https://i.imgur.com/Vv73ag4.png)

- From the button bar at the top of the page, click the "Test" button, and you will be taken to a page on which you should configure a test event.

![](https://i.imgur.com/VvuJGiB.png)

- In this case there is no need to fine-tune the test event that you are defining.
- Lambda maintains the set of test events that you define for each function, so that you can reuse them.
- Complete the definition of your test event (likely this ust involves giving it a name), and you will be returned to the function designer page.

![](https://i.imgur.com/9ltClvw.png)

- With the event that you defined selected in the pull-down menu to the left of the "Test" button, click the "Test" button.
- You should see the following "Execution result" pane appear.

![](https://i.imgur.com/UMSRgDU.png)

- The above execution success includes the data returned from the function, using the HTTP status code 200, to indicate everything is OK, and the body of the request containing the text that was returned by the test function's code.
- Now, using the "breadcrumbs" in the top-left of the button bar or otherwise, return to the Lambda page and define a new function.

![](https://i.imgur.com/4tMpcFK.png)

- This time select "Use a blueprint".
- Search the "Blueprints" for "s3-get-object-python" and select that blueprint.
- Scroll down to the "Basic information" pane to name our function.
- Ensure that you select the AWS Educate role, or you will likely encounter missing permission errors.
 
![](https://i.imgur.com/LStLnuA.png)

- Note that the above blueprint includes an S3 trigger. Scroll down to see the configuration that I used, as shown below.
- You will need to specify the S3 bucket to use, and you will not be able to access dme26-test. You can use the instructions in [Lab 8] to create an S3 bucket again, noting that it does not need to contain any files.

![](https://i.imgur.com/XpErLj2.png)

- Scroll down further to see the function's code.
- Note that the Python function is using the boto3 library, just as we did, in [Lab 8].

![](https://i.imgur.com/eNip8IE.png)

- When you have read through the Python code, click "Create function".
- You will reach the configuration page of your new test function, just as you did for the hello world example, shown previously.
- Note that in this case, the S3 trigger is shown.

![](https://i.imgur.com/xUncYsR.png)

- First, let's try a test event that will fail.
- Click "Test" to configure a new test event.
- Create your event from the "Amazon S3 Put" template, and give your event a name.

![](https://i.imgur.com/oZSTOAf.png)

(The screenshot below is scrolled down from the screenshot shown above.)

- Click the "Create" button to define your test event.

![](https://i.imgur.com/ny2pmgN.png)

- Now, back at the function configuration page, select your test event and click the "Test" button.

![](https://i.imgur.com/LpHGEbw.png)

- If you scroll down to the source code pane, you should see an "Execution Result" sub-panel.
- Note that the stack trace indicates where the problem occurred in the code.
- The cause is that an invalid S3 bucket was specified in the test event's template, and we did not change it.

![](https://i.imgur.com/vcooUs3.png)

- The test selection pull-down menu allows you to "Configure test events" to fix the bucket being referred to.

![](https://i.imgur.com/KOnZIe7.png)

- In a separate web browser tab or window, navigate to the S3 bucket that you indend to use.
- Selectng the bucket from the S3 console should show a pop-up information panel.
- Click the "Copy Bucket ARN" button, to place the specific Amazon Resource Name into the clipboard.

![](https://i.imgur.com/mWQ6iwi.png)

- In your other browser tab, where you are editing your test event, paste in your ARN as the value of the "arn" key in the test event's JSON.
- Also change the "name" of your "bucket" to the value that you are using.
- Note that in the "object" section, the "key" of the object that will be retrieved defaults to `test/key`. 
    - Recall that S3 keys are the names of S3 objects.
    - You need to change the value `test/key` to an S3 key of an object that is actually in your bucket.
    - (Also, if you have no objects in your S3 bucket, you will first need to upload one.)
 
![](https://i.imgur.com/2o22qRF.png)

- Save your updated test event.

:::info
Note that we are using fields for the S3 "put" template, despite the function's code reading from an S3 bucket rather than writing to it. So much of the JSON structure is the same, though, that this approach is still faster than setting up the test event from scratch. Irrelevant fields will be ignored.
:::

- Now re-run your test event, and if your S3 bucket is set up to host a website (and thus serves an HTML page when you visit the bucket address in a web browser), then hopefully you will see an execution success.

:::info
Recall that [Lab 8] steps through activating website hosting features such as specifying an index file (you will need to first upload such an index file to your S3 bucket).
:::

![](https://i.imgur.com/DjFMaGV.png)

- Further down in the function code pane you should see an execution response that indicates the type of the content that the function retrieved from your S3 bucket.

![](https://i.imgur.com/0sioGjX.png)

- For more information, you can click on the "Monitoring" tab at the top of your function's configuration page.
- This will display CloudWatch metrics related to the operation of your function. (Note that there is sometimes a delay between a function executing and the CloudWatch metrics updating.)

![](https://i.imgur.com/RxKXDzF.png)

- Scrolling down will show you a "Recent invocations" list, and information about the cost of the function.

![](https://i.imgur.com/d6PqA5m.png)

- So far we have been manually testing the Lambda function.
- We need to activate the S3 trigger for the function to actually react to S3 events.
- Return to the "Configuration" tab of your function, and click on the S3 box under and to the left of your function.

![](https://i.imgur.com/sBrlCLy.png)

- Scroll down below the "Designer" pane, and you will see S3 information instead of your function's code.
- Note that the S3 trigger is disabled.

![](https://i.imgur.com/87nke6v.png)

- Enable the S3 trigger so that S3 modifications will cause your function to run.

![](https://i.imgur.com/A1V470v.png)

- In a browser window focused on your S3 console, click the "Upload" button.

![](https://i.imgur.com/rUH0Es2.png)

- You should see a panel into which you can drag-and-drop (or otherwise) upload a file.
- Select a non-sensitive file with a known file-type and drag-and-drop this into the upload panel.

![](https://i.imgur.com/srmPYvC.png)

- You can then click the "Upload" button in the bottom-left.

![](https://i.imgur.com/evCuoY6.png)

- You should be returned to the S3 overview page, and see your new object (`mascot-cosc349.jp2` in this screen-shot).

![](https://i.imgur.com/c3QrWvV.png)

- Back in your browser window examining your function's logs, you should now see another invocation on your "Recent invocations".

![](https://i.imgur.com/cwEFRGZ.png)

- Near the top of the "CloudWatch metrics" tab, in the right-hand-side there is a button labeled "View logs in CloudWatch". Click this button.

![](https://i.imgur.com/R9AUjIa.png)

- You will be taken to the CloudWatch service web pages.
- You should see some Log Streams.
- Click on the Log Stream related to the event around the time you modified your S3 bucket.

![](https://i.imgur.com/KKJT1Vu.png)

- Within that Log Stream, you should see displayed the console output of your Lambda function.

![](https://i.imgur.com/wmlLS2I.png)

- In the case shown above, the content type of the uploaded file is correct—an `image/jp2` is was what I had uploaded.
    - Irrelevant aside: the `jp2` extension is normally used by JPEG-2000 images. As the name suggests JPEG-2000 is a newer file format inspired by JPEG. In general JPEG-2000 files do a much better job than JPEG in creating small file sizes that retain high quality... however the format has not gained widespread adoption.

- When you reach this point in the lab exercise, you should have convinced yourself that you can attach code that gets run in response to changes to a particular S3 bucket.

:::success
Exercise: 
- Develop an AWS Lambda function of your own design that notices when objects with names that have a particular prefix (e.g., `images/`) have been uploaded to an S3 bucket and updates content within that bucket.
- A first step might be to use S3 put to deposit an object with a related name that contains information about objects you uploaded, e.g., uploading `images/pic1.jpg` might trigger your function, which uploads an information file `info/pic1.jpg.txt`.
- Going further, you could develop code that on seeing upload of `images/pic1.jpg` creates a thumbnail, which is stored into `thumb/pic1.jpg`.
- There is an ImageMagick example in the function templates, but note that it uses Node.js rather than Python, so may not be intuitive to work with, if you are not familiar with JavaScript.
- It is expected that you will need to read documentation of the API functions used to achieve this task.
:::