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
    testCompile('org.springframework.boot:spring-boot-starter-test')
    testCompile group: 'org.mockito', name: 'mockito-core', version: '2.23.0'
}
```

## **Setup IntelliJ**
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

## **Development**

Using the standard Controller + Service implemenation. The controller implements the API contracts, but contains little to no business logic. That logic is pushed down into the Service class.

### Create a Service
Using standard unit testing style, it tests the implementation of the Service.

`src/test/java/hello/HelloServiceTest.java`
```
package hello;

import org.junit.Test;

import static org.hamcrest.core.Is.is;
import static org.junit.Assert.assertThat;

public class HelloServiceTest {
    @Test
    public void retrieveHelloWorldStatementReturnsHelloWorldStatement() {
        HelloService helloService = new HelloService();

        String helloWorldStatement = helloService.retrieveHelloWorldStatement();

        assertThat(helloWorldStatement, is("Hello World!"));
    }
}
```
`src/main/java/hello/HelloService.java`
```
package hello;

import org.springframework.stereotype.Component;

@Component
public class HelloService {

    public String retrieveHelloWorldStatement() {
        return "Hello World!";
    }

}

```

### Add a Controller
Testing with MockMVC and mocked service. This allows testing the Spring annotations are configured correctly and the implementation uses the Service class rather than containing a lot of internal logic.

`src/test/java/hello/HelloControllerTest.java`.
```
package hello;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;

import static org.hamcrest.Matchers.containsString;
import static org.mockito.Mockito.when;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultHandlers.print;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@RunWith(SpringRunner.class)
@SpringBootTest
@AutoConfigureMockMvc
public class HelloControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private HelloService mockHelloService;

    @Test
    public void shouldReturnServiceHelloWorldStatement() throws Exception {
        when(mockHelloService.retrieveHelloWorldStatement()).thenReturn("Hello PillarCon");

        mockMvc.perform(get("/"))
                .andDo(print())
                .andExpect(status().isOk())
                .andExpect(content().string(containsString("Hello PillarCon")));
    }
}
```


`src/main/java/hello/HelloController.java`
```
package hello;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    private HelloService helloService;

    @Autowired
    public HelloController(HelloService helloService) {
        this.helloService = helloService;
    }

    @RequestMapping("/")
    public String index() {
        return helloService.retrieveHelloWorldStatement();
    }

}
```


### Notes
Java 11 and Mockito 2.24.0 are incompatible. Downgrade to 2.23.0 until a new version functions appropriately with Java 11.