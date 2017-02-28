---
title: Live Rates Feeds FIX API Reference

toc_footers:
  - <a href='http://liveratesfeeds.com'>Open an account now!</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

search: true
---

# Introduction

Welcome to the Live Rates Feeds FIX API!
You can use our API to access Live Rates Feeds market and trades our products.

The Live Rates Feeds API was written in Java using the great FIX engine
of [Onix.biz](http://www.onixs.biz/).
Use your FIX protocol engine to communicate with Live Rates Feeds system!

This API documentation was created with [Slate](https://github.com/tripit/slate).

In any question, please feel free to contact our support in [LRF support](tech@liveratesfeeds.com).

<aside class="notice">
In all examples below we are using <i>Onixs.biz</i> FIX engine and <i>Java</i> programming langauge but of course you can use your own FIX engine and langauge.
To understand how to do the same as in our examples please contact your FIX engine provider.
</aside>

# Configuration

To connect to Live Rates Feeds API you must set your configuration file to have this mandatury values:

Name | Value
---- | -----
**host** | some host
**port** | some port 
**Sender Computer ID** | your unique computer ID
**Target Computer ID** | LRFCP
**FIX Version** | 4.4

<aside class="warning">
  Live Rates Feeds uses FIX4.4 version for its API messages parsing.
  Any use of other version will cause the connection to be closed immidiatley.
</aside>

# Authentication

Live Rates Feeds uses SSL encryption and username & password credentials to allow access to the API.

The SSL certificate will be given by us.
Username & password will be generated automatically for you once you open an account.

> To authorize, use this code:

```java
SSLContext sslContext;
final Session initiator = new Session( <SenderCompID<, <TargetCompI>, Version.FIX44);
try {
  sslContext = SslContextFactory.getInstance(<keyStoreName>, <trustStoreName>, <keyPassword>, <keyStorePassword>, <trustStorePassword>);
  initiator.setSSLContext(sslContext);
} catch (GeneralSecurityException e) {
  // Some actions on failure
} 
initiator.logonAsInitiator(<LRF host, LRF port>);
```

# FIX Messages

As for the moment, Live Rates Feeds supports the following FIX messages only.

<aside class="notice">
  Some of the messages will response with other FIX messages.
  Please notice the <i>response</i> part in each message to understand the response values.
</aside>
<aside class="notice">
  If no response is being specified then you can assume that there is no special response.
</aside>
<aside class="notice">
  Unless specified, a response for each of the messages will be one of two:
  <aside class="success"><b>An <i>ExecutionReport(8)</i> FIX message.</b></aside>
  <aside class="warning"><b>A <i>Reject(3)</i> message.</b></aside>
</aside>

## Logon(A)

Use this message to Logon to Live Rates Feeds market. 

### Message request Tags:

Name | Number | Optional? | Values | Meaning
---- | ------ | --------- | ------ | ------- 
**EncryptMethod** | 98 | No | 0 | encryption of the messages
**HeartBtInt** | 108 | No | 0-100 | interval for heartbeat check
**Username** | 553 | No | | Account username
**Password** | 554 | No | | Account password

>To connect use this code:

```java
final Message customLogonMsg = Message.create(MsgType.Logon, Version.FIX44); 
customLogonMsg.set(Tag.Username, <Your username>);
customLogonMsg.set(Tag.Password, <Your password>); 
initiator.logonAsInitiator(<LRF host>, <LRF port>, <HeartBtInt>, customLogonMsg);
```

<aside class="success">
  A connection established message wil be sent back uppon a successful connection.
</aside>

## New Order Single(D)

Use this message to create a new order in the market.

### Message request tags:

Name | Number | Optional? | Values | Meaning
---- | ------ | --------- | ------ | ------- 
**ClOrdID** | 11 | No | |Unique identifier of the order as assigned by institution or by the intermediary
**Symbol** | 55 | No | see [Securities](#) | Common, "human understood" representation of the security.
**Side** | 54 | No | 1, 2 | 
**TransactTime** | 60 | No | Time this order request was initiated/released by the trader.
**OrderQty** | 38 | No | quantity of security units to be traded.
**OrdType** | 40 | No | 1 |
**AccountType | 581 | 1 ,2 | 

## Reject(3)

<aside class="notice">
  We use this message to response to FIX messages and wee are not supporting requests with this message.
</aside>

on failure, we will response a failure reason within this message.

### Message response tags: 

Name | Number | Value
---- | ------ | -----
**Text** | 45 | reject reason