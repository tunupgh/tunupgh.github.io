---
layout: post
title: MSSQL auto populate creation time
date: 2015-10-18 16:06
comments: true
categories:
- misc
- mssql
---
It's good practice to add a creation time to every record in your database which marks the time and date when the record was created. This is ridiculously easy since it's a feature of MSSQL.

Simply use `DATETIME2()` type and `GETDATE()` function. 

```sql
CREATE TABLE tblExample
       (id           INT IDENTITY PRIMARY KEY
       ,createTime   DATETIME2(0) DEFAULT GETDATE()
       )
```

Now, automatically, every time a record is created the creation time is automatically populated in the database.

There isn't such a function for modified time. That would have to be done programmatically in whatever program is used to populate the database.

