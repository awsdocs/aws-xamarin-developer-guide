.. Copyright 2010-2016 Amazon.com, Inc. or its affiliates. All Rights Reserved.

   This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0
   International License (the "License"). You may not use this file except in compliance with the
   License. A copy of the License is located at http://creativecommons.org/licenses/by-nc-sa/4.0/.

   This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND,
   either express or implied. See the License for the specific language governing permissions and
   limitations under the License.

.. highlight:: csharp

########################
Set Up the |sdk-xamarin|
########################

To get started with the |sdk-xamarin|, you can set up the SDK and start building a new project, or
you can integrate the SDK with an existing project. You can also clone and run the `samples
<https://github.com/awslabs/aws-sdk-net-samples/tree/master/XamarinSamples>`_ to get a sense of how
the SDK works.

Prerequisites
=============

Before you can get started using the |sdk-xamarin|, you must do the following:

- Create an `An AWS Account <http://aws.amazon.com/>`_
- Install the `Xamarin platform <https://xamarin.com/>`_
- **Windows users**: Complete `Xamarin: Getting Started for Windows <http://developer.xamarin.com/guides/cross-platform/windows/>`_
- **Mac users**: Complete `Xamarin: Getting Started for Mac <http://developer.xamarin.com/guides/mac/getting_started/installation/>`_

After completing the prerequisites, do the following to get started:

#. Obtain AWS credentials using Amazon Cognito.
#. Set the required permissions for each AWS service that you will use in your application.
#. Create a new project in your IDE.
#. Install the |sdk-xamarin|.
#. Configure the |sdk-xamarin|.

Step 1: Obtain AWS Credentials
==============================

To make calls to AWS in your application, you must first obtain AWS credentials. You do this using
Amazon Cognito, an AWS service that allows your application to access the services in the SDK
without having to embed your private AWS credentials in your application.

To get started with Amazon Cognito, you need to create an identity pool. An identity pool is a store
of information that is specific to your account and is identified by a unique identity pool ID that
looks like this::

  "us-east-1:00000000-0000-0000-0000-000000000000"

To create an identity pool:

#. Log in to the `Amazon Cognito Console <https://console.aws.amazon.com/cognito/home>`_ and click
   :guilabel:`Create new identity pool`.

#. Enter a name for your identity pool and select the checkbox to enable access to unauthenticated
   identities. Click :guilabel:`Create Pool` to create your identity pool.

#. Click :guilabel:`Allow` to create the two default roles associated with your identity pool--one
   for unauthenticated users and one for authenticated users. These default roles provide your
   identity pool access to Cognito Sync and Mobile Analytics.

Typically, you will only use one identity pool per application.

With your identity pool created, you can obtain AWS credentials by creating a CognitoAWSCredentials
object (passing it your identity pool id) and then passing it to the constructor of an AWS client
like so::

    CognitoAWSCredentials credentials = new CognitoAWSCredentials (
        "us-east-1:00000000-0000-0000-0000-000000000000", // Your identity pool ID
        RegionEndpoint.USEast1 // Region
    );

    //example for Mobile Analytics
    analyticsManager = MobileAnalyticsManager.GetOrCreateInstance(
      credentials,
      RegionEndpoint.USEast1, // Region
      APP_ID // app id
    );

Step 2: Set Permissions
=======================

You need to set permissions for every AWS service that you want to use in your application. First,
we need to understand how AWS views the users of your application.

When someone uses your application and makes calls to AWS, that end user is assigned an identity by
AWS. The identity pool that we created in the previous step is where AWS stores these identities.
There are two types of identities: authenticated and unauthenticated. Authenticated identities
belong to users who are authenticated by a public login provider (e.g. Facebook, Amazon, Google).
Unauthenticated identities belong to guest users.

Every identity is associated with an Identity and Access Management role, or "IAM role" for short.
In the previous step, you created two IAM roles, one for authenticated users and one for
unauthenticated users. Every IAM role has a policy or policies attached to it that specify which AWS
services the identities assigned to that role can access. For example, the following sample policy
grants access to an S3 bucket::

    {
      "Statement": [
        {
          "Action": [
            "s3:AbortMultipartUpload",
            "s3:DeleteObject",
            "s3:GetObject",
            "s3:PutObject"
          ],
          "Effect": "Allow",
          "Resource": "arn:aws:s3:::MYBUCKETNAME/*",
          "Principal": "*"
        }
      ]
    }

To set permissions for the AWS services that you want to use in your application, simply modify the
policy attached to the roles:

#. Go to the `IAM Console > Roles <https://console.aws.amazon.com/iam/home>`_. Type your identity
   pool name into the search box. Choose the IAM role that you want to configure. If your
   application allows both authenticated and unauthenticated users, you will need to grant
   permissions for both roles.

#. Click :guilabel:`Attach Policy`, select your desired policy, and then click :guilabel:`Attach
   Policy`. The default policies for the IAM roles that we created provide access to Cognito Sync
   and Mobile Analytics.

For more information about creating policies or to choose from a list of existing policies, see `IAM
Policies`_.

Step 3: Create a New Project
============================

Windows
-------

You can use either Visual Studio or Xamarin Studio to develop your application.

OS X
----

You must use the Xamarin Studio IDE to develop your applications. iOS development using Xamarin
requires access to a Mac to run your app. For more information, see `Installing Xamarin.iOS on
Windows <http://developer.xamarin.com/guides/ios/getting_started/installation/windows>`_.

Step 4: Install the |sdk-xamarin|
=================================

Windows
-------

Option 1: Install using the Package Manager console
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The |sdk-xamarin| consists a set of .NET assemblies. To install the |sdk-xamarin|, run the
install-package command for each package in the Package Manager console. For example, to install
Cognito Identity, run::

  Install-Package AWSSDK.CognitoIdentity

The AWS Core Runtime and Amazon Cognito Identity packages are required for all projects. Below is a
full list of package names for each service:

====================================== =======================================
Service                                Package name
====================================== =======================================
AWS Core Runtime                       AWSSDK.Core
Amazon Cognito Sync Manager            AWSSDK.SyncManager
Amazon Cognito Identity                AWSSDK.CognitoIdentity
Amazon DynamoDB                        AWSSDK.DynamoDBv2
Amazon Mobile Analytics                AWSSDK.MobileAnalytics
Amazon S3                              AWSSDK.S3
Amazon SNS                             AWSSDK.SimpleNotificationService
====================================== =======================================

To include a prerelease package, include the -Pre command line argument while installing the package
like so::

  Install-Package AWSSDK.SyncManager -Pre

You can find a complete list of AWS service packages at `AWS SDK packages on NuGet
<https://www.nuget.org/packages?q=+aws-sdk-v3>`_ or at the `AWS SDK for .NET Github Repository
<https://github.com/aws/aws-sdk-net#nuget-packages>`_.

Option 2: Install using your IDE
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**If using Visual Studio:**

#. Right-click on the project, and then click **Manage NuGet Packages...**

#. In the window that appears, search for the package name that you want to add to your project. To
   include the prelease NuGet packages, select **Include Prelease**. You can find a complete list of
   AWS service packages at `AWS SDK packages on NuGet
   <https://www.nuget.org/packages?q=+aws-sdk-v3>`_.

#. Select the package and click **Install**.

**If using Xamarin Studio:**

#. Right-click on the packages folder, and then click **Add Packages...**

#. In the **Add Packages** window, search for the package name that you want to add to your project.
   To include the prelease NuGet packages, select the **Show pre-release packages** checkbox. You
   can find a complete list of AWS service packages at `AWS SDK packages on NuGet
   <https://www.nuget.org/packages?q=+aws-sdk-v3>`_.

#. Select the checkbox next to the desired package, and then click **Add Package**.

Mac (OS X)
----------

In Xamarin Studio:

#. Right-click on the packages folder, and then click **Add Packages...**

#. In the **Add Packages** window, search for the package name that you want to add to your project.
   To include the prelease NuGet packages, select the **Show pre-release packages** checkbox. You
   can find a complete list of AWS service packages at `AWS SDK packages on NuGet
   <https://www.nuget.org/packages?q=+aws-sdk-v3>`_.

#. Select the checkbox next to the desired package, and then click **Add Package**.

.. important:: If you are developing using a Portable Class Library, you must also add the
   AWSSDK.Core NuGet package to all projects deriving from the Portable Class Library.

Step 5: Configure the |sdk-xamarin|
===================================

Set Logging
-----------

You can configure logging settings like so::

  var loggingConfig = AWSConfigs.LoggingConfig;
  loggingConfig.LogMetrics = true;
  loggingConfig.LogResponses = ResponseLoggingOption.Always;
  loggingConfig.LogMetricsFormat = LogMetricsFormatOption.JSON;
  loggingConfig.LogTo = LoggingOptions.SystemDiagnostics;

When you log to SystemDiagnostics, the framework internally prints the output to the System.Console.
If you want to log HTTP responses, set the LogResponses flag. The values can be Always, Never, or
OnError.

You can also log performance metrics for HTTP requests using the LogMetrics property, the log format
can be specified using LogMetricsFormat property, valid values are JSON or standard.

Set the Region Endpoint
------------------------

To configure the default region for all service clients::

  AWSConfigs.AWSRegion="us-east-1";

This sets the default region for all the service clients in the SDK. This setting can be overridden
by explicitly specifying the region at the time of creating an instance of the service client, like
so::

  IAmazonS3 s3Client = new AmazonS3Client(credentials,RegionEndpoint.USEast1);

Configure the HTTP Proxy Settings
---------------------------------

If your network is behind a proxy, you can configure the proxy settings for the HTTP requests as
follows::

  var proxyConfig = AWSConfigs.ProxyConfig;
  proxyConfig.Host = "localhost";
  proxyConfig.Port = 80;
  proxyConfig.Username = "<username>";
  proxyConfig.Password = "<password>";

Correct for Clock Skew
----------------------

This property determines if the SDK should correct for client clock skew by determining the correct
server time and reissuing the request with the correct time.

::

  AWSConfigs.CorrectForClockSkew = true;

This field will be set if a service call resulted in an exception and the SDK has determined that
there is a difference between local and server times.

::

  var offset = AWSConfigs.ClockOffset;

To learn more about clock skew, see `Clock-skew Correction
<https://blogs.aws.amazon.com/net/post/Tx2HM54KL5LMTGI/Clock-skew-correction>`_ on the AWS Blog.

Next Steps
==========

Now that you have the |sdk-xamarin| set up, you can:

- **Get Started**: Read :doc:`getting-started-xamarin` to see quick-start instructions on how to use
  and configure the services in the |sdk-xamarin|.

- **Explore the Service Topics**: Read about each service in the |sdk-xamarin| and learn how each
  service works in greater detail.

- **Run the demos**: View our `sample Xamarin applications
  <https://github.com/awslabs/aws-sdk-net-samples/tree/master/XamarinSamples>`_ that demonstrate
  common use cases. To run the sample apps, set up the |sdk-xamarin| as described above, and then
  follow the instructions contained in the README files of the individual samples.

- **Read the API Reference**: View the `API Reference
  <http://docs.aws.amazon.com/sdkfornet/v3/apidocs/Index.html>`_ for the |sdk-xamarin|.

- **Ask questions**: Post questions on the `AWS Mobile SDK Forums
  <https://forums.aws.amazon.com/forum.jspa?forumID=88>`_ or `open an issue on Github
  <https://github.com/awslabs/aws-sdk-xamarin/issues>`_.

.. _IAM Policies: http://docs.aws.amazon.com/IAM/latest/UserGuide/PoliciesOverview.html
