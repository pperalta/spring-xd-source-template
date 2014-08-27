# Spring XD Source Module Sample

## Introduction
[Spring XD](http://projects.spring.io/spring-xd/) ships with 
[many source modules](http://docs.spring.io/spring-xd/docs/1.0.0.RELEASE/reference/html/#sources)
including HTTP/REST, TCP, and various messaging systems such as JMS and RabbitMQ.
However, advanced users may encounter the need to create custom source
modules. This sample project provides a starting point to create
custom source modules.

## Project Components
Source modules in Spring XD are [http://docs.spring.io/spring-xd/docs/1.0.0.RELEASE/reference/html/#streams]
(defined as Spring Integration output adapters). Therefore, the creation of a custom
module requires the wiring of a Spring Integration adapter and placing it in the
`$XD_HOME/xd/modules/source` directory.

### Gradle Build
The first place to start is the `build.gradle` file that ships with this project.
Other than setting dependencies, this build file contains a target called `xdModule`.
This will create a `build/modules/source/sample` directory which contains
the module artifacts. This directory should be moved/copied/sym-linked to
the `$XD_HOME/xd/modules/source` directory when the module is ready for use.

### Module XML file
The module XML file consists of:

```
<?xml version="1.0" encoding="UTF-8"?>
<beans:beans xmlns:beans="http://www.springframework.org/schema/beans"
			 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
			 xmlns="http://www.springframework.org/schema/integration"
			 xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/integration http://www.springframework.org/schema/integration/spring-integration.xsd">

	<channel id="output"/>

	<beans:bean class="hello.Sample">
		<beans:property name="autoStartup" value="false" />
		<beans:property name="outputChannel" ref="output" />
	</beans:bean>

</beans:beans>
```

The `<channel id="output"/>` element is assigning the name "output" to the output
channel. This is followed by a bean definition for the class `hello.Sample`
which accepts the channel as property "outputChannel". The "autoStartup"
property is set to "false" to ensure that the module is initialized when
the channels are established.

### Java Source

The Java class `Sample` extends from a convenient super class `MessageProducerSupport`
which provides the ability to send messages as well as lifecycle hooks. Here are
the key highlights:

* `doStart` and `doStop` methods
* dedicated thread for creating source output
* `sendMessage` is invoked when the module has output
* the optional use of [`Tuple`](http://docs.spring.io/spring-xd/docs/1.0.0.RELEASE/reference/html/#tuples)

