# Spring Boot Practical Programs

> Code only. Import statements are intentionally omitted as requested.
> Use Spring Boot Web, Spring Data JPA, and H2 Database dependencies.
> Keep each program in its own Spring Boot project. All Java files in
> each project use the same package: `com.example.demo`.

------------------------------------------------------------------------

# 1. Set 2 --- Course Management

## File structure

``` text
course-management/
└── src/main/
    ├── java/com/example/demo/
    │   ├── entity/Course.java
    │   ├── repository/CourseRepository.java
    │   ├── service/CourseService.java
    │   └── controller/CourseController.java
    └── resources/application.properties
```

## `application.properties`

``` properties
server.port=8080
spring.datasource.url=url
spring.datasource.username=root
spring.datasource.password=password
spring.jpa.hibernate.ddl-auto=update
```


## `entity/Course.java`

``` java
package com.example.demo.entity;

@Entity
public class Course {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;

    private String name;
    private String duration;
    private double fee;

    public Course() {}

    public Course(String name, String duration, double fee) {
        this.name = name;
        this.duration = duration;
        this.fee = fee;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getDuration() {
        return duration;
    }

    public void setDuration(String duration) {
        this.duration = duration;
    }

    public double getFee() {
        return fee;
    }

    public void setFee(double fee) {
        this.fee = fee;
    }
}
```

## `repository/CourseRepository.java`

``` java
package com.example.demo.repository;

public interface CourseRepository extends JpaRepository<Course, Integer> {
}
```

## `service/CourseService.java`

``` java
package com.example.demo.service;

@Service
public class CourseService {

    @Autowired
    private CourseRepository repository;

    public Course addCourse(Course course) {
        return repository.save(course);
    }

    public List<Course> getAllCourses() {
        return repository.findAll();
    }
}
```

## `controller/CourseController.java`

``` java
package com.example.demo.controller;

@RestController
@RequestMapping("/courses")
public class CourseController {

    @Autowired
    private CourseService service;

    @PostMapping
    public Course addCourse(@RequestBody Course course) {
        return service.addCourse(course);
    }

    @GetMapping
    public List<Course> getAllCourses() {
        return service.getAllCourses();
    }
}
```

## API endpoints

``` text
POST http://localhost:8080/courses
GET  http://localhost:8080/courses
```

## POST request body

``` json
{
  "name": "Java Full Stack",
  "duration": "6 Months",
  "fee": 15000
}
```

------------------------------------------------------------------------

# 2. Set 4 --- Patient Vital Statistics

## File structure

``` text
patient-vitals/
└── src/main/
    ├── java/com/example/demo/
    │   ├── entity/VitalReading.java
    │   ├── repository/VitalReadingRepository.java
    │   ├── service/VitalReadingService.java
    │   └── controller/VitalReadingController.java
    └── resources/application.properties
```

## `application.properties`

``` properties
server.port=8080
spring.datasource.url=url
spring.datasource.username=root
spring.datasource.password=password
spring.jpa.hibernate.ddl-auto=update
```


## `entity/VitalReading.java`

``` java
package com.example.demo.entity;

@Entity
public class VitalReading {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;

    private int patientId;
    private double temperature;
    private int heartRate;
    private String bloodPressure;
    private double oxygenLevel;

    public VitalReading() {}

    public VitalReading(int patientId, double temperature, int heartRate,
                        String bloodPressure, double oxygenLevel) {
        this.patientId = patientId;
        this.temperature = temperature;
        this.heartRate = heartRate;
        this.bloodPressure = bloodPressure;
        this.oxygenLevel = oxygenLevel;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public int getPatientId() {
        return patientId;
    }

    public void setPatientId(int patientId) {
        this.patientId = patientId;
    }

    public double getTemperature() {
        return temperature;
    }

    public void setTemperature(double temperature) {
        this.temperature = temperature;
    }

    public int getHeartRate() {
        return heartRate;
    }

    public void setHeartRate(int heartRate) {
        this.heartRate = heartRate;
    }

    public String getBloodPressure() {
        return bloodPressure;
    }

    public void setBloodPressure(String bloodPressure) {
        this.bloodPressure = bloodPressure;
    }

    public double getOxygenLevel() {
        return oxygenLevel;
    }

    public void setOxygenLevel(double oxygenLevel) {
        this.oxygenLevel = oxygenLevel;
    }
}
```

## `repository/VitalReadingRepository.java`

``` java
package com.example.demo.repository;

public interface VitalReadingRepository extends JpaRepository<VitalReading, Integer> {

    List<VitalReading> findByPatientId(int patientId);
}
```

## `service/VitalReadingService.java`

``` java
package com.example.demo.service;

@Service
public class VitalReadingService {

    @Autowired
    private VitalReadingRepository repository;

    public VitalReading addReading(VitalReading reading) {
        return repository.save(reading);
    }

    public List<VitalReading> getReadingsByPatientId(int patientId) {
        return repository.findByPatientId(patientId);
    }
}
```

## `controller/VitalReadingController.java`

``` java
package com.example.demo.controller;

@RestController
@RequestMapping("/vitals")
public class VitalReadingController {

    @Autowired
    private VitalReadingService service;

    @PostMapping
    public VitalReading addReading(@RequestBody VitalReading reading) {
        return service.addReading(reading);
    }

    @GetMapping("/{patientId}")
    public List<VitalReading> getReadingsByPatientId(@PathVariable int patientId) {
        return service.getReadingsByPatientId(patientId);
    }
}
```

## API endpoints

``` text
POST http://localhost:8080/vitals
GET  http://localhost:8080/vitals/101
```

## POST request body

``` json
{
  "patientId": 101,
  "temperature": 98.6,
  "heartRate": 75,
  "bloodPressure": "120/80",
  "oxygenLevel": 98
}
```

------------------------------------------------------------------------

# 3. Set 6 --- Employee Management

## File structure

``` text
employee-management/
└── src/main/
    ├── java/com/example/demo/
    │   ├── entity/Employee.java
    │   ├── repository/EmployeeRepository.java
    │   ├── service/EmployeeService.java
    │   └── controller/EmployeeController.java
    └── resources/application.properties
```

## `application.properties`

``` properties
server.port=8080
spring.datasource.url=url
spring.datasource.username=root
spring.datasource.password=password
spring.jpa.hibernate.ddl-auto=update
```


## `entity/Employee.java`

``` java
package com.example.demo.entity;

@Entity
public class Employee {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;

    private String name;
    private String department;
    private double salary;

    public Employee() {}

    public Employee(String name, String department, double salary) {
        this.name = name;
        this.department = department;
        this.salary = salary;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getDepartment() {
        return department;
    }

    public void setDepartment(String department) {
        this.department = department;
    }

    public double getSalary() {
        return salary;
    }

    public void setSalary(double salary) {
        this.salary = salary;
    }
}
```

## `repository/EmployeeRepository.java`

``` java
package com.example.demo.repository;

public interface EmployeeRepository extends JpaRepository<Employee, Integer> {
}
```

## `service/EmployeeService.java`

``` java
package com.example.demo.service;

@Service
public class EmployeeService {

    @Autowired
    private EmployeeRepository repository;

    public Employee addEmployee(Employee employee) {
        return repository.save(employee);
    }

    public List<Employee> getAllEmployees() {
        return repository.findAll();
    }
}
```

## `controller/EmployeeController.java`

``` java
package com.example.demo.controller;

@RestController
@RequestMapping("/employees")
public class EmployeeController {

    @Autowired
    private EmployeeService service;

    @PostMapping
    public Employee addEmployee(@RequestBody Employee employee) {
        return service.addEmployee(employee);
    }

    @GetMapping
    public List<Employee> getAllEmployees() {
        return service.getAllEmployees();
    }
}
```

## API endpoints

``` text
POST http://localhost:8080/employees
GET  http://localhost:8080/employees
```

## POST request body

``` json
{
  "name": "Ram",
  "department": "Development",
  "salary": 45000
}
```

------------------------------------------------------------------------

# 4. Set 10 --- Book Management

## File structure

``` text
book-management/
└── src/main/
    ├── java/com/example/demo/
    │   ├── entity/Book.java
    │   ├── repository/BookRepository.java
    │   ├── service/BookService.java
    │   └── controller/BookController.java
    └── resources/application.properties
```

## `application.properties`

``` properties
server.port=8080
spring.datasource.url=url
spring.datasource.username=root
spring.datasource.password=password
spring.jpa.hibernate.ddl-auto=update
```


## `entity/Book.java`

``` java
package com.example.demo.entity;

@Entity
public class Book {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;

    private String title;
    private String author;
    private double price;

    public Book() {}

    public Book(String title, String author, double price) {
        this.title = title;
        this.author = author;
        this.price = price;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public String getAuthor() {
        return author;
    }

    public void setAuthor(String author) {
        this.author = author;
    }

    public double getPrice() {
        return price;
    }

    public void setPrice(double price) {
        this.price = price;
    }
}
```

## `repository/BookRepository.java`

``` java
package com.example.demo.repository;

public interface BookRepository extends JpaRepository<Book, Integer> {
}
```

## `service/BookService.java`

``` java
package com.example.demo.service;

@Service
public class BookService {

    @Autowired
    private BookRepository repository;

    public Book addBook(Book book) {
        return repository.save(book);
    }

    public List<Book> getAllBooks() {
        return repository.findAll();
    }
}
```

## `controller/BookController.java`

``` java
package com.example.demo.controller;

@RestController
@RequestMapping("/books")
public class BookController {

    @Autowired
    private BookService service;

    @PostMapping
    public Book addBook(@RequestBody Book book) {
        return service.addBook(book);
    }

    @GetMapping
    public List<Book> getAllBooks() {
        return service.getAllBooks();
    }
}
```

## API endpoints

``` text
POST http://localhost:8080/books
GET  http://localhost:8080/books
```

## POST request body

``` json
{
  "title": "Effective Java",
  "author": "Joshua Bloch",
  "price": 650
}
```
