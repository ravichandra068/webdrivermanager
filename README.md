# WebDriverManager [![][Logo]][GitHub Repository]

[![Maven Central](https://maven-badges.herokuapp.com/maven-central/io.github.bonigarcia/webdrivermanager/badge.svg)](https://maven-badges.herokuapp.com/maven-central/io.github.bonigarcia/webdrivermanager)
[![Build Status](https://travis-ci.org/bonigarcia/webdrivermanager.svg?branch=master)](https://travis-ci.org/bonigarcia/webdrivermanager)
[![License (LGPL version 2.1)](https://img.shields.io/badge/license-GNU%20LGPL%20version%202.1-brightgreen.svg?style=flat-square)](http://opensource.org/licenses/LGPL-2.1)

This library is aimed to automate the [Selenium Webdriver] binaries management within a Java project in runtime.

If you have ever used [Selenium Webdriver], you probably know that in order to use some browsers such as **Chrome**, **Internet Explorer**, **Opera**, **Microsoft Edge**, **PhantomJS**, or **Firefox** (using **Marionette**) you need to download a binary which allows WebDriver to handle the browser. In addition, the absolute path to this binary must be set as Java variables, as follows:

```java
System.setProperty("webdriver.chrome.driver", "/absolute/path/to/binary/chromedriver");
System.setProperty("webdriver.opera.driver", "/absolute/path/to/binary/operadriver");
System.setProperty("webdriver.ie.driver", "C:/absolute/path/to/binary/IEDriverServer.exe");
System.setProperty("webdriver.edge.driver", "C:/absolute/path/to/binary/MicrosoftWebDriver.exe");
System.setProperty("phantomjs.binary.path", "/absolute/path/to/binary/phantomjs");
System.setProperty("webdriver.gecko.driver", "/absolute/path/to/binary/geckodriver");
```

This is quite annoying since it forces you to link directly this binary in your source code. In addition, you have to check manually when new versions of the binaries are released. This library comes to the rescue, performing in an automated way all this dirty job for you.

WebDriverManager is open source, released under the terms of [LGPL License 2.1].

## Usage

In order to use WebDriverManager in a Maven project, first add the following dependency to your `pom.xml`:

```xml
<dependency>
	<groupId>io.github.bonigarcia</groupId>
	<artifactId>webdrivermanager</artifactId>
	<version>1.4.10</version>
</dependency>
```

Then you can let WebDriverManager to do manage WebDriver binaries for your application/test. Take a look to this JUnit example which uses Chrome with Selenium WebDriver:

```java
public class ChromeTest {

	protected WebDriver driver;

	@BeforeClass
	public static void setupClass() {
		ChromeDriverManager.getInstance().setup();
	}

	@Before
	public void setupTest() {
		driver = new ChromeDriver();
	}

	@After
	public void teardown() {
		if (driver != null) {
			driver.quit();
		}
	}

	@Test
	public void test() {
		// Using Selenium WebDriver to carry out automated web testing
	}

}
```

Notice that simple adding ``ChromeDriverManager.getInstance().setup();`` WebDriverManager does magic for you:

1. It checks the latest version of the WebDriver binary file
2. It downloads the binary WebDriver if it is not present in your system
3. It exports the required Java variable by Selenium WebDriver

So far, WebDriverManager supports **Chrome**, **Opera**, **Internet Explorer**, **Microsoft Edge**,  **PhantomJS**, or **Marionette** as follows:

```java
ChromeDriverManager.getInstance().setup();
InternetExplorerDriverManager.getInstance().setup();
OperaDriverManager.getInstance().setup();
EdgeDriverManager.getInstance().setup();
PhantomJsDriverManager.getInstance().setup();
MarionetteDriverManager.getInstance().setup();
```

## Advanced

Configuration parameters for WebDriverManager are set in the ``application.properties`` file:

```properties
wdm.targetPath=~/.m2/repository/webdriver
wdm.forceCache=false
wdm.override=false
wdm.timeout=30
wdm.seekErrorRetries=3

wdm.chromeDriverUrl=http://chromedriver.storage.googleapis.com/
wdm.chromeDriverExport=webdriver.chrome.driver
wdm.chromeDriverVersion=LATEST

wdm.operaDriverUrl=https://api.github.com/repos/operasoftware/operachromiumdriver/releases
wdm.operaDriverExport=webdriver.opera.driver
wdm.operaDriverVersion=LATEST

wdm.internetExplorerDriverUrl=http://selenium-release.storage.googleapis.com/
wdm.internetExplorerExport=webdriver.ie.driver
wdm.internetExplorerVersion=LATEST

wdm.edgeDriverUrl=http://www.microsoft.com/en-us/download/details.aspx?id=48740
wdm.edgeExport=webdriver.edge.driver
wdm.edgeVersion=LATEST
	
wdm.phantomjsDriverUrl=https://npm.taobao.org/mirrors/phantomjs/
wdm.phantomjsDriverExport=phantomjs.binary.path
wdm.phantomjsDriverVersion=LATEST

wdm.marionetteDriverUrl=https://api.github.com/repos/mozilla/geckodriver/releases
wdm.marionetteDriverExport=webdriver.gecko.driver
wdm.marionetteDriverVersion=LATEST

wdm.gitHubTokenName=
wdm.gitHubTokenSecret=
```

The variable ``wdm.targetPath`` is the default folder in which WebDriver binaries are going to be stored. Notice that by default the path of the Maven local repository is used. The URLs to check the latest version of Chrome, Opera, Internet Explorer, Edge, PhantomJS, and Marionette are set using the variables ``wdm.chromeDriverUrl``, ``wdm.operaDriverExport``, ``wdm.operaDriverUrl``, ``wdm.edgeDriverUrl``, ``wdm.phantomjsDriverUrl``, and ``wdm.marionetteDriverUrl``. 

This properties can be overwritten with Java system properties. For example:

```java
System.setProperty("wdm.targetPath", "/my/custom/path/to/driver/binaries");
```

... or by command line, for example:

```properties
-Dwdm.override=true
```

In addition, the usage of a architecture (32 or 64 bits) can be forced (except for Edge driver). By default, the suitable binary version for your system and architecture is downloaded and used. The architecture can be forced as follows:

```java
new ChromeDriverManager().setup(Architecture.x32);
new ChromeDriverManager().setup(Architecture.x64);

new InternetExplorerDriverManager().setup(Architecture.x32);
new InternetExplorerDriverManager().setup(Architecture.x64);

new OperaDriverManager().setup(Architecture.x32);
new OperaDriverManager().setup(Architecture.x64);
```

By default, WebDriverManager downloads the latest version of the WebDriver binary. Concrete versions of WebDriver binaries can be forced, for instance: 

```java
ChromeDriverManager.getInstance().setup("2.24");
InternetExplorerDriverManager.getInstance().setup("2.46");
OperaDriverManager.getInstance().setup("0.2.0");
EdgeDriverManager.getInstance().setup("3.14366");
PhantomJsDriverManager.getInstance().setup("2.1.1");
MarionetteDriverManager.getInstance().setup("0.10.0");
```

This can also be done by changing the value of the variables ``wdm.chromeDriverVersion``, ``wdm.operaDriverVersion``,  ``wdm.internetExplorerVersion``, or  ``wdm.edgeVersion`` from its default value (``LATEST``) to a concrete version. For instance:

```properties
-Dwdm.chromeDriverVersion=2.24
-Dwdm.internetExplorerVersion=2.46
-Dwdm.operaDriverVersion=0.2.0
-Dwdm.edgeVersion=3.14366
-Dwdm.phantomjsDriverVersion=2.1.1
-Dwdm.marionetteDriverVersion=0.10.0
```

If no version is specified, WebDriverManager sends a request to the server hosting the binary. In order to avoid this request and check if any binary has been previously downloaded, the key `wdm.forceCache` can be used.

### HTTP Proxy

If you use an HTTP Proxy in your Internet connection, you can configure your settings exporting the Java environment variable ``HTTP_PROXY`` or ``HTTPS_PROXY`` using the following notation: ``my.http.proxy:1234``.  

### Known Issues

Some of the binaries (Opera and Marionette) are hosted on GitHub. When several consecutive requests are made by WebDriverManager, GitHub servers return an **HTTP 403 error** response as follows:

```
Caused by: java.io.IOException: Server returned HTTP response code: 403 for URL: https://api.github.com/repos/operasoftware/operachromiumdriver/releases
	at sun.net.www.protocol.http.HttpURLConnection.getInputStream0(HttpURLConnection.java:1840)
	at sun.net.www.protocol.http.HttpURLConnection.getInputStream(HttpURLConnection.java:1441)
	at sun.net.www.protocol.https.HttpsURLConnectionImpl.getInputStream(HttpsURLConnectionImpl.java:254)
	at io.github.bonigarcia.wdm.BrowserManager.openGitHubConnection(BrowserManager.java:463)
	at io.github.bonigarcia.wdm.OperaDriverManager.getDrivers(OperaDriverManager.java:55)
	at io.github.bonigarcia.wdm.BrowserManager.manage(BrowserManager.java:168)
```

```
Caused by: java.io.IOException: Server returned HTTP response code: 403 for URL: https://api.github.com/repos/mozilla/geckodriver/releases
	at sun.net.www.protocol.http.HttpURLConnection.getInputStream0(HttpURLConnection.java:1840)
	at sun.net.www.protocol.http.HttpURLConnection.getInputStream(HttpURLConnection.java:1441)
	at sun.net.www.protocol.https.HttpsURLConnectionImpl.getInputStream(HttpsURLConnectionImpl.java:254)
	at io.github.bonigarcia.wdm.MarionetteDriverManager.getDrivers(MarionetteDriverManager.java:61)
	at io.github.bonigarcia.wdm.BrowserManager.manage(BrowserManager.java:163)
```

In order to avoid this problem, [authenticated requests] should be done. The procedure is the following:

1. Create a pair token/secret in your [GitHub account]
2. Tell WebDriverManager the value of this pair token/secret. To do that you should use the configuration keys ``wdm.gitHubTokenName`` and ``wdm.gitHubTokenSecret``. You can pass them as command line Java parameters as follows:

```properties
-Dwdm.gitHubTokenName=<your-token-name>
-Dwdm.gitHubTokenSecret=<your-token-secret>
```

## About

WebDriverManager (Copyright &copy; 2015-2016) is a personal project of [Boni Garcia] licensed under [LGPL License]. Comments, questions and suggestions are always very welcome!

[Logo]: http://bonigarcia.github.io/img/webdrivermanager.png
[Selenium Webdriver]: http://docs.seleniumhq.org/projects/webdriver/
[LGPL License 2.1]: http://www.gnu.org/licenses/lgpl-2.1.html
[Boni Garcia]: http://bonigarcia.github.io/
[GitHub Repository]: https://github.com/bonigarcia/webdrivermanager
[authenticated requests]: https://developer.github.com/v3/#rate-limiting
[GitHub account]: https://github.com/settings/tokens
