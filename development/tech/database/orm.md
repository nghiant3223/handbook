# The pros and cons of Object Relational Mapping (ORM)
## Definition
- A technique that lets you `query` and `manipulate` data usually from a database using an `object-oriented paradigm`.
- A way of `retrieving` and `manipulating` data `without having to understand` the `underlying data architecture`.
- `Another layer of abstraction` in your application.

In SQL
```
SELECT `id`, `username`, `name`, `email`
FROM users
WHERE id = 1;
```
In PHP, using the Laravel framework
```
<?php
 
\App\Models\User::where('id', 1)->first();
```
## Pros
- Models are `DRY` (don't repeat yourself): only write your model `once`.
- `A lot of things are done` for you, from handling the underlying database connection(s) to localisation.
- Prevent `SQL injection` because queries are `prepared` and `sanitised`.
- SQL queries may already `be optimized`, developers can sometimes be bad at writing SQL.
- When `changing database`, you may `not have to rewrite code`
- Use `OOP`-> can `extend` and `inherit` (the data models).
- `Performance` between ORM and direct SQL are similarly the `same`, and `ORM` can `do anything` SQL do.
- ORMs get you the data you need in a much `more readable format`.
- `Improve development time` and prevent `disjointed` code bases
- Work very well with CRUD
## Cons
- The `complex` queries can lead to performance issues if not written properly.
- The `N+1` issue: can cause `fetching the related tables many times`. -> solved by 'bringing' the related table with the original table, reducing the calls to the database.
- This will cause `N+1`
```
<?php
 
$users = User::all();
 
foreach($users as $user) {
    echo "User: " . $user->name . "<br>";
    foreach($user->roles as $role) {
        // this will make a call to the database for every user.
        echo "\tRole: " . $role->name . "<br>";
    }
}
 ```
- SOLUTION
 ```
$users = User::with('roles')->all();
 
foreach($users as $user) {
    echo "User: " . $user->name . "<br>";
    foreach($user->roles as $role) {
        // this already has the role information for each user so will not call the database again.
        echo "\tRole: " . $role->name . "<br>";
    }
}
```
- If you don't have much understanding of the underline database it is `hard to debug/fix` when something goes wrong in the underline database.
- You have to `spend a bit of time` learning `how to use the ORM` -> have an initial impact on development.
- `Developers` don't necessarily learn about SQL, which can be a massive `issue` if they `ignore learning SQL`.

## Reference
- [The pros and cons of Object Relational Mapping (ORM)
](https://midnite.uk/blog/the-pros-and-cons-of-object-relational-mapping-orm)