# Functional Dependency

## Definition

A functional dependency is a constraint that specifies the relationship between two sets of attributes where one set can accurately determine the value of other sets. It is denoted as X → Y, where X is a set of attributes that is capable of determining the value of Y. The attribute set on the left side of the arrow, X is called Determinant, while on the right side, Y is called the Dependent. **If for each instance of X, there is only one instance of Y, then Y is functionally dependent on X.**

## Type of Functional Dependency

### Trivial Functional Dependency

In Trivial Functional Dependency, a dependent is always a subset of the determinant.
i.e. If X → Y and Y is the subset of X, then it is called trivial functional dependency.

For example, 

| ssn | name  | age |
|:---:|:-----:|:---:|
| 42  | Alice | 17  |
| 43  | Bob   | 18  |
| 44  | Chad  | 18  |

Here, {ssn, name} → name is a trivial functional dependency, since the dependent name is a subset of determinant set {ssn, name}. Similarly, ssn → ssn is also an example of trivial functional dependency. 

### Non-trivial Functional Dependency

In Non-trivial functional dependency, the dependent is strictly not a subset of the determinant. i.e. If X → Y and Y is not a subset of X, then it is called Non-trivial functional dependency.

For example,

| ssn | name  | age |
|:---:|:-----:|:---:|
| 42  | Alice | 17  |
| 43  | Bob   | 18  |
| 44  | Chad  | 18  |

Here, ssn → name is a non-trivial functional dependency, since the dependent name is not a subset of determinant ssn
Similarly, {ssn, name} → age is also a non-trivial functional dependency, since age is not a subset of {ssn, name} 

### Multivalued Functional Dependency

In Multivalued functional dependency, entities of the dependent set are not dependent on each other. i.e. If a → {b, c} and there exists no functional dependency between b and c, then it is called a multivalued functional dependency.

For example,

| ssn | name  | age |
|:---:|:-----:|:---:|
| 42  | Alice | 17  |
| 43  | Bob   | 18  |
| 44  | Chad  | 18  |
| 45  | Dart  | 19  |

Here, ssn → {name, age} is a multivalued functional dependency, since the dependents name & age are not dependent on each other(i.e. name → age or age → name doesn't exist)

### Transitive Functional Dependency

In transitive functional dependency, dependent is indirectly dependent on determinant. i.e. If a → b & b → c, then according to axiom of transitivity, a → c. This is a transitive functional dependency.

For example,

| ssn | name   | dept | building |
|:---:|:------:|:----:|:--------:|
| 42  | Alice  | CO   | 4        |
| 43  | Bob    | EC   | 2        |
| 44  | Chad   | IT   | 1        |
| 45  | David  | EC   | 2        |

Here, ssn → dept and dept → building_no. Hence, according to the axiom of transitivity, ssn → building_no is a valid functional dependency. This is an indirect functional dependency, hence called Transitive functional dependency.