## Lab 12—AWS Simple Notification Service
<!-- Screen captures of 950x800 -->

[Lab 11]: /aPDgQ1qBRvm_4g71edc4DA

In this lab we will explore the AWS Simple Notification Service (SNS), which provides a means to disseminate alerts and notifications to sets of heterogeneous targets including email and SMS messaging.

:::info
:hourglass_flowing_sand: 
Generally by this time in semester everyone is very busy, so the extent of new material has been more specifically focused, in order for lab time to be usable for catch-up and getting assistance. There are of course many places that you can further experiment, if you are ahead in terms of the lab material. (Further specifics available on request, as always.)
:::

## Amazon Simple Notification Service (SNS)

- To interactively set up SNS, start in the AWS Management Console...

![](https://hackmd.io/_uploads/SkRNqBKl6.png)

- ... and navigate to the SNS console in the usual way. (Note that if you get taken to the SNS Dashboard, you can use the "breadcrumbs" to return to the top-level SNS web page shown in the following screen capture.)

![](https://hackmd.io/_uploads/Bkrj9Btea.png)

- The SNS welcome page provides a way to get started using the service. Choose a topic name, and enter it in the provided form control. Click "Next step", to proceed to the more detailed "Create topic" page.

![](https://hackmd.io/_uploads/H1wxjSFx6.png)

- Feel free to explore the additional options, but in our test, all that is needed is to scroll to the bottom of that page and click the "Create topic" button.

![](https://hackmd.io/_uploads/SkkGsBtgT.png)

- Assuming all goes well, you should be returned to the "Topics" display, showing your topic's information, and displaying a green confirmation near the top of the page.
- The page indicating that you have created a topic includes a button to publish a message to that topic. Click the "Publish message" button.
- The message subject can be set to a value.

![](https://hackmd.io/_uploads/r1smjHFxp.png)

- For the moment choose the left-most option regarding "Message structure": to deliver identical payload data to all of the delivery protocols (e.g., different types of devices).
- By scrolling down that web page you will find the message body text box.
- Enter some content that you will recognise when it is delivered.

![](https://hackmd.io/_uploads/Sy3LiStxp.png)

- At the bottom of the page find and click the "Publish message".
- You will be returned to the "Topics" page, with a confirmation banner displayed, if all went well.

![](https://hackmd.io/_uploads/HJbOsHFla.png)

- Select the "Dashboard" link on the left-side menu.

![](https://hackmd.io/_uploads/H1rsiBFl6.png)

- The dashboard indicates that within the `us-east-1` region, this account has a topic registered, but no applications or subscriptions. (In my case I also have some other topic that I didn't define, giving a total of two, and you may or may not have the same total in your AWS Academy account.)
- Publishing a message to a topic that has no subscribers will not achieve any visible effect.
- Click on the "Subscriptions" left-side menu option.

![](https://hackmd.io/_uploads/H1qknSFxp.png)

- Now "Create subscription" to provide a target for publications to reach.
- Select the ARN from the pull-down menu that refers to the topic that you created.
- Select "Email" as the protocol.
- Enter an email address that you control (i.e., not mine, even though it is shown in the screen capture).

![](https://hackmd.io/_uploads/S1fzhHFl6.png)

- Click the "Create subscription" button at the bottom of that page.
- You will be returned to the "Subscriptions" page, which will indicate the endpoint and the status, which will be "Pending confirmation".

![](https://hackmd.io/_uploads/SkpX2BKl6.png)

- Meanwhile, a confirmation request will have been emailed to the address that you entered, and thus you should be able to visit the URL contained within that email message.
- A confirmation such as the following screen-capture should be presented.

![](https://hackmd.io/_uploads/BJ5q2BYxa.png)

- If you return to the "Subscriptions" page...

![](https://hackmd.io/_uploads/B1fhhHYga.png)

- ...you should see that the status has changed to "Confirmed".
- Now, just for your interest, select the "Push notifications" left-menu item.

![](https://hackmd.io/_uploads/r1Hp2Btxa.png)

- This lab and COSC349 do not go into the details of mobile push notifications, but recall that they are discussed in the Middleware / MBaaS lectures.
- Instead, select the "Text messaging (SMS)" left-menu item.

![](https://hackmd.io/_uploads/BJUe6Htl6.png)

:::warning
:warning:  You might want to try to use the "Publish text message" facility shown on this page before working through the addition of your own mobile number in the steps that follow. In 2019 I had no trouble receiving SMS messages from AWS SNS, however in 2020 I seemed to be unable to receive them, in 2021, I was able to receive them again. In 2022 amd 2023, testing SMS worked (after I enrolled my mobile number into the SMS Sandbox), but actual SNS delivery did not seem to work: I just ensured that I had both email and SMS subscriptions to the topic, and thus could look at email, at least.
:::

- Select "Subscribe number to topic".
- You will be returned to the "Create subscription" display, but this type with the "Protocol" changed to "SMS".

![](https://hackmd.io/_uploads/SywMRBFla.png)

- Mobile numbers that you have enrolled in your "sandbox" will be available from a drop-down menu. The owner of that number will have needed to confirm it before they can receive SMSs from AWS SNS.
- Create that subscription.
- Return to the "Topics" left-side menu.

![](https://hackmd.io/_uploads/ryLH0rtlp.png)

- Select your test topic from the list of topics, and click the "Publish message" button.
- You can select an appropriate subject...

![](https://hackmd.io/_uploads/r1GDRSYea.png)

- ... and body-text content.

![](https://hackmd.io/_uploads/HJ4YCBFla.png)

- For your interest, select the "Custom payload for each delivery protocol." radio button, and note the JSON that appears as the message body.

![](https://hackmd.io/_uploads/By8cCBYx6.png)

- Some of the delivery protocols have additional required fields, beyond the body-text itself.
- Once you select "Publish message", you should now receive content both by email and on your phone via SMS.

![](https://hackmd.io/_uploads/BJ120Htl6.png)


:::info
:heavy_check_mark: 
You have now used the main elements of SNS. Let's integrate use of SNS into AWS Lamda.
:::

## Linking SNS to AWS Lambda

- Now, let's use SNS from AWS Lambda. In the usual way, navigate to your AWS Lambda console.

![](https://hackmd.io/_uploads/H1JPy8Kxp.png)

- Choose the function that was developed in Lab 10 that reacts to changes in your S3 storage...

![](https://hackmd.io/_uploads/BJscJLFla.png)

- ... and click "Actions" -> "View details".

![](https://hackmd.io/_uploads/S1AjkUYx6.png)

- Using the panels on the "Designer", check your function's code and S3 configuration:
    - Using Lambda's "Test" button, and then by using S3 bucket modifications, ensure that your function is working as expected. However, note that this form of testing appears not to trigger the SNS destination, and just runs the function itself.

:::success
:pencil: 
Suggested exercise: Extend your Lambda function so that it disseminates a notification containing relevant data when it has run. For example, when an object is added to the S3 bucket that triggers this Lambda function, the SNS could note the S3 object name and its filetype.

**NOTE**: It may be necessary to change the execution role of your Lambda function. I created a new `myLambdaRole` and was then able to allow the SNS target to add permissions to this role to access SNS.
:::

:::success
:pencil: 
Suggested exercise: Look up how to interact with SNS using the `aws` command line, and scripting such as `boto3`. (Scripting using `boto3` will be very similar to the code used within your Lambda function, but for the need to manage your credentials differently.)
:::

:::success
:pencil: 
Suggested exercise: Reverse the earlier example by developing an AWS Lambda function that subscribes to an SNS topic, rather than generating a publication.
:::