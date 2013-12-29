---
layout: page
name: Getting Started
---

# Building an Echo Server

Just as [building a discard server](../building-a-discard-server), you don't need to write any code to build an [echo](http://tools.ietf.org/html/rfc862 "Echo Protocol") server with JRuyi.

### 1. Start JRuyi

Run the following command under $JRUYI_HOME.

```
bin/ruyi
```

Or you can use the same JRuyi running instance hosting the discard server you built in [Building a Discard Server](../building-a-discard-server).

### 2. Create a Session Service Endpoint

Run the following command under $JRUYI_HOME to create a TCP server (Session Service) listening on port 9007.

```
bin/ruyi-cli conf:create jruyi.io.tcpserver jruyi.me.endpoint.id=eg.echo.tcpsvr port=9007
```

The corresponding Session Service Endpoint is identified with *eg.echo.tcpsvr*.

### 3. Configure the Routing Table

Run the following command under $JRUYI_HOME to set a route.

```
bin/ruyi-cli route:set eg.echo.tcpsvr eg.echo.tcpsvr
```

This is to tell Messaging Engine to dispatch any message from endpoint *eg.echo.tcpsvr* back to itself.

### 4. Test

You can use telnet to test the echo server you just built.

For example, you can run the following command.

```
telnet localhost 9007
```

Then just type something. And whatever you entered will be echoed.

To see the data that the echo server received and sent, you can add a MsgLog Filter to the filter chain of the Session Service *eg.echo.tcpsvr* by running the following command under $JRUYI_HOME.

```
bin/ruyi-cli conf:update '"(jruyi.me.endpoint.id=eg.echo.tcpsvr)"' filters=jruyi.io.msglog.filter
```
