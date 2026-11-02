# Java Servlets Practical 1 --- Complete Eclipse Setup and Execution Guide

This document records the complete process followed to create and run
the first Java Servlet in **Eclipse IDE** using **JDK 21** and **Apache
Tomcat 10.1**.

The practical created a Servlet named `HelloServlet` that displays HTML
content in a browser.

------------------------------------------------------------------------

## 1. Software and configuration used

  Component                       Configuration
  ------------------------------- ----------------------------------------------------
  IDE                             Eclipse IDE for Enterprise Java and Web Developers
  Java                            JDK 21
  Java version shown in Eclipse   JavaSE-21
  Servlet container               Apache Tomcat 10.1
  Tomcat installation directory   `E:\Servers\apache-tomcat-10.1.61`
  Eclipse workspace               `E:\eclips`
  Project name                    `ServletPracticals`
  Java package                    `com.example`
  Servlet class                   `HelloServlet`
  Servlet URL mapping             `/hello`
  Dynamic Web Module version      `6.0`
  Project context root            `ServletPracticals`

> **Important compatibility note:** Tomcat 10.1 uses Jakarta Servlet
> APIs. Use imports beginning with `jakarta.servlet`, not the older
> `javax.servlet` package.

------------------------------------------------------------------------

## 2. Install and open Eclipse

1.  Download and install **Eclipse IDE for Enterprise Java and Web
    Developers**.
2.  Launch Eclipse.
3.  When Eclipse asks for a workspace, choose a folder. In this setup,
    the workspace was: `E:\eclips`
4.  Click **Launch** and wait for the Eclipse workbench to open.

The Enterprise Java and Web Developers edition includes tools for Java
web applications, Servlets, JSP and server configuration.

------------------------------------------------------------------------

## 3. Configure JDK 21 in Eclipse

Eclipse must know which Java installation to use when compiling and
running the project.

### 3.1 Open Installed JREs

1.  Click **Window → Preferences**.
2.  In the left panel, expand **Java**.
3.  Click **Installed JREs**.

### 3.2 Select JDK 21

The Installed JREs list showed several Java installations, including:

-   `jdk-21` --- `C:\Program Files\Java\jdk-21`
-   Java 8 installations
-   A default JRE managed by another tool

1.  Select the checkbox beside `jdk-21`.
2.  Confirm that `jdk-21` is marked as the default.
3.  Click **Apply and Close**.

**Why this matters:** The JDK contains the Java compiler and development
tools. Selecting JDK 21 ensures new projects use the intended Java
version.

------------------------------------------------------------------------

## 4. Add Apache Tomcat 10.1 to Eclipse

Tomcat is the Servlet container. It receives HTTP requests, runs
Servlets and sends HTTP responses to browsers.

### 4.1 Open the Servers view

1.  Click **Window → Show View → Servers**.
2.  If Servers is not listed, choose **Window → Show View → Other**.
3.  Expand **Server**.
4.  Select **Servers** and click **Open**.

The Servers view initially displayed:

`No servers are available. Click this link to create a new server.`

### 4.2 Create a new Tomcat server

1.  Click the link to create a new server.
2.  In the **New Server** window, find and expand **Apache**.
3.  Select **Tomcat v10.1 Server**.
4.  Keep the server hostname as `localhost`.
5.  Keep the server name as `Tomcat v10.1 Server at localhost`.
6.  Click **Next**.

### 4.3 Set the Tomcat installation directory

On the Tomcat Server configuration page:

1.  Keep the name as `Apache Tomcat v10.1`.

2.  Beside **Tomcat installation directory**, click **Browse**.

3.  Select the existing Tomcat folder:

    `E:\Servers\apache-tomcat-10.1.61`

4.  In the **JRE** dropdown, select `jdk-21`.

5.  Click **Finish**.

The Servers view should now contain:

`Tomcat v10.1 Server at localhost [Stopped]`

This means Eclipse has registered the server. It is not necessarily
running yet.

------------------------------------------------------------------------

## 5. Create a Dynamic Web Project

A Dynamic Web Project provides the structure needed for a Java web
application.

### 5.1 Open the project wizard

1.  Click **File → New → Dynamic Web Project**.
2.  If it is not visible in the menu, use **File → New → Other**, expand
    **Web**, and choose **Dynamic Web Project**.

### 5.2 Enter the project settings

Use these settings:

  Field                        Value
  ---------------------------- -------------------------------------------------
  Project name                 `ServletPracticals`
  Use default location         Checked
  Target runtime               `Apache Tomcat v10.1`
  Dynamic web module version   `6.0`
  Configuration                `Default Configuration for Apache Tomcat v10.1`

Click **Next**.

### 5.3 Configure the Java build path

The Java configuration page showed:

-   Source folder: `src\main\java`
-   Default output folder: `build\classes`

Keep these values unchanged and click **Next**.

### 5.4 Configure the Web Module

The Web Module page contains the context root and content directory.

Set:

  Field                                    Value
  ---------------------------------------- ---------------------
  Context root                             `ServletPracticals`
  Content directory                        `src/main/webapp`
  Generate web.xml deployment descriptor   Unchecked

**Important:** The context root must be `ServletPracticals`, not
`ServletPracticalsServletPracticals`. If the wizard shows the duplicated
name, select the entire Context root value and correct it before
finishing.

Leave **Generate web.xml deployment descriptor** unchecked because this
practical uses the `@WebServlet` annotation to map the Servlet.

Click **Finish**.

### 5.5 If an Import Projects window appears

An Import Projects window appeared during this setup and displayed the
message that the folder was already imported.

That window was not needed because Eclipse had already created or
recognized the project.

1.  Click **Cancel** in the Import Projects window.
2.  Return to Project Explorer.
3.  Confirm that the project appears.

The project was initially displayed with a duplicated name. It was
corrected using:

1.  Right-click the project in Project Explorer.
2.  Select **Refactor → Rename**, or select it and press `F2`.
3.  Enter `ServletPracticals`.
4.  Confirm the rename.

The project should be named `ServletPracticals`.

------------------------------------------------------------------------

## 6. Verify the project structure

Expand the project in Project Explorer.

The important parts of the structure should look similar to this:

``` text
ServletPracticals
├── Deployment Descriptor
├── JAX-WS Web Services
├── Java Resources
│   ├── src/main/java
│   └── Libraries
│       ├── JRE System Library [JavaSE-21]
│       └── Server Runtime [Apache Tomcat v10.1]
├── build
└── src
    └── main
        ├── java
        └── webapp
            ├── META-INF
            │   └── MANIFEST.MF
            └── WEB-INF
                └── lib
```

The main locations are:

-   `src/main/java` --- Java source code, including Servlet classes.
-   `src/main/webapp` --- web application files such as HTML, CSS and
    JavaScript.
-   `WEB-INF` --- web application configuration and protected resources.
-   `Libraries` --- Java and Tomcat runtime libraries.

Expand **Java Resources** and confirm that the project has:

-   `src/main/java`
-   `JRE System Library [JavaSE-21]`
-   `Server Runtime [Apache Tomcat v10.1]`

------------------------------------------------------------------------

## 7. Create the first Servlet using the Eclipse wizard

A Servlet is a Java class that runs inside a Servlet container such as
Tomcat. It can receive HTTP requests and generate HTTP responses.

### 7.1 Open the Create Servlet wizard

1.  In Project Explorer, expand **Java Resources**.
2.  Right-click `src/main/java`.
3.  Select **New → Servlet**.

If Servlet is not listed:

1.  Select **New → Other**.
2.  Expand **Web**.
3.  Select **Servlet**.
4.  Click **Next**.

### 7.2 Enter the Servlet class details

In the Create Servlet wizard, use:

  Field           Value
  --------------- ------------------------------------
  Project         `ServletPracticals`
  Source folder   `/ServletPracticals/src/main/java`
  Java package    `com.example`
  Class name      `HelloServlet`
  Superclass      `jakarta.servlet.http.HttpServlet`

Keep **Use an existing Servlet class or JSP** unchecked.

Click **Next**.

### 7.3 Configure the Servlet URL mapping

The next page contains the Servlet name and URL mappings.

-   Name: `HelloServlet`
-   Description: leave empty
-   Initialization parameters: leave empty
-   URL mapping: change the default `/HelloServlet` to `/hello`
-   Asynchronous Support: leave unchecked

To change the mapping:

1.  Select `/HelloServlet`.
2.  Click **Edit**.
3.  Enter `/hello`.
4.  Click **OK**.

Click **Next**.

### 7.4 Select the HTTP method

The method selection page offers method stubs such as `doGet`, `doPost`,
`doPut` and `doDelete`.

For this first practical:

-   Keep `doGet` checked.
-   Uncheck `doPost`.
-   Keep `public` selected.
-   Leave the other HTTP method options unchecked.
-   Keep the generated constructor and inherited abstract method options
    as they are.

Click **Finish**.

Eclipse creates `HelloServlet.java` in the `com.example` package.

------------------------------------------------------------------------

## 8. Understand the code generated by Eclipse

Eclipse generated a Servlet class similar to the following:

``` java
package com.example;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;

/**
 * Servlet implementation class HelloServlet
 */
@WebServlet("/hello")
public class HelloServlet extends HttpServlet {

    private static final long serialVersionUID = 1L;

    /**
     * @see HttpServlet#HttpServlet()
     */
    public HelloServlet() {
        super();
        // TODO Auto-generated constructor stub
    }

    /**
     * @see HttpServlet#doGet(HttpServletRequest request,
     *      HttpServletResponse response)
     */
    protected void doGet(HttpServletRequest request,
                         HttpServletResponse response)
            throws ServletException, IOException {

        // TODO Auto-generated method stub
        response.getWriter().append("Served at: ")
                .append(request.getContextPath());
    }
}
```

### 8.1 Package declaration

``` java
package com.example;
```

A package organizes related Java classes. The `HelloServlet` class
belongs to the `com.example` package.

### 8.2 Imports

  -----------------------------------------------------------------------
  Import                              Purpose
  ----------------------------------- -----------------------------------
  `ServletException`                  Represents Servlet-related
                                      exceptions

  `WebServlet`                        Provides annotation-based Servlet
                                      URL mapping

  `HttpServlet`                       Base class for HTTP Servlets

  `HttpServletRequest`                Provides information about the
                                      incoming request

  `HttpServletResponse`               Used to construct the response

  `IOException`                       Represents input/output errors
  -----------------------------------------------------------------------

We later added:

``` java
import java.io.PrintWriter;
```

`PrintWriter` is used to write text and HTML into the HTTP response.

### 8.3 The `@WebServlet` annotation

``` java
@WebServlet("/hello")
```

This annotation maps the Servlet to the `/hello` URL.

When a browser requests the `/hello` path under this web application,
Tomcat can route the request to `HelloServlet`.

### 8.4 The class declaration

``` java
public class HelloServlet extends HttpServlet {
```

-   `public` makes the class accessible.
-   `class` declares a Java class.
-   `HelloServlet` is the class name.
-   `extends HttpServlet` inherits HTTP Servlet functionality.

### 8.5 The serial version field

``` java
private static final long serialVersionUID = 1L;
```

This is a serialization version identifier. Eclipse generates it as part
of the Servlet class template.

### 8.6 The constructor

``` java
public HelloServlet() {
    super();
}
```

The constructor runs when an instance of the class is created. `super()`
calls the parent class constructor.

### 8.7 The `doGet()` method

``` java
protected void doGet(HttpServletRequest request,
                     HttpServletResponse response)
        throws ServletException, IOException {
```

`doGet()` handles HTTP GET requests.

-   `request` contains information sent by the client.
-   `response` is used to send information back to the client.
-   `throws` declares exceptions that the method may propagate.

When the browser opens the Servlet URL, it normally sends a GET request.
Tomcat routes it to this method.

------------------------------------------------------------------------

## 9. Build the HTML response gradually

The generated code was not replaced all at once. The method was
developed one step at a time to understand what each statement does.

### 9.1 Set the response content type

Inside `doGet()`, add:

``` java
response.setContentType("text/html");
```

This tells the browser that the response contains HTML.

Examples of common content types:

  Content type         Meaning
  -------------------- --------------
  `text/html`          HTML content
  `text/plain`         Plain text
  `application/json`   JSON data

### 9.2 Create a PrintWriter

Add this import at the top of the file:

``` java
import java.io.PrintWriter;
```

Then add this statement inside `doGet()`:

``` java
PrintWriter out = response.getWriter();
```

Explanation:

-   `PrintWriter` is the Java class used to write character data.
-   `out` is the variable name.
-   `response.getWriter()` obtains a writer for the HTTP response.

The Servlet can now use `out` to send text or HTML to the browser.

### 9.3 Write the first HTML heading

Add:

``` java
out.println("<h1>Hello World</h1>");
```

`println()` writes the supplied text to the response.

The browser interprets `<h1>` as a level-one HTML heading and displays
`Hello World` as a large heading.

### 9.4 Add a second heading

Add:

``` java
out.println("<h2>My First Servlet Program</h2>");
```

HTML headings range from `<h1>` to `<h6>`. The `<h1>` heading is
generally the highest-level heading, while `<h6>` is the lowest-level
heading.

### 9.5 Add a paragraph

Add:

``` java
out.println("<p>Servlet is working successfully.</p>");
```

The `<p>` tag creates a paragraph.

### 9.6 Remove the generated demonstration output

Eclipse initially generated:

``` java
response.getWriter().append("Served at: ")
        .append(request.getContextPath());
```

This was removed because the practical now sends its own HTML through
the `out` variable.

------------------------------------------------------------------------

## 10. Final `HelloServlet.java` code

After completing the steps above, the relevant Servlet code is:

``` java
package com.example;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;
import java.io.PrintWriter;

@WebServlet("/hello")
public class HelloServlet extends HttpServlet {

    private static final long serialVersionUID = 1L;

    public HelloServlet() {
        super();
    }

    @Override
    protected void doGet(HttpServletRequest request,
                         HttpServletResponse response)
            throws ServletException, IOException {

        response.setContentType("text/html");

        PrintWriter out = response.getWriter();

        out.println("<h1>Hello World</h1>");
        out.println("<h2>My First Servlet Program</h2>");
        out.println("<p>Servlet is working successfully.</p>");
    }
}
```

Save the file using **Ctrl + S**.

### Explanation of the complete request-response flow

``` text
Browser
   |
   | 1. Sends GET request to /ServletPracticals/hello
   v
Tomcat 10.1
   |
   | 2. Finds @WebServlet("/hello")
   v
HelloServlet
   |
   | 3. Executes doGet(request, response)
   v
PrintWriter
   |
   | 4. Writes HTML into the response
   v
Tomcat sends HTTP response
   |
   v
Browser displays the HTML
```

------------------------------------------------------------------------

## 11. Start Tomcat and run the Servlet

### 11.1 First attempt and port conflict

When the project was first run, Eclipse displayed an error similar to:

`Several ports (8005, 8080, 8009) required by Tomcat v10.1 Server at localhost are already in use.`

This happened because another Tomcat process was already using one or
more of the required ports. The earlier Tomcat instance had been started
outside Eclipse.

The Servlet code was not the cause of this error.

### 11.2 Stop the previous Tomcat process

Open Windows Command Prompt:

1.  Press `Windows + R`.
2.  Type `cmd`.
3.  Press Enter.
4.  Execute:

``` cmd
cd /d E:\Servers\apache-tomcat-10.1.61\bin
```

5.  Then execute:

``` cmd
shutdown.bat
```

This sends a shutdown request to the Tomcat instance associated with
that installation.

### 11.3 Start Tomcat from Eclipse

1.  Return to Eclipse.
2.  Open the **Servers** view.
3.  Find `Tomcat v10.1 Server at localhost`.
4.  Right-click the actual server entry, not the Tomcat configuration
    folder in Project Explorer.
5.  Click **Start**.

The server status changed to:

`Tomcat v10.1 Server at localhost [Started, Synchronized]`

This confirmed that Tomcat was running successfully under Eclipse.

### 11.4 If the project is not deployed

If `ServletPracticals` does not appear beneath the Tomcat server:

1.  Right-click the Tomcat server in the Servers view.
2.  Select **Add and Remove...**.
3.  Select `ServletPracticals` under Available.
4.  Click **Add** to move it to Configured.
5.  Click **Finish**.

------------------------------------------------------------------------

## 12. Open the Servlet in the browser

After Tomcat starts and the project is deployed, open:

``` text
http://localhost:8080/ServletPracticals/hello
```

The URL consists of:

  URL part               Meaning
  ---------------------- ------------------------------
  `localhost`            Your own computer
  `8080`                 Tomcat's HTTP port
  `/ServletPracticals`   Web application context root
  `/hello`               Servlet URL mapping

### Expected output

The browser displayed:

# Hello World

## My First Servlet Program

Servlet is working successfully.

This confirms that:

-   Eclipse compiled the Servlet.
-   Tomcat deployed the web application.
-   The URL mapping worked.
-   Tomcat invoked the `doGet()` method.
-   The Servlet sent HTML to the browser.
-   The browser rendered the HTML response.

**Practical 1 was completed successfully.**

------------------------------------------------------------------------

## 13. Important concepts learned

  -----------------------------------------------------------------------
  Concept                             Explanation
  ----------------------------------- -----------------------------------
  Eclipse IDE                         Environment used to create and
                                      manage the Java web project

  JDK 21                              Java development kit used to
                                      compile the code

  Dynamic Web Project                 Eclipse project type for Java web
                                      applications

  Apache Tomcat                       Servlet container that runs Java
                                      Servlets

  Servlet                             Java component that processes web
                                      requests

  `HttpServlet`                       Base class for HTTP-based Servlets

  `@WebServlet`                       Maps a Servlet to a URL

  `doGet()`                           Handles HTTP GET requests

  `HttpServletRequest`                Represents the incoming HTTP
                                      request

  `HttpServletResponse`               Represents the outgoing HTTP
                                      response

  `PrintWriter`                       Writes response content

  `setContentType()`                  Sets the response content type

  `out.println()`                     Writes text or HTML into the
                                      response
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## 14. Common mistakes and solutions

  ----------------------------------------------------------------------------
  Problem                 Cause                   Solution
  ----------------------- ----------------------- ----------------------------
  JDK 21 not selected     Eclipse uses another    Select `jdk-21` under
                          JRE                     Installed JREs

  Tomcat not found        Server runtime not      Add Tomcat 10.1 under
                          configured              Servers

  Servlet wizard not      Web tools menu not      Use New → Other → Web →
  visible                 opened                  Servlet

  Wrong URL               Incorrect mapping or    Check
                          context root            `/ServletPracticals/hello`

  Port already in use     Another Tomcat process  Stop the previous instance
                          is running              before starting Eclipse
                                                  Tomcat

  Project not appearing   Project not added to    Use Add and Remove
  under server            Tomcat                  

  `jakarta.servlet`       Servlet API/runtime     Verify Tomcat 10.1 runtime
  errors                  configuration issue     and project configuration

  Old generated output    Generated `Served at:`  Remove that statement if
  appears                 statement remains       using the custom HTML output
  ----------------------------------------------------------------------------

------------------------------------------------------------------------

## 15. Practical 1 completion checklist

-   [x] Installed and opened Eclipse IDE.
-   [x] Configured JDK 21.
-   [x] Registered Apache Tomcat 10.1.
-   [x] Created a Dynamic Web Project.
-   [x] Set the context root to `ServletPracticals`.
-   [x] Verified the project structure.
-   [x] Created `HelloServlet.java`.
-   [x] Set the URL mapping to `/hello`.
-   [x] Selected the `doGet()` method.
-   [x] Added the HTML response content type.
-   [x] Created a `PrintWriter` object.
-   [x] Wrote HTML headings and a paragraph.
-   [x] Resolved the Tomcat port conflict.
-   [x] Started Tomcat from Eclipse.
-   [x] Opened the Servlet in the browser.
-   [x] Verified the expected output.

------------------------------------------------------------------------

## 16. Next practicals

Continue using the same Eclipse project and Tomcat server unless a
practical requires a separate project.

  -----------------------------------------------------------------------
  Practical               Topic                   Main concepts
  ----------------------- ----------------------- -----------------------
  1                       Introduction to         Servlet, `HttpServlet`,
                          Servlets                `doGet()`, HTML
                                                  response

  2                       Display current date    Java date/time APIs and
                          and time                Servlet response

  3                       HTML and Servlet        HTML forms, request
                          communication           parameters, `doGet()`
                                                  and `doPost()`

  4                       Auto-refresh a page     HTML meta refresh or
                                                  response refresh
                                                  headers

  5                       Session tracking        `HttpSession`

  6                       Insert, delete and      JDBC and SQL
                          update database records 

  7                       Add cookie to selected  `Cookie` and HTTP
                          value                   response headers
  -----------------------------------------------------------------------

**Recommended practice:** Before starting Practical 2, try to explain
what `@WebServlet("/hello")`, `doGet()`, `HttpServletRequest`,
`HttpServletResponse` and `PrintWriter` do. Understanding these concepts
will make the next practicals easier.
