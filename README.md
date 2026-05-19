[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/Qr3lBpHw)

# University Management System

This project is a Spring Boot microservice-based university management system for WM2 Lab 8.

The system manages students, courses, course enrollments, enrollment dates, course prerequisites, and course lookup by student name.

## Project Structure

```text
wm2_spring_2026/university-system
├── student-service
├── course-service
├── docker-compose.yml
├── build.gradle
└── settings.gradle
```

## Technologies Used

- Java 21
- Spring Boot 3.3.5
- Spring Web
- Spring Data JPA
- Spring Validation
- Spring Cloud OpenFeign
- PostgreSQL
- Docker Compose
- Gradle
- Swagger / OpenAPI

## Services

### Student Service

Base URL:

```text
http://localhost:9090
```

Responsibilities:

- create students
- list students
- get student by ID
- update students
- delete students
- search students by name

Swagger UI:

```text
http://localhost:9090/swagger-ui/index.html
```

### Course Service

Base URL:

```text
http://localhost:8081
```

Responsibilities:

- create courses
- list courses
- get course by ID
- update courses
- delete courses
- enroll students into courses
- store enrollment date
- validate prerequisite courses before enrollment
- list students enrolled in a course
- get courses by student name

Swagger UI:

```text
http://localhost:8081/swagger-ui/index.html
```

## Database Setup

The project uses PostgreSQL databases through Docker Compose.

Databases:

- `studentDB` for student-service
- `courseDB` for course-service

Docker Compose starts:

- student PostgreSQL database
- course PostgreSQL database
- student-service
- course-service

## How to Run the Project

Go to the university system folder:

```bash
cd wm2_spring_2026/university-system
```

Start all services:

```bash
docker compose up --build
```

Student service will run on:

```text
http://localhost:9090
```

Course service will run on:

```text
http://localhost:8081
```

To stop the services:

```bash
docker compose down
```

To stop services and remove database volumes:

```bash
docker compose down -v
```

## How to Run Tests

From the university system folder:

```bash
./gradlew test
```

## API Examples

## Create Student

Request:

```bash
curl -X POST http://localhost:9090/api/v1/students \
  -H "Content-Type: application/json" \
  -d '{
    "firstName": "Islam",
    "lastName": "Muradov",
    "email": "islam.muradov@example.com",
    "age": 20
  }'
```

Example response:

```json
{
  "id": 1,
  "firstName": "Islam",
  "lastName": "Muradov",
  "email": "islam.muradov@example.com",
  "age": 20
}
```

## Get All Students

Request:

```bash
curl http://localhost:9090/api/v1/students
```

## Search Students by Name

Request:

```bash
curl "http://localhost:9090/api/v1/students/search?name=Islam"
```

This endpoint searches students by first name or last name.

## Create Course Without Prerequisite

Request:

```bash
curl -X POST http://localhost:8081/api/v1/courses \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Introduction to Programming",
    "code": "CS101",
    "credits": 4,
    "prerequisiteCourseId": null
  }'
```

Example response:

```json
{
  "id": 1,
  "title": "Introduction to Programming",
  "code": "CS101",
  "credits": 4,
  "prerequisiteCourseId": null
}
```

## Create Course With Prerequisite

Request:

```bash
curl -X POST http://localhost:8081/api/v1/courses \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Data Structures",
    "code": "CS201",
    "credits": 4,
    "prerequisiteCourseId": 1
  }'
```

Example response:

```json
{
  "id": 2,
  "title": "Data Structures",
  "code": "CS201",
  "credits": 4,
  "prerequisiteCourseId": 1
}
```

## Get All Courses

Request:

```bash
curl http://localhost:8081/api/v1/courses
```

## Enroll Student Into Course

Request:

```bash
curl -X POST http://localhost:8081/api/v1/courses/1/students/1
```

Example response:

```json
{
  "enrollmentId": 1,
  "courseId": 1,
  "studentId": 1,
  "enrollmentDate": "2026-05-19",
  "message": "Student enrolled successfully."
}
```

## Prerequisite Validation

If a course has a prerequisite, the student must already be enrolled in the prerequisite course.

Example:

- Course `CS201` has prerequisite course ID `1`
- Student must first enroll in course `1`
- Only after that, the student can enroll in `CS201`

Invalid enrollment example:

```bash
curl -X POST http://localhost:8081/api/v1/courses/2/students/1
```

Example error response:

```json
{
  "timestamp": "2026-05-19T12:00:00",
  "status": 400,
  "error": "Bad Request",
  "message": "Student 1 must complete prerequisite course 1 before enrolling in course 2.",
  "path": "/api/v1/courses/2/students/1"
}
```

## Get Students Enrolled in a Course

Request:

```bash
curl http://localhost:8081/api/v1/courses/1/students
```

Example response:

```json
{
  "courseId": 1,
  "courseTitle": "Introduction to Programming",
  "students": [
    {
      "id": 1,
      "firstName": "Islam",
      "lastName": "Muradov",
      "email": "islam.muradov@example.com",
      "age": 20
    }
  ]
}
```

## Get Courses by Student Name

Request:

```bash
curl "http://localhost:8081/api/v1/courses/by-student-name?name=Islam"
```

Example response:

```json
[
  {
    "id": 1,
    "title": "Introduction to Programming",
    "code": "CS101",
    "credits": 4,
    "prerequisiteCourseId": null
  }
]
```

## Swagger Documentation

Swagger UI is available for both services.

Student Service Swagger:

```text
http://localhost:9090/swagger-ui/index.html
```

Course Service Swagger:

```text
http://localhost:8081/swagger-ui/index.html
```

The API documentation includes Azerbaijani endpoint descriptions and DTO field descriptions.

## Important Notes

- `prerequisiteCourseId` is optional.
- If a course has no prerequisite, `prerequisiteCourseId` should be `null`.
- Enrollment date is automatically set by the system when a student is enrolled.
- Duplicate enrollment of the same student into the same course is rejected.
- Enrollment into a course with an unmet prerequisite is rejected with a meaningful error response.
- Course service communicates with student-service using OpenFeign and RestTemplate.
- Both services use separate PostgreSQL databases.
```

One issue: because this README contains a project tree code block inside a larger Markdown code block here, be careful when copying. Start from # University Management System` and end at `Both services use separate PostgreSQL databases.