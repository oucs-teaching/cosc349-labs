---
tags: cosc349
---
# COSC349 Lab 11—Cloud Architecture—2021
## Lab 11—AWS Simple Notification Service

[Lab 12]: /aPDgQ1qBRvm_4g71edc4DA

In this lab we will explore the AWS Simple Notification Service (SNS), which provides a means to disseminate alerts and notifications to sets of heterogeneous targets including email and SMS messaging.

:::info
Note that to help with assignment two, there have been some techniques and tips added into the start of the [Lab 12] material.
:::

:::info
A number of students are a lab or so behind, so the extent of new material has been more specifically focused, in order for lab time to be usable for catch-up and getting assistance. There are of course many places that you can further experiment, if you are ahead in terms of the lab material. (Further specifics available on request, as always.)
:::

## Amazon Simple Notification Service (SNS)

- To interactively set up SNS, start in the AWS Management Console...

![](https://i.imgur.com/BUNf9Hi.png)

- ... and navigate to the SNS console in the usual way.

![](https://i.imgur.com/OVGObAP.png)

- The SNS welcome page provides a way to get started using the service. Choose a topic name, and enter it in the provided form control. Click "Next step", to proceed to the more detailed "Create topic" page.

![](https://i.imgur.com/UFYM4TK.png)

- Feel free to explore the additional options, but in our test, all that is needed is to scroll to the bottom of that page and click the "Create topic" button.

![](https://i.imgur.com/meZRs3A.png)

- Assuming all goes well, you should be returned to the "Topics" display, showing your topic's information, and displaying a green confirmation near the top of the page.
- The page indicating that you have created a topic includes a button to publish a message to that topic. Click the "Publish message" button.
- The message subject can be set to a value.

![](https://i.imgur.com/DnDVYr6.png)

- For the moment choose the left-most option regarding "Message structure": to deliver identical payload data to all of the delivery protocols (e.g., different types of devices).
- By scrolling down that web page you will find the message body text box.
- Enter some content that you will recognise when it is delivered.

![](https://i.imgur.com/jIXNo38.png)

- At the bottom of the page find and click the "Publish message".
- You will be returned to the "Topics" page, with a confirmation banner displayed, if all went well.

![](https://i.imgur.com/RyPbMid.png)

- Select the "Dashboard" link on the left-side menu.

![](https://i.imgur.com/a4s6kRr.png)

- The dashboard indicates that within the `us-east-1` region, this account has one topic registered, but no applications or subscriptions.
- Publishing a message to a topic that has no subscribers will not achieve any visible effect.
- Click on the "Subscriptions" left-side menu option.

![](https://i.imgur.com/dyvH2WM.png)

- Now "Create subscription" to provide a target for publications to reach.
- Select the ARN from the pull-down menu that refers to the topic that you created.
- Select "Email" as the protocol.
- Enter an email address that you control (i.e., not mine, as shown in the above screen capture).

![](https://i.imgur.com/h1TuUCV.png)

- Click the "Create subscription" button at the bottom of that page.
- You will be returned to the "Subscriptions" page, which will indicate the endpoint and the status, which will be "Pending confirmation".

![](https://i.imgur.com/txT3Kz4.png)

- Meanwhile, a confirmation request will have been emailed to the address that you entered, and thus you should be able to visit the URL contained within that email message.
- A confirmation such as the following screen-capture should be presented.

![](https://i.imgur.com/18IXvyl.png)

- If you return to the "Subscriptions" page...

![](https://i.imgur.com/HPSBRhd.png)

- ...you should see that the status has changed to "Confirmed".
- Now, just for your interest, select the "Push notifications" left-menu item.

![](https://i.imgur.com/1wK0t3V.png)

- This lab and COSC349 do not go into the details of mobile push notifications, but recall that they are discussed in the XaaS and Middleware / MBaaS lectures.
- Instead, select the "Text messaging (SMS)" left-menu item.

![](https://i.imgur.com/zAlLRqH.png)

:::warning
:warning:  You might want to try to use the "Publish text message" facility shown on this page before working through the addition of your own mobile number in the steps that follow. In 2019 I had no trouble receiving SMS messages from AWS SNS, however in 2020 I seemed to be unable to receive them, in 2021, I am able to receive them again.
:::

- Select "Subscribe number to topic".
- You will be returned to the "Create subscription" display, but this type with the "Protocol" changed to "SMS".

![](https://i.imgur.com/IIWdd9R.png)

- Mobile numbers that you have enrolled in your "sandbox" will be available from a drop-down menu. The owner of that number will have needed to confirm it before they can receive SMSs from AWS SNS.
- Create that subscription.
- Return to the "Topics" left-side menu.

![](https://i.imgur.com/hkYE1Cb.png)

- Select your test topic from the list of topics, and click the "Publish message" button.
- You can select an appropriate subject...

![](https://i.imgur.com/McjIuZB.png)

- ... and body-text content.

![](https://i.imgur.com/wOhuKoF.png)

- For your interest, select the "Custom payload for each delivery protocol." radio button, and note the JSON that appears as the message body.

![](https://i.imgur.com/4DN0TQ9.png)

- Some of the delivery protocols have additional required fields, beyond the body-text itself.
- Once you select "Publish message", you should now receive content both by email and on your phone via SMS.

![](https://i.imgur.com/RLjsNRJ.png)


:::info
:heavy_check_mark: 
You have now used the main elements of SNS. Let's integrate use of SNS into AWS Lamda.
:::

## Linking SNS to AWS Lambda

- Now, let's use SNS from AWS Lambda. In the usual way, navigate to your AWS Lambda console.

![](https://i.imgur.com/LBphu6i.png)

- Choose the function that was developed in Lab 10 that reacts to changes in your S3 storage...

![](https://i.imgur.com/b5cQEU7.png)

- ... and click "Actions" -> "View details".

![](https://i.imgur.com/ugXE01I.png)

- Using the panels on the "Designer", check your function's code and S3 configuration:
    - Using Lambda's "Test" button, and then by using S3 bucket modifications, ensure that your function is working as expected. However, note that this form of testing appears not to trigger the SNS destination, and just runs the function itself.

:::success
Suggested exercise: Extend your Lambda function so that it disseminates a notification containing relevant data when it has run. For example, when an object is added to the S3 bucket that triggers this Lambda function, the SNS could note the S3 object name and its filetype.

**NOTE**: It may be necessary to change the execution role of your Lambda function. I created a new `myLambdaRole` and was then able to allow the SNS target to add permissions to this role to access SNS.
:::

:::success
Suggested exercise: Look up how to interact with SNS using the `aws` command line, and scripting such as `boto3`. (Scripting using `boto3` will be very similar to the code used within your Lambda function, but for the need to manage your credentials differently.)
:::

:::success
Suggested exercise: Reverse the earlier example by developing an AWS Lambda function that subscribes to an SNS topic, rather than generating a publication.
:::