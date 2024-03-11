# Data Normalization and Entity-Relationship Diagramming

Original Form:

| assignment_id | student_id | due_date | professor | assignment_topic                | classroom | grade | relevant_reading    | professor_email   |
| :------------ | :--------- | :------- | :-------- | :------------------------------ | :-------- | :---- | :------------------ | :---------------- |
| 1             | 1          | 23.02.21 | Melvin    | Data normalization              | WWH 101   | 80    | Deumlich Chapter 3  | l.melvin@foo.edu  |
| 2             | 7          | 18.11.21 | Logston   | Single table queries            | 60FA 314  | 25    | Dümmlers Chapter 11 | e.logston@foo.edu |
| 1             | 4          | 23.02.21 | Melvin    | Data normalization              | WWH 101   | 75    | Deumlich Chapter 3  | l.melvin@foo.edu  |
| 5             | 2          | 05.05.21 | Logston   | Python and pandas               | 60FA 314  | 92    | Dümmlers Chapter 14 | e.logston@foo.edu |
| 4             | 2          | 04.07.21 | Nevarez   | Spreadsheet aggregate functions | WWH 201   | 65    | Zehnder Page 87     | i.nevarez@foo.edu |
| ...           | ...        | ...      | ...       | ...                             | ...       | ...   | ...                 | ...               |


## what makes this data set not compliant with 4NF
1. **Multi-valued Dependencies**: The table may contain fields that are multi-valued dependencies. For example, the professor field is dependent on the course_id but not on the student_id or assignment_id.
2. **Non-key Attributes**: Non-key attributes in the table depend on other non-key attributes, rather than a superkey. For example, relevant_reading depends on the assignment_topic, not on the entire key of the relation.
3. **Redundancy**: the course_name is repeated for every section and assignment associated with a course, leading to unnecessary duplication.


**Adding more fields:**

| assignment_id | student_id | student_name | student_email      | course_id | course_name               | section_id | section_location | professor | assignment_topic                | classroom | grade | relevant_reading    | professor_email   | due_date |
|---------------|------------|--------------|--------------------|-----------|---------------------------|------------|------------------|-----------|---------------------------------|-----------|-------|---------------------|-------------------|----------|
| 1             | 1          | John Doe     | john.doe@foo.edu   | CS101     | Introduction to Computing | A1         | 12 Waverly Place Room101          | Melvin    | Data normalization              | WWH 101   | 80    | Deumlich Chapter 3  | l.melvin@foo.edu  | 23.02.21 |
| 2             | 7          | Jane Smith   | jane.smith@foo.edu | DB201     | Database Fundamentals     | B2         | 181 Mercer Street Room206         | Logston   | Single table queries            | 60FA 314  | 25    | Dümmlers Chapter 11 | e.logston@foo.edu | 18.11.21 |
| 1             | 4          | Mike Brown   | mike.brown@foo.edu | CS101     | Introduction to Computing | A2         | 12 Waverly Place Room101          | Melvin    | Data normalization              | WWH 101   | 75    | Deumlich Chapter 3  | l.melvin@foo.edu  | 23.02.21 |
| 5             | 2          | Sarah Green  | sarah.green@foo.edu| PY303     | Advanced Python           | C3         | 181 Mercer Street Room301         | Logston   | Python and pandas               | 60FA 314  | 92    | Dümmlers Chapter 14 | e.logston@foo.edu | 05.05.21 |
| 4             | 2          | Sarah Green  | sarah.green@foo.edu| EX402     | Excel for Data Analysis  | D4         | 12 Waverly Place Room201          | Nevarez   | Spreadsheet aggregate functions | WWH 201   | 65    | Zehnder Page 87     | i.nevarez@foo.edu | 04.07.21 |
| ...           | ...        | ...          | ...                | ...       | ...                       | ...        | ...              | ...       | ...                             | ...       | ...   | ...                 | ...               | ...      |



### Covert to 4NF

1. **Student Table** 

Primary Key: student_id

Fields: student_id, student_name, student_email

| student_id | student_name | student_email      |
|------------|--------------|--------------------|
| 1          | John Doe     | john.doe@foo.edu   |
| 7          | Jane Smith   | jane.smith@foo.edu |
| 4          | Mike Brown   | mike.brown@foo.edu |
| 2          | Sarah Green  | sarah.green@foo.edu|
| ...        | ...          | ...                |

2. **Course Table**

Primary Key: course_id

Fields: course_id, course_name

| course_id | course_name               |
|-----------|---------------------------|
| CS101     | Introduction to Computing |
| DB201     | Database Fundamentals     |
| PY303     | Advanced Python           |
| EX402     | Excel for Data Analysis   |
| ...       | ...                       |

3. **Section Table**

Primary Key: section_id

Foreign Key: course_id

Fields: section_id, course_id, section_location, professor, professor_email, classroom

| section_id | course_id | section_location                | professor | professor_email   | classroom |
|------------|-----------|---------------------------------|-----------|-------------------|-----------|
| A1         | CS101     | 12 Waverly Place Room101        | Melvin    | l.melvin@foo.edu  | WWH 101   |
| B2         | DB201     | 181 Mercer Street Room206       | Logston   | e.logston@foo.edu | 60FA 314  |
| A2         | CS101     | 12 Waverly Place Room101        | Melvin    | l.melvin@foo.edu  | WWH 101   |
| C3         | PY303     | 181 Mercer Street Room301       | Logston   | e.logston@foo.edu | 60FA 314  |
| D4         | EX402     | 12 Waverly Place Room201        | Nevarez   | i.nevarez@foo.edu | WWH 201   |
| ...        | ...       | ...                             | ...       | ...               | ...       |

4. **Assignment Table**

Primary Key: assignment_id, section_id (to ensure uniqueness of assignments within sections)

Foreign Key: section_id (the section assignment belongs to)

Fields: assignment_id, section_id, assignment_topic, due_date, relevant_reading

| assignment_id | section_id | assignment_topic                | due_date  | relevant_reading   |
|---------------|------------|---------------------------------|-----------|--------------------|
| 1             | A1         | Data normalization              | 23.02.21  | Deumlich Chapter 3 |
| 2             | B2         | Single table queries            | 18.11.21  | Dümmlers Chapter 11|
| 1             | A2         | Data normalization              | 23.02.21  | Deumlich Chapter 3 |
| 5             | C3         | Python and pandas               | 05.05.21  | Dümmlers Chapter 14|
| 4             | D4         | Spreadsheet aggregate functions | 04.07.21  | Zehnder Page 87    |
| ...           | ...        | ...                             | ...       | ...                |


 5. **Grade Table**

 Primary Key: assignment_id, student_id (assuming a student can only have one grade per assignment)

 Foreign Key:  assignment_id, student_id

 Fields: assignment_id, student_id, grade

 | assignment_id | student_id | grade |
|---------------|------------|-------|
| 1             | 1          | 80    |
| 2             | 7          | 25    |
| 1             | 4          | 75    |
| 5             | 2          | 92    |
| 4             | 2          | 65    |
| ...           | ...        | ...   |

#### Make the dataset 4NF-compliant

1. Decomposition into Separate Tables

Entities like Student, Course, Section, Assignment, and Grades were separated into individual tables to eliminate redundancy and ensure that each table represents one entity or concept.

2. Elimination of Multi-valued Dependencies

3. Creation of Composite Primary Keys:

 For example, the Grade table has a composite primary key of assignment_id and student_id, as a grade is specific to an assignment and a student.

4. Establishment of Foreign Key Relationships


**The ER Diagram**

![Example Image](/images/ER%20Diagram.drawio.svg)

