---
layout: userguide
subject: 1. Overview  
next: 2. Installation
next_link: /installation
---

JRuyi is built on OSGi platform.  It mainly uses [OSGi Declarative Services](http://wiki.osgi.org/wiki/Declarative_Services) as the service component model itself and uses [OSGi Configuration Admin Service](http://wiki.osgi.org/wiki/Configuration_Admin) for configuring components.

### <a name="architecture"></a>1.1. Architecture

![Architecture]({{ site.url }}/public/images/userguide/architecture.png "JRuyi Architecture")

### <a name="entities"></a>1.2. Entities

* Messaging Engine – A point-to-point messaging system backed by an in-memory message queue.

* Endpoint – An endpoint is registered to the messaging engine and identified with the service property _jruyi.me.endpoint.id_.  It represents a message consumer/producer to receive/send messages from/to the message queue.

* Processor – A processor is registered to the messaging engine as an endpoint and identified with the service property _jruyi.me.endpoint.id_.  It consumes messages dispatched to it and then passes on.

* Route – A route consists of three elements: from-endpoint, to-endpoint and filter.  It defines a rule for messages to be routed between two endpoints in one way.  If a route is defined as follows: from-endpoint = A, to-endpoint = B and filter = F, then any message that is sent by A and matches F will be routed to B to consume.

* Route Set – All the routes that have the same from-endpoint are grouped together as a route set.

* Routing Table – The routing table consists of route sets.  It contains all the defined routes.

* Message – A message is a data carrier to be put into the message queue for routing.

* Prehandler – A prehandler handles a message after it left the message queue and before it is given to an endpoint for consuming.

* Posthandler – A posthandler handles a message after it left the endpoint and before it is put into the message queue.

* Session – A session represents the context of an IO connection.

* Session Service – A component/service provides an event-driven IO programming model.  It conducts the actual IO operations and hides the detailed IO operations.

* Session Listener – A session listener is a consumer of session events.

* Session Service Endpoint – An Endpoint represents a Session Service to communicate with other Endpoints or Processors via Messaging Engine.  i.e. TCP Server endpoint.

* Filter – A filter intercepts incoming/outgoing data from/to the network.

* Timeout Admin - A concurrent and scalable timer service using timer wheel algorithm.

* Workshop – A thread pool service.

### <a name="synopsis"></a>1.3. Synopsis

JRuyi provides an event-driven IO channel framework implemented in the [Reactor pattern](http://en.wikipedia.org/wiki/Reactor_pattern) to read/write data from/to the network.  Built on this IO channel framework, there are 6 types of Session Services.  They are _tcpserver_, _tcpclient_, _tcpclient.shortconn_, _tcpclient.connpool_, _udpserver_ and _udpclient_.
 
A Session Service reads data from the network, and passes them to the Filter chain to parse.  The result is then passed to the Session Service Endpoint which is an IO session listener receiving session events from the corresponding Session Service.  The Session Service Endpoint creates a Message to carry the result, and pass it to the PostHandler chain to do some work before sending it to the Messaging Engine.  The Messaging Engine will then dispatch the Message to the next Endpoint or Processor for consuming based on the Routing Table.

If a Message is dispatched to the Session Service Endpoint by the Messaging Engine, it will go through the PreHandler chain first.  Then the Session Service Endpoint takes the data carried by this Message and passes them to the Filter chain to build outgoing data.  The resultant data will be passed to the Session Service to write to the network.

An Endpoint or a Processor consumes Messages every time in a separate thread fetched from the Workshop.  So they have to be re-enterable.

