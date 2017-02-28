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

In any question, please feel free to contact our support in `tech@liveratesfeeds.com`.

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

Live Rates Feeds uses SSL encryption and username & password credentials to allow access to the API.

The SSL certificate will be given by us.
Username & password will be generated automatically for you once you open an account.

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

## Logon(==A)

>To connect use this code:

```java
final Message customLogonMsg = Message.create(MsgType.Logon, Version.FIX44); 
customLogonMsg.set(Tag.Username, <Your username>);
customLogonMsg.set(Tag.Password, <Your password>); 
initiator.logonAsInitiator(<LRF host>, <LRF port>, <HeartBtInt>, customLogonMsg);
```

Use this message to Logon to Live Rates Feeds market. 

### Message request Tags:

Name | Number | Optional? | Values | Meaning
---- | ------ | --------- | ------ | ------- 
**EncryptMethod** | 98 | No | 0 | encryption of the messages
**HeartBtInt** | 108 | No | 0-100 | interval for heartbeat check
**Username** | 553 | No | | Account username
**Password** | 554 | No | | Account password

<aside class="success">
  A connection established message wil be sent back uppon a successful connection.
</aside>

## New Order Single(==D)

```java
final static private SimpleDateFormat sdf = new SimpleDateFormat("yyyyMMdd-HH:mm:ss");

Message order = Message.create(MsgType.NewOrderSingle, Version.FIX44);

order.set(Tag.ClOrdID, "1234");
order.set(Tag.Symbol, "SEC");
order.set(Tag.Side, 1); //Buy side
order.set(Tag.TransactTime, sdf.format(new Date()));
order.set(Tag.OrdType, 1); // Market
order.set(Tag.OrderQty, 1000);
order.set(Tag.AccountType, 2);

sessin.send(order);
```

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
**AccountType** | 581 | 1 ,2 | 

### Message response tags:

<aside class="success">
  Remember - on success, an Execution Report(8) is being responed.
</aside>

Name | Number | Values | Meaning
---- | ------ | ------ | ------- 
**OrderID** | 37 | Long number | unique order ID generated by our system
**ExecID** | 17 | String ID | unique execution ID generated by our system
**ExecType** | 150 | 0, 3 | 0 if AccountType = 2, 3 if AccountType = 1 | type of execution report
**OrdStatus** | 39 | 0, 2 | 0 if AccountType = 2, 2 if AccountType = 1 | order status
**Side** | 54 | Same as in the request |
**LastQty** | 32 | 0, integer number | 0 if AccountType = 2, quantity of last trade if AccountType = 1
**LastPx** | 31 | 0, float number | 0 if AccountType = 2, price of last trade for 1 unit if AccountType = 1
**LeavesQty** | 151 | integer number | remaining quantity in order to be traded
**CumQty** | 14 | integer number | total quantity already traded
**AvgPx** | 6 | 0, float number | 0 if AccountType = 2, average price of last trade if AccountType = 1
**TradeDate** | 75 | date string | 
**TransactTime** | 60 | time string | 

## OrderCancelRequest(==F)

```java
final static private SimpleDateFormat sdf = new SimpleDateFormat("yyyyMMdd-HH:mm:ss");

Message cancelOrder = Message.create(MsgType.OrderCancelRequest, Version.FIX44);
		
cancelOrder.set(Tag.OrigClOrdID, "1234"); //client order id to cancel
cancelOrder.set(Tag.ClOrdID, "5678");
cancelOrder.set(Tag.Symbol, "SEC");
cancelOrder.set(Tag.Side, 1);
cancelOrder.set(Tag.TransactTime, sdf.format(new Date()));

session.send(cancelOrder);
```

Use this message to cancel an existing order in the market.

### Message request tags:

Name | Number | Optional? | Values | Meaning
---- | ------ | --------- | ------ | ------- 
**OrigClordID** | 41 | No | | Client order ID to cancel
**ClordID** | 11 | No | | Unique identifier of the order as assigned by institution or by the intermediary
**Symbol** | 55 | No | | Common, "human understood" representation of the security
**Side** | 54 | No | 1, 2 | must be the same as the order you want to cancel
**TransactTime** | 60 | No | | Time this order request was initiated/released by the trader.

### Message response tags:

<aside class="success">
  Remember - on success, an Execution Report(8) is being responed.
</aside>

Name | Number | Values | Meaning
---- | ------ | ------ | ------- 
**OrderID** | 37 | Long number | cancelled order ID
**ExecID** | 17 | String ID | unique execution ID generated by our system
**ExecType** | 150 | 4 | execution type of cancelled
**OrdStatus** | 39 | 4 | order status of cancelled
**Side** | 54 | Same as in the request |
**LastQty** | 32 | 0 |
**LastPx** | 31 | float number | price of last trade for 1 unit
**LeavesQty** | 151 | integer number | remaining quantity in order to be traded
**CumQty** | 14 | integer number | total quantity already traded
**AvgPx** | 6 | float number | average price of last trade
**TradeDate** | 75 | date string | 
**TransactTime** | 60 | time string |

## OrderCancelReplaceRequest(==G)

```java
final static private SimpleDateFormat sdf = new SimpleDateFormat("yyyyMMdd-HH:mm:ss");

Message replaceOrder = Message.create(MsgType.OrderCancelReplaceRequest, Version.FIX44);

replaceOrder.set(Tag.OrigClOrdID, "1234"); //client order id to cancel
replaceOrder.set(Tag.ClOrdID, "0246");
replaceOrder.set(Tag.Symbol, "SEC");
replaceOrder.set(Tag.Side, 1);
replaceOrder.set(Tag.TransactTime, sdf.format(new Date()));
replaceOrder.set(Tag.OrderQty, 100); //replace quantity

session.send(replaceOrder)
```

Use this message to replace an existing order in the market.

### Message request tags:

Name | Number | Optional? | Values | Meaning
---- | ------ | --------- | ------ | ------- 
**OrigClordID** | 41 | No | | Client order ID to cancel
**ClordID** | 11 | No | | Unique identifier of the order as assigned by institution or by the intermediary
**Symbol** | 55 | No | | Common, "human understood" representation of the security
**Side** | 54 | No | 1, 2 | must be the same as the order you want to cancel
**TransactTime** | 60 | No | | Time this order request was initiated/released by the trader
**OrderQty** | 38 | No | | quantity to replace (0 quantity will cancel the order)

### Message response tags:

<aside class="success">
  Remember - on success, an Execution Report(8) is being responed.
</aside>

Name | Number | Values | Meaning
---- | ------ | ------ | ------- 
**OrderID** | 37 | Long number | cancelled order ID
**ExecID** | 17 | String ID | unique execution ID generated by our system
**ExecType** | 150 | 5 | execution type of replaced
**OrdStatus** | 39 | 0, 1 | same status of order as before the replacement
**Side** | 54 | Same as in the request |
**LastQty** | 32 | 0 |
**LastPx** | 31 | float number | price of last trade for 1 unit
**LeavesQty** | 151 | integer number | remaining quantity in order to be traded
**CumQty** | 14 | integer number | total quantity already traded
**AvgPx** | 6 | float number | average price of last trade
**TradeDate** | 75 | date string | 
**TransactTime** | 60 | time string |

## QuoteRequest(==R)

```java
Message quoteRequest = Message.create(MsgType.QuoteRequest, Version.FIX44);

quoteRequest.set(Tag.QuoteReqID, gen.generate());
final Group groupRelatedSym = message.setGroup(Tag.NoRelatedSym, 1);
groupRelatedSym.set(Tag.Symbol, 0, "SEC");

session.send(replaceOrder)
```

Use this message to request the current prices of a security

<aside class="notice">
  This request uses FIX group of Symbol(55) to to support multiple security quote requests in one message.
</aside>

### Message request tags:

Name | Number | Optional? | Values | Meaning
---- | ------ | --------- | ------ | ------- 
**QuoteReqID** | 131 | No | | Unique quote request ID
**NoRelatedSym** | 146 | No | Number of symbols in request
**Symbol** | 55 | No | symbol of security (reapeat this tag NoRelatedSym times!)

### Message response tags:

<aside class="notice">
  On success, a QuoteResponse(AJ) will be sent back for each symol in the request
</aside>

Name | Number | Values | Meaning
---- | ------ | ------ | -------
**QuoteReqID** | 131 | | ID of request
**Symbol** | 55 | | symbol of request
**BidPx** | 132 | | bid price of symbol
**OfferPx | 133 | | offer price of symbol

## Reject(3)

<aside class="notice">
  We use this message to response to FIX messages and wee are not supporting requests with this message.
</aside>

on failure, we will response a failure reason within this message.

### Message response tags: 

Name | Number | Value
---- | ------ | -----
**Text** | 45 | reject reason