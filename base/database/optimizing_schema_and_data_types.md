# Optimizing Schema And Data Types

Good logical and physical design is the cornerstone of high performance, and you must design your schema for the specific queries you will run. This often involves trade-offs. For example, a denormalized schema can speed up some types of queries but slow down others. Adding counter and summary tables is a great way to optimize queries, but they can be expensive to maintain. MySQL’s particular features and implementation details influence this quite a bit.

## Choosing Optimal Data Types

MySQL supports a large variety of data types, and choosing the correct type to store your data is crucial to getting good performance. The following simple guidelines can help you make better choices, no matter what type of data you are storing:

- Smaller is usually better:

  In general, try to use the smallest data type that can correctly store and represent your data. Smaller data types are usually faster, because they use less space on the disk, in memory, and in the CPU cache. They also generally require fewer CPU cycles to process.
  
  Make sure you don’t underestimate the range of values you need to store, though, because increasing the data type range in multiple places in your schema can be a painful and time-consuming operation. If you’re in doubt as to which is the best data type to use, choose the smallest one that you don’t think you’ll exceed.

- Simple is good: 

  Fewer CPU cycles are typically required to process operations on simpler data types. For example, integers are cheaper to compare than characters, because character sets and collations (sorting rules) make character comparisons compli- cated. Here are two examples: you should store dates and times in MySQL’s built- in types instead of as strings, and you should use integers for IP addresses. We discuss these topics further later.

- Avoid NULL if possible:

  A lot of tables include nullable columns even when the application does not need to store NULL (the absence of a value), merely because it’s the default. It’s usually best to specify columns as NOT NULL unless you intend to store NULL in them.
  
  It’s harder for MySQL to optimize queries that refer to nullable columns, because they make indexes, index statistics, and value comparisons more complicated. A nullable column uses more storage space and requires special processing inside MySQL. When a nullable column is indexed, it requires an extra byte per entry and can even cause a fixed-size index (such as an index on a single integer column) to be converted to a variable-sized one in MyISAM.

  The performance improvement from changing NULL columns to NOT NULLisusually small, so don’t make it a priority to find and change them on an existing schema unless you know they are causing problems. However, if you’re planning to index columns, avoid making them nullable if possible.

  There are exceptions, of course. For example, it’s worth mentioning that InnoDB stores NULL with a single bit, so it can be pretty space-efficient for sparsely populated data. This doesn’t apply to MyISAM, though.
  
A DATETIME and a TIMESTAMP column can store the same kind of data: date and time, to a precision of one second. However, TIMESTAMP uses only half as much storage space, is time zone–aware, and has special autoupdating capabilities. On the other hand, it has a much smaller range of allowable values, and 
sometimes its special capabilities can be a handicap.

## Whole Numbers

If you’re storing whole numbers, use one of the integer types: TINYINT, SMALLINT, MEDIUMINT, INT, or BIGINT. These require 8, 16, 24, 32, and 64 bits of storage space, respectively. They can store values from −2(N–1) to 2(N–1)–1, where N is the number of bits of storage space they use.

Signed and unsigned types use the same amount of storage space and have the same performance, so use whatever’s best for your data range. Your choice determines how MySQL stores the data, in memory and on disk. However, integer computations generally use 64-bit BIGINT integers, even on 32-bit architectures. (The exceptions are some aggregate functions, which use DECIMAL or DOUBLE to perform computations.)

## Real Numbers

DECICMAL, FLOAT and DOUBLE can be specified by the following parameters (size, d). Where size is the total size of the String, and d represents precision. E.g: to store a number like 1234.567, you will set the datatype to DOUBLE(7, 3) where 7 is the total number of digits and 3 is the number of digits to follow the decimal point.

FLOAT and DOUBLE, both represent floating point numbers. A FLOAT is for single-precision, while a DOUBLE is for double-precision numbers. A precision from 0 to 23 results in a 4-byte single-precision FLOAT column. A precision from 24 to 53 results in an 8-byte double-precision DOUBLE column. FLOAT is accurate to approximately 7 decimal places, and DOUBLE upto 14.

Decimal’s declaration and functioning is similar to Double. But there is one big difference between floating point values and decimal (numeric) values. We use DECIMAL data type to store exact numeric values, where we do not want precision but exact and accurate values. A Decimal type can store a Maximum of 65 Digits, with 30 digits after decimal point. So, for the most accurate and precise value, Decimal would be the best option.

Read more about MySQL numeric datatypes [here](https://code.rohitink.com/2013/06/12/mysql-integer-float-decimal-data-types-differences/).

## String Types

### VARCHAR and CHAR types

#### VARCHAR

VARCHAR stores variable-length character strings and is the most common string data type. It can require less storage space than fixed-length types, because it uses only as much space as it needs (i.e., less space is used to store shorter values). 

VARCHAR uses 1 or 2 extra bytes to record the value’s length: 1 byte if the column’s maximum length is 255 bytes or less, and 2 bytes if it’s more. Assuming the latin1 character set, a VARCHAR(10) will use up to 11 bytes of storage space. A VARCHAR(1000) can use up to 1002 bytes, because it needs 2 bytes to store length information.

VARCHAR helps performance because it saves space. However, because the rows are variable-length, they can grow when you update them, which can cause extra work. If a row grows and no longer fits in its original location, the behavior is storage engine–dependent. For example, MyISAM may fragment the row, and InnoDB may need to split the page to fit the row into it. Other storage engines may never update data in-place at all.

#### CHAR

CHAR is fixed-length: MySQL always allocates enough space for the specified num- ber of characters. When storing a CHAR value, MySQL removes any trailing spaces. Values are padded with spaces as needed for comparisons.

CHAR is useful if you want to store very short strings, or if all the values are nearly the same length. For example, CHAR is a good choice for MD5 values for user pass- words, which are always the same length. CHAR is also better than VARCHAR for data that’s changed frequently, because a fixed-length row is not prone to fragmenta- tion. For very short columns, CHAR is also more efficient than VARCHAR; a CHAR(1) designed to hold only Y and N values will use only one byte in a single-byte character set,1 but a VARCHAR(1) would use two bytes because of the length byte.

The sibling types for CHAR and VARCHAR are BINARY and VARBINARY, which store binary strings. Binary strings are very similar to conventional strings, but they store bytes instead of characters. Padding is also different: MySQL pads BINARY values with \0 (the zero byte) instead of spaces and doesn’t strip the pad value on retrieval.

These types are useful when you need to store binary data and want MySQL to compare the values as bytes instead of characters. The advantage of byte-wise comparisons is more than just a matter of case insensitivity. MySQL literally compares BINARY strings one byte at a time, according to the numeric value of each byte. As a result, binary comparisons can be much simpler than character comparisons, so they are faster.

### BLOB and TEXT types

BLOB and TEXT are string data types designed to store large amounts of data as either binary or character strings, respectively.

In fact, they are each families of data types: the character types are TINYTEXT, SMALL TEXT, TEXT, MEDIUMTEXT, and LONGTEXT, and the binary types are TINYBLOB, SMALLBLOB, BLOB, MEDIUMBLOB, and LONGBLOB. BLOB is a synonym for SMALLBLOB, and TEXT is a synonym for SMALLTEXT.

The only difference between the BLOB and TEXT families is that BLOB types store binary data with no collation or character set, but TEXT types have a character set and collation.

### Using ENUM instead of a string type

The biggest downside of **ENUM** is that the list of strings is fixed, and adding or removing strings requires the use of **ALTER TABLE**. Thus, it might not be a good idea to use **ENUM** as a string data type when the list of allowed string values is likely to change arbitrarily in the future, unless it’s acceptable to add them at the end of the list, which can be done without a full rebuild of the table in MySQL 5.1.

Because MySQL stores each value as an integer and has to do a lookup to convert it to its string representation, **ENUM** columns have some overhead.  In particular, it can be slower to join a **CHAR** or **VARCHAR** column to an **ENUM** column than to another **CHAR** or **VARCHAR** column.

### Date and Time Types

The finest granularity of time MySQL can store is one second.

#### DATETIME

This type can hold a large range of values, from the year 1000 to the year 9999, with a precision of one second. It stores the date and time packed into an integer in YYYYMMDDHHMMSS format, independent of time zone. This uses eight bytes of storage space.
By default, MySQL displays DATETIME values in a sortable, unambiguous format, such as 2008-01-16 22:37:08. This is the ANSI standard way to represent dates and times.

#### TIMESTAMP

As its name implies, the TIMESTAMP type stores the number of seconds elapsed since 1970-01-01 00:00:00, Greenwich Mean Time--the same as a Unix timestamp. TIMESTAMP uses only four bytes of storage, so it has a much smaller range than DATETIME: from the year 1970 to   the year 2038.

The value a TIMESTAMP displays also depends on the time zone. The MySQL server, operating system, and client connections all have time zone settings.

Thus, a TIMESTAMP that stores the value 0 actually displays it as 1969-12-31 19:00:00 in Eastern Standard Time (EST), which is -5 timezone. It’s worth emphasizing this difference: if you store or access data from multiple time zones, the behavior of TIMESTAMP and DATETIME will be very different. The former preserves values relative to the time zone in use, while the latter preserves the textual representation of the date and time.

Special behavior aside, in general if you can use TIMESTAMP you should, because it is more space-efficient than DATETIME. Sometimes people store Unix timestamps as integer values, but this usually doesn’t gain you anything. The integer format is often less convenient to deal with, so we do not recommend doing this.

### Choosing Identifiers

When choosing a type for an identifier column, you need to consider not only the storage type, but also how MySQL performs computations and comparisons on that type. For example, MySQL stores ENUM and SET types internally as integers but converts them to strings when doing comparisons in a string context.

Once you choose a type, make sure you use the same type in all related tables. Choose the smallest size that can hold your required range of values, and leave room for future growth if necessary. A TINYINT should be sufficient and is three bytes smaller. If you use this value as a foreign key in other tables, three bytes can make a big difference.

Here are a few tips:
- Integer types: Integers are usually the best choice for identifiers, because they’re fast and they work with ** AUTO_INCREMENT**.
- **ENUM** and **SET**: The **ENUM** and **SET** types are generally a poor choice for identifiers, though they can be okay for static “definition tables” that contain status or “type” values. **ENUM** and **SET** columns are appropriate for holding information such as an order’s status, a product’s type, or a person’s gender.
- String types: Avoid string types for identifiers if possible, because they take up a lot of space and are generally slower than integer types. You should also be very careful with completely “random” strings, such as those produced by MD5(), SHA1(), or UUID(). Each new value you generate with them will be distributed in arbitrary ways over a large space, which can slow INSERT and some types of SELECT queries:
  - They slow INSERT queries because the inserted value has to go in a random location in indexes. This causes page splits, random disk accesses, and clus- tered index fragmentation for clustered storage engines. More about this in the next chapter.
  - They slow SELECT queries because logically adjacent rows will be widely dis- persed on disk and in memory.
  - Random values cause caches to perform poorly for all types of queries because they defeat locality of reference, which is how caching works. If the entire dataset is equally “hot,” there is no advantage to having any particular part of the data cached in memory, and if the working set does not fit in memory, the cache will have a lot of flushes and misses.

## Normalization and Denormalization

### Pros and Cons of a Normalized Schema

- Inserts, updates on normalized schemas are usually fasster than denormalized schemas.
- There is little or no duplicated data, so there's less data to change.
- Normalized tables are usually smaller, so they fit better in memory and perform better.
- The lack of reduntdant data means there's less need for DISTINCT or GROUP BY queries.

The drawbacks of a normalized schema usually have to do with retrieval. Any nontrivial query on a well-normalized schema will probably require at least one join, and perhaps several. This is not only expensive, but it can make some indexing strategies impossible. For example, normalizing may place columns in different tables that would benefit from belonging to the same index.

### Pros and Cons of a Denormalized Schema

A denormalized schema works well because everything is in the same table, which avoids joins. If you don’t need to join tables, the worst case for most queries—even the ones that don’t use indexes—is a full table scan. This can be much faster than a join when the data doesn’t fit in memory, because it avoids random I/O.

A single table can also allow more efficient indexing strategies. Suppose you have a website where users post their messages, and some users are premium users. Now say you want to view the last 10 messages from premium users. If you’ve normalized the schema and indexed the publishing dates of the messages, the query might look like this:

```sql
SELECT message_text, user_name
FROM message
INNER JOIN user ON message.user_id=user.id
WHERE user.account_type='premiumv
ORDER BY message.published DESC LIMIT 10;
```

To execute this query efficiently, MySQL will need to scan the published index on the message table. For each row it finds, it will need to probe into the user table and check whether the user is a premium user. This is inefficient if only a small fraction of users have premium accounts.

The other possible query plan is to start with the user table, select all premium users, get all messages for them, and do a filesort. This will probably be even worse.

The problem is the join, which is keeping you from sorting and filtering simultaneously with a single index. If you denormalize the data by combining the tables and add an index on(account_type, published), you can write the query without a join. This will be very efficient:

```sql
SELECT message_text,user_name
FROM user_messages
WHERE account_type='premium'
ORDER BY published DESC
LIMIT 10;
```

### A Mixture of Normalized and Denormalized

Fully normalized and fully denormalized schemas are like laboratory rats: they usually have little to do with the real world. In the real world, you often need to mix the approaches, possibly using a partially normalized schema, cache tables, and other techniques. The most common way to denormalize data is to **duplicate, or cache, selected columns from one table in another table**.

In our website example, for instance, instead of denormalizing fully you can store account_type in both the user and message tables. This avoids the insert and delete problems that come with full denormalization, because you never lose information about the user, even when there are no messages. It won’t make the user_message table much larger, but it will let you select the data efficiently.

However, it’s now more expensive to update a user’s account type, because you have to change it in both tables. To see whether that’s a problem, you must consider how frequently you’ll have to make such changes and how long they will take, compared to how often you’ll run the SELECT query.

Another good reason to move some data from the parent table to the child table is for sorting. For example, it would be extremely expensive to sort messages by the author’s name on a normalized schema, but you can perform such a sort very efficiently if you cache the author_name in the message table and index it.

It can also be useful to cache derived values. If you need to display how many messages each user has posted (as many forums do), either you can run an expensive subquery to count the data every time you display it, or you can have a num_messages column in the user table that you update whenever a user posts a new message.

## Cache and Summary Tables


