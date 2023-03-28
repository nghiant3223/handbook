# PostgreSQL datatypes
## Numeric Types
### Integer Types
- The types `smallint`, `integer`, and `bigint` store whole numbers without fractional components, of various ranges.
- Attempts to store values outside of the allowed range will result in an error.
### Arbitrary Precision Numbers
- `numeric`:
    - Can store numbers with a very large number of digits. 
    - Recommended for storing monetary amounts and other quantities where exactness is required.
    - Calculations with numeric values yield exact results where possible, e.g., addition, subtraction, multiplication. 
    - However, calculations on numeric values are very slow compared to the integer types, or to the floating-point types.
    - The `precision` of a `numeric` is the total count of significant digits in the number of digits to both sides of the decimal point.
    - The `scale` of a `numeric` is the count of decimal digits in the fractional part, to the right of the decimal point.
    - `23.5141` has a `precision` of 6 and a `scale` of 4.
    - Integers can be considered to have a scale of zero.
    ```json
    NUMERIC(precision, scale)
    ```
    - If `scale` of a value to be stored is greater than the declared `scale` of the column, the system will round the value to the specified number of fractional digits.
    - If the number of digits to the left of the decimal point exceeds the declared precision minus the declared scale, an error is raised.
    - Beginning in `PostgreSQL 15`, it is allowed to declare a numeric column with a `negative` scale. Then values will be rounded to the left of the decimal point. The `precision` still represents the maximum number of non-rounded digits.
        - `NUMERIC(2, -3)` round values to the nearest thousand and can store values between -99000 and 99000
    - Allowed to declare `scale` > `precision`, can only hold fractional values, the number of zero to the right of decimal point is at least `scale` - `precision`.
        - `NUMERIC(3, 5)` round values to 5 decimal places and can store values between -0.00999 and 0.00999
    - The actual storage requirement is two bytes for each group of four decimal digits, plus three to eight bytes overhead.
    - Has several special values: `Infinity`, `-Infinity` and `NaN`, represent “infinity”, “negative infinity”, and “not-a-number”.
    - The types `decimal` and `numeric` are equivalent. Both types are part of the SQL standard.

### Floating-Point Types
- `real` and `double` precision are `inexact`, variable-precision numeric types -> some values cannot be converted exactly to the internal format and are stored as approximations -> storing and retrieving a value might show slight discrepancies.
- If you require exact storage and calculations (such as for monetary amounts), use the `numeric` type instead.
- Comparing two floating-point values for equality might not always work as expected.
- `real` type has a range of around `1E-37` to `1E+37` with a precision of at least `6` decimal digits.
- `double precision` type has a range of around `1E-307` to `1E+308` with a precision of at least `15` digits. 
- Values that are too large or too small will cause an error.
- Rounding might take place if the precision of an input number is too high.
- Has several special values: `Infinity`, `-Infinity` and `NaN`, represent “infinity”, “negative infinity”, and “not-a-number”.
- Supports the SQL-standard notations `float` and `float(p)`, `p` specifies the minimum acceptable precision in binary digits.
- `float(1)` to `float(24)` -> `real`
- `float(25)` to `float(53)` -> `double precision`
- `float` with no precision specified -> `double precision`

### Serial Types
-  `smallserial`, `serial` and `bigserial` used for creating unique identifier columns (similar to the   `AUTO_INCREMENT` property)
```
CREATE TABLE tablename (
    colname SERIAL
);
```
equivalent to
```
CREATE SEQUENCE tablename_colname_seq AS integer;
CREATE TABLE tablename (
    colname integer NOT NULL DEFAULT nextval('tablename_colname_seq')
);
ALTER SEQUENCE tablename_colname_seq OWNED BY tablename.colname;
```
- There may be "holes" or gaps in the sequence of values which appears in the column, even if no rows are ever deleted because value allocated from the sequence is still "used up" even if a row containing that value is never successfully inserted into the table column.
- To insert the next value of the sequence into the serial column, specify that the `serial` column should be assigned its default value -> by excluding the column from the list of columns in the INSERT statement or the use of the `DEFAULT` key word.
## Monetary Types
- The `money` type stores a currency amount with a fixed fractional precision.
- The fractional precision is determined by the database's `lc_monetary` setting. 
-  Input is accepted in a variety of formats, including integer and floating-point literals, as well as typical currency formatting, such as `$1,000.00`. 
- Output is generally in the latter form but depends on the locale.
- Since the output of this data type is `locale-sensitive`, it might not work to load money data into a database that has a different setting of `lc_monetary`.
- To avoid problems, before restoring a dump into a new database make sure `lc_monetary` has the same or equivalent value as in the database that was dumped.
- Size: 8 bytes
- Range: -92233720368547758.08 to +92233720368547758.07
- Values of the `numeric`, `int`, and `bigint` data types can be cast to `money`.
- Conversion from the `real` and `double precision` data types can be done by casting to `numeric` first but not recommended to store money as these types because of unexactness.
- A `money` value can be cast to `numeric` without loss of precision.
## Character Types
- `varchar(n)`: variable-length with limit
- `char(n)`: fixed-length, blank padded
- `text`: variable unlimited length
- An attempt to store a longer string into a column of these types will result in an error, unless the excess characters are all spaces
- If the string to be stored is shorter than the declared length, values of type `char(n)` will be space-padded; values of type `varchar(n)` will simply store the shorter string.
- If one explicitly casts a value to `varchar(n)` or `char(n)`, then an over-length value will be truncated to n characters without raising an error.
- Trailing spaces are treated as semantically insignificant and disregarded when comparing two values of type `char(n)`. In collations where whitespace is significant, this behavior can produce unexpected results.
- Trailing spaces are removed when converting a `char(n)` value to one of the other string types.
- Trailing spaces are semantically significant in `varchar(n)` and `text` values -> when using pattern matching: `LIKE` and regular expressions.
- The characters that can be stored in any of these data types are determined by the `database character set`, which is selected when the database is created.
- Regardless of the specific character set, the character with code zero (sometimes called NUL) cannot be stored.
- The storage requirement for a short string (up to `126 bytes`) is `1 byte` + the actual string, longer strings have `4 bytes` overhead.
- Long strings are compressed by the system automatically, so the physical requirement on disk might be less.
- Very long values are also stored in background tables so that they do not interfere with rapid access to shorter column values.
- In any case, the longest possible character string that can be stored is about `1 GB`.
## Binary Data Types
- name: `bytea`
- size: `1 or 4 bytes` + the actual binary string
- Store variable-length binary string
- A sequence of bytes
- Distinguished from character strings in two ways.
    - Allow storing bytes of value zero and other `non-printable` bytes (outside the decimal range `32` to `126`). Character strings disallow zero bytes + invalid bytes according to the database's `selected character set encoding`.
    - Operations on binary strings process the actual bytes, whereas the processing of character strings depends on locale settings.
- Appropriate for storing data that the programmer thinks of as `raw bytes`, whereas character strings are appropriate for storing `text`.
### bytea Hex Format
- The `hex` format encodes binary data as `2 hexadecimal digits per byte`
- Preceded by the sequence \x (to distinguish it from the escape format)
- For input, the hexadecimal digits can be either upper or lower case, and whitespace is permitted between digit pairs.
- Compatible with a wide range of external applications and protocols
- Faster to convert than the `escape` format.
- `IS PREFERRED`
- Example:
```
SELECT '\xDEADBEEF';
```
### bytea Escape Format
- `Traditional PostgreSQL format` for the `bytea` type.
- Representing a binary string as a sequence of `ASCII characters` while converting those bytes that cannot be represented as an ASCII character into special escape sequences.
- Usually confusing because it fuzzes up the distinction between `binary strings` and `character strings`.
- Should probably be `avoided` for most new applications.
## Date/Time Types
| Name                                    | Storage Size | Description                           | Low Value        | High Value      | Resolution    |
|-----------------------------------------|--------------|---------------------------------------|------------------|-----------------|---------------|
| timestamp [ (p) ] [ without time zone ] | 8 bytes      | both date and time (no time zone)     | 4713 BC          | 294276 AD       | 1 microsecond |
| timestamp [ (p) ] with time zone        | 8 bytes      | both date and time, with time zone    | 4713 BC          | 294276 AD       | 1 microsecond |
| date                                    | 4 bytes      | date (no time of day)                 | 4713 BC          | 5874897 AD      | 1 day         |
| time [ (p) ] [ without time zone ]      | 8 bytes      | time of day (no date)                 | 00:00:00         | 24:00:00        | 1 microsecond |
| time [ (p) ] with time zone             | 12 bytes     | time of day (no date), with time zone | 00:00:00+1559    | 24:00:00-1559   | 1 microsecond |
| interval [ fields ] [ (p) ]             | 16 bytes     | time interval                         | -178000000 years | 178000000 years | 1 microsecond |
### Date/Time Input
- Any date or time literal input needs to be enclosed in `single quotes`, like text strings
- `Date` input:

| Example          | Description                                                                             |
|------------------|-----------------------------------------------------------------------------------------|
| 1999-01-08       | ISO 8601; January 8 in any mode (recommended format)                                    |
| January 8, 1999  | unambiguous in any datestyle input mode                                                 |
| 1/8/1999         | January 8 in MDY mode; August 1 in DMY mode                                             |
| 1/18/1999        | January 18 in MDY mode; rejected in other modes                                         |
| 01/02/03         | January 2, 2003 in MDY mode; February 1, 2003 in DMY mode; February 3, 2001 in YMD mode |
| 1999-Jan-08      | January 8 in any mode                                                                   |
| Jan-08-1999      | January 8 in any mode                                                                   |
| 08-Jan-1999      | January 8 in any mode                                                                   |
| 99-Jan-08        | January 8 in YMD mode, else error                                                       |
| 08-Jan-99        | January 8, except error in YMD mode                                                     |
| Jan-08-99        | January 8, except error in YMD mode                                                     |
| 19990108         | ISO 8601; January 8, 1999 in any mode                                                   |
| 990108           | ISO 8601; January 8, 1999 in any mode                                                   |
| 1999.008         | year and day of year                                                                    |
| J2451187         | Julian date                                                                             |
| January 8, 99 BC | year 99 BC                                                                              |

- `Time` input:

| Example                              | Description                                               |
|--------------------------------------|-----------------------------------------------------------|
| 04:05:06.789                         | ISO 8601                                                  |
| 04:05:06                             | ISO 8601                                                  |
| 04:05                                | ISO 8601                                                  |
| 040506                               | ISO 8601                                                  |
| 04:05 AM                             | same as 04:05; AM does not affect value                   |
| 04:05 PM                             | same as 16:05; input hour must be <= 12                   |
| 04:05:06.789-8                       | ISO 8601, with time zone as UTC offset                    |
| 04:05:06-08:00                       | ISO 8601, with time zone as UTC offset                    |
| 04:05-08:00                          | ISO 8601, with time zone as UTC offset                    |
| 040506-08                            | ISO 8601, with time zone as UTC offset                    |
| 040506+0730                          | ISO 8601, with fractional-hour time zone as UTC offset    |
| 040506+07:30:00                      | UTC offset specified to seconds (not allowed in ISO 8601) |
| 04:05:06 PST                         | time zone specified by abbreviation                       |
| 2003-04-12 04:05:06 America/New_York | time zone specified by full name                          |
| J2451187                             | Julian date                                               |
| January 8, 99 BC                     | year 99 BC                                                |

- `Time Zone` input

| Example                              | Description                                               |
|--------------------------------------|-----------------------------------------------------------|
| PST                                  | Abbreviation (for Pacific Standard Time)                  |
| America/New_York                     | Full time zone name                                       |
| PST8PDT                              | POSIX-style time zone specification                       |
| -8:00:00                             | UTC offset for PST                                        |
| -8:00                                | UTC offset for PST (ISO 8601 extended format)             |
| -800                                 | UTC offset for PST (ISO 8601 basic format)                |
| -8                                   | UTC offset for PST (ISO 8601 basic format)                |
| zulu                                 | Military abbreviation for UTC                             |
| z                                    | Short form of zulu (also in ISO 8601)                     |
| 040506-08                            | ISO 8601, with time zone as UTC offset                    |
| 040506+0730                          | ISO 8601, with fractional-hour time zone as UTC offset    |
| 040506+07:30:00                      | UTC offset specified to seconds (not allowed in ISO 8601) |
| 04:05:06 PST                         | time zone specified by abbreviation                       |
| 2003-04-12 04:05:06 America/New_York | time zone specified by full name                          |
| J2451187                             | Julian date                                               |
| January 8, 99 BC                     | year 99 BC                                                |

### Time Stamps input
- Valid input for the `timestamp` types consists of the concatenation of a `date` and a `time`, followed by an optional `time zone`, followed by an optional `AD` or `BC`.
- The SQL standard differentiates` timestamp without time zone` and `timestamp with time zone` literals by the presence of a “+” or “-” symbol and `time zone offset` after the `time`.
```
TIMESTAMP '2004-10-19 10:23:54+02'
```
- In a literal that has been determined to be `timestamp without time zone`, PostgreSQL will `silently ignore any time zone indication`. 
- For `timestamp with time zone`, the `internally stored value` is always in `UTC`. 
- An input value that has an explicit time zone specified is `converted to UTC` using the appropriate offset for that time zone.
- If no time zone in the input -> assumed to be in the time zone indicated by the system's `TimeZone` parameter, and then `converted to UTC`.
- When a `timestamp with time zone` value is output, it is always `converted from UTC to the current timezone zone`, and `displayed as local time` in that zone.
- To see the time in another time zone, either `change timezone` or use the `AT TIME ZONE` construct.
- Conversions between `timestamp without time zone` and `timestamp with time zone` normally assume that the `timestamp without time zone` value should be taken or given as `timezone local time`. A different time zone can be specified for the conversion using `AT TIME ZONE`.
### Special Values
- All of these values need to be `enclosed in single quotes` when used as constants in SQL commands.

| Input String | Valid Types           | Description                                    |
|--------------|-----------------------|------------------------------------------------|
| epoch        | date, timestamp       | 1970-01-01 00:00:00+00 (Unix system time zero) |
| infinity     | date, timestamp       | later than all other time stamps               |
| -infinity    | date, timestamp       | earlier than all other time stamps             |
| now          | date, time, timestamp | current transaction's start time               |
| today        | date, timestamp       | midnight (00:00) today                         |
| tomorrow     | date, timestamp       | midnight (00:00) tomorrow                      |
| yesterday    | date, timestamp       | midnight (00:00) yesterday                     |
| allballs     | time                  | 00:00:00.00 UTC                                |

### Date/Time Output
- `ISO 8601`, `SQL (Ingres)`, `traditional POSTGRES (Unix date format)`, or `German`
- The default is the `ISO` format.

| Style Specification | Description            | Example                      |
|---------------------|------------------------|------------------------------|
| ISO                 | ISO 8601, SQL standard | 1997-12-17 07:37:16-08       |
| SQL                 | traditional style      | 12/17/1997 07:37:16.00 PST   |
| Postgres            | original style         | Wed Dec 17 07:37:16 1997 PST |
| German              | regional style         | 17.12.1997 07:37:16.00 PST   |
- The date/time style can be selected by the user using the `SET datestyle` command, the `DateStyle` parameter in the `postgresql.conf` configuration file, or the `PGDATESTYLE` environment variable on the server or client.
- The formatting function `to_char` is also available as a more flexible way to format date/time output.
### Time Zones
- Recommend using date/time types that `contain both date and time` when using `time zones`.
- PostgreSQL assumes your `local time zone` for any type `containing only date or time`.
- PostgreSQL allows you to specify time zones in three different forms:
    - A full time zone name, for example `America/New_York`.
    - A time zone abbreviation, for example `PST`. 
    - In addition to the timezone names and abbreviations, PostgreSQL will accept POSIX-style time zone specifications.
- `2014-06-04 12:00 America/New_York` represents noon local time in `New York`, and is equivalent to `2014-06-04 12:00 EDT`.
- In Moscow MSK has meant `UTC+3` in some years and `UTC+4` in others.
- In all cases, timezone names and abbreviations are recognized `case-insensitively`.
- Neither timezone names nor abbreviations are hard-wired into the server -> obtained from `configuration files` stored under `.../share/timezone/` and `.../share/timezonesets/` of the installation directory.
- The `TimeZone` configuration parameter can be set in the file `postgresql.conf`.
- The SQL command `SET TIME ZONE` sets the time zone for the session.
### Interval Input
| Example                                            | Description                                                                     |
|----------------------------------------------------|---------------------------------------------------------------------------------|
| 1-2                                                | SQL standard format: 1 year 2 months                                            |
| 3 4:05:06                                          | SQL standard format: 3 days 4 hours 5 minutes 6 seconds                         |
| 1 year 2 months 3 days 4 hours 5 minutes 6 seconds | Traditional Postgres format: 1 year 2 months 3 days 4 hours 5 minutes 6 seconds |
| P1Y2M3DT4H5M6S                                     | ISO 8601 “format with designators”: same meaning as above                       |
| P0001-02-03T04:05:06                               | ISO 8601 “alternative format”: same meaning as above                            |
### Interval Output

| Style Specification | Year-Month Interval | Day-Time Interval              | Mixed Interval                                    |
|---------------------|---------------------|--------------------------------|---------------------------------------------------|
| sql_standard        | 1-2                 | 3 4:05:06                      | -1-2 +3 -4:05:06                                  |
| postgres            | 1 year 2 mons       | 3 days 04:05:06                | -1 year -2 mons +3 days -04:05:06                 |
| postgres_verbose    | @ 1 year 2 mons     | @ 3 days 4 hours 5 mins 6 secs | @ 1 year 2 mons -3 days 4 hours 5 mins 6 secs ago |
| iso_8601            | P1Y2M               | P3DT4H5M6S                     | P-1Y-2M3DT-4H-5M-6S                               |
## Boolean Type
| Name    | Storage Size | Description            |
|---------|--------------|------------------------|
| boolean | 1 byte       | state of true or false |
## Enumerated Types
### Declaration
- Created using the `CREATE TYPE` command
```
CREATE TYPE mood AS ENUM ('sad', 'ok', 'happy');
```
```
CREATE TYPE mood AS ENUM ('sad', 'ok', 'happy');
CREATE TABLE person (
    name text,
    current_mood mood
);
INSERT INTO person VALUES ('Moe', 'happy');
SELECT * FROM person WHERE current_mood = 'happy';
 name | current_mood
------+--------------
 Moe  | happy
```
### Ordering
- The ordering of the values in an enum type is the order in which the values were listed when the type was created. 
- All standard comparison operators and related aggregate functions are supported.
### Type Safety
- Separate and cannot be compared with other enumerated types.
- If you really need to do something like that, you can either add explicit casts to your query:
```
SELECT person.name, holidays.num_weeks FROM person, holidays
  WHERE person.current_mood::text = holidays.happiness::text;
 name | num_weeks
------+-----------
 Moe  |         4
```
### Implementation Details
- Case sensitive 
- White space in the labels is significant too.
- support for adding new values to an existing enum type, and for renaming values (see ALTER TYPE).
- Existing values cannot be removed from an enum type, nor can the sort ordering of such values be changed, short of dropping and re-creating the enum type.
- Occupies `four bytes` on disk.
- The length of an enum value's textual label is limited by the `NAMEDATALEN` setting compiled into PostgreSQL; in standard builds this means `at most 63 bytes`.