# Complete Guide: JavaFX Development with VSCode

This comprehensive guide provides step-by-step instructions for setting up and working with JavaFX projects in Visual Studio Code.

## Table of Contents
1. [One-Time Setup](#one-time-setup)
2. [Creating a New JavaFX Project](#creating-a-new-javafx-project)
3. [Running and Debugging](#running-and-debugging)
4. [Common Issues and Solutions](#common-issues-and-solutions)
5. [Working with Existing Projects](#working-with-existing-projects)
6. [FXML and SceneBuilder Integration](#fxml-and-scenebuilder-integration)

## One-Time Setup

These steps only need to be done once on your system.

### 1. Install Required Software

```bash
# Install OpenJDK 21
sudo apt install openjdk-21-jdk

# Verify Java installation
java --version
javac --version
```

### 2. Download and Install JavaFX SDK

```bash
# Create a directory for JavaFX
mkdir -p ~/javafx

# Download JavaFX (adjust version if needed)
cd ~/javafx
wget https://download2.gluonhq.com/openjfx/21.0.7/openjfx-21.0.7_linux-x64_bin-sdk.zip

# Extract the SDK
unzip openjfx-21.0.7_linux-x64_bin-sdk.zip

# Verify installation
ls -la ~/javafx/javafx-sdk-21.0.7/lib/
```

### 3. Set Environment Variables

Add these lines to your `~/.bashrc` file:

```bash
# Java and JavaFX environment variables
export JAVA_HOME=/usr/lib/jvm/java-21-openjdk-amd64
export JAVAFX_HOME=~/javafx/javafx-sdk-21.0.7
export PATH_TO_FX=$JAVAFX_HOME/lib
export JAVAFX_MODULES="javafx.controls,javafx.fxml,javafx.graphics,javafx.web,javafx.media,javafx.swing,javafx.base"
```

Apply the changes:
```bash
source ~/.bashrc
```

### 4. Install Required VSCode Extensions

1. Open VSCode
2. Install the following extensions:
   - Extension Pack for Java
   - Maven for Java
   - JavaFX Support

### 5. Configure VSCode User Settings

1. Open Settings (Ctrl+,)
2. Click the "Open Settings (JSON)" icon in the top-right corner
3. Add these settings:

```json
"java.home": "/usr/lib/jvm/java-21-openjdk-amd64",
"java.configuration.runtimes": [
    {
        "name": "JavaSE-21",
        "path": "/usr/lib/jvm/java-21-openjdk-amd64",
        "default": true
    }
],
"java.jdt.ls.vmargs": "-XX:+UseParallelGC -XX:GCTimeRatio=4 -XX:AdaptiveSizePolicyWeight=90 -Dsun.zip.disableMemoryMapping=true -Xmx1G -Xms100m --module-path=/home/milesbaack/javafx/javafx-sdk-21.0.7/lib --add-modules=ALL-MODULE-PATH",
"java.configuration.updateBuildConfiguration": "automatic",
"terminal.integrated.env.linux": {
    "JAVA_HOME": "/usr/lib/jvm/java-21-openjdk-amd64",
    "JAVAFX_HOME": "~/javafx/javafx-sdk-21.0.7",
    "PATH_TO_FX": "~/javafx/javafx-sdk-21.0.7/lib",
    "JAVAFX_MODULES": "javafx.controls,javafx.fxml,javafx.graphics,javafx.web,javafx.media,javafx.swing,javafx.base"
}
```

## Creating a New JavaFX Project

### 1. Create Project Directory Structure

```bash
# Create project directory
mkdir -p ~/my-javafx-project
cd ~/my-javafx-project

# Create Maven standard directory structure
mkdir -p src/main/java/com/myapp
mkdir -p src/main/resources
mkdir -p src/test/java/com/myapp
mkdir -p .vscode
```

### 2. Create Module Definition

Create `src/main/java/module-info.java`:

```java
module com.myapp {
    requires javafx.controls;
    requires javafx.fxml;
    requires javafx.graphics;
    
    exports com.myapp;
    opens com.myapp to javafx.fxml;
}
```

### 3. Create Main Application Class

Create `src/main/java/com/myapp/App.java`:

```java
package com.myapp;

import javafx.application.Application;
import javafx.scene.Scene;
import javafx.scene.control.Label;
import javafx.scene.layout.StackPane;
import javafx.stage.Stage;

public class App extends Application {

    @Override
    public void start(Stage stage) {
        String javaVersion = System.getProperty("java.version");
        
        Label label = new Label("Hello, JavaFX!\nRunning on Java " + javaVersion);
        StackPane root = new StackPane(label);
        Scene scene = new Scene(root, 640, 480);
        
        stage.setScene(scene);
        stage.setTitle("My JavaFX Application");
        stage.show();
    }

    public static void main(String[] args) {
        launch();
    }
}
```

### 4. Create Maven POM File

Create `pom.xml` in the project root:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.myapp</groupId>
    <artifactId>my-javafx-project</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.release>21</maven.compiler.release>
        <javafx.version>21.0.7</javafx.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.openjfx</groupId>
            <artifactId>javafx-controls</artifactId>
            <version>${javafx.version}</version>
        </dependency>
        <dependency>
            <groupId>org.openjfx</groupId>
            <artifactId>javafx-fxml</artifactId>
            <version>${javafx.version}</version>
        </dependency>
        <dependency>
            <groupId>org.openjfx</groupId>
            <artifactId>javafx-graphics</artifactId>
            <version>${javafx.version}</version>
        </dependency>
        <dependency>
            <groupId>org.openjfx</groupId>
            <artifactId>javafx-base</artifactId>
            <version>${javafx.version}</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.11.0</version>
                <configuration>
                    <release>${maven.compiler.release}</release>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.openjfx</groupId>
                <artifactId>javafx-maven-plugin</artifactId>
                <version>0.0.8</version>
                <configuration>
                    <mainClass>com.myapp/com.myapp.App</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

### 5. Create VSCode Configuration Files

Create `.vscode/settings.json`:

```json
{
    "java.project.sourcePaths": ["src/main/java"],
    "java.project.outputPath": "target/classes",
    "java.project.referencedLibraries": [
        "lib/**/*.jar"
    ],
    "java.configuration.updateBuildConfiguration": "automatic"
}
```

Create `.vscode/launch.json`:

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "type": "java",
            "name": "Launch JavaFX Application",
            "request": "launch",
            "mainClass": "com.myapp.App",
            "vmArgs": "--module-path=${env:PATH_TO_FX} --add-modules=javafx.controls,javafx.fxml"
        }
    ]
}
```

### 6. Open Project in VSCode

```bash
code ~/my-javafx-project
```

## Running and Debugging

### Running from VSCode

1. Open `App.java`
2. Click the "Run" button (play icon) that appears above the `main` method
3. Alternatively, go to Run > Start Debugging and select "Launch JavaFX Application"

### Running with Maven

In the VSCode terminal:

```bash
mvn clean javafx:run
```

### Debugging

1. Set breakpoints by clicking in the gutter (line number area)
2. Start the application using the "Debug" button or Run > Start Debugging
3. The application will pause when it hits a breakpoint

## Common Issues and Solutions

### VSCode Shows Errors But Maven Builds Successfully

If VSCode shows errors like "cannot find symbol: class Application" but Maven builds and runs correctly:

1. Open Command Palette (Ctrl+Shift+P)
2. Select "Java: Clean Java Language Server Workspace"
3. Choose "Restart and delete"

### Module Not Found Error

If you get "module not found" errors when running:

1. Check your `module-info.java` file
2. Ensure the module name matches your package structure
3. Verify that all required modules are listed in the `requires` statements

### JavaFX Classes Not Found

If JavaFX classes are not recognized:

1. Verify environment variables:
   ```bash
   echo $PATH_TO_FX
   echo $JAVAFX_HOME
   ```
2. Check that the JavaFX SDK is installed correctly:
   ```bash
   ls -la $JAVAFX_HOME/lib/*.jar
   ```
3. Ensure VSCode settings include the JavaFX module path

### Cannot Run Application

If the application won't start:

1. Check console for error messages
2. Verify the main class path in your pom.xml:
   ```xml
   <mainClass>com.myapp/com.myapp.App</mainClass>
   ```
3. Make sure your `module-info.java` properly exports your package:
   ```java
   exports com.myapp;
   ```

## Working with Existing Projects

### Opening an Existing Project

1. Open VSCode
2. File > Open Folder...
3. Navigate to the project root directory and click "Open"
4. Wait for Maven to import the project

### Configuring an Existing Project for JavaFX

If you're adding JavaFX to an existing project:

1. Add JavaFX dependencies to pom.xml
2. Create or update module-info.java to include JavaFX modules
3. Create a .vscode folder with settings.json and launch.json files
4. Clean the Java Language Server workspace

## FXML and SceneBuilder Integration

### Creating FXML Files

1. Create a new file under `src/main/resources` with `.fxml` extension
2. Add a basic FXML structure:

```xml
<?xml version="1.0" encoding="UTF-8"?>

<?import javafx.scene.layout.VBox?>
<?import javafx.scene.control.Label?>
<?import javafx.scene.control.Button?>

<VBox alignment="CENTER" spacing="20.0" xmlns="http://javafx.com/javafx/21" xmlns:fx="http://javafx.com/fxml/1" fx:controller="com.myapp.MainController">
   <children>
      <Label text="Hello, JavaFX!" />
      <Button text="Click Me" onAction="#handleButtonClick" />
   </children>
</VBox>
```

### Creating a Controller Class

Create `src/main/java/com/myapp/MainController.java`:

```java
package com.myapp;

import javafx.fxml.FXML;
import javafx.scene.control.Label;

public class MainController {
    
    @FXML
    private Label messageLabel;
    
    @FXML
    private void handleButtonClick() {
        messageLabel.setText("Button clicked!");
    }
}
```

### Loading FXML in Your Application

Update your App.java to load the FXML:

```java
package com.myapp;

import javafx.application.Application;
import javafx.fxml.FXMLLoader;
import javafx.scene.Parent;
import javafx.scene.Scene;
import javafx.stage.Stage;

public class App extends Application {

    @Override
    public void start(Stage stage) throws Exception {
        Parent root = FXMLLoader.load(getClass().getResource("/com/myapp/main.fxml"));
        Scene scene = new Scene(root, 640, 480);
        stage.setScene(scene);
        stage.setTitle("My JavaFX FXML Application");
        stage.show();
    }

    public static void main(String[] args) {
        launch();
    }
}
```

### Installing SceneBuilder

1. Download SceneBuilder from Gluon website
2. Install it according to your OS instructions
3. Configure the path in VSCode settings:

```json
"javafx.scenebuilder.home": "/path/to/SceneBuilder"
```

### Using SceneBuilder with VSCode

1. Right-click on an FXML file
2. Select "Open with SceneBuilder"
3. Edit the UI visually
4. Save to update the FXML file

---

