# Java MVC Application

## Project Structure

``` text
StudentMVC/
├── src/main/java/
│   └── com/example/
│       ├── controller/
│       │   └── StudentController.java
│       └── model/
│           └── Student.java
└── src/main/webapp/
    ├── student.html
    └── result.jsp
```

## 1. Student.java

``` java
package com.example.model;

public class Student {
    private int id;
    private String name;
    private String branch;

    public Student(int id, String name, String branch) {
        this.id = id;
        this.name = name;
        this.branch = branch;
    }

    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public String getBranch() {
        return branch;
    }
}
```

## 2. StudentController.java

``` java
package com.example.controller;

import java.io.IOException;

import com.example.model.Student;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/student")
public class StudentController extends HttpServlet {
    private static final long serialVersionUID = 1L;

    protected void doPost(HttpServletRequest request,
                          HttpServletResponse response)
            throws ServletException, IOException {

        int id = Integer.parseInt(request.getParameter("id"));
        String name = request.getParameter("name");
        String branch = request.getParameter("branch");

        Student student = new Student(id, name, branch);

        request.setAttribute("student", student);

        request.getRequestDispatcher("result.jsp")
               .forward(request, response);
    }
}
```

## 3. student.html

``` html
<!DOCTYPE html>
<html>
<head>
    <title>Student Details</title>
</head>
<body>
    <h2>Student Details</h2>

    <form action="student" method="post">
        Student ID:
        <input type="number" name="id" required>
        <br><br>

        Student Name:
        <input type="text" name="name" required>
        <br><br>

        Branch:
        <input type="text" name="branch" required>
        <br><br>

        <input type="submit" value="Submit">
    </form>
</body>
</html>
```

## 4. result.jsp

``` jsp
<!DOCTYPE html>
<html>
<head>
    <title>Student Result</title>
</head>
<body>
    <h2>Student Information</h2>

    ID: ${student.id}
    <br><br>

    Name: ${student.name}
    <br><br>

    Branch: ${student.branch}
</body>
</html>
```
