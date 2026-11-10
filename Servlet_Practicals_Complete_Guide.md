# Java Servlet Practicals -- Complete Lab Record

**Technology:** Java Servlets, HTML, JDBC, MySQL\
**IDE:** Eclipse\
**Server:** Apache Tomcat\
**Servlet API:** Jakarta Servlet (`jakarta.servlet.*`)

## Contents

1.  Session Tracking using `HttpSession`
2.  Add and Retrieve a Cookie
3.  Display Current Date and Time with Automatic Refresh
4.  HTML Form and Servlet Communication
5.  JDBC CRUD Operations -- Insert, Update, Delete Student Records

------------------------------------------------------------------------

## Common Setup

Before running these programs:

1.  Create a **Dynamic Web Project** in Eclipse.
2.  Configure Apache Tomcat in Eclipse and add it to the project.
3.  Use the Jakarta Servlet API supported by your Tomcat version. The
    examples below use `jakarta.servlet.*` (Tomcat 10 or later).
4.  Create Java servlet classes inside your project's Java source folder
    and HTML files inside the web application content folder (commonly
    `src/main/webapp` or `WebContent`).
5.  Run the project on the server and open the HTML page through the
    Tomcat URL. Do not open the HTML file directly from the file system.

> **Important:** The `action` in an HTML form must match the servlet's
> `@WebServlet` URL pattern exactly.

------------------------------------------------------------------------

# Practical 1: Session Tracking using HttpSession

## Aim

Develop a Servlet application to accept student details through an HTML
form and demonstrate session tracking.

## Concept

HTTP is stateless. It does not automatically remember a user between
separate requests. A session allows the server to store information for
a particular user across requests.

Important methods:

  ------------------------------------------------------------------------
  Method                               Purpose
  ------------------------------------ -----------------------------------
  `request.getSession()`               Gets the current session or creates
                                       a new one

  `request.getSession(false)`          Gets the existing session; returns
                                       `null` if none exists

  `session.setAttribute(key, value)`   Stores data in the session

  `session.getAttribute(key)`          Retrieves stored data

  `session.invalidate()`               Ends the session
  ------------------------------------------------------------------------

## HTML: `SessionTracking.html`

``` html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Session Tracking</title>
</head>
<body>
    <h1>Student Session Tracking</h1>

    <form action="SessionTracking" method="post">
        Enter Student Name:
        <input type="text" name="UserName" required>
        <input type="submit" value="Create Session">
    </form>
</body>
</html>
```

## Servlet: `SessionTracking.java`

``` java
package com.example;

import java.io.IOException;
import java.io.PrintWriter;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import jakarta.servlet.http.HttpSession;

@WebServlet("/SessionTracking")
public class SessionTracking extends HttpServlet {
    private static final long serialVersionUID = 1L;

    protected void doGet(HttpServletRequest request,
                         HttpServletResponse response)
            throws ServletException, IOException {

        response.setContentType("text/html");
        PrintWriter out = response.getWriter();

        // Do not create a new session just to check for one.
        HttpSession session = request.getSession(false);

        if (session != null) {
            String name = (String) session.getAttribute("UserName");
            out.println("<h1>Welcome " + name + "</h1>");
            out.println("<a href='SessionTracking?logout=true'>Logout</a>");
        } else {
            out.println("<h2>No Session Found</h2>");
            out.println("<a href='SessionTracking.html'>Create Session</a>");
        }

        if ("true".equals(request.getParameter("logout")) && session != null) {
            session.invalidate();
            response.getWriter().println("<p>Session invalidated. Refresh the page.</p>");
        }
    }

    protected void doPost(HttpServletRequest request,
                          HttpServletResponse response)
            throws ServletException, IOException {

        response.setContentType("text/html");
        PrintWriter out = response.getWriter();

        String name = request.getParameter("UserName");

        HttpSession session = request.getSession();
        session.setAttribute("UserName", name);

        out.println("<h1>Session Created for " + name + "</h1>");
        out.println("<a href='SessionTracking'>Track Session</a>");
    }
}
```

## How to Run

1.  Run the project on Tomcat.
2.  Open `SessionTracking.html` in the browser through the application
    URL.
3.  Enter a student name and click **Create Session**.
4.  Click **Track Session**. The servlet reads the name stored in the
    session.
5.  To test `getSession(false)`, invalidate the session or open a fresh
    browser session and request the servlet.

## Expected Output

-   After form submission: `Session Created for <student name>`
-   After clicking Track Session: `Welcome <student name>`
-   When no session exists: `No Session Found`

## Important Viva Question

**Why do we use `request.getSession(false)`?**

It checks whether a session already exists without creating a new one.
If no session exists, it returns `null`. In contrast,
`request.getSession()` creates a session when necessary.

------------------------------------------------------------------------

# Practical 2: Add and Retrieve a Cookie

## Aim

Develop a Servlet application to accept a selected value from an HTML
page, store it in a cookie, and retrieve the cookie value.

## Concept

A cookie is a small piece of data stored in the client's browser. The
server sends it using the response, and the browser sends it back in
later requests to the same applicable site.

Important methods:

  Method                         Purpose
  ------------------------------ ----------------------------------
  `new Cookie(name, value)`      Creates a cookie
  `response.addCookie(cookie)`   Sends the cookie to the browser
  `request.getCookies()`         Gets cookies sent by the browser
  `cookie.getName()`             Gets the cookie name
  `cookie.getValue()`            Gets the cookie value
  `cookie.setMaxAge(seconds)`    Sets the cookie lifetime

## HTML: `Cookies.html`

``` html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Cookies</title>
</head>
<body>
    <h1>Favourite Programming Language</h1>

    <form action="Cookies" method="post">
        Select Language:
        <select name="lang">
            <option value="Java">Java</option>
            <option value="C">C</option>
            <option value="C++">C++</option>
        </select>

        <input type="submit" value="Add Cookie">
    </form>

    <p><a href="Cookies">Retrieve Cookie</a></p>
</body>
</html>
```

## Servlet: `Cookies.java`

``` java
package com.example;

import java.io.IOException;
import java.io.PrintWriter;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.Cookie;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/Cookies")
public class Cookies extends HttpServlet {
    private static final long serialVersionUID = 1L;

    protected void doPost(HttpServletRequest request,
                          HttpServletResponse response)
            throws ServletException, IOException {

        response.setContentType("text/html");
        PrintWriter out = response.getWriter();

        String lang = request.getParameter("lang");

        Cookie cookie = new Cookie("FavLang", lang);
        cookie.setMaxAge(60 * 60); // 1 hour
        response.addCookie(cookie);

        out.println("<h2>Cookie Added Successfully!</h2>");
        out.println("<p>Selected Language: " + lang + "</p>");
        out.println("<a href='Cookies'>Retrieve Cookie</a>");
    }

    protected void doGet(HttpServletRequest request,
                         HttpServletResponse response)
            throws ServletException, IOException {

        response.setContentType("text/html");
        PrintWriter out = response.getWriter();

        Cookie[] cookies = request.getCookies();
        boolean found = false;

        if (cookies != null) {
            for (Cookie cookie : cookies) {
                if ("FavLang".equals(cookie.getName())) {
                    out.println("<h2>Favourite Language: "
                            + cookie.getValue() + "</h2>");
                    found = true;
                    break;
                }
            }
        }

        if (!found) {
            out.println("<h2>Favourite language cookie not found.</h2>");
        }
    }
}
```

## How to Run

1.  Run the project on Tomcat.
2.  Open `Cookies.html`.
3.  Select a language and click **Add Cookie**.
4.  Click **Retrieve Cookie**.
5.  The servlet reads the browser's cookie and displays its value.

## Expected Output

For example, if Java is selected:

-   `Cookie Added Successfully!`
-   `Favourite Language: Java`

## Where to Check Cookies in Microsoft Edge

1.  Open the application in Edge.
2.  Press `F12` or `Ctrl + Shift + I` to open Developer Tools.
3.  Open the **Application** tab.
4.  Under **Storage**, expand **Cookies** and select your localhost
    site.
5.  Find the cookie named `FavLang`.

If the Application tab is not visible, select the `>>` overflow menu in
Developer Tools. You can also open Edge Settings and search for site
data/cookies.

------------------------------------------------------------------------

# Practical 3: Display Current Date and Time with Automatic Refresh

## Aim

Develop a Servlet application to display the current date and time and
automatically refresh the page.

## Concept

-   `new java.util.Date()` returns the current date and time.
-   The HTML meta refresh instruction reloads the page after the
    specified number of seconds.
-   The browser sends another request to the servlet after each refresh,
    so the displayed time is updated.

## Servlet: `DateTime.java`

``` java
package com.example;

import java.io.IOException;
import java.io.PrintWriter;
import java.util.Date;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/DateTime")
public class DateTime extends HttpServlet {
    private static final long serialVersionUID = 1L;

    protected void doGet(HttpServletRequest request,
                         HttpServletResponse response)
            throws ServletException, IOException {

        response.setContentType("text/html");
        PrintWriter out = response.getWriter();

        out.println("<!DOCTYPE html>");
        out.println("<html><head><title>Date and Time</title>");
        out.println("<meta http-equiv='refresh' content='5'>");
        out.println("</head><body>");
        out.println("<h1>Current Date and Time</h1>");
        out.println("<h2>" + new Date() + "</h2>");
        out.println("<p>Page refreshes automatically every 5 seconds.</p>");
        out.println("</body></html>");
    }
}
```

## How to Run

1.  Run the project on Tomcat.
2.  Open the servlet URL:
    `http://localhost:8080/YourProjectName/DateTime`.
3.  Observe the displayed date and time.
4.  Wait five seconds. The page reloads and displays the updated time.

## Expected Output

The page displays the current date and time and refreshes automatically
every five seconds.

> To change the refresh interval, change `content='5'` to another
> number. The value is in seconds.

------------------------------------------------------------------------

# Practical 4: HTML Form and Servlet Communication

## Aim

Develop an HTML page to accept student details and a Servlet to receive
and display the submitted information.

## Concept

1.  The user enters details in an HTML form.
2.  The form sends the values to the servlet using the HTTP `POST`
    method.
3.  The servlet reads the submitted values using
    `request.getParameter()`.
4.  The servlet displays the received information using `PrintWriter`.

## HTML: `StudentConn.html`

``` html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Student Management</title>
</head>
<body>
    <h2>Student Management System</h2>

    <form action="StudentConn" method="post">
        Student ID:
        <input type="number" name="id" required>
        <br><br>

        Student Name:
        <input type="text" name="name" required>
        <br><br>

        Email:
        <input type="email" name="email" required>
        <br><br>

        Branch:
        <input type="text" name="branch" required>
        <br><br>

        <input type="submit" value="Submit">
    </form>
</body>
</html>
```

## Servlet: `StudentConn.java`

``` java
package com.example;

import java.io.IOException;
import java.io.PrintWriter;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/StudentConn")
public class StudentConn extends HttpServlet {
    private static final long serialVersionUID = 1L;

    protected void doPost(HttpServletRequest request,
                          HttpServletResponse response)
            throws ServletException, IOException {

        response.setContentType("text/html");
        PrintWriter out = response.getWriter();

        int id = Integer.parseInt(request.getParameter("id"));
        String name = request.getParameter("name");
        String email = request.getParameter("email");
        String branch = request.getParameter("branch");

        out.println("<html><body>");
        out.println("<h2>Student Details Received</h2>");
        out.println("<p>Student ID: " + id + "</p>");
        out.println("<p>Student Name: " + name + "</p>");
        out.println("<p>Email: " + email + "</p>");
        out.println("<p>Branch: " + branch + "</p>");
        out.println("</body></html>");
    }
}
```

## How to Run

1.  Run the project on Tomcat.
2.  Open `StudentConn.html`.
3.  Enter Student ID, Name, Email, and Branch.
4.  Click **Submit**.
5.  The servlet receives the form values and displays them.

## Expected Output

The servlet displays the submitted Student ID, Student Name, Email, and
Branch.

## Important Viva Question

**What is the purpose of `request.getParameter("name")`?**

It retrieves the value submitted by the form field whose `name`
attribute is `name`.

------------------------------------------------------------------------

# Practical 5: JDBC CRUD Operations -- Insert, Update, Delete

## Aim

Develop a Servlet application to insert, update, and delete student
records in a MySQL database using JDBC.

## Concept

CRUD stands for:

  Operation   SQL command   Purpose
  ----------- ------------- -----------------------------
  Create      `INSERT`      Adds a new record
  Read        `SELECT`      Retrieves records
  Update      `UPDATE`      Modifies an existing record
  Delete      `DELETE`      Removes a record

This practical implements Insert, Update, and Delete.

## Step 1: Create the Database and Table

Open MySQL Workbench or your MySQL command-line client and execute:

``` sql
CREATE DATABASE college;

USE college;

CREATE TABLE student (
    id INT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    email VARCHAR(100),
    branch VARCHAR(30)
);
```

To check the table:

``` sql
DESC student;
SELECT * FROM student;
```

## Step 2: Add the MySQL JDBC Driver

1.  Download the **MySQL Connector/J** JAR compatible with your MySQL
    setup.
2.  In Eclipse, right-click the project.
3.  Select **Build Path → Configure Build Path**.
4.  Open **Libraries**.
5.  Select **Add External JARs** and choose the MySQL Connector/J JAR.
6.  Apply and close.

The driver class used in this program is `com.mysql.cj.jdbc.Driver`.

## Step 3: HTML: `ServletCurd.html`

``` html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Student CRUD</title>
</head>
<body>
    <h2>Student Management System</h2>

    <form action="ServletCurd" method="post">
        Student ID:
        <input type="number" name="id" required>
        <br><br>

        Student Name:
        <input type="text" name="name">
        <br><br>

        Email:
        <input type="email" name="email">
        <br><br>

        Branch:
        <input type="text" name="branch">
        <br><br>

        Operation:
        <select name="operation">
            <option value="insert">Insert</option>
            <option value="update">Update</option>
            <option value="delete">Delete</option>
        </select>
        <br><br>

        <input type="submit" value="Submit">
    </form>
</body>
</html>
```

## Step 4: Servlet: `ServletCurd.java`

``` java
package com.example;

import java.io.IOException;
import java.io.PrintWriter;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/ServletCurd")
public class ServletCurd extends HttpServlet {
    private static final long serialVersionUID = 1L;

    protected void doPost(HttpServletRequest request,
                          HttpServletResponse response)
            throws ServletException, IOException {

        response.setContentType("text/html");
        PrintWriter out = response.getWriter();

        int id = Integer.parseInt(request.getParameter("id"));
        String name = request.getParameter("name");
        String email = request.getParameter("email");
        String branch = request.getParameter("branch");
        String operation = request.getParameter("operation");

        String url = "jdbc:mysql://localhost:3306/college";
        String dbUser = "root";
        String dbPassword = "password"; // Replace with your MySQL password.

        try {
            Class.forName("com.mysql.cj.jdbc.Driver");

            try (Connection con = DriverManager.getConnection(
                        url, dbUser, dbPassword)) {

                PreparedStatement ps;

                if ("insert".equals(operation)) {
                    ps = con.prepareStatement(
                        "INSERT INTO student (id, name, email, branch) VALUES (?, ?, ?, ?)");
                    ps.setInt(1, id);
                    ps.setString(2, name);
                    ps.setString(3, email);
                    ps.setString(4, branch);

                } else if ("update".equals(operation)) {
                    ps = con.prepareStatement(
                        "UPDATE student SET name=?, email=?, branch=? WHERE id=?");
                    ps.setString(1, name);
                    ps.setString(2, email);
                    ps.setString(3, branch);
                    ps.setInt(4, id);

                } else if ("delete".equals(operation)) {
                    ps = con.prepareStatement(
                        "DELETE FROM student WHERE id=?");
                    ps.setInt(1, id);

                } else {
                    out.println("<h2>Invalid operation selected.</h2>");
                    return;
                }

                try (PreparedStatement statement = ps) {
                    int result = statement.executeUpdate();

                    if (result > 0) {
                        out.println("<h2>" + operation
                                + " operation successful!</h2>");
                    } else {
                        out.println("<h2>No record found / no rows affected.</h2>");
                    }
                }
            }

        } catch (Exception e) {
            out.println("<h2>Database Error</h2>");
            out.println("<p>" + e.getMessage() + "</p>");
        }
    }
}
```

## Step 5: Configure Your MySQL Credentials

In `ServletCurd.java`, update these lines:

``` java
String dbUser = "root";
String dbPassword = "password";
```

Replace `password` with the actual password of your MySQL `root`
account. Do not change the database name unless you also change the JDBC
URL and SQL database.

## Step 6: Run and Test

1.  Start MySQL.
2.  Confirm that the `college` database and `student` table exist.
3.  Start Tomcat from Eclipse.
4.  Open `ServletCurd.html` through the application URL.
5.  Enter student details and select an operation.
6.  Click **Submit**.
7.  Verify the result in MySQL:

``` sql
USE college;
SELECT * FROM student;
```

### Test Insert

Enter:

  Field          Value
  -------------- ----------------
  Student ID     101
  Student Name   Ravi
  Email          ravi@gmail.com
  Branch         CSE
  Operation      Insert

Expected: `insert operation successful!`

### Test Update

Use Student ID `101`, change the name/email/branch, and select
**Update**.

Expected: `update operation successful!`

### Test Delete

Enter Student ID `101`, select **Delete**, and submit.

Expected: `delete operation successful!`

Verify after each operation using:

``` sql
SELECT * FROM student;
```

> For Delete, only the Student ID is needed. For Update, provide the ID
> and the new values. For Insert, provide all required student details.

------------------------------------------------------------------------

# Quick Revision for Practical Examination

  -----------------------------------------------------------------------
  Practical               Main concept            Key method / statement
  ----------------------- ----------------------- -----------------------
  1\. Session Tracking    Store and retrieve user `getSession()`,
                          data                    `setAttribute()`,
                                                  `getAttribute()`

  2\. Cookies             Store and retrieve      `addCookie()`,
                          browser data            `getCookies()`

  3\. Date & Time         Display current time    `new Date()`, meta
                          and refresh             refresh

  4\. HTML + Servlet      Receive form data       `getParameter()`

  5\. JDBC CRUD           Database operations     `PreparedStatement`,
                                                  `executeUpdate()`
  -----------------------------------------------------------------------

## Common Errors and Fixes

  ----------------------------------------------------------------------------------------
  Error / Problem                                      Check
  ---------------------------------------------------- -----------------------------------
  HTTP 404                                             Check the project URL, servlet
                                                       mapping, and HTML form `action`

  HTTP 405                                             Check whether the form method
                                                       (`GET` or `POST`) matches the
                                                       servlet method

  `ClassNotFoundException: com.mysql.cj.jdbc.Driver`   Add the MySQL Connector/J JAR to
                                                       the project

  `Access denied for user 'root'`                      Check MySQL username and password

  `Unknown database 'college'`                         Create the database or correct the
                                                       JDBC URL

  `Table 'student' doesn't exist`                      Run the `CREATE TABLE` statement in
                                                       the correct database

  Duplicate entry for primary key                      Use a new Student ID for Insert

  `getSession(false)` returns `null`                   No existing session is available
                                                       for that browser request

  Cookie not found                                     Submit the form first, then
                                                       retrieve it from the same browser
                                                       and application
  ----------------------------------------------------------------------------------------

## Final Practical Checklist

-   [ ] Create the Dynamic Web Project and configure Tomcat.
-   [ ] Keep servlet URL mappings and HTML form actions identical.
-   [ ] Use the correct HTTP method (`GET` or `POST`).
-   [ ] Add the MySQL JDBC driver before running the CRUD practical.
-   [ ] Replace the sample MySQL password with your own.
-   [ ] Test each practical using the browser.
-   [ ] Verify database changes using `SELECT * FROM student;`.
