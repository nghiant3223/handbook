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
