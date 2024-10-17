Most database systems store data records, which can be represented as a 2-D array with row and column. The intersection of a row and a column is a field - a single value of some type.

A collection of values that belong **logically** to the same record constitutes a row.

Intuitively we can store the data as distinct rows, and this is the case for most mainstream databases - MySQL and Postgres. The alternative is to store data as contiguous columns.

## Row-Oriented Layout
Storing data as distinct row improving look up efficiency due to spatial locality. Fields that logically belonging to the same record can be read in one operation. For example, user's email, username are often retrieved and used together.

When creating a record, we write them together as well. For example, when a user creates a new account, we can write all fields together in one operation.

As we optimised for row spatial locality, it means we sacrifice efficiency when we want to fetch individual fields for multiple user records. For example, when retrieving the phone number for multiple users, we see inefficiency as we page in other data as well.

### Column-Oriented Layout
An alternative is to store columns together. Here, the column data is stored contiguously on disk. Here we improve on spatial locality when accessing values of a column. This is useful for use cases such as historical stock price / quote query.

From a logical perspective, the data can still be represented as a row oriented table
```
ID | price | Symbol
1  | 11    | DOW
2  | 32    | DOW
3  | 20    | S&P
```

But internally, the columns are stored contiguously
```
symbol: 1:DOW, 2:DOW, 3:S&P
price: 1:11, 2:32, 3:20
```
In order to reconstruct the logical row, we see that each field has an additional ID to associate it with other fields in other columns. This introduces duplication and increases storage size. Some column stores use implicit identifiers and offset to map it back to related value.

### [Wide Column Stores](Wide%20Column%20Stores.md)
Column wide stores are a type of NoSQL database.

They can store data in a schema-less, flexible format organised around column.

The representative wide column stores are BigTable, or HBase. Here, data is represented as a multidimensional map, where columns are grouped into column families, and inside each column family, data is stored row-wise. This layout is best for storing data retrieved by a key, or a sequence of keys.



