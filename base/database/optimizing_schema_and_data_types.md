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
  
### Differences between **DATETIME** and **TIMESTAMP**

a DATETIME and a TIMESTAMP column can store the same kind of data: date and time, to a precision of one second. However, TIMESTAMP uses only half as much storage space, is time zone–aware, and has special autoupdating capabilities. On the other hand, it has a much smaller range of allowable values, and sometimes its special capabilities can be a handicap.
