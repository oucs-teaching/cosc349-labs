---
title: COSC349 Lab 10—Cloud Architecture—2026
tags: [cosc349, lab]

---

## Lab 10—AWS Simple Notification Service
<!-- Screen captures of 1080x820 -->

In this lab we will explore the AWS Simple Notification Service (SNS), which provides a means to disseminate alerts and notifications to sets of targets including through the use of email and SMS messaging.

:::info
:hourglass_flowing_sand: 
Generally by this time in semester everyone is very busy, so the extent of new material has been more specifically focused, in order for lab time to be usable for catch-up and getting assistance. There are of course many places that you can further experiment, if you are ahead in terms of the lab material. (Further specifics available on request, as always.)
:::

## Amazon Simple Notification Service (SNS)

- To interactively set up SNS, start in the AWS Management Console...

![image](https://hackmd.io/_uploads/BkBat9qFzg.png)

- ... and navigate to the SNS console in the usual way. (Note that if you get taken to the SNS Dashboard, you can use the "breadcrumbs" to return to the top-level SNS web page shown in the following screen capture.)

![image](https://hackmd.io/_uploads/HJE-q9cFGx.png)

- The SNS welcome page provides a way to get started using the service. Choose a topic name, and enter it in the provided form control. Click "Next step", to proceed to the more detailed "Create topic" page.

![image](https://hackmd.io/_uploads/SydU9q9Ffg.png)

- Feel free to explore the additional options, but in our test, all that is needed is to scroll to the bottom of that page and click the "Create topic" button.

![image](https://hackmd.io/_uploads/Hym_99qFMg.png)

- Assuming all goes well, you should be returned to the "Topics" display, showing your topic's information, and displaying a green confirmation near the top of the page.
- The page indicating that you have created a topic includes a button to publish a message to that topic. Click the "Publish message" button.
- The message subject can be set to a value.
- Note that I once received a page such as the following that won't work because the ARN is "undefined". 

![image](https://hackmd.io/_uploads/r1dlj9qFMe.png)

- I just returned to the Topics using the web page's breadcrumbs and then navigated to the publish message page a second time and it looked as follows:

![image](https://hackmd.io/_uploads/HJ2l395YGx.png)

- For the moment choose the left-most option regarding "Message structure": to deliver identical payload data to all of the delivery protocols (e.g., different types of devices).
- By scrolling down that web page you will find the message body text box.
- Enter some content that you will recognise when it is delivered.

![image](https://hackmd.io/_uploads/ByaX399FGg.png)

- At the bottom of the page find and click the "Publish message".
- You will be returned to the "Topics" page, with a confirmation banner displayed, if all went well.

![image](https://hackmd.io/_uploads/BkpE399Fzl.png)

- Select the "Dashboard" link on the left-side menu.

![image](https://hackmd.io/_uploads/SJRShqcFGe.png)

- The dashboard indicates that within the `us-east-1` region, this account has a topic registered, but no applications or subscriptions. (In my case I also have some other topic that I didn't define, giving a total of two, and you may or may not have the same total in your AWS Academy account.)
- Publishing a message to a topic that has no subscribers will not achieve any visible effect.
- Click on the "Subscriptions" left-side menu option.

![image](https://hackmd.io/_uploads/ByKQ2icFfg.png)

- Now "Create subscription" to provide a target for publications to reach.
- Select the ARN from the pull-down menu that refers to the topic that you created.
- Select "Email" as the protocol.
- Enter an email address that you control (i.e., not mine, even though it is shown in the screen capture).

![image](https://hackmd.io/_uploads/HJ1Lhj5FMe.png)

- Click the "Create subscription" button at the bottom of that page.
- You will be returned to the "Subscriptions" page, which will indicate the endpoint and the status, which will be "Pending confirmation".

![image](https://hackmd.io/_uploads/HycIhs9Kfe.png)

- Meanwhile, a confirmation request will have been emailed to the address that you entered, and thus you should be able to visit the URL contained within that email message.
- A confirmation such as the following screen-capture should be presented.

![image](https://hackmd.io/_uploads/BJTt3j9KGg.png)

- If you return to the "Subscriptions" page...

![image](https://hackmd.io/_uploads/r1ys3o9YGx.png)

- ...you should see that the status has changed to "Confirmed".
- Now, just for your interest, select the "Push notifications" left-menu item.

![image](https://hackmd.io/_uploads/rybo7fzkJg.png)

- This lab and COSC349 do not go into the details of mobile push notifications, but recall that they are discussed in the Middleware / MBaaS lectures.
- In the past, and on your own AWS accounts, you can send SMS messages, although AWS Academy has recently disabled this feature.
- You can examine the "Text messaging (SMS)" left-menu item if you are interested.

![image](https://hackmd.io/_uploads/SJ7T2i5tzx.png)

:::info
:heavy_check_mark: 
You have now used the main elements of SNS. Let's integrate use of SNS into AWS Lamda.
:::

## Linking SNS to AWS Lambda

- Now, let's use SNS from AWS Lambda. In the usual way, navigate to your AWS Lambda console.

![image](https://hackmd.io/_uploads/HkoJTo5FGx.png)

- Choose the function that was developed in the lab that reacts to changes in your S3 storage...

![image](https://hackmd.io/_uploads/BJnW6s5KGe.png)

- Click "Add destination":

![image](https://hackmd.io/_uploads/B1nL6icYfe.png)

- Change the invocation to be "On success"
- Ensure the "Destination type" is "SNS topic"
- Select the "Destination ARN" to match your SNS topic.

![image](https://hackmd.io/_uploads/H1g5ai5tGg.png)

- You should now see your SNS destination on the Lambda designer view.

![image](https://hackmd.io/_uploads/B1riTocKMx.png)

- Scroll down and activate the "Test" tab.

![image](https://hackmd.io/_uploads/BkK0aicYzl.png)

- Invoke a test event to ensure that your configuration is working.
    - Note, however, that (at least for me) this testing within AWS Lambda does not trigger the SNS destination, and just runs the Lambda function itself.

![image](https://hackmd.io/_uploads/rykxCi5Yfe.png)

- Make a modification to your S3 bucket to trigger the AWS Lambda function that itself will generate an SNS notification that should get emailed to you.

:::success
:pencil: 
Suggested exercise: Extend your Lambda function so that it disseminates a notification containing relevant data when it has run. For example, when an object is added to the S3 bucket that triggers this Lambda function, the SNS could note the S3 object name and its filetype.
:::

:::success
:pencil: 
Suggested exercise: Look up how to interact with SNS using the `aws` command line, and scripting such as `boto3`. (Scripting using `boto3` will be very similar to the code used within your Lambda function, but for the need to manage your credentials differently.)
:::

:::success
:pencil: 
Suggested exercise: Reverse the earlier example by developing an AWS Lambda function that subscribes to an SNS topic, rather than generating a publication.
:::## Lab 10—AWS Simple Notification Service
<!-- Screen captures of 1080x820 -->

In this lab we will explore the AWS Simple Notification Service (SNS), which provides a means to disseminate alerts and notifications to sets of targets including through the use of email and SMS messaging.

:::info
:hourglass_flowing_sand: 
Generally by this time in semester everyone is very busy, so the extent of new material has been more specifically focused, in order for lab time to be usable for catch-up and getting assistance. There are of course many places that you can further experiment, if you are ahead in terms of the lab material. (Further specifics available on request, as always.)
:::

## Amazon Simple Notification Service (SNS)

- To interactively set up SNS, start in the AWS Management Console...

![image](https://hackmd.io/_uploads/BkBat9qFzg.png)

- ... and navigate to the SNS console in the usual way. (Note that if you get taken to the SNS Dashboard, you can use the "breadcrumbs" to return to the top-level SNS web page shown in the following screen capture.)

![image](https://hackmd.io/_uploads/HJE-q9cFGx.png)

- The SNS welcome page provides a way to get started using the service. Choose a topic name, and enter it in the provided form control. Click "Next step", to proceed to the more detailed "Create topic" page.

![image](https://hackmd.io/_uploads/SydU9q9Ffg.png)

- Feel free to explore the additional options, but in our test, all that is needed is to scroll to the bottom of that page and click the "Create topic" button.

![image](https://hackmd.io/_uploads/Hym_99qFMg.png)

- Assuming all goes well, you should be returned to the "Topics" display, showing your topic's information, and displaying a green confirmation near the top of the page.
- The page indicating that you have created a topic includes a button to publish a message to that topic. Click the "Publish message" button.
- The message subject can be set to a value.
- Note that I once received a page such as the following that won't work because the ARN is "undefined". 

![image](https://hackmd.io/_uploads/r1dlj9qFMe.png)

- I just returned to the Topics using the web page's breadcrumbs and then navigated to the publish message page a second time and it looked as follows:

![image](https://hackmd.io/_uploads/HJ2l395YGx.png)

- For the moment choose the left-most option regarding "Message structure": to deliver identical payload data to all of the delivery protocols (e.g., different types of devices).
- By scrolling down that web page you will find the message body text box.
- Enter some content that you will recognise when it is delivered.

![image](https://hackmd.io/_uploads/ByaX399FGg.png)

- At the bottom of the page find and click the "Publish message".
- You will be returned to the "Topics" page, with a confirmation banner displayed, if all went well.

![image](https://hackmd.io/_uploads/BkpE399Fzl.png)

- Select the "Dashboard" link on the left-side menu.

![image](https://hackmd.io/_uploads/SJRShqcFGe.png)

- The dashboard indicates that within the `us-east-1` region, this account has a topic registered, but no applications or subscriptions. (In my case I also have some other topic that I didn't define, giving a total of two, and you may or may not have the same total in your AWS Academy account.)
- Publishing a message to a topic that has no subscribers will not achieve any visible effect.
- Click on the "Subscriptions" left-side menu option.

![image](https://hackmd.io/_uploads/ByKQ2icFfg.png)

- Now "Create subscription" to provide a target for publications to reach.
- Select the ARN from the pull-down menu that refers to the topic that you created.
- Select "Email" as the protocol.
- Enter an email address that you control (i.e., not mine, even though it is shown in the screen capture).

![image](https://hackmd.io/_uploads/HJ1Lhj5FMe.png)

- Click the "Create subscription" button at the bottom of that page.
- You will be returned to the "Subscriptions" page, which will indicate the endpoint and the status, which will be "Pending confirmation".

![image](https://hackmd.io/_uploads/HycIhs9Kfe.png)

- Meanwhile, a confirmation request will have been emailed to the address that you entered, and thus you should be able to visit the URL contained within that email message.
- A confirmation such as the following screen-capture should be presented.

![image](https://hackmd.io/_uploads/BJTt3j9KGg.png)

- If you return to the "Subscriptions" page...

![image](https://hackmd.io/_uploads/r1ys3o9YGx.png)

- ...you should see that the status has changed to "Confirmed".
- Now, just for your interest, select the "Push notifications" left-menu item.

![image](https://hackmd.io/_uploads/rybo7fzkJg.png)

- This lab and COSC349 do not go into the details of mobile push notifications, but recall that they are discussed in the Middleware / MBaaS lectures.
- In the past, and on your own AWS accounts, you can send SMS messages, although AWS Academy has recently disabled this feature.
- You can examine the "Text messaging (SMS)" left-menu item if you are interested.

![image](https://hackmd.io/_uploads/SJ7T2i5tzx.png)

:::info
:heavy_check_mark: 
You have now used the main elements of SNS. Let's integrate use of SNS into AWS Lamda.
:::

## Linking SNS to AWS Lambda

- Now, let's use SNS from AWS Lambda. In the usual way, navigate to your AWS Lambda console.

![image](https://hackmd.io/_uploads/HkoJTo5FGx.png)

- Choose the function that was developed in the lab that reacts to changes in your S3 storage...

![image](https://hackmd.io/_uploads/BJnW6s5KGe.png)

- Click "Add destination":

![image](https://hackmd.io/_uploads/B1nL6icYfe.png)

- Change the invocation to be "On success"
- Ensure the "Destination type" is "SNS topic"
- Select the "Destination ARN" to match your SNS topic.

![image](https://hackmd.io/_uploads/H1g5ai5tGg.png)

- You should now see your SNS destination on the Lambda designer view.

![image](https://hackmd.io/_uploads/B1riTocKMx.png)

- Scroll down and activate the "Test" tab.

![image](https://hackmd.io/_uploads/BkK0aicYzl.png)

- Invoke a test event to ensure that your configuration is working.
    - Note, however, that (at least for me) this testing within AWS Lambda does not trigger the SNS destination, and just runs the Lambda function itself.

![image](https://hackmd.io/_uploads/rykxCi5Yfe.png)

- Make a modification to your S3 bucket to trigger the AWS Lambda function that itself will generate an SNS notification that should get emailed to you.

:::success
:pencil: 
Suggested exercise: Extend your Lambda function so that it disseminates a notification containing relevant data when it has run. For example, when an object is added to the S3 bucket that triggers this Lambda function, the SNS could note the S3 object name and its filetype.
:::

:::success
:pencil: 
Suggested exercise: Look up how to interact with SNS using the `aws` command line, and scripting such as `boto3`. (Scripting using `boto3` will be very similar to the code used within your Lambda function, but for the need to manage your credentials differently.)
:::

:::success
:pencil: 
Suggested exercise: Reverse the earlier example by developing an AWS Lambda function that subscribes to an SNS topic, rather than generating a publication.
:::