# VUA-SEN307 Week 4 Practical Lab 2: Database Design and Normalization

#EZEKAKPU NKEMDILIM PERPETUA
VUG/SEN/24/12356 
VUA-SEN307 Database Systems

| Entity | Attributes | Primary key |
| Department | dept_id, dept_name | dept_id |
| Student | student_id, matric_no, full_name, dept_id | student_id |
| Lecturer | lecturer_id, lecturer_name, dept_id | lecturer_id |
| Course | course_id, course_code, course_title, dept_id | course_id |
| Enrolment | student_id, course_id, session | student_id + course_id + session |
| Course_Lecturer | course_id, lecturer_id, session | course_id + lecturer_id + session |

- One department can have many students; each student belongs to one department (1:M).
- One student can register for many courses, and one course can have many students (M:N). Enrolment resolves this relationship.
- One course can be taught by multiple lecturers, and one lecturer can teach multiple courses (M:N). Course_Lecturer resolves this relationship.
- One department can have many lecturers and many courses; each lecturer/course belongs to one department (1:M).

## Part 2: Normalization

### Unnormalized table (UNF)
The original registration sheet contains these columns:

`matric_no, student_name, student_dept, course_codes, course_titles, lecturers, lecturer_depts, session`

The original data includes:
- 20231234 | Ada Obi | Software Engineering | SEN307, SEN301 | Database Systems, Software Architecture | Dr A. Okafor, Dr M. Bello | Software Eng., Software Eng. | 2026/2027
- 20231235 | John Musa | Computer Science | SEN307, COS301 | Database Systems, Operating Systems | Dr A. Okafor, Mrs F. Eze | Software Eng., Computer Sci. | 2026/2027
- 20231236 | Grace Okon | Software Engineering | SEN301 | Software Architecture | Dr M. Bello | Software Eng. | 2026/2027

#First Normal Form (1NF)
Each cell contains one value, so each student-course combination has its own row. The composite primary key is `(matric_no, course_code, session)`.

| matric_no | student_name | student_dept | course_code | course_title | lecturer_name | lecturer_dept | session |
| 20231234 | Ada Obi | Software Engineering | SEN307 | Database Systems | Dr A. Okafor | Software Eng. | 2026/2027 |
| 20231234 | Ada Obi | Software Engineering | SEN301 | Software Architecture | Dr M. Bello | Software Eng. | 2026/2027 |
| 20231235 | John Musa | Computer Science | SEN307 | Database Systems | Dr A. Okafor | Software Eng. | 2026/2027 |
| 20231235 | John Musa | Computer Science | COS301 | Operating Systems | Mrs F. Eze | Computer Sci. | 2026/2027 |
| 20231236 | Grace Okon | Software Engineering | SEN301 | Software Architecture | Dr M. Bello | Software Eng. | 2026/2027 |

**What was fixed:** Multi-valued cells were split into individual student-course rows so every field contains one value.

#Second Normal Form (2NF)
Remove attributes that depend on only part of the composite key by separating students, courses and enrolments.

Student_2NF
`Student_2NF(matric_no PK, student_name, student_dept)`

| matric_no (PK) | student_name | student_dept |
|---|---|---|
| 20231234 | Ada Obi | Software Engineering |
| 20231235 | John Musa | Computer Science |
| 20231236 | Grace Okon | Software Engineering |

Course_2NF
`Course_2NF(course_code PK, course_title)`

| course_code (PK) | course_title |
| SEN307 | Database Systems |
| SEN301 | Software Architecture |
| COS301 | Operating Systems |

Enrolment_2NF
`Enrolment_2NF(matric_no PK/FK, course_code PK/FK, session PK)`

| matric_no (PK/FK) | course_code (PK/FK) | session (PK) |
| 20231234 | SEN307 | 2026/2027 |
| 20231234 | SEN301 | 2026/2027 |
| 20231235 | SEN307 | 2026/2027 |
| 20231235 | COS301 | 2026/2027 |
| 20231236 | SEN301 | 2026/2027 |

#Third Normal Form (3NF)
Move department details and lecturer details into separate tables. Use a junction table to record which lecturer teaches which course in each session.

Department
`Department(dept_id PK, dept_name)`

| dept_id (PK) | dept_name |
| 1 | Software Engineering |
| 2 | Computer Science |

Student
`Student(student_id PK, matric_no UNIQUE, full_name, dept_id FK -> Department.dept_id)`

Lecturer
`Lecturer(lecturer_id PK, lecturer_name, dept_id FK -> Department.dept_id)`

Course
`Course(course_id PK, course_code UNIQUE, course_title, dept_id FK -> Department.dept_id)`

Enrolment  
`Enrolment(student_id PK/FK -> Student.student_id, course_id PK/FK -> Course.course_id, session PK)`

Course_Lecturer 
`Course_Lecturer(course_id PK/FK -> Course.course_id, lecturer_id PK/FK -> Lecturer.lecturer_id, session PK)`

#Part 3: Final schema (PKs and FKs)

- `Department(dept_id PK, dept_name)`
- `Student(student_id PK, matric_no UNIQUE, full_name, dept_id FK -> Department.dept_id)`
- `Lecturer(lecturer_id PK, lecturer_name, dept_id FK -> Department.dept_id)`
- `Course(course_id PK, course_code UNIQUE, course_title, dept_id FK -> Department.dept_id)`
- `Enrolment(student_id PK/FK -> Student.student_id, course_id PK/FK -> Course.course_id, session PK)`
- `Course_Lecturer(course_id PK/FK -> Course.course_id, lecturer_id PK/FK -> Lecturer.lecturer_id, session PK)`
