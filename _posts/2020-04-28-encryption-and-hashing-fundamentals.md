---
id: 3418
title: Encryption And Hashing Fundamentals
date: 2020-04-28T16:45:04+01:00
author: waz
layout: post
guid: https://devscover.com/?p=3418
permalink: /?p=3418
image: /wp-content/uploads/2020/04/ransomware-2321110_960_720.jpg
---
 

Having worked in numerous organisations where PII data is vitally important, especially in the era of GDPR, I&#8217;ve seen first hand many implementations of Encryption and Hashing.

Yet in this time, I&#8217;ve very often experienced people using the terms interchangeably, or incorrectly.

Therefore I aim here to help demystify some of the terminology and help share the essentials&#8230;.

## Personal Identifiable Information (PII) {#Encryption&HashingFundamentals-PersonalIdentifiableInformation(PII)}

Personally identifiable information (**PII**) is any data that can be used to&nbsp;**identify a specific individual**. NI numbers, email address, phone numbers are the traditional examples. More recently, other technological elements can now be considered PII such as an IP address, reward card IDs, location and behavioral data.

## Encryption {#Encryption&HashingFundamentals-Encryption}

**Encryption**&nbsp;is a process that encodes a message or file so that it can be only be read by certain people. An example is WhatsApp, where your data is encrypted on your phone, sent encoded over the internet, and decrypted by the phone receiving your message.&nbsp;

The idea is that at some point you will want to read that data in the clear again, but throughout its journey that should not be possible.

There are two typical flavours of Encryption &#8211; Symmetric and Asymmetric.&nbsp;

Commonly, companies use **Asymmetric Encryption. **This is where there are two separate **Key**s:

  * There are the&nbsp;**Public Keys**, which are used to&nbsp;**encrypt**&nbsp;**data**; these can be shared fairly widely as there is little to no security risk with someone obtaining these.
  * There are the&nbsp;**Private Keys**, which are used&nbsp;**decrypt data**; these are a security risk. Anyone with these keys are able to decrypt data which is why they must remain very secure.

An important thing to note is that when you&nbsp;**encrypt**&nbsp;a value such as &#8216;ABC&#8217; the**&nbsp;encrypted value will always be different**.

_**With Encryption, you aim to encrypt PII data where you may want to see it again in the clear, through a decryption service.**_

## Hashing {#Encryption&HashingFundamentals-Hashing}

**Hashing**&nbsp;is the process of converting a message or file into a fixed size string of text, meaningless to someone reading it, using a mathematical function.

The purpose of Hashing is very Different to Encrypting. Hashing should be&nbsp;**one way;**&nbsp;there is no expectation that the resulting value will be converted back into it&#8217;s original form.

An example may be where you want to join or compare datasets together to find out if more people purchase Cornish Pasties in Devon or London, however you have no need to see who those people are.

There are various ways of Hashing, using various technologies, formats and algorithms.

Of these there are two options:

  * Hashing without a salt
  * Salted Hashing / Keyed Hashing

Even within these two options, there are may ways of doing so. One common way I&#8217;ve experienced is a type of keyed hashing service called** HMAC Hashing.**

An important thing to note is that when you&nbsp;**hash**&nbsp;a value such as &#8216;ABC&#8217; the**&nbsp;hashed value will always be the same**.&nbsp;

## HMAC Hashing {#Encryption&HashingFundamentals-HMACHashing}

This type of hashing is similar to salted hashing, in that when a message is passed through a mathematical function to generate the hash, a value is also passed with it. In salted hashing this is a&nbsp;**salt**, in keyed hashing this is a&nbsp;**key.**

**This gives the security benefit that for a value to be reverse engineered by an attacker, they would not only need to guess the input and&nbsp;mathematical algorithm, but also have the salt or key as input. (see FAQ Below)**

The main difference between salted and keyed hashing is that the salt is not assumed to be unknown to the attacker, but the key is. An additional difference is that salts often vary; if you hash three passwords within the same system, then you should use three distinct salt values, whereas keys are reused. Furthermore, Keyed hashing is usually faster than salted hashing.

HMAC Hashing is just a form of Keyed Hashing.

_**With Hashing you aim to hash PII data where you want to perform analytics on datasets without the need to see individual personal data.**_

# FAQ {#Encryption&HashingFundamentals-FAQ}

## What Is Tokenisation? {#Encryption&HashingFundamentals-WhatIsTokenisation?}

Tokenisation is another technique used in organisations, in my experience more rarely. In this case PII is replaced by a randomly generated value that has no intrinsic value.

## How Is Our Data Classified? {#Encryption&HashingFundamentals-HowIsOurDataClassified?}

Each company generally has their own agreed documentation on which data needs to be in the clear, encrypted or hashed.

## Are Hashes really one way? / Why would we use a Salted / HMAC Hash? {#Encryption&HashingFundamentals-AreHashesreallyoneway?/WhywouldweuseaSaltedHMACHash?}

They should be, but it is possible to retrieve the original value by cracking / hacking the Hash. The reason is down to the fact that hashing a value without using a salt returns the same value each time.&nbsp;

Therefore if someone has hashed &#8216;John&#8217; and it produced &#8216;X74HZ&#8217;, all the attack has to do, is use the same hashing algorithm on a bunch of names like &#8216;Steve&#8217;, &#8216;Amy&#8217;, &#8216;Ben&#8217;..etc until they hash &#8216;John&#8217; and find the result is&nbsp;&#8216;X74HZ&#8217;. They then realise that the input must have been John.