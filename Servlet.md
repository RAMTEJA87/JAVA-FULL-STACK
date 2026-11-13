Java Servlet Practical Programs — Complete Runnable Code
These programs use Java 21, Tomcat 10.1, Jakarta Servlet 6.0, Maven, and MySQL 8. All Java files include their imports and use the same package: `com.example`.
1. Project structure
Create a Maven project named `ServletPracticals` with this structure:
```text
ServletPracticals/
├── pom.xml
├── database.sql
└── src/
    └── main/
        ├── java/
        │   └── com/
        │       └── example/
        │           ├── SessionTracking.java
        │           ├── Cookies.java
        │           ├── DateTime.java
        │           ├── StudentConn.java
        │           └── ServletCurd.java
        └── webapp/
            ├── index.html
            ├── SessionTracking.html
            ├── Cookies.html
            ├── StudentConn.html
            └── ServletCurd.html
```
2. `pom.xml`
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>ServletPracticals</artifactId>
    <version>1.0.0</version>
    <packaging>war</packaging>

    <properties>
        <maven.compiler.release>21</maven.compiler.release>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <dependency>
            <groupId>jakarta.servlet</groupId>
            <artifactId>jakarta.servlet-api</artifactId>
            <version>6.0.0</version>
            <scope>provided</scope>
        </dependency>

        <dependency>
            <groupId>com.mysql</groupId>
            <artifactId>mysql-connector-j</artifactId>
            <version>9.1.0</version>
        </dependency>
    </dependencies>

    <build>
        <finalName>ServletPracticals</finalName>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.13.0</version>
                <configuration>
                    <release>21</release>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-war-plugin</artifactId>
                <version>3.4.0</version>
            </plugin>
        </plugins>
    </build>
</project>
```
3. Session Tracking
`src/main/java/com/example/SessionTracking.java`
```java
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

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {

        response.setContentType("text/html;charset=UTF-8");

        try (PrintWriter out = response.getWriter()) {
            HttpSession session = request.getSession(false);

            out.println("<!DOCTYPE html><html><head><title>Session Status</title></head><body>");

            if (session != null && session.getAttribute("UserName") != null) {
                out.println("<h2>Welcome " + escapeHtml(
                        session.getAttribute("UserName").toString()) + "</h2>");
                out.println("<p>Session ID: " + escapeHtml(session.getId()) + "</p>");
                out.println("<a href='Logout'>Logout</a>");
            } else {
                out.println("<h2>No active session found.</h2>");
                out.println("<a href='SessionTracking.html'>Create a session</a>");
            }

            out.println("</body></html>");
        }
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {

        request.setCharacterEncoding("UTF-8");
        String name = request.getParameter("UserName");

        if (name == null || name.trim().isEmpty()) {
            response.sendError(HttpServletResponse.SC_BAD_REQUEST, "Student name is required.");
            return;
        }

        HttpSession session = request.getSession();
        session.setAttribute("UserName", name.trim());

        response.sendRedirect("SessionTracking");
    }

    private String escapeHtml(String value) {
        return value.replace("&", "&amp;")
                    .replace("<", "&lt;")
                    .replace(">", "&gt;")
                    .replace("\"", "&quot;")
                    .replace("'", "&#x27;");
    }
}
```
`src/main/java/com/example/Logout.java` (optional logout endpoint)
If you want the Logout link above to work, create this additional file:
```java
package com.example;

import java.io.IOException;

import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import jakarta.servlet.http.HttpSession;

@WebServlet("/Logout")
public class Logout extends HttpServlet {
    private static final long serialVersionUID = 1L;

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws IOException {
        HttpSession session = request.getSession(false);
        if (session != null) {
            session.invalidate();
        }
        response.sendRedirect("SessionTracking.html");
    }
}
```
`src/main/webapp/SessionTracking.html`
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Session Tracking</title>
</head>
<body>
    <h1>Session Tracking</h1>
    <form action="SessionTracking" method="post">
        Enter Student Name:
        <input type="text" name="UserName" required>
        <input type="submit" value="Create Session">
    </form>
</body>
</html>
```
4. Cookies
`src/main/java/com/example/Cookies.java`
```java
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

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {

        response.setContentType("text/html;charset=UTF-8");
        String language = request.getParameter("lang");

        if (language == null || language.trim().isEmpty()) {
            response.sendError(HttpServletResponse.SC_BAD_REQUEST, "Select a language.");
            return;
        }

        Cookie cookie = new Cookie("FavLang", language);
        cookie.setMaxAge(60 * 60); // 1 hour
        cookie.setHttpOnly(true);
        cookie.setPath(request.getContextPath().isEmpty() ? "/" : request.getContextPath());
        response.addCookie(cookie);

        try (PrintWriter out = response.getWriter()) {
            out.println("<!DOCTYPE html><html><head><title>Cookie Result</title></head><body>");
            out.println("<h2>Cookie added successfully.</h2>");
            out.println("<p>Favourite language: " + escapeHtml(language) + "</p>");
            out.println("<a href='Cookies.html'>Go Back</a>");
            out.println("</body></html>");
        }
    }

    private String escapeHtml(String value) {
        return value.replace("&", "&amp;")
                    .replace("<", "&lt;")
                    .replace(">", "&gt;")
                    .replace("\"", "&quot;")
                    .replace("'", "&#x27;");
    }
}
```
`src/main/webapp/Cookies.html`
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Cookies</title>
</head>
<body>
    <h1>Cookies</h1>
    <form action="Cookies" method="post">
        Select Favourite Language:
        <select name="lang" required>
            <option value="Java">Java</option>
            <option value="C">C</option>
            <option value="C++">C++</option>
        </select>
        <input type="submit" value="Add Cookie">
    </form>
</body>
</html>
```
5. Current Date and Time with automatic refresh
`src/main/java/com/example/DateTime.java`
```java
package com.example;

import java.io.IOException;
import java.io.PrintWriter;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/DateTime")
public class DateTime extends HttpServlet {
    private static final long serialVersionUID = 1L;

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {

        response.setContentType("text/html;charset=UTF-8");
        response.setHeader("Refresh", "1");

        String currentDateTime = LocalDateTime.now()
                .format(DateTimeFormatter.ofPattern("dd-MM-yyyy HH:mm:ss"));

        try (PrintWriter out = response.getWriter()) {
            out.println("<!DOCTYPE html><html><head><title>Current Date and Time</title></head><body>");
            out.println("<h1>Current Date and Time</h1>");
            out.println("<h2>" + currentDateTime + "</h2>");
            out.println("<p>This page refreshes every second.</p>");
            out.println("</body></html>");
        }
    }
}
```
There is no separate HTML file required for this servlet. Open `/ServletPracticals/DateTime`.
6. HTML and Servlet Communication — Student Details
`src/main/java/com/example/StudentConn.java`
```java
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

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {

        request.setCharacterEncoding("UTF-8");
        response.setContentType("text/html;charset=UTF-8");

        String idValue = request.getParameter("id");
        String name = request.getParameter("name");
        String email = request.getParameter("email");
        String branch = request.getParameter("branch");

        if (idValue == null || name == null || email == null || branch == null
                || idValue.trim().isEmpty() || name.trim().isEmpty()) {
            response.sendError(HttpServletResponse.SC_BAD_REQUEST, "Please enter all required details.");
            return;
        }

        int id;
        try {
            id = Integer.parseInt(idValue);
        } catch (NumberFormatException e) {
            response.sendError(HttpServletResponse.SC_BAD_REQUEST, "Student ID must be a number.");
            return;
        }

        try (PrintWriter out = response.getWriter()) {
            out.println("<!DOCTYPE html><html><head><title>Student Details</title></head><body>");
            out.println("<h2>Student Details Received</h2>");
            out.println("<p>Student ID: " + id + "</p>");
            out.println("<p>Name: " + escapeHtml(name) + "</p>");
            out.println("<p>Email: " + escapeHtml(email) + "</p>");
            out.println("<p>Branch: " + escapeHtml(branch) + "</p>");
            out.println("<a href='StudentConn.html'>Go Back</a>");
            out.println("</body></html>");
        }
    }

    private String escapeHtml(String value) {
        return value.replace("&", "&amp;")
                    .replace("<", "&lt;")
                    .replace(">", "&gt;")
                    .replace("\"", "&quot;")
                    .replace("'", "&#x27;");
    }
}
```
`src/main/webapp/StudentConn.html`
```html
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
7. JDBC — Insert, Update and Delete Student Records
`database.sql`
Run this SQL script in MySQL Workbench or the MySQL command line:
```sql
CREATE DATABASE IF NOT EXISTS college;

USE college;

CREATE TABLE IF NOT EXISTS student (
    id INT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    email VARCHAR(100),
    branch VARCHAR(30)
);
```
`src/main/java/com/example/ServletCurd.java`
```java
package com.example;

import java.io.IOException;
import java.io.PrintWriter;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/ServletCurd")
public class ServletCurd extends HttpServlet {
    private static final long serialVersionUID = 1L;

    private static final String JDBC_URL =
            "jdbc:mysql://localhost:3306/college?useSSL=false&serverTimezone=UTC";
    private static final String JDBC_USER = "root";
    // Change this to your actual MySQL root password.
    private static final String JDBC_PASSWORD = "password";

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {

        request.setCharacterEncoding("UTF-8");
        response.setContentType("text/html;charset=UTF-8");

        String idValue = request.getParameter("id");
        String name = request.getParameter("name");
        String email = request.getParameter("email");
        String branch = request.getParameter("branch");
        String operation = request.getParameter("operation");

        if (idValue == null || idValue.trim().isEmpty()
                || operation == null || operation.trim().isEmpty()) {
            response.sendError(HttpServletResponse.SC_BAD_REQUEST, "Student ID and operation are required.");
            return;
        }

        int id;
        try {
            id = Integer.parseInt(idValue);
        } catch (NumberFormatException e) {
            response.sendError(HttpServletResponse.SC_BAD_REQUEST, "Student ID must be a number.");
            return;
        }

        String sql;
        switch (operation) {
            case "insert":
                if (name == null || name.trim().isEmpty()) {
                    response.sendError(HttpServletResponse.SC_BAD_REQUEST, "Name is required for insert.");
                    return;
                }
                sql = "INSERT INTO student (id, name, email, branch) VALUES (?, ?, ?, ?)";
                break;

            case "update":
                if (name == null || name.trim().isEmpty()) {
                    response.sendError(HttpServletResponse.SC_BAD_REQUEST, "Name is required for update.");
                    return;
                }
                sql = "UPDATE student SET name = ?, email = ?, branch = ? WHERE id = ?";
                break;

            case "delete":
                sql = "DELETE FROM student WHERE id = ?";
                break;

            default:
                response.sendError(HttpServletResponse.SC_BAD_REQUEST, "Invalid operation.");
                return;
        }

        try (Connection con = DriverManager.getConnection(JDBC_URL, JDBC_USER, JDBC_PASSWORD);
             PreparedStatement ps = con.prepareStatement(sql);
             PrintWriter out = response.getWriter()) {

            switch (operation) {
                case "insert":
                    ps.setInt(1, id);
                    ps.setString(2, name.trim());
                    ps.setString(3, email);
                    ps.setString(4, branch);
                    break;

                case "update":
                    ps.setString(1, name.trim());
                    ps.setString(2, email);
                    ps.setString(3, branch);
                    ps.setInt(4, id);
                    break;

                case "delete":
                    ps.setInt(1, id);
                    break;
            }

            int rows = ps.executeUpdate();

            out.println("<!DOCTYPE html><html><head><title>Database Result</title></head><body>");
            if (rows > 0) {
                out.println("<h2>" + operation.toUpperCase() + " successful.</h2>");
            } else {
                out.println("<h2>No matching student record found.</h2>");
            }
            out.println("<a href='ServletCurd.html'>Go Back</a>");
            out.println("</body></html>");

        } catch (SQLException e) {
            log("Database operation failed", e);
            response.setStatus(HttpServletResponse.SC_INTERNAL_SERVER_ERROR);
            try (PrintWriter out = response.getWriter()) {
                out.println("<h2>Database error. Check the MySQL connection, credentials, and table.</h2>");
            }
        }
    }
}
```
`src/main/webapp/ServletCurd.html`
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Student CRUD</title>
</head>
<body>
    <h2>Student Management System — JDBC CRUD</h2>

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
        <select name="operation" required>
            <option value="insert">Insert</option>
            <option value="update">Update</option>
            <option value="delete">Delete</option>
        </select>
        <br><br>

        <input type="submit" value="Submit">
    </form>

    <p>For Delete, only Student ID and Operation are required.</p>
</body>
</html>
```
8. Home page
`src/main/webapp/index.html`
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Servlet Practicals</title>
</head>
<body>
    <h1>Java Servlet Practicals</h1>
    <ul>
        <li><a href="SessionTracking.html">Session Tracking</a></li>
        <li><a href="Cookies.html">Cookies</a></li>
        <li><a href="DateTime">Current Date and Time</a></li>
        <li><a href="StudentConn.html">HTML and Servlet Communication</a></li>
        <li><a href="ServletCurd.html">JDBC Insert, Update and Delete</a></li>
    </ul>
</body>
</html>
```
9. Run the project
Install JDK 21, Maven, MySQL 8, and Apache Tomcat 10.1.
In Eclipse, choose File → Import → Maven → Existing Maven Projects and select the `ServletPracticals` folder.
Wait for Maven dependencies to download.
Run `database.sql` in MySQL Workbench.
Open `ServletCurd.java` and change `JDBC_PASSWORD` from `password` to your actual MySQL root password. Confirm the database name, username, and port.
Add Tomcat 10.1 in Eclipse: Window → Show View → Servers, create a Tomcat server, and add this project.
Start Tomcat and open:
```text
http://localhost:8080/ServletPracticals/
```
10. Important compatibility notes
These examples use `jakarta.servlet.*`, so use Tomcat 10.1. They will not run unchanged on Tomcat 9, which uses `javax.servlet.*`.
The `pom.xml` includes the Servlet API and MySQL JDBC driver. Do not manually add another copy of the same JAR when Maven has already downloaded it.
`@WebServlet` annotations provide servlet URL mappings; no `web.xml` is required.
The JDBC example expects MySQL to be running and the `college.student` table to exist.
The CRUD example's class name is kept as `ServletCurd` to match the supplied practical material, although “CRUD” is the usual spelling.
