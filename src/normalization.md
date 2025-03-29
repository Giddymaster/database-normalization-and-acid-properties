# Normalization

Its a process of designing a database effectively such that will avoid data anomalies/redundancy.

## Why Normalize?

- Minimizes duplicate data.
- Prevents update anomalies (errors when inserting, updating, or deleting data).
- Makes the database more scalable.

## Levels of Normalization/ Normal Forms

1. First Normal Form(1NF)
2. Second Normal Form(2NF)
3. Third Normal Form (3NF) - most popular
4. Fourth Normal Form (4NF)
5. Fifth Normal Form(5NF)

etc...

## Steps by step Normalizing a dataset

### 1. First Normal Form (1NF)

To ensure we are in 1NF, we need to make sure that each column has atomic values (no multiple values in a single column like “Math, Physics” for the Courses column). We would create a table where each course for a student gets its own row.

**Example: Unnormalized Table (Violates 1NF)**

![unnormalized](src/assets/before-1NF.png)

**Normalized (1NF):**

To normalize this into 1NF, we need to create a new table where each row represents a single course per student.

![normalized](src/assets/After-1NF.png)

### 2. Second Normal Form (2NF)

Here I create 4 tables: university, students, courses and student_courses. The university table is the unnormalized table that violates 1NF and 2NF, we can proceed with normalizing it step-by-step into 2NF  

![unnormalized](src/assets/university-2nf.png)

Step 1: Normalize to 1NF (Separate out the values in the student, courses, instructor, and grade columns).

![university normalized](src/assets/UNI-1NF.png)

Step 2: Normalize to 2NF (Remove partial dependencies by separating the data into appropriate tables).

- Create a students table to store information about students, including student-id and student-name.

![student normalized](src/assets/student-2nf.png)

- Create a courses table to store information about courses, including course and instructor.

![course normalized](src/assets/courses-2nf.png)

- Create a student_courses table to represent the relationship between students and courses, storing student-id, course-id, and grade.

![student-course normalized](src/assets/student-courses-2nf.png)

### 3. Third Normal Form (3NF)

- Must satisfy 2NF.
- No transitive dependencies (non-key columns shouldn’t depend on other non-key columns).

![university data unnormalized](src/assets/university-3nf.png)

first normalization.

![university data 1NF normalized](src/assets/univ-1nf.png)

second normalization -  we create the students table (stores student information). courses table (stores course and instructor information) and student_courses table (tracks which student is enrolled in which course, and their grade):

- student- table: stores student information

![student 2NF normalized](src/assets/students-3nf.png)

- courses -table: stores course and instructor information

![courses 2NF normalized](src/assets/courses-3nf.png)

- student_courses table: tracks which student is enrolled in which course, and their grade

![student-courses 2NF normalized](src/assets/student-course-3nf.png)

third normalizations - To move to 3NF, we need to remove transitive dependencies. In this case, the department column should not be in the students table because it is dependent on the student_id, and we don't want to store it redundantly.  
Create the departments table (stores department information) 

![departments normalized](src/assets/depatments.png)
and Modify the students table (remove department column from students and link it to departments)



## When to Stop Normalizing?

- Over-normalization can make queries slower (due to joins).  
- Balance between redundancy and performance.
