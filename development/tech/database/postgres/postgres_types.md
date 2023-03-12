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