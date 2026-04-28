# Student Information Management System

A fully normalized relational database system for managing academic records, course enrollment, faculty assignments, and institutional data for a university environment.

---

## Overview

This project implements a production-grade relational database schema designed to manage all core entities of a student information system — students, faculty, courses, enrollments, grades, and departments. The schema adheres to Third Normal Form (3NF) / Boyce-Codd Normal Form (BCNF) to eliminate data redundancy and ensure referential integrity.

---

## Features

- **Normalized Schema (3NF/BCNF)** — All tables designed to eliminate partial and transitive dependencies
- **Referential Integrity** — Comprehensive foreign key constraints with appropriate cascade rules
- **Stored Procedures** — Pre-built procedures for enrollment, grade recording, and transcript generation
- **Triggers** — Automatic GPA calculation on grade update; enrollment cap enforcement
- **Views** — Simplified interfaces for common queries (student transcripts, course rosters, department reports)
- **Indexing Strategy** — Indexes on high-frequency lookup columns for query performance
- **Sample Data** — Seed scripts for realistic testing across 5 departments, 50 courses, and 200 students

---

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Database | MySQL 8.0 / PostgreSQL 15 |
| Schema Design | ERD (Entity-Relationship Diagram) |
| Scripting | SQL (DDL, DML, DCL) |
| Normalization | 1NF → 2NF → 3NF / BCNF |
| Tooling | MySQL Workbench, pgAdmin, DBeaver |

---

## Schema Overview

### Core Entities

```
students          — Student demographic and enrollment status
faculty           — Instructor profiles and department assignment
departments       — Academic departments and degree programs
courses           — Course catalog with credit hours and prerequisites
sections          — Scheduled course sections (semester, time, room)
enrollments       — Student-section relationships with grade tracking
prerequisites     — Course prerequisite dependency graph
```

### Entity-Relationship Summary

```
Department (1) ──< Faculty (M)
Department (1) ──< Course (M)
Course (M) ──< Section (M) (semester/year)
Student (M) >──< Section (M)  [via Enrollment]
Course (M) >──< Course (M)    [via Prerequisite]
```

---

## Installation

```bash
git clone https://github.com/Ramez-Khalil/student-info-management.git
cd student-info-management
```

**MySQL:**
```bash
mysql -u root -p < sql/schema.sql
mysql -u root -p student_db < sql/seed_data.sql
```

**PostgreSQL:**
```bash
psql -U postgres -f sql/schema_pg.sql
psql -U postgres -d student_db -f sql/seed_data.sql
```

---

## Key SQL Examples

**Student transcript query:**
```sql
SELECT
    s.student_id,
    s.first_name,
    s.last_name,
    c.course_code,
    c.course_name,
    e.grade,
    c.credit_hours
FROM students s
JOIN enrollments e ON s.student_id = e.student_id
JOIN sections sec ON e.section_id = sec.section_id
JOIN courses c ON sec.course_id = c.course_id
ORDER BY s.last_name, c.course_code;
```

**GPA calculation view:**
```sql
CREATE VIEW vw_student_gpa AS
SELECT
    s.student_id,
    s.first_name,
    s.last_name,
    ROUND(
        SUM(grade_points * c.credit_hours) / SUM(c.credit_hours), 2
    ) AS cumulative_gpa
FROM students s
JOIN enrollments e ON s.student_id = e.student_id
JOIN sections sec ON e.section_id = sec.section_id
JOIN courses c ON sec.course_id = c.course_id
WHERE e.grade IS NOT NULL
GROUP BY s.student_id, s.first_name, s.last_name;
```

---

## Project Structure

```
student-info-management/
├── sql/
│   ├── schema.sql           # Full DDL — tables, constraints, indexes
│   ├── schema_pg.sql        # PostgreSQL-compatible DDL
│   ├── views.sql            # Transcript, roster, and GPA views
│   ├── procedures.sql       # Stored procedures (enroll, drop, record grade)
│   ├── triggers.sql         # GPA recalculation and cap enforcement triggers
│   └── seed_data.sql        # Sample data for testing
├── erd/
│   └── erd_diagram.png      # Entity-Relationship Diagram
├── docs/
│   └── normalization.md     # Step-by-step normalization walkthrough
└── README.md
```

---

## Normalization Walkthrough

| Normal Form | Requirement | Status |
|-------------|-------------|--------|
| 1NF | Atomic values, no repeating groups | ✅ |
| 2NF | No partial dependencies on composite keys | ✅ |
| 3NF | No transitive dependencies | ✅ |
| BCNF | Every determinant is a candidate key | ✅ |

A full normalization walkthrough with before/after table comparisons is documented in `docs/normalization.md`.

---

## Skills Demonstrated

- Relational database design and ER modeling
- SQL DDL/DML: table creation, constraints, indexes, joins, aggregations
- Stored procedures, views, and triggers
- Database normalization theory and application (1NF through BCNF)
- Query optimization and indexing strategies

---

*Developed as part of a database systems course at Cal Poly Pomona.*
