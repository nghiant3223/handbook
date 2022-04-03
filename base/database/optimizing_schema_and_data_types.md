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

VARCHAR stores variable-length character strings and is the most common string data type. It can require less storage space than fixed-length types, because it uses only as much space as it needs (i.e., less space is used to store shorter values). 

VARCHAR uses 1 or 2 extra bytes to record the value’s length: 1 byte if the column’s maximum length is 255 bytes or less, and 2 bytes if it’s more. Assuming the latin1 character set, a VARCHAR(10) will use up to 11 bytes of storage space. A VARCHAR(1000) can use up to 1002 bytes, because it needs 2 bytes to store length information.

VARCHAR helps performance because it saves space. However, because the rows are variable-length, they can grow when you update them, which can cause extra work. If a row grows and no longer fits in its original location, the behavior is storage engine–dependent. For example, MyISAM may fragment the row, and InnoDB may need to split the page to fit the row into it. Other storage engines may never update data in-place at all.
