---
tags: cosc349
---
# COSC349 Lab 11—Cloud Architecture—2019
## Lab 11—AWS Simple Notification Service

[gitsurvey]: https://forms.office.com/Pages/ResponsePage.aspx?id=xe8lAv54KEmxV57ySAnpuqui8exv8e1Ps7Lrl2YV1z1UN0wzNVNHWlhLUVJRQzhQQ0kyUUVQNDBHOS4u
[Lab 12]: /aPDgQ1qBRvm_4g71edc4DA

The original goal of this lab was to investigate cloud orchestration and coordination technologies, however limitations in the Amazon Educate accounts prevented this.

Instead, we will explore the AWS Simple Notification Service (SNS), which provides a means to disseminate alerts and notifications to sets of heterogeneous targets including email and SMS messaging.

:::info
Please do consider taking a minute to complete the [`git` teaching survey][gitsurvey]. It will take even less than a minute if you don't answer all the questions! :tada:
:::

:::info
Note that to help with assignment two, there have been some techniques and tips added into the start of the [Lab 12] material.
:::

:::info
A number of students are a lab or so behind, so the extent of new material has been more specifically focused, in order for lab time to be usable for catch-up and getting assistance. There are of course many places that you can further experiment, if you are ahead in terms of the lab material. (Further specifics available on request, as always.)
:::

## Amazon Simple Notification Service (SNS)

- To interactively set up SNS, start in the AWS Management Console...

![](https://i.imgur.com/KZVfhoq.png)

- ... and navigate to the SNS console in the usual way.

![](https://i.imgur.com/4soePWT.png)

- The SNS welcome page provides a way to get started using the service. Choose a topic name, and enter it in the provided form control. Click "Next step", to proceed to the more detailed "Create topic" page.

![](https://i.imgur.com/h9emlyA.png)

- Feel free to explore the additional options, but in our test, all that is needed is to scroll to the bottom of that page and click the "Create topic" button.

![](https://i.imgur.com/Dpxmn44.png)

- Assuming all goes well, you should be returned to the "Topics" display, showing your topic's information, and displaying a green confirmation near the top of the page.
- The page indicating that you have created a topic includes a button to publish a message to that topic. Click the "Publish message" button.

![](https://i.imgur.com/qP3YieE.png)

- The message subject can be set to a value.
- For the moment choose the left-most option: to deliver identical payload data to all of the delivery protocols (e.g., different types of devices).

![](https://i.imgur.com/OZKXxfF.png)

- By scrolling down that web page you will find the message body text box.
- Enter some content that you will recognise when it is delivered.
- At the bottom of the page find and click the "Publish message".

![](https://i.imgur.com/lB3KKWU.png)

- You will be returned to the "Topics" page, with a confirmation banner displayed, if all went ell.
- Select the "Dashboard" link on the left-side menu.

![](https://i.imgur.com/OKyf1du.png)

- The dashboard indicates that within the `us-east-1` region, this account has one topic registered, but no applications or subscriptions.
- Publishing a message to a topic that has no subscribers will not achieve any visible effect.
- Click on the "Subscriptions" left-side menu option.

![](https://i.imgur.com/WhbxKxI.png)

- Now "Create subscription" to provide a target for publications to reach.

![](https://i.imgur.com/4JLMnGk.png)

- Select the ARN from the pull-down menu that refers to the topic that you created.
- Select "Email" as the protocol.
- Enter an email address that you control (i.e., not mine, as shown in this screen capture).

![](https://i.imgur.com/iq0BJrW.png)

- You will be returned to the "Subscriptions" window, which will indicate the endpoint and the status, which will be "Pending confirmation".
- Meanwhile, a confirmation request will have been emailed to the address that you enter, and thus you should be able to visit the URL contained within that email message.

![](https://i.imgur.com/dcImBGs.png)

- A confirmation such as the above screen-capture should be presented.
- If you return to the "Subscriptions" page...

![](https://i.imgur.com/wnllBfW.png)

- ...you should see that the status has changed to "Confirmed".
- Now select the "Push notifications" left-menu item.

![](https://i.imgur.com/pMtVAiy.png)

- This lab and COSC349 do not go into the details of mobile push notifications, but recall that they are discussed in the XaaS and Middleware / MBaaS lectures.
- Instead, select the "Text messaging (SMS)" left-menu item.

![](https://i.imgur.com/klKZ1qj.png)

- Select "Subscribe number to topic".

![](https://i.imgur.com/8mckrdb.png)

- You will be returned to the "Create subscription" display, but this type with the "Protocol" changed to "SMS".
- Enter your mobile number, or that of someone who will want to receive your message and will be willing to confirm the subscription.
- Return to the "Topics" left-side menu.

![](https://i.imgur.com/FPoaaB6.png)

- Select your test topic from the list of topics, and click the "Publish message" button.

![](https://i.imgur.com/YoTks6g.png)

- You can select an appropriate subject...

![](https://i.imgur.com/DcQ1S7n.png)

- ... and body-text content.

![](https://i.imgur.com/Yb2RBjw.png)

- For your interest, select the "Custom payload for each delivery protocol." radio button, and note the JSON that appears as the message body.
- Some of the delivery protocols have additional required fields, beyond the body-text itself.

![](https://i.imgur.com/M9H6imW.png)

- Once you select "Publish message", you should now receive content both by email and on your phone via SMS.

:::info
You have now used the main elements of SNS. Let's integrate use of SNS into AWS Lamda.
:::

## Linking SNS to AWS Lambda

- Now, let's use SNS from AWS Lambda. In the usual way, navigate to your AWS Lambda console.

![](https://i.imgur.com/QgxsuI4.png)

- Choose the function that was developed in Lab 10 that reacts to changes in your S3 storage...

![](https://i.imgur.com/kyfNDOE.png)

- ... and click "Activities" -> "View details".

![](https://i.imgur.com/Cp4d7cp.png)

- Using the panels on the "Designer", check your function's code and S3 configuration:
    - Using Lambda's "Test" button, and then by using S3 bucket modifications, ensure that your function is working as expected.

:::success
Exercise: Extend your Lambda function so that it disseminates a notification containing relevant data when it has run. For example, when an object is added to the S3 bucket that triggers this Lambda function, the SNS could note the S3 object name and its filetype.
:::

:::success
Exercise: Look up how to interact with SNS using the `aws` command line, and scripting such as `boto3`. (Scripting using `boto3` will be very similar to the code used within your Lambda function, but for the need to manage your credentials differently.)
:::

:::success
Exercise: Reverse the earlier example by developing an AWS Lambda function that subscribes to an SNS topic, rather than generating a publication.
:::