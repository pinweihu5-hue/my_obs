

### `CHAR(n)`, `VARCHAR(n)`, and `TEXT`
- unlike many other db, these 3 data type don’t have perf difference!
- `VARCHAR(n)` just let the db help you validate the length of n
- `text`: variable with unlimited length
- `VARCHAR`: variable and limited length by setting `VARCHAR(n)`
- `CHAR`: fixed length by setting `CHAR(n)`
- `VARCHAR` if using this without the length specifier, it will be same as using `TEXT`