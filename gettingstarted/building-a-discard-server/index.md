---
layout: page
name: Getting Started
---

# Building a Discard Server

With JRuyi, you don't need to write any code to build a [discard](http://tools.ietf.org/html/rfc863 "Discard Protocol") server.

### 1. Start JRuyi

Go to $JRUYI_HOME, and start JRuyi as follows.

```
bin/ruyi
```

### 2. Create a Session Service Endpoint

Open a new console and run the following command under $JRUYI_HOME to create a TCP server (Session Service) listening on port 9009.

```
bin/ruyi-cli conf:create jruyi.io.tcpserver jruyi.me.endpoint.id=eg.discard.tcpsvr port=9009
```

The created TCP Server Endpoint is identified with *eg.discard.tcpsvr*.

As you see, a Session Service Endpoint of tcpserver is created by creating a configuration of *jruyi.io.tcpserver*.

### 3. Configure the Routing Table

Run the following command under $JRUYI_HOME to set a route.

```
bin/ruyi-cli route:set eg.discard.tcpsvr jruyi.me.endpoint.null
```

This is to tell Messaging Engine to dispatch any message from endpoint *eg.discard.tcpsvr* to endpoint *jruyi.me.endpoint.null* which is a builtin endpoint to swallow any message dispatched to it.

That's it! You just built a discard server using JRuyi.

### 4. Test

You can use telnet to test the discard server you just built.

For example, you can run the following command.

```
telnet localhost 9009
```

Then just type something and you won't get any response. 

To see the data that the discard server received, you can add a MsgLog Filter to the filter chain of the Session Service *eg.discard.tcpsvr* by running the following command under $JRUYI_HOME.

```
bin/ruyi-cli conf:update '"(jruyi.me.endpoint.id=eg.discard.tcpsvr)"' filters=jruyi.io.msglog.filter
```

### 5. Shutdown JRuyi

To shutdown JRuyi, simply enter Ctrl+C in the console in which JRuyi was started. Alternatively, you can run the following command under $JRUYI_HOME.  

```
bin/ruyi-cli shutdown
```

