## Lab 10—AWS Lambda (Serverless computing)

[Lab 8]: /a8wHsmkVTh6-ud_vNAe2Mw

In this lab we will explore AWS Lambda, which is Amazon's Function as a Service (FaaS) platform.

## Navigate to AWS Lambda

- In the usual (unusual) AWS Academy Learner Lab way, reach your AWS Management Console.

![](https://hackmd.io/_uploads/SyhHHlDJa.png)

- Using "Find Services" or otherwise, click through to AWS Lambda.

![](https://hackmd.io/_uploads/HJ72HxPJa.png)

- Click the "Create function" button that should be near the top right of the web interface you see. You should reach a form from which you can select "Author from scratch" ...

![](https://hackmd.io/_uploads/rkI6Sevkp.png)

- ... and scrolling further down that page, other options that you should set up are shown. In particular
    - You need to name your function
    - In the walk-through here Python 3.11 is used—although of course in your own experimentation feel free to use whatever language you want to.
    - Note that the "Execution role" must be set to the existing role: the AWS Academy accounts do not have sufficient IAM privileges to be able to create roles.

![](https://hackmd.io/_uploads/rkozLewka.png)

- Click "Create function" and you should be taken to a page dedicated to your function. 

![](https://hackmd.io/_uploads/rkNEUlPJT.png)

- The Function overview shows your AWS Lambda function, and the resources it could interact with. The test function currently does not interact with other AWS services—we will later interact with AWS S3.
- Scroll down and you will find the "Code source" pane (under the "Code" tab).
- In this case the "hello world" type of message will be sufficient for testing.

![](https://hackmd.io/_uploads/SJ8UIlDyT.png)

- From the button bar at 
the top of the page, click the "Test" button, and you will be taken to a page on which you should configure a test event.

![](https://hackmd.io/_uploads/ryWu8evy6.png)

- In this case there is no need to fine-tune the test event that you are defining.
- Lambda maintains the set of test events that you define for each function, so that you can reuse them.
- Complete the definition of your test event (likely this just involves giving it a name), and you will be returned to the function designer page.

![](https://hackmd.io/_uploads/BJMiLxv1p.png)

- With the event that you defined selected in the pull-down menu to the right of the "Test" button, click the "Test" button.
- You should see the following "Execution result" pane appear.

![](https://hackmd.io/_uploads/SkVaLxwyT.png)

- The above execution success includes the data returned from the function, using the HTTP status code 200, to indicate everything is OK, and the body of the request containing the text that was returned by the test function's code.
- Now, using the "breadcrumbs" in the top-left of the button bar or otherwise, return to the Lambda page and define a new function.
- This time select "Use a blueprint".

![](https://hackmd.io/_uploads/HykPOgw1a.png)

- Search the "Blueprints" for "s3-get-object-python" and select that blueprint.

![](https://hackmd.io/_uploads/HkzTulwka.png)

- In the "Basic information" pane can can name your function.
- Ensure that you select the AWS Academy role, or you will likely encounter missing permission errors.

![](https://hackmd.io/_uploads/By3QKev1p.png)

- Note that the above blueprint includes an S3 trigger. Scroll down to see the configuration that I used, as shown below.
- You will need to specify the S3 bucket to use, and you will not be able to access `dme26-test`. You can use the instructions in [Lab 8] to create an S3 bucket again, noting that it does not need to contain any files.

![](https://hackmd.io/_uploads/SypKYxP1p.png)

- Scroll up to see the function's code.
- Note that the Python function is using the boto3 library, just as we did, in [Lab 8].

![](https://hackmd.io/_uploads/Byentgwya.png)

- When you have read through the Python code, click "Create function" button at the end of the web page.
- You will reach the configuration page of your new test function, just as you did for the hello world example, shown previously.
- Note that in this case, the S3 trigger is shown.

![](https://hackmd.io/_uploads/HkOyqxPJa.png)

- First, let's try a test event that will fail.
- Click "Test" to configure a new test event.
- Create your event from the "Amazon S3 Put" template, and give your event a name.

![](https://hackmd.io/_uploads/BklhXqgwJp.png)

(The screenshot below is scrolled down from the screenshot shown above.)
- Click the "Save" button to define your test event.

![](https://hackmd.io/_uploads/SymLceD1a.png)

- Now, back at the function configuration page, select your test event and click the "Test" button.

![](https://hackmd.io/_uploads/BJSDcxDJp.png)

- You should see an "Execution Result" sub-panel appear.
- Note that the stack trace indicates where the problem occurred in the code.
- The cause is that an invalid S3 bucket was specified in the test event's template, and we did not change it.

![](https://hackmd.io/_uploads/rJ7hcgD1a.png)

- The test selection pull-down menu allows you to "Configure test events" to fix the bucket being referred to.

![](https://hackmd.io/_uploads/ryQp5ePyp.png)

- In a separate web browser tab or window, navigate to the S3 bucket that you intend to use.
- Selecting the bucket from the S3 console.
- Click the "Copy ARN" button, to place the specific Amazon Resource Name into the clipboard.

![](https://hackmd.io/_uploads/rJrzieDyT.png)

- In your other browser tab, where you are editing your test event, paste in your ARN as the value of the "arn" key in the test event's JSON.
- Also change the "name" of your "bucket" to the value that you are using.
- Note that in the "object" section, the "key" of the object that will be retrieved defaults to `test/key` (URI encoded as `test%2fkey`). 
    - Recall that S3 keys are the names of S3 objects.
    - You need to change the value `test/key` to an S3 key of an object that is actually in your bucket.
    - (Also, if you have no objects in your S3 bucket, you will first need to upload one.)

![](https://hackmd.io/_uploads/rJcg3evy6.png)

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

![](https://hackmd.io/_uploads/r1eMnlwy6.png)

- For more information, you can click on the "Monitor" tab that's just above the "Code source" panel.
- This will display CloudWatch metrics related to the operation of your function. (Note that there is sometimes a delay between a function executing and the CloudWatch metrics updating.)

![](https://hackmd.io/_uploads/HkaVheDJ6.png)

- If you instead select the "Logs" tab that's directly to the right of the Cloud Watch "Metrics" tab that is highlighted by default, you can see your "Recent invocations" list, and information about the cost of the function. (Depending on what you (or I) have clicked, you may see a different number of invocations)

![](https://hackmd.io/_uploads/r1zOnev16.png)

- So far we have been manually testing the Lambda function.
- An S3 "trigger" is the mechanism that reacts to S3 events.
- To view your S3 trigger, select the "Configuration" tab of your function (to the right of the "Monitor" tab you were just using), and click on the "Triggers" left menu item.

![](https://hackmd.io/_uploads/HyaohgD1a.png)

- In a browser window focused on your S3 console, click the "Upload" button.

![](https://hackmd.io/_uploads/HktRhxwJT.png)

- You should see a panel into which you can drag-and-drop (or otherwise) upload a file.
- Select a non-sensitive file with a known file-type and drag-and-drop this into the upload panel.

![](https://hackmd.io/_uploads/SyBP6ewkp.png)

- You can then click the "Upload" button in the bottom-left.

![](https://hackmd.io/_uploads/BJ8saeD1a.png)

- You should be returned to the S3 overview page, and see your new object (`mascot-cosc349.jp2` in this screen-shot).

![](https://hackmd.io/_uploads/B1NTpgDy6.png)

- Back in your browser window examining your function's logs, you should now see another invocation on your "Recent invocations". (Note again that CloudWatch may not update the Recent invocations immediately.)

![](https://hackmd.io/_uploads/BJqW0xP1p.png)

- Just underneath the "Logs" tab, there is a button labeled "View logs in CloudWatch". Click this button.

![](https://hackmd.io/_uploads/ByIQCxvka.png)

- You will be taken to the CloudWatch service web pages.
- You should see some Log Streams.

![](https://hackmd.io/_uploads/rJCNAxvy6.png)

- Click on the Log Stream related to the event around the time you modified your S3 bucket.
- Within that Log Stream, you should see displayed the console output of your Lambda function.

![](https://hackmd.io/_uploads/SyA_Clv16.png)

- In the case shown above, the content type of the uploaded file is shown as an `binary/octet-stream` is was what I had uploaded.

:::info
:confused:
**Irrelevant aside**: the `jp2` extension is normally used by JPEG-2000 images. Uploading in the past has identified as `image/jp2`, but something changed this year (I am using a different web browser for the operation, for starters).

As the name suggests JPEG-2000 is a newer file format inspired by JPEG. In general JPEG-2000 files do a much better job than JPEG in creating small file sizes that retain high quality... however the format has not gained widespread adoption... although it can be used within PDF documents. So there you go.
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

## Cleaning up

You should ensure that you leave your Lambda functions in a safe state. For example, if a Google search crawler, or a potentially malicious script can trigger your function, you may end up causing costs for yourself.

For this lab's example, though, generally you would be the only user with write access to your S3 bucket, and thus your Lambda function should probably be safe to leave active. (If concerned, you can delete your Lambda function.)