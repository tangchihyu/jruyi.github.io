---
layout: page
name: Getting Started
---

# Writing a Daytime Server

In this example, we are going to build a [TCP based daytime service](http://tools.ietf.org/html/rfc867) which need send back the current date and time when a connection is established.

Through this example, you will learn how to create a Session Service of *tcpserver* programmatically and not touch Messaging Engine. That is to say, no need to create a Session Service Endpoint.

The full source code of this example can be found in [jruyi-eg](https://github.com/jruyi/jruyi-eg).

### 1. Write a DaytimeServer Component

* Write an immediate component *jruyi.eg.daytime* which will be activated/deactivated when bundle starts/stops. The implementation class is *org.jruyi.eg.daytime.DaytimeServer* which extends [SessionListener](http://www.jruyi.org/javadoc/{{ site.jruyi-api-version }}/org/jruyi/io/SessionListener.html).

* Create an instance of [ISessionService](http://www.jruyi.org/javadoc/{{ site.jruyi-api-version }}/org/jruyi/io/ISessionService.html) of tcpserver, and start it when component is being activated; stop it when component is being deactivated.

* Construct the daytime message and send it to the client when a connection is established.

* Close the connection through which a daytime message is sent.

```java
package org.jruyi.eg.daytime;

import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.Hashtable;
import java.util.Locale;
import java.util.Map;

import org.apache.felix.scr.annotations.Component;
import org.apache.felix.scr.annotations.Properties;
import org.apache.felix.scr.annotations.Property;
import org.apache.felix.scr.annotations.Reference;
import org.jruyi.io.Codec;
import org.jruyi.io.IBuffer;
import org.jruyi.io.ISession;
import org.jruyi.io.ISessionService;
import org.jruyi.io.IoConstants;
import org.jruyi.io.SessionListener;
import org.osgi.service.component.ComponentConstants;
import org.osgi.service.component.ComponentFactory;
import org.osgi.service.component.ComponentInstance;

@Component(name = "jruyi.eg.daytime",
	immediate = true, // immediate component
	createPid = false, // Don't create property service.pid
	metatype = true // create metatype descriptor file
)
@Properties({
	// This IO service is identified with jruyi.eg.daytime.
	@Property(name = IoConstants.SERVICE_ID, value = "jruyi.eg.daytime",
		/* non-configurable property */ propertyPrivate = true),
	// Configurable property for the address to be bound to
	// Bound to any local address by default
	@Property(name = "bindAddr", value = "0.0.0.0"),
	// Configurable property for the listening port
	// Listen on port 9013 by default
	@Property(name = "port", intValue = 9013),
	// Configurable property for enabling/disabling Nagle's algorithm
	// Disable Nagle's algorithm by default
	@Property(name = "tcpNoDelay", boolValue = true) })
public class DaytimeServer extends SessionListener {

	// Specify the dependency service
	@Reference(name = "tcpServerFactory", target = "("
			+ ComponentConstants.COMPONENT_NAME + "="
			+ IoConstants.CN_TCPSERVER_FACTORY + ")")
	private ComponentFactory m_tcpServerFactory;

	private ComponentInstance m_tcpServer;
	private ISessionService m_ss;

	// This method is called when a connection is established
	// and ready to send/recv data.
	@Override
	public void onSessionOpened(ISession session) {
		// Current date and time
		SimpleDateFormat dateFormat = new SimpleDateFormat(
				"EEEE, MMMM d, yyyy HH:mm:ss-zzz", Locale.ENGLISH);
		String daytime = dateFormat.format(new Date());

		// Allocate the buffer and construct the message
		IBuffer out = session.createBuffer();
		out.write(daytime, Codec.us_ascii());
		out.write("\r\n", Codec.us_ascii());

		// Send out the message
		m_ss.write(session, out);
	}

	// This method is called after the given msg is sent.
	@Override
	public void onMessageSent(ISession session, Object msg) {
		// Close the connection
		m_ss.closeSession(session);
	}

	protected void bindTcpServerFactory(ComponentFactory factory) {
		m_tcpServerFactory = factory;
	}

	protected void unbindTcpServerFactory(ComponentFactory factory) {
		m_tcpServerFactory = null;
	}

	// This method is called when this component is being activated.
	protected void activate(Map<String, ?> properties) throws Exception {
		// Create the Session Service of tcpserver
		ComponentInstance tcpServer = m_tcpServerFactory
				.newInstance(new Hashtable<String, Object>(properties));
		ISessionService ss = (ISessionService) tcpServer.getInstance();

		// Set SessionListener
		ss.setSessionListener(this);

		// Start the Session Service
		ss.start();
		m_tcpServer = tcpServer;
		m_ss = ss;
	}

	// This method is called when this component is being deactivated.
	protected void deactivate() {
		// Stop the Session Service
		m_tcpServer.dispose();
		m_tcpServer = null;
		m_ss = null;
	}
}
```

### 2. Start JRuyi

Go to $JRUYI_HOME, and start JRuyi as follows.

```
bin/ruyi
```

### 3. Build This Example

We will use /home/jruyi/jruyi-eg as the path of the local copy of [jruyi-eg](https://github.com/jruyi/jruyi-eg) repository.

To build this example, go to /home/jruyi/jruyi-eg/daytime and run the following command.

```
mvn clean install
```

You will get the bundle *org.jruyi.eg.daytime-1.0.0-SNAPSHOT.jar* under /home/jruyi/jruyi-eg/daytime/target.

### 4. Deploy

Go to $JRUYI_HOME, and run the following command to deploy the daytime bundle.

```
bin/ruyi-cli bundle:start file:/home/jruyi/jruyi-eg/daytime/target/org.jruyi.eg.daytime-1.0.0-SNAPSHOT.jar
```

Or if you copied the bundle to $JRUYI_HOME/bundles, then you can run the following command to deploy.

```
bin/ruyi-cli bundle:start ${jruyi.bundle.base.url}org.jruyi.eg.daytime-1.0.0-SNAPSHOT.jar
```

### 5. Test

You can use telnet to test the daytime server you just built.
For example, you can run the following command.

```
telnet localhost 9013
```

You will get the current date and time in the following format.

```
Weekday, Month Day, Year Time-Zone
```

And the connection will be closed by the server right after you get the message.

### 6. Change the Listening Port Dynamically

You can change the listening port dynamically without shutting down JRuyi. In fact, you can change any configurable property dynamically.

First, run the following command to see if configuration *jruyi.eg.daytime* exists.

```
bin/ruyi-cli conf:list | grep jruyi.eg.daytime
```

If nothing is printed, it means that configuration *jruyi.eg.daytime* does not exist. Otherwise, configuration *jruyi.eg.daytime* exists.

Second, if configuration *jruyi.eg.daytime* does not exist, please change the listening port by creating the configuration *jruyi.eg.daytime* as follows.

```
bin/ruyi-cli conf:create jruyi.eg.daytime port=10013
```

Otherwise, please change the listening port by updating the configuration *jruyi.eg.daytime* as follows.

```
bin/ruyi-cli conf:update jruyi.eg.daytime port=10013
```

Third, you can see that the port is changed by testing with telnet.

The connection will be refused if you run the following command.

```
telnet localhost 9013
```

While you will get the current date and time successfully by running the following command.

```
telnet localhost 10013
```

