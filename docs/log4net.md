# Logz.io log4net Appender

- [Configuration](#configuration)
	- [XML](#xml)
	- [Code](#code)
- [Custom Fields](#custom_fields)
- [Extensibility](#extensibility)


Install the log4net appender from the Package Manager Console:

    Install-Package Logzio.DotNet.Log4net

If you prefer to install the library manually, download the latest version from the releases page.

## Configuration
### XML
If you configure your logging in an XML file, simply add a reference to the Logz.io appender.

```xml
    <log4net>
    	<appender name="LogzioAppender" type="Logzio.DotNet.Log4net.LogzioAppender, Logzio.DotNet.Log4net">
    		<!-- 
				Required fields 
			-->
			<!-- Your Logz.io API token -->
			<token>DKJiomZjbFyVvssSZmWATeHAHAnDARWz</token>
			
			<!-- 
				Optional fields (with their default values) 
			-->
			<!-- The type field will be added to each log message, making it 
			easier for you to differ between different types of logs. -->
    		        <type>log4net</type>
			<!-- The URL of the Lgz.io listener -->
    		        <listenerUrl>https://listener.logz.io:8071</listenerUrl>
                        <!--Optional proxy server address:
                        proxyAddress = "http://your.proxy.com:port" -->
			<!-- The maximum number of log lines to send in each bulk -->
    		        <bufferSize>100</bufferSize>
			<!-- The maximum time to wait for more log lines, in a hh:mm:ss.fff format -->
    		        <bufferTimeout>00:00:05</bufferTimeout>
			<!-- If connection to Logz.io API fails, how many times to retry -->
    	         	<retriesMaxAttempts>3</retriesMaxAttempts>
    		        <!-- Time to wait between retries, in a hh:mm:ss.fff format -->
			<retriesInterval>00:00:02</retriesInterval>
			<!-- Set the appender to compress the message before sending it -->
		        <gzip>true</gzip>
			<!-- Enable the appender's internal debug logger (sent to the console output and trace log) -->
			<debug>false</debug>
    	</appender>
    
    	<root>
    		<level value="INFO" />
    		<appender-ref ref="LogzioAppender" />
    	</root>
    </log4net>
```
### Code
To add the Logz.io appender via code, add the following lines:

```C#
	var hierarchy = (Hierarchy)LogManager.GetRepository();
	var logzioAppender = new LogzioAppender();
	logzioAppender.AddToken("DKJiomZjbFyVvssSZmWATeHAHAnDARWz");
	logzioAppender.AddListenerUrl("https://webhook.site/b229776a-3396-4c9d-9e05-b5350aeb98fa");
         // Uncomment and edit this line to enable proxy routing: 
         // logzioAppender.AddProxyAddress("http://your.proxy.com:port");
	 // Uncomment these lines to enable gzip compression 
	 // logzioAppender.AddGzip(true);
         // logzioAppender.ActivateOptions();
	hierarchy.Root.AddAppender(logzioAppender);
	hierarchy.Configured = true;
```

## Custom Fields

You can add static keys and values to be added to all log messages. For example:

```XML
    	<appender name="LogzioAppender" type="Logzio.DotNet.Log4net.LogzioAppender, Logzio.DotNet.Log4net">
			<token>DKJiomZjbFyVvssSZmWATeHAHAnDARWz</token>
			<customField>
				<key>Environment</key>
				<value>Production</value>
			<customField>
			<customField>
				<key>Location</key>
				<value>New Jerseay B1</value>
			</customField>
    	</appender>
```

## Extensibility 

If you want to change some of the fields or add some of your own, inherit the appender and override the `ExtendValues` method:

```C#
	public class MyAppLogzioAppender : LogzioAppender
	{
		protected override void ExtendValues(LoggingEvent loggingEvent, Dictionary<string, string> values)
		{
			values["logger"] = "MyPrefix." + values["logger"];
			values["myAppClientId"] = new ClientIdProvider().Get();
		}
	}
```

You will then have to change your configuration in order to use your own appender.
