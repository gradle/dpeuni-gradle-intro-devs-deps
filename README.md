# DPE University Training

<p align="left">
<img width="10%" height="10%" src="https://user-images.githubusercontent.com/120980/174325546-8558160b-7f16-42cb-af0f-511849f22ebc.png">
</p>

## Gradle Tasks Exercise

This is a hands-on exercise to go along with the
[Introduction to Gradle Build Tool for Developers](https://dpeuniversity.gradle.com/app/courses/012de84f-fcd3-45d4-9c4c-284382eb3f3f)
training module. In this exercise you will go over the following:

* See dependencies for a project
* Adding dependencies
* Examining dependency conflict resolution

---
## Prerequisites

* Completed the first and second hands-on exercise
* You can perform the exercises in the same Gradle project used in previous exercises

---
## See Dependencies for a Project

1. Open the terminal run
   `./gradlew :app:dependencies`. You will see dependencies grouped by
   configurations.

<p align="center">
<img width="60%" height="60%" src="https://user-images.githubusercontent.com/120980/174330482-f470305a-210f-4780-b47e-5340c6a7de70.png">
</p>

2. To see the dependencies of just the runtimeClasspath configuration run
   `./gradlew :app:dependencies --configuration=runtimeClasspath`

<p align="center">
<img width="60%" height="60%" src="https://user-images.githubusercontent.com/120980/174330540-ed0e3ad2-d76b-4cca-8830-7c27d324261c.png">
</p>

---
## Adding Dependencies

Let's add some more functionality to our application. Currently it only prints
a greeting message. Let's make it fetch some data from a url and print it. We
will use the [Google Http Client](https://github.com/googleapis/google-http-java-client)
for this.

We need to get the Gradle dependency statement for this library. Follow the
steps below to get this:

1. Go to [https://mvnrepository.com/](https://mvnrepository.com/)

2. In the search bar type `google http client`

3. One of the top results will be `com.google.http-client » google-http-client`,
   click on it, and then click on the `1.44.1` version

4. Click on the `Gradle (Kotlin)` tab

5. Copy just the module ID and version, not the dependency configuration

<p align="center">
<img width="60%" height="60%" src="https://github.com/gradle/dpeuni-gradle-build-cache-deep-dive-runtime-api/assets/120980/8d6c3e91-9370-4d86-8ba5-89fb446463f3">
</p>

6. Open the version catalog file which is `gradle/libs.versions.toml`

7. Under the `libraries` section, add an entry called `google-http-client` and set it to the copied contents:

```toml
google-http-client = "com.google.http-client:google-http-client:1.44.1"
```

8. If using IntelliJ, hit the Gradle refresh icon

<p align="center">
<img width="20%" height="20%" src="https://github.com/gradle/dpeuni-gradle-build-cache-deep-dive-runtime-api/assets/120980/481adcf1-a98c-419e-9576-dc913c04de8c">
</p>

9. In `app/build.gradle.kts`, in the `dependencies` section add an `implementation` dependency to `libs.google.http.client`. Notice that instead of dashes you will use dots:

```
implementation(libs.google.http.client)
```

10. Run `./gradlew :app:dependencies --configuration=runtimeClasspath` again
    and notice the additional dependencies - both direct and transitive.

<p align="center">
<img width="60%" height="60%" src="https://github.com/gradle/dpeuni-gradle-build-cache-deep-dive-runtime-api/assets/120980/91ae502c-d7d2-4964-9b96-299c3aa59b98">
</p>

---
## Examining Dependency Conflict Resolution

1. Notice in the dependencies, the `google-http-client` library depends on
   `guava`, which is also a direct dependency.

<p align="center">
<img width="60%" height="60%" src="https://github.com/gradle/dpeuni-gradle-build-cache-deep-dive-runtime-api/assets/120980/f83d46ab-f8be-4fad-a1d9-4706dfcdf3b4">
</p>

2. The `guava` version `google-http-client` depends on is older than the
   version in the direct dependency, so you will see Gradle handles the
   conflict by using the highest version of the `guava` library.

<p align="center">
<img width="60%" height="60%" src="https://github.com/gradle/dpeuni-gradle-build-cache-deep-dive-runtime-api/assets/120980/3f596dd7-537c-421b-92b7-4d9501f1bf82">
</p>

---
## Update Application

1. Open the `app/src/main/java/com/gradle/lab/App.java` file and replace the
   contents *after the package declaration* with the following:

```java
import com.google.api.client.http.GenericUrl;
import com.google.api.client.http.HttpRequest;
import com.google.api.client.http.HttpRequestFactory;
import com.google.api.client.http.javanet.NetHttpTransport;

import java.io.IOException;

public class App {
    public String getGreeting() {
        return "Hello World!";
    }

    public String getUrl() {
        // This is a small website and easily prints.
        return "https://wiby.me/";
    }

    public static void main(String[] args) throws IOException {
        App app = new App();
        System.out.println(app.getGreeting());

        HttpRequestFactory requestFactory = new NetHttpTransport().createRequestFactory();
        HttpRequest request = requestFactory.buildGetRequest(new GenericUrl(app.getUrl()));
        String rawResponse = request.execute().parseAsString();
        System.out.println("\n---------\n");
        System.out.println(rawResponse);
    }
}
```

2. Then open the terminal and execute `./gradlew :app:run`, notice the website
   contents are also printed.

<p align="center">
<img width="70%" height="70%" src="https://user-images.githubusercontent.com/120980/174330977-494f5958-e164-4d29-b4d9-180cec1bd56d.png">
</p>

---
## Solution Reference

If you get stuck you can refer to the project files in this repository.