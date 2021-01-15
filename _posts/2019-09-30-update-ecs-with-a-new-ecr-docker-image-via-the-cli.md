---
id: 196
title: Update ECS with a new ECR Docker image via the CLI
date: 2019-09-30T14:47:54+01:00
author: waz
layout: post
image: /wp-content/uploads/2019/09/cargo-container-commerce-commercial-906494-scaled.jpg
---
So you have an ECS cluster running with an image on ECR. Want to update the image that cluster is running via the CLI? Here&#8217;s how you do it:

First build your docker file locally

<pre class="wp-block-code"><code lang="bash" class="language-bash">docker build --pull -t Jenkins/jenkins:lts --no-cache -t our-services/nonprod/jenkins:latest .</code></pre>

Tag your built image for your repo

<pre class="wp-block-code"><code lang="bash" class="language-bash">docker tag our-services/nonprod/jenkins:latest 1234.dkr.ecr.eu-west-1.amazonaws.com/our-services/nonprod/jenkins:latest</code></pre>

Push your updated docker image to ECR

<pre class="wp-block-code"><code lang="bash" class="language-bash">docker push 1234.dkr.ecr.eu-west-1.amazonaws.com/our-services/nonprod/jenkins:latest</code></pre>

**_If you need to point to a new task definition do so in the UI, or terraform_**

Update your service to set desired count of instances to 0

<pre class="wp-block-code"><code lang="bash" class="language-bash">aws ecs update-service --cluster our-services-nonprod-ecs --service our-services-nonprod-jenkins --desired-count 0</code></pre>

For the next step, you need to find the ARN of the tasks running in that service with 

<pre class="wp-block-code"><code lang="bash" class="language-bash">aws ecs list-tasks --region eu-west-1 --cluster our-services-nonprod-ecs --service our-services-nonprod-jenkins</code></pre>

This should produce a list of ARNs similar to this:

<pre class="wp-block-code"><code lang="json" class="language-json">"taskArns": [
        "arn:aws:ecs:eu-west-1:1234:task/c15edbfb-4aa6-4934-ad18-9083e048e650"
    ]</code></pre>

Then stop the task, replacing the id below with the one you found from the &#8216;list-tasks&#8217; command

<pre class="wp-block-code"><code lang="bash" class="language-bash">aws ecs stop-task --cluster our-services-nonprod-ecs --task c15edbfb-4aa6-4934-ad18-9083e048e650 --region eu-west-1</code></pre>

And set the desired count back to 1

<pre class="wp-block-code"><code lang="bash" class="language-bash">aws ecs update-service --cluster our-services-nonprod-ecs --service our-services-nonprod-jenkins --desired-count 1</code></pre>