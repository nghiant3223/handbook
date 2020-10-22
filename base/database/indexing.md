# Indexing

## Single-Level Ordered Indexes

- **Primary index**: specified on the ordering key field of an ordered file of records
- **Clustering index**: specified on the ordering non-key field of an ordered file of records
- A file can have at most one physical ordering field, so it can have **either primary index or clustering index**
- **Secondary index**: specified on a field that is a candidate key and has a unique value in every record, or on a nonkey field with duplicate values

![Primary Index](images/primary_index.png)
![Clustering Index 1](images/clustering_index_1.png)
![Clustering Index 2](images/clustering_index_2.png)
![Secondary Index 1](images/secondary_index_1.png)
![Secondary Index 2](images/secondary_index_2.png)

## Multi-Level Indexes

Use B-Tree and B+-Tree for better searching
![Multilevel Index](images/multi_level_index.png)
