# Amazon Pinpoint With .NET Core

Web Apis for amazon pinpoint text, voice message and email with .net core 2.2

# Send Email Using Amazon Pinpoint With C#

Prerequisite: Visual Studio, AWS Account, Basic Knowledge of C# Programming.

This blog is extension of Send SMS Using Amazon Pinpoint With C#, so I will not explain step by step details in this blog.

In my previous blog, I explained how to set up Amazon Pinpoint service and code for C# to be able to send SMS (Text Message), now I will explain how to set up C# application to send Email.

Sending Email can be helpful in many ways but we should not spam the users. 😉

Please refer to Send SMS Using Amazon Pinpoint With C# until STEP 26.

Additional Point: Go to Test Message from Navigation Pane in Pinpoint Project, test Email and check if you are receiving an email or not.

You just need to create new policy under IAM section and copy following JSON code in the JSON Editor.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogStream",
                "logs:PutLogEvents",
                "logs:CreateLogGroup"
            ],
            "Resource": "arn:aws:logs:*:*:*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "mobiletargeting:UpdateEndpoint",
                "mobiletargeting:GetEndpoint"
            ],
            "Resource": "arn:aws:mobiletargeting:region:955506310031:apps/fc7a54d1dac84288ad87e9b0dc86c554/endpoints/*"
        }
    ]
}

```

Above JSON code will give you permission to send email. you can refer to Amazon Pinpoint Developer Guide for changes there but for this tutorial, you can tweak JSON in order to get your hands dirty with permissions.

Once this policy is ready, next step is to attach the policy to the group (group that you created in STEP 16)

Next you will need to create new project (Console App) and give some name like PinpointEmailClient.

Navigate to Nuget Package Manager and install AWSSDK.Pinpoint.


Required Nuget Package for sending Email.
Following C# code is the template to set up the application to send Email.

```
using System;
using Amazon;
using System.Collections.Generic;
using Amazon.Runtime;
using Amazon.Pinpoint;
using Amazon.Pinpoint.Model;
using System.Threading.Tasks;
namespace AWSPinpointEmailDemo
{
class Program
{
private static readonly string awsKeyId = ""; //Access Key For USer.
private static readonly string awsKeySecret = ""; //Secret Access Key For User.
private static readonly string region = "us-east-1"; //Closest Supported Region to you
private static readonly string appId = ""; //Pinpoint application unique id.
static string senderAddress = ""; //Verified email address with Amazon Pinpoint.
static string toAddress = ""; //You have to upgrade amazon pinpoint limits to be able to send email to unverified email address.
static string subject = "Amazon Pinpoint Email test";
static string textBody = @"Amazon Pinpoint Email Test (.NET)
---------------------------------
This email was sent using the Amazon Pinpoint API using the AWS SDK for .NET.";
static string htmlBody = @"<html>
<head></head>
<body>
<h1>Amazon Pinpoint Email Test (AWS SDK for .NET)</h1>
<p>This email was sent using the
<a href='https://aws.amazon.com/pinpoint/'>Amazon Pinpoint</a> API
using the <a href='https://aws.amazon.com/sdk-for-net/'>
AWS SDK for .NET</a>.</p>
</body>
</html>";
static string charset = "UTF-8";
static void Main(string[] args)
{
try
{
Console.WriteLine("Sending message...");
Task.Run(() => SendEmail().Wait());
Console.WriteLine("Message sent!");
}
catch (Exception ex)
{
Console.WriteLine("The message wasn't sent. Error message: " + ex.Message);
}
finally
{
Console.ReadKey();
}
}
private static async Task SendEmail()
{
var awsCredentials = new BasicAWSCredentials(awsKeyId, awsKeySecret);
using (var client = new AmazonPinpointClient(awsCredentials, RegionEndpoint.GetBySystemName(region)))
{
var sendRequest = new SendMessagesRequest
{
ApplicationId = appId,
MessageRequest = new MessageRequest
{
Addresses = new Dictionary<string, AddressConfiguration>
{
{
toAddress,
new AddressConfiguration
{
ChannelType = "EMAIL"
}
}
},
MessageConfiguration = new DirectMessageConfiguration
{
EmailMessage = new EmailMessage
{
FromAddress = senderAddress,
SimpleEmail = new SimpleEmail
{
HtmlPart = new SimpleEmailPart
{
Charset = charset,
Data = htmlBody
},
TextPart = new SimpleEmailPart
{
Charset = charset,
Data = textBody
},
Subject = new SimpleEmailPart
{
Charset = charset,
Data = subject
}
}
}
}
}
};
SendMessagesResponse response = await client.SendMessagesAsync(sendRequest);
}
}
}
}

```

In the above code, as you can see I am using AmazonPinpointClient to send Email just like SMS.

You can further add functionality to send attachment using raw email instead of simple email body and also you can add functionality to know whether email is delivered or not, I am putting link down below for Amazon Pinpoint API with C#, feel free to add functionalities in that repository or also feel free to use that code for your projects.

Amazon Pinpoint With C#.

Let me know if you face any issues with this tutorial or if your application is not able to send Email, write down in the response section. Claps if you like it, follow if you love it and feel free to leave feedback or any questions or any suggestion in the response section and also let me know if you want me to write blog on some specific topic.
