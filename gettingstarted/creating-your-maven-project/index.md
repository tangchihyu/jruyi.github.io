---
layout: page
name: Getting Started
---

# Creating Your Maven Project

### 1. JRuyi Maven Repository

Add JRuyi maven repository to your pom as follows.

```xml
...
<repositories>
	...
	<repository>
		<id>jruyi-repo</id>
		<url>http://repo.jruyi.org/maven/releases</url>
	</repository>
	...
</repositories>
...
```

### 2. JRuyi API

Add JRuyi API to your pom as follows.

```xml
...
<dependencies>
	...
	<dependency>
		<groupId>org.jruyi</groupId>
		<artifactId>jruyi-api</artifactId>
		<version>1.0.0</version>
		<scope>provided</scope>
	</dependency>
	...
</dependencies>
...
```

### 3. Maven Bundle Plugin

To simplify building OSGi bundles, add [Maven Bundle Plugin](https://cwiki.apache.org/confluence/display/FELIX/Apache+Felix+Maven+Bundle+Plugin+%28BND%29) to your pom as follows.

```xml
...
<packaging>bundle</packaging>
...
<build>
	<plugins>
		...
		<plugin>
			<groupId>org.apache.felix</groupId>
			<artifactId>maven-bundle-plugin</artifactId>
			<version>2.4.0</version>
			<extensions>true</extensions>
		</plugin>
		...
	</plugins>
</build>
```

### 4. Maven SCR Plugin

To simplify the OSGi-based development, you can use [Declarative Services](http://wiki.osgi.org/wiki/Declarative_Services) as the service-oriented component model though there are other alternatives such as [iPOJO](http://felix.apache.org/site/apache-felix-ipojo.html).

To ease the development of OSGi DS components and services, you can use [Maven SCR Plugin](https://cwiki.apache.org/confluence/display/FELIX/Apache+Felix+Maven+SCR+Plugin) by adding this to your pom.

```xml
...
<dependencies>
	...
	<dependency>
		<groupId>org.apache.felix</groupId>
		<artifactId>org.apache.felix.scr.annotations</artifactId>
		<version>1.9.6</version>
		<scope>provided</scope>
	</dependency>
	...
</dependencies>
...
<build>
	<plugins>
		...
		<plugin>
			<groupId>org.apache.felix</groupId>
			<artifactId>maven-scr-plugin</artifactId>
			<version>1.15.0</version>
			<dependencies>
				<dependency>
					<groupId>org.slf4j</groupId>
					<artifactId>slf4j-simple</artifactId>
					<version>1.7.5</version>
				</dependency>
			</dependencies>
			<executions>
				<execution>
					<id>generate-scr-scrdescriptor</id>
					<goals>
						<goal>scr</goal>
					</goals>
				</execution>
			</executions>
		</plugin>
		...
	</plugins>
</build>
```

