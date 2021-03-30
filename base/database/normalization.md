# Normalization

## Introduction

Data redundancy unnecessarily increases the size of the database as the same data is repeated in many places. Inconsistency problems also arise during insertion, delete and update operations.

Database normalization is the process of organizing the attributes of the database to reduce or eliminate data redundancy (having the same data but at different places).

## Normal Forms

### First Normal Form (1NF)

If a relation contain composite or multi-valued attribute, it violates first normal form or a relation is in first normal form if it does not contain any composite or multi-valued attribute. A relation is in first normal form if every attribute in that relation is singled valued attribute.

Example,

| id | name | courses |
|----|------|---------|
| 1  | A    | c1, c2  |
| 2  | E    | c3      |
| 3  | M    | c2, c3  |

In the above table, `course` is a multi-valued attribute, so it is not in 1NF.
Below table is in 1NF as there is no multi-valued attribute.

| id  | name | courses |
|:---:|:----:|:-------:|
| 1   | A    | c1      |
| 1   | A    | c2      |
| 2   | E    | c3      |
| 3   | M    | c2      |
| 3   | M    | c3      |

### Second Normal Form (2NF)

To be in second normal form, a relation must be in first normal form and relation must not contain any partial dependency. A relation is in 2NF if it has No Partial Dependency, i.e., no non-prime attribute (attributes which are not part of any candidate key) is dependent on any proper subset of any candidate key of the table.
> Partial dependency happens if the proper subset of candidate key determines non-prime attribute.

Example, 

| student_no | course_no | course_fee |
|:----------:|:---------:|:----------:|
| 1          | c1        | 1000       |
| 2          | c2        | 1500       |
| 1          | c4        | 2000       |
| 4          | c3        | 1000       |
| 4          | c1        | 1000       |
| 2          | c5        | 2000       |

Here, course_fee cannot alone decide the value of course_no or student_no, course_fee together with student_no cannot decide the value of course_no, course_fee together with course_no cannot decide the value of student_no.

Hence, course_fee would be a non-prime attribute, as it does not belong to the one only candidate key {student_no, course_no}.

But, course_no → course_no , i.e., course_fee is dependent on course_no, which is a proper subset of the candidate key. Non-prime attribute course_fee is dependent on a proper subset of the candidate key, which is a partial dependency and so this relation is not in 2NF.

To convert the above relation to 2NF, we need to split the table into two tables such as:  
Table 1: student_no, course_no  
Table 2: course_no, course_fee  

| student_no | course_no |
|:----------:|:---------:|
| 1          | c1        |
| 2          | c2        |
| 1          | c4        |
| 4          | c3        |
| 4          | c1        |

| student_no | course_fee |
|:----------:|:-----------:|
| 1          | c1          |
| 2          | c2          |
| 1          | c4          |
| 4          | c3          |
| 4          | c1          |

### Third Normal Form (3NF)

A relation is in third normal form, if there is no transitive dependency for non-prime attributes as well as it is in second normal form. A relation is in 3NF if at least one of the following condition holds in every non-trivial function dependency X → Y: (1) X is a super key and (2) Y is a prime attribute (each element of Y is part of some candidate keys).

> Transitive dependency: If A → B and B → C are two FDs then A → C is called transitive dependency.

| no  | name | state | country | age |
|:---:|:----:|:-----:|:-------:|:----|
| 1   | A    | HCM   | Vietnam | 20  |
| 2   | B    | HCM   | Vietnam | 19  |
| 3   | C    | Deli  | India   | 22  |

Apparently, candidate key is {no}. In this table, no → state and state → country are true. country is transitively dependent on no. It violates the third normal form.

To convert it in third normal form, we will decompose the relation into two tables:  
Table 1: no, name, age  
Table 2: state, country  

| no  | name | state | age |
|:---:|:----:|:-----:|:---:|
| 1   | A    | HCM   | 20  |
| 2   | B    | HCM   | 19  |
| 3   | C    | Deli  | 22  |

| state | country |
|:-----:|:-------:|
| HCM   | Vietnam |
| Deli  | India   |

### Boyce-Codd Normal Form (BCNF)

A relation R is in BCNF if R is in 3NF and for every functional dependency, its left-hand-side is super key. A relation is in BCNF iff in every non-trivial functional dependency X –> Y, X is a super key.

Example, Find the highest normal form of a relation R(A,B,C,D,E) with FD set as {BC->D, AC->BE, B->E}
