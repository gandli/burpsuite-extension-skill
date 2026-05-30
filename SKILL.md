---
name: burpsuite-extension-skill
description: Helps develop Burp Suite extensions in Java (Montoya API) or Python (Jython). Use when creating new extensions, implementing HTTP listeners, custom scanner checks, intruder payloads, or any Burp Suite extension development task.
version: 1.0.0
license: MIT
tags:
  - burp
  - security
  - extension
  - java
  - python
  - jython
  - penetration-testing
allowed-tools: Bash Read Write Edit
---

# Burp Suite Extension Development

Develop Burp Suite extensions in **Java** (Montoya API) or **Python** (Jython).

## Official Documentation

- [Creating Extensions](https://portswigger.net/burp/documentation/desktop/extend-burp/extensions/creating)
- [Starter Project (Java)](https://portswigger.net/burp/documentation/desktop/extend-burp/extensions/creating/set-up/starter-project)
- [First Extension Guide](https://portswigger.net/burp/documentation/desktop/extend-burp/extensions/creating/first-extension)
- [Montoya API JavaDoc](https://portswigger.github.io/burp-extensions-montoya-api/javadoc/burp/api/montoya/MontoyaApi.html)
- [Montoya API GitHub](https://github.com/PortSwigger/burp-extensions-montoya-api)
- [Example Extensions](https://github.com/PortSwigger/burp-extensions-montoya-api-examples)
- [Legacy Extender API JavaDoc](https://portswigger.net/burp/extender/api/)

## When to Use

- Creating new Burp Suite extensions
- Implementing HTTP traffic interceptors/modifiers
- Creating custom scanner checks
- Developing intruder payload generators
- Adding custom UI tabs to Burp Suite

## Choose Your Language

| Feature | Java (Montoya API) | Python (Jython) |
|---------|-------------------|-----------------|
| API | Montoya API (New) | Extender API (Legacy) |
| Java Version | JDK 21+ | JDK 17+ |
| Performance | Best | Slower |
| Setup Complexity | Medium | Simple |
| Best For | Production extensions | Quick scripts/prototypes |

---

## Environment Setup

### macOS

#### Install Java (Homebrew)
```bash
# Install Homebrew if not installed
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install Java 21
brew install openjdk@21

# Add to PATH
echo 'export PATH="/opt/homebrew/opt/openjdk@21/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc

# Verify
java -version
```

#### Install IntelliJ IDEA
```bash
brew install --cask intellij-idea-ce
```

#### Install Jython (for Python development)
```bash
brew install jython
```

### Windows

#### Install Java (PowerShell)
```powershell
# Using winget (Windows Package Manager)
winget install EclipseAdoptium.Temurin.21.JDK

# Or download from: https://adoptium.net/

# Set environment variables
$env:JAVA_HOME = "C:\Program Files\Eclipse Adoptium\jdk-21"
$env:Path += ";$env:JAVA_HOME\bin"

# Make permanent (run as Administrator)
[System.Environment]::SetEnvironmentVariable("JAVA_HOME", "C:\Program Files\Eclipse Adoptium\jdk-21", "Machine")
[System.Environment]::SetEnvironmentVariable("Path", $env:Path + ";$env:JAVA_HOME\bin", "Machine")

# Verify
java -version
```

#### Install IntelliJ IDEA
```powershell
winget install JetBrains.IntelliJIDEA.Community
```

#### Install Jython (for Python development)
```powershell
# Download Jython installer from: https://www.jython.org/download
# Run installer and note the installation path
```

### Linux (Ubuntu/Debian)

#### Install Java
```bash
# Add Adoptium repository
wget -qO - https://packages.adoptium.net/artifactory/api/gpg/key/public | sudo apt-key add -
sudo add-apt-repository --yes https://packages.adoptium.net/artifactory/deb/
sudo apt-get update

# Install Java 21
sudo apt-get install temurin-21-jdk

# Verify
java -version
```

#### Install IntelliJ IDEA
```bash
# Using Snap
sudo snap install intellij-idea-community --classic

# Or using Flatpak
flatpak install flathub com.jetbrains.IntelliJ-IDEA-Community
```

#### Install Jython
```bash
# Download Jython
wget https://repo1.maven.org/maven2/org/python/jython-standalone/2.7.3/jython-standalone-2.7.3.jar

# Make executable
chmod +x jython-standalone-2.7.3.jar

# Install
java -jar jython-standalone-2.7.3.jar
```

### Linux (Fedora/RHEL)

#### Install Java
```bash
# Install Java 21
sudo dnf install java-21-openjdk-devel

# Verify
java -version
```

---

## Path A: Java Development (Recommended)

### Prerequisites

1. **Burp Suite Professional or Community Edition** - [Download](https://portswigger.net/burp/communitydownload)
2. **Java JDK 21+** - [Adoptium](https://adoptium.net/)
3. **IntelliJ IDEA** (Recommended) - [Download](https://www.jetbrains.com/idea/)

### Quick Start: Using the Starter Project

```bash
# Clone the starter project
git clone https://github.com/PortSwigger/burp-extensions-montoya-api-starter.git my-extension
cd my-extension

# Build with Gradle
./gradlew build  # macOS/Linux
gradlew.bat build  # Windows

# Output: build/libs/my-extension.jar
```

### Montoya API Key Interfaces

#### Extension Entry Point
```java
package burp.extension;

import burp.api.montoya.BurpExtension;
import burp.api.montoya.MontoyaApi;

public class MyExtension implements BurpExtension {
    @Override
    public void initialize(MontoyaApi api) {
        api.extension().setName("My Extension");
        api.logging().logToOutput("Extension loaded!");
    }
}
```

#### HTTP Proxy Handler
```java
import burp.api.montoya.proxy.http.InterceptedRequest;
import burp.api.montoya.proxy.http.ProxyRequestHandler;
import burp.api.montoya.proxy.http.ProxyRequestReceivedAction;
import burp.api.montoya.proxy.http.ProxyRequestToBeSentAction;

public class MyProxyHandler implements ProxyRequestHandler {
    private final MontoyaApi api;
    
    public MyProxyHandler(MontoyaApi api) {
        this.api = api;
    }
    
    @Override
    public ProxyRequestReceivedAction handleRequestReceived(InterceptedRequest interceptedRequest) {
        api.logging().logToOutput("Request: " + interceptedRequest.url());
        return ProxyRequestReceivedAction.continueWith(interceptedRequest);
    }
    
    @Override
    public ProxyRequestToBeSentAction handleRequestToBeSent(InterceptedRequest interceptedRequest) {
        return ProxyRequestToBeSentAction.continueWith(interceptedRequest);
    }
}
```

#### Scanner Check
```java
import burp.api.montoya.scanner.AuditResult;
import burp.api.montoya.scanner.ScanCheck;
import burp.api.montoya.http.message.HttpRequestResponse;

public class MyScanCheck implements ScanCheck {
    private final MontoyaApi api;
    
    public MyScanCheck(MontoyaApi api) {
        this.api = api;
    }
    
    @Override
    public AuditResult passiveAudit(HttpRequestResponse baseRequestResponse) {
        // Passive scan logic
        return null;
    }
    
    @Override
    public AuditResult activeAudit(HttpRequestResponse baseRequestResponse, 
                                    AuditResult auditResult) {
        // Active scan logic
        return null;
    }
}
```

#### UI Tab
```java
import burp.api.montoya.ui.swing.SwingComponent;
import javax.swing.*;

public class MyTab implements burp.api.montoya.ui.Tab {
    private final JPanel panel;
    
    public MyTab() {
        panel = new JPanel();
        panel.add(new JLabel("My Extension Tab"));
    }
    
    @Override
    public String caption() {
        return "My Extension";
    }
    
    @Override
    public SwingComponent uiComponent() {
        return SwingComponent.swingComponent(panel);
    }
}
```

### Registering Components

```java
public class MyExtension implements BurpExtension {
    @Override
    public void initialize(MontoyaApi api) {
        api.extension().setName("My Extension");
        
        // Register HTTP listener
        api.proxy().registerRequestHandler(new MyProxyHandler(api));
        
        // Register scanner check
        api.scanner().registerScanCheck(new MyScanCheck(api));
        
        // Register UI tab
        api.userInterface().registerSuiteTab("My Extension", new MyTab());
        
        // Register context menu
        api.userInterface().registerContextMenuItemsProvider(new MyContextMenu(api));
    }
}
```

### Project Structure (Gradle)

```
my-extension/
├── src/main/java/
│   └── burp/extension/
│       ├── MyExtension.java
│       ├── MyProxyHandler.java
│       └── MyScanCheck.java
├── build.gradle
├── settings.gradle
└── README.md
```

### build.gradle

```groovy
plugins {
    id 'java'
}

java {
    toolchain {
        languageVersion = JavaLanguageVersion.of(21)
    }
}

repositories {
    mavenCentral()
}

dependencies {
    implementation 'net.portswigger.burp.extensions:montoya-api:2.3.+'
}

tasks.named('jar') {
    manifest {
        attributes 'Extension-ClassName': 'burp.extension.MyExtension'
    }
}
```

### Build & Load

```bash
# Build (macOS/Linux)
./gradlew build

# Build (Windows)
gradlew.bat build

# Load in Burp Suite:
# 1. Extensions → Installed → Add
# 2. Select Java
# 3. Browse to build/libs/my-extension.jar
# 4. Click Next
```

---

## Path B: Python Development (Jython)

### Prerequisites

1. **Burp Suite Professional or Community Edition** - [Download](https://portswigger.net/burp/communitydownload)
2. **Jython Standalone JAR** - [Download](https://www.jython.org/download)

### Setup Jython in Burp Suite

1. Open Burp Suite
2. Go to **Settings** → **Extensions** → **Python Environment**
3. Set path to Jython JAR file

### Python Extension Template

```python
# burp_extender.py
from burp import IBurpExtender
from burp import IHttpListener
from burp import IScannerCheck
from java.io import PrintWriter

class BurpExtender(IBurpExtender):
    def registerExtenderCallbacks(self, callbacks):
        self._callbacks = callbacks
        self._helpers = callbacks.getHelpers()
        
        callbacks.setExtensionName("My Python Extension")
        
        # Output streams
        self._stdout = PrintWriter(callbacks.getStdout(), True)
        self._stderr = PrintWriter(callbacks.getStderr(), True)
        
        self._stdout.println("Extension loaded!")
        
        # Register listeners
        callbacks.registerHttpListener(HttpListener(self))
        
        return


class HttpListener(IHttpListener):
    def __init__(self, extender):
        self._extender = extender
        self._callbacks = extender._callbacks
        self._helpers = extender._helpers
    
    def processHttpMessage(self, toolFlag, messageIsRequest, messageInfo):
        if messageIsRequest:
            self.processRequest(messageInfo)
        else:
            self.processResponse(messageInfo)
    
    def processRequest(self, messageInfo):
        requestInfo = self._helpers.analyzeRequest(messageInfo)
        url = str(requestInfo.getUrl())
        self._extender._stdout.println("Request: " + url)
    
    def processResponse(self, messageInfo):
        responseInfo = self._helpers.analyzeResponse(messageInfo.getResponse())
        statusCode = responseInfo.getStatusCode()
        self._extender._stdout.println("Response: " + str(statusCode))


class ScannerCheck(IScannerCheck):
    def __init__(self, extender):
        self._extender = extender
        self._callbacks = extender._callbacks
        self._helpers = extender._helpers
    
    def doPassiveScan(self, baseRequestResponse):
        # Passive scan logic
        return None
    
    def doActiveScan(self, baseRequestResponse, insertionPoint):
        # Active scan logic
        return None
    
    def consolidateDuplicateIssues(self, existingIssue, newIssue):
        return 0
```

### Load Python Extension

1. Open Burp Suite
2. Go to **Extensions** → **Installed** → **Add**
3. Select **Python** as extension type
4. Browse to `burp_extender.py`
5. Click **Next**

---

## Best Practices

### 1. Error Handling

**Java:**
```java
@Override
public ProxyRequestReceivedAction handleRequestReceived(InterceptedRequest interceptedRequest) {
    try {
        // Process request
        return ProxyRequestReceivedAction.continueWith(interceptedRequest);
    } catch (Exception e) {
        api.logging().logToError("Error processing request: " + e.getMessage());
        return ProxyRequestReceivedAction.continueWith(interceptedRequest);
    }
}
```

**Python:**
```python
def processHttpMessage(self, toolFlag, messageIsRequest, messageInfo):
    try:
        if messageIsRequest:
            self.processRequest(messageInfo)
    except Exception as e:
        self._extender._stderr.println("Error: " + str(e))
```

### 2. Performance

- **Never block** in HTTP listeners
- **Use async processing** for heavy operations
- **Cache results** when possible
- **Limit scope** - check `isInScope()` before processing

### 3. Logging

**Java:**
```java
// Use Montoya API logging
api.logging().logToOutput("Info message");
api.logging().logToError("Error message");
api.logging().logToDebug("Debug message");
```

**Python:**
```python
# Use PrintWriter
self._stdout.println("Info message")
self._stderr.println("Error message")
```

### 4. Scope Checking

**Java:**
```java
if (api.scope().isInScope(request.url())) {
    // Process only in-scope requests
}
```

**Python:**
```python
if self._callbacks.isInScope(requestInfo.getUrl()):
    # Process only in-scope requests
```

### 5. Resource Cleanup

**Java:**
```java
public class MyExtension implements BurpExtension {
    private MontoyaApi api;
    
    @Override
    public void initialize(MontoyaApi api) {
        this.api = api;
        api.extension().setName("My Extension");
        
        // Register unload handler
        api.extension().registerUnloadHandler(() -> {
            api.logging().logToOutput("Extension unloading...");
            // Cleanup resources
        });
    }
}
```

### 6. Configuration

**Java:**
```java
// Use Burp's persistence API
String configValue = api.persistence().extensionData().getString("config_key", "default_value");
api.persistence().extensionData().setString("config_key", "new_value");
```

### 7. Thread Safety

```java
// Use thread-safe collections
private final ConcurrentHashMap<String, Integer> requestCounts = new ConcurrentHashMap<>();

// Or synchronize access
private final Object lock = new Object();

public void incrementCount(String url) {
    synchronized (lock) {
        // Thread-safe operation
    }
}
```

---

## Debugging

### Java (IntelliJ)

1. Add JVM argument to Burp Suite:
   ```
   -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:5005
   ```
2. IntelliJ: Run → Edit Configurations → Remote JVM Debug
3. Set port to 5005
4. Start Burp Suite with JVM argument
5. Attach debugger in IntelliJ

### Python

- Use `self._stdout.println()` for logging
- Check **Extensions** → **Output** tab
- Add `print()` statements (goes to stdout)

---

## Common Issues

| Issue | Solution |
|-------|----------|
| Extension won't load (Java) | Check Java version (21+ required) |
| ClassNotFoundException | Verify Montoya API dependency |
| Python import errors | Check Jython version and path |
| No HTTP traffic | Ensure listener is registered |
| Performance issues | Use async processing, limit scope |
| OutOfMemoryError | Increase JVM heap size: `-Xmx1g` |

---

## Resources

### Java
- [Montoya API JavaDoc](https://portswigger.github.io/burp-extensions-montoya-api/javadoc/)
- [Montoya API GitHub](https://github.com/PortSwigger/burp-extensions-montoya-api)
- [Example Extensions](https://github.com/PortSwigger/burp-extensions-montoya-api-examples)

### Python
- [Legacy Extender API JavaDoc](https://portswigger.net/burp/extender/api/)
- [Jython Download](https://www.jython.org/download)

### General
- [BApp Store](https://portswigger.net/bappstore)
- [Burp Suite Documentation](https://portswigger.net/burp/documentation)
