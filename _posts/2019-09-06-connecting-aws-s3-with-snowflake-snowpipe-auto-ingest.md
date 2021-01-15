---
id: 123
title: 'Connecting AWS S3 with Snowflake: SnowPipe Auto-Ingest'
date: 2019-09-06T14:22:13+01:00
author: waz
layout: post
fabulist-selected-sidebar:
  - sidebar-1
image: /wp-content/uploads/2019/09/Snowflake-600x650.jpeg
---
Working at a big retail client in the UK, we had a problem where had some very sensitive data in S3 buckets, and we needed to move that data into SnowFlake without a person having access, and being able to see that data.

Many people drew big diagrams including multiple Lambdas, SQS, SNS and other AWS features to make this work, however we started looking at a new feature called &#8220;Auto Ingest&#8221;.

This is how we set it up.

## Setup {#S3->Snowflake(SNOWPIPEAuto-Ingest)Guide-Setup}

The setup for this may seem straightforward, however there are some constraints within our organisation&#8217;s configuration that makes this somewhat more complicated. Therefore this guide serves to detail how to set up an Auto Ingest pipeline.

Firstly, it&#8217;s important to conceptualise how this works. In this setup, we are actually using the S3 bucket as a staging area. It&#8217;s referred to as an &#8220;external stage&#8221;.

We then create notifications via SQS from that bucket to a table using a pipe.<figure class="wp-block-image">

![](https://sainsburys-confluence.valiantys.net/download/attachments/61547802/HowSnowpipeWorks.png?version=1&modificationDate=1567762991000&api=v2) </figure> 

### Table {#S3->Snowflake(SNOWPIPEAuto-Ingest)Guide-Table}

To create the table, we first need to run:<figure class="wp-block-table">

<table class="">
  <tr>
    <td>
      12345
    </td>
    
    <td>
      <code>USE&nbsp;SCHEMA</code>&nbsp;<code>GDPR_DATA;</code>&nbsp;<code>CREATE</code>&nbsp;<code>OR</code>&nbsp;<code>REPLACE</code>&nbsp;<code>TABLE</code>&nbsp;<code>TestGDPRTable(DATA variant);</code>
    </td>
  </tr>
</table></figure> 

You can then confirm the table was created with<figure class="wp-block-table">

<table class="">
  <tr>
    <td>
      1
    </td>
    
    <td>
      <code>SELECT</code>&nbsp;<code>*&nbsp;FROM</code>&nbsp;<code>GDPR_DATA.TestGDPRTable;</code>
    </td>
  </tr>
</table></figure> 

### Stage {#S3->Snowflake(SNOWPIPEAuto-Ingest)Guide-Stage}

We then need to create the stage. This is essentially telling Snowflake to use an S3 bucket as a stage:<figure class="wp-block-table">

<table class="">
  <tr>
    <td>
      1234
    </td>
    
    <td>
      <code>CREATE</code>&nbsp;<code>OR</code>&nbsp;<code>REPLACE</code>&nbsp;<code>STAGE TestGDPRStage url='s3://gdpr-data-holding/'credentials = (aws_role =&nbsp;'arn:aws:iam::0000:role/snowflake_access_role')encryption=(type='AWS_SSE_KMS'</code>&nbsp;<code>kms_key_id =&nbsp;'aws/key');</code>
    </td>
  </tr>
</table></figure> 

The arn here is for a role in our AWS account containing the bucket. The role doesn&#8217;t have to actually exist yet. You can confirm the stage was created with<figure class="wp-block-table">

<table class="">
  <tr>
    <td>
      1
    </td>
    
    <td>
      <code>SHOW STAGES;</code>
    </td>
  </tr>
</table></figure> 

Note this stage wont actuallywork until we have configured security, later on. If you are unable to create it at this point, wait until you get to &#8220;Create A Role&#8221; section.

### Pipe {#S3->Snowflake(SNOWPIPEAuto-Ingest)Guide-Pipe}

We now create a pipe that transfers data from the stage to the table:<figure class="wp-block-table">

<table class="">
  <tr>
    <td>
      1234
    </td>
    
    <td>
      <code>CREATE</code>&nbsp;<code>OR</code>&nbsp;<code>REPLACE</code>&nbsp;<code>PIPE GDPR_DATA.TestGDPRPipe auto_ingest=true</code>&nbsp;<code>ascopy&nbsp;into</code> <code>GDPR_DATA.TestGDPRTable from</code>&nbsp;<code>@GDPR_DATA.TestGDPRStage file_format = (type =&nbsp;'JSON');</code>
    </td>
  </tr>
</table></figure> 

As with the stage and table, we can confirm the pipe has been created with:<figure class="wp-block-table">

<table class="">
  <tr>
    <td>
      1
    </td>
    
    <td>
      <code>SHOW PIPES;</code>
    </td>
  </tr>
</table></figure> 

### S3 Notification {#S3->Snowflake(SNOWPIPEAuto-Ingest)Guide-S3Notification}

At this point we need to create a notification on the S3 bucket to one the aforementioned Snowflake Stage. To do this log into AWS, go to your S3 bucket and under Properties configure an Event Notification:<figure class="wp-block-table">

<table class="">
  <tr>
    <td>
      <code>Name: Name of the event notification (e.g. Auto-ingest-UseCaseName).</code><br /><br /><code>Events: Select the ObjectCreate (All) option - we will need to use prefix processed_&lt;usecaseName&gt;</code><br /><br /><code>Send to: Select SQS Queue from the dropdown list.</code><br /><br /><code>SQS: Select Add SQS queue ARN from the dropdown list.</code><br /><br /><code>SQS queue ARN: Paste the SQS queue name from the SHOW PIPES output.</code>
    </td>
  </tr>
</table></figure> 

### **Security** {#S3->Snowflake(SNOWPIPEAuto-Ingest)Guide-Security}

Theoretically we can follow the guide to setting up security from Snowflake &#8211;&nbsp;<https://docs.snowflake.net/manuals/user-guide/data-load-s3-config.html>&nbsp;&#8211; However this doesn&#8217;t fully work, as even users with admin access on an account in our organisation do not have&nbsp;`iam:UpdateAssumeRolePolicy` permissions.

Therefore we must do the following, inside the AWS account with the bucket you are trying to use:

#### Create a Policy {#S3->Snowflake(SNOWPIPEAuto-Ingest)Guide-CreateaPolicy}

Under IAM create a policy named&nbsp;`snowflake_access&nbsp;`with this JSON policy document:

<pre class="wp-block-code"><code lang="json" class="language-json line-numbers">{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
              "s3:PutObject",
              "s3:GetObject",
              "s3:GetObjectVersion",
              "s3:DeleteObject",
              "s3:DeleteObjectVersion"
            ],
            "Resource": "arn:aws:s3:::&lt;bucket_name>/&lt;prefix>/*"
        },
        {
            "Effect": "Allow",
            "Action": "s3:ListBucket",
            "Resource": "arn:aws:s3:::&lt;bucket_name>",
            "Condition": {
                "StringLike": {
                    "s3:prefix": [
                        "mydata/*"
                    ]
                }
            }
        }
    ]
}</code></pre>

  
If you don&#8217;t use a prefix, you can omit the prefix from the resource name and completely remove this section

<pre class="wp-block-code"><code lang="json" class="language-json line-numbers">"Condition": {
                "StringLike": {
                    "s3:prefix": [
                        "mydata/*"
                    ]
                }
            }</code></pre>

#### Create A Role {#S3->Snowflake(SNOWPIPEAuto-Ingest)Guide-CreateARole}

Now we create a role for the snowflake user.

  * The type of trusted entity should be another AWS account. Snowflake specifically should have account ID:&nbsp;_**520339422657**_
  * You must request an external ID, and for now we must use:&nbsp;_**0000**_
  * When attaching a policy, ensure you attach the policy created in the step above.
  * The name you use for this must match the name of the role set in the stage above, so in this case &#8220;snowflake\_access\_role&#8221; will generate a role with this ARN:

<pre class="wp-block-code"><code lang="bash" class="language-bash line-numbers">arn:aws:iam::0000:role/snowflake_access_role</code></pre>

At this point if you were unable to create a stage earlier on, run the SQL for CREATE STAGE.

#### Update Role {#S3->Snowflake(SNOWPIPEAuto-Ingest)Guide-UpdateRole}

Now we must update the role with details from the STAGE:<figure class="wp-block-table">

<table class="">
  <tr>
    <td>
      1
    </td>
    
    <td>
      <code>desc</code>&nbsp;<code>stage TestPIIStage;</code>
    </td>
  </tr>
</table></figure> 

You would now normally edit the Trust Relationship of the role mentioned above, setting the values of:

  * &nbsp;_**sts:ExternalId**_&nbsp;to the value listed as&nbsp;_**AWS\_EXTERNAL\_ID**_&nbsp;in the describe stage command
  * _**AWS**_&nbsp;under&nbsp;_**Principal**_&nbsp;to the value of&nbsp;_**SNOWFLAKE\_IAM\_USER**_

&nbsp;However, even with Admin access you won&#8217;t be able to do this, therefore the best way is to create a new role, this time replacing the external ID of&nbsp;_**0000**_ with the value of&nbsp;_**AWS\_EXTERNAL\_ID.**_

According to the guide, we should also change the&nbsp;_**sts:ExternalId**_, however you can&#8217;t do this using the wizard, and therefore it will grant the principle to root. This will, however, still work. You will then have a trust relationship policy document like this:

<pre class="wp-block-code"><code lang="json" class="language-json line-numbers">{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::520339422657:root"
      },
      "Action": "sts:AssumeRole",
      "Condition": {
        "StringEquals": {
          "sts:ExternalId": "ORG_SFCRole=123_xxx"
        }
      }
    }
  ]
}</code></pre>

### Finally {#S3->Snowflake(SNOWPIPEAuto-Ingest)Guide-Finally}

At this point you should be good to go. You may need to re-run the create pipe command, as you have recreated your stage.

Note that you SHOULD NOT re-create your stage, else you will have to go through the process of applying a role policy again, with the new output from that stage.

You can attempt to list all the files in your S3 bucket:<figure class="wp-block-table">

<table class="">
  <tr>
    <td>
      1
    </td>
    
    <td>
      <code>list @TestGDPRStage;</code>
    </td>
  </tr>
</table></figure> 

You can view the status of your pipe with:<figure class="wp-block-table">

<table class="">
  <tr>
    <td>
      1
    </td>
    
    <td>
      <code>SELECT</code>&nbsp;<code>SYSTEM$PIPE_STATUS(&nbsp;'GDPR_DATA.TestGDPRPipe'</code>&nbsp;<code>);</code>
    </td>
  </tr>
</table></figure> 

The pipe will only pull files into the table from your stage if there is an SQS Notification. This means that at the point of creating all this, any files already in the bucket won&#8217;t be automatically pulled into a table. You can, however, run a command to start the process off for existing files using:<figure class="wp-block-table">

<table class="">
  <tr>
    <td>
      1
    </td>
    
    <td>
      <code>ALTER</code>&nbsp;<code>PIPE GDPR_DATA.TestGDPRPipe REFRESH;</code>
    </td>
  </tr>
</table></figure> 

In my experience, it can take a fairly long time (1+ hour) for even small files to appear in the table. After a period of waiting time, you can check that they have appeared with:<figure class="wp-block-table">

<table class="">
  <tr>
    <td>
      1
    </td>
    
    <td>
      <code>SELECT</code>&nbsp;<code>*&nbsp;FROM</code>&nbsp;<code>GDPR_DATA.TestGDPRTable;</code>
    </td>
  </tr>
</table></figure> 

Good luck, and please let me know if this helped!

#### Additional Info {#S3->Snowflake(SNOWPIPEAuto-Ingest)Guide-AdditionalInfo}

For our work, we simply wanted to get the JSON data from a file in S3 into a single column of a table. At this point we&#8217;d grant access to the table we created to another team, so they can move the data into what table/schema/format they wish using snowflake&#8217;s PARSE_JSON() method.