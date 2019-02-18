# Java Spring Boot Kata
This is a repository for an exercise in setting up a simple Spring Boot application using Java.

# Pre-Reqs
## **Java**
Check installation:
`java -version`

Option 1: Install using Brew (Uses OpenJDK)
 - `brew update`
 - `brew tap homebrew/cask-versions`
 - `brew cask install java`

Option 2: Install from Oracle (Uses Oracle JDK - allows for licensing Long Term Support)
   - [Java 8 (LTS)](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
   - [Java 11 (LTS)](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)

Note: [Oracle JDK Licensing](https://stackoverflow.com/questions/22358071/differences-between-oracle-jdk-and-openjdk)

```
Starting with JDK 11 accessing the long time support Oracle JDK/Java SE will now require a commercial license. You should now pay attention to which JDK you're installing as Oracle JDK without subscription could stop working.
```

## **Gradle**
Check installation: `gradle --version`

Install using Brew 
 - `brew install gradle`

 # Building from Scratch w/ Gradle
https://spring.io/guides/gs/spring-boot/#scratch

## **Curated Instructions**
Assuming you have created a desired project directory and are navigated inside of it

- `mkdir -p src/main/java/hello`
- create `build.gradle` file at root of project
```
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:2.0.5.RELEASE")
    }
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'idea'
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'

bootJar {
    baseName = 'gs-spring-boot'
    version =  '0.1.0'
}

repositories {
    mavenCentral()
}

sourceCompatibility = 11 // replace with 1.8 for Java 8
targetCompatibility = 11 // replace with 1.8 for Java 8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-web")
    testCompile("junit:junit")
}
```

## Setup IntelliJ
Set Project SDK
- `File` -> `Project Structure` -> `Project` -> Set Project SDK to your Java installation

Import Gradle Project
- Restart IntelliJ
- Upon reopening, select the `Import Gradle Project` popup
  - If you miss the popup, select the text at the bottom of the IDE `Unlinked Gradle Project?: Import...` and then import
- Within the `Import Module from Gradle` menu
  - Check `Use Auto-import`
    - This allows build.gradle changes to be reflected immediately
  - Gradle home: `/usr/local/Cellar/gradle/X.X/libexec` 
    - X.X is the version of gradle installed through Brew
  - Everything else should be able to be left as the default
  - Select Apply


## **Application Entry**
Every Java project requires a main method as the entry point to the application. We need ours to run Spring.

Create the Java class your main method will live within
 - ex: `src/main/java/hello/Application.java`
 ```
package hello;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

}
 ```

 Adding Tests


 ##
