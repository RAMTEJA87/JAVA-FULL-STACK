1. Session Tracking
SessionTracking.java
```java
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

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();
        HttpSession s = request.getSession(false);
        if (s != null)
            out.println("<h1>Welcome" + s.getAttribute("UserName") + "</h1>");
        else
            out.println("No Session");
    }

    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();
        HttpSession s = request.getSession();
        String name = request.getParameter("UserName");
        s.setAttribute("UserName", name);
        out.println("<h1>Session Created for " + name + "</h1>");
        out.println("<a href='SessionTracking'>Track Session </a>");
    }
}
```
SessionTracking.html
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
        Enter Student Name
        <input type="text" name="UserName" required>
        <input type="submit" value="Submit">
    </form>
</body>
</html>
```
2. Cookies
Cookies.java
```java
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

    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();
        String Lang = request.getParameter("lang");
        Cookie c = new Cookie("FavLang", Lang);
        c.setMaxAge(60 * 60);
        response.addCookie(c);
        out.println("Cookie added");
    }
}
```
Cookies.html
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
        Select Language
        <select name="lang">
            <option value="Java">Java</option>
            <option value="c">c</option>
            <option value="c++">c++</option>
        </select>
        <input type="submit" value="Add Cookie">
    </form>
</body>
</html>
```
3. Current Date and Time
DateTime.java
```java
import java.io.IOException;
import java.io.PrintWriter;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/DateTime")
public class DateTime extends HttpServlet {
    private static final long serialVersionUID = 1L;

    public DateTime() {
        super();
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();
        out.println("Date & Time : " + new java.util.Date());
    }
}
```
4. HTML and Servlet Communication
StudentConn.java
```java
package com.example;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

@WebServlet("/StudentConn")
public class StudentConn extends HttpServlet {
    private static final long serialVersionUID = 1L;

    public StudentConn() {
        super();
    }

    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();
        int id = Integer.parseInt(request.getParameter("id"));
        String name = request.getParameter("name");
        String email = request.getParameter("email");
        String branch = request.getParameter("branch");
        out.println(id);
        out.println(name);
        out.println(email);
        out.println(branch);
    }
}
```
StudentConn.html
```html
<!DOCTYPE html>
<html>
<head>
    <title>Student Management</title>
</head>
<body>
    <h2>Student Management System</h2>
    <form action="StudentConn" method="post">
        Student ID:
        <input type="number" name="id" required>
        <br>
        Student Name:
        <input type="text" name="name">
        <br>
        Email:
        <input type="email" name="email">
        <br>
        Branch:
        <input type="text" name="branch">
        <br>
        <input type="submit" value="Submit">
    </form>
</body>
</html>
```
5. JDBC Insert, Update and Delete
ServletCurd.java
```java
package com.example;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;
import java.sql.*;

@WebServlet("/ServletCurd")
public class ServletCurd extends HttpServlet {
    private static final long serialVersionUID = 1L;

    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();
        int id = Integer.parseInt(request.getParameter("id"));
        String name = request.getParameter("name");
        String email = request.getParameter("email");
        String branch = request.getParameter("branch");
        String operation = request.getParameter("operation");

        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            Connection con = DriverManager.getConnection(
                "jdbc:mysql://localhost:3306/college", "root", "password"
            );

            PreparedStatement ps = null;
            if (operation.equals("insert")) {
                ps = con.prepareStatement("INSERT INTO student VALUES(?,?,?,?)");
                ps.setInt(1, id);
                ps.setString(2, name);
                ps.setString(3, email);
                ps.setString(4, branch);
            } else if (operation.equals("update")) {
                ps = con.prepareStatement("UPDATE student SET name=?, email=?, branch=? WHERE id=?");
                ps.setString(1, name);
                ps.setString(2, email);
                ps.setString(3, branch);
                ps.setInt(4, id);
            } else if (operation.equals("delete")) {
                ps = con.prepareStatement("DELETE FROM student WHERE id=?");
                ps.setInt(1, id);
            }

            int result = ps.executeUpdate();
            if (result > 0) {
                out.println("<h2>Operation Successful!</h2>");
            } else {
                out.println("<h2>No Record Found!</h2>");
            }
            ps.close();
            con.close();
        } catch (Exception e) {
            out.println(e);
        }
    }
}
```
ServletCurd.html
```html
<!DOCTYPE html>
<html>
<head>
    <title>Student Management</title>
</head>
<body>
    <h2>Student Management System</h2>
    <form action="ServletCurd" method="post">
        Student ID:
        <input type="number" name="id" required>
        <br>
        Student Name:
        <input type="text" name="name">
        <br>
        Email:
        <input type="email" name="email">
        <br>
        Branch:
        <input type="text" name="branch">
        <br>
        Operation:
        <select name="operation">
            <option value="insert">Insert</option>
            <option value="update">Update</option>
            <option value="delete">Delete</option>
        </select>
        <br>
        <input type="submit" value="Submit">
    </form>
</body>
</html>
```
database.sql
```sql
CREATE DATABASE college;

USE college;

CREATE TABLE student (
    id INT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    email VARCHAR(100),
    branch VARCHAR(30)
);
```
