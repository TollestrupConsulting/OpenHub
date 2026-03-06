# Execute permision on DB schema

More info here: [Grant execute permission for a user on all stored procedures in database?](https://stackoverflow.com/questions/5428406/grant-execute-permission-for-a-user-on-all-stored-procedures-in-database)
and her: [http://www.patrickkeisler.com/2012/10/grant-execute-permission-on-all-stored.html](https://www.patrickkeisler.com/2012/10/grant-execute-permission-on-all-stored.html)

First let’s create the schema:
```sql
IF EXISTS (SELECT name FROM sys.schemas WHERE name = N'mySchema')
BEGIN
  PRINT 'Dropping the mySchema schema'
  DROP SCHEMA [mySchema]
END
GO

PRINT ' Creating the mySchema schema'
GO

CREATE SCHEMA [mySchema] AUTHORIZATION [dbo]
GO
```

Secondly let’s create the database role:
```sql
IF  EXISTS (SELECT * FROM sys.database_principals 
WHERE name = N'db_execproc' AND type = 'R')
DROP ROLE [db_execproc]
GO

--Create a database role....
CREATE ROLE [db_execproc] AUTHORIZATION [dbo]
GO
```

Thirdly let’s grant permission to the database role:
```sql
--...with EXECUTE permission at the schema level...
GRANT EXECUTE ON SCHEMA::mySchema TO db_execproc;
GO
```

Lastly let’s assign users to the database role:
```sql
IF  EXISTS (SELECT * FROM sys.database_principals WHERE name = N'asp_net')
DROP USER asp_net
GO

--...add a user e.g. for the NETWORK SERVICE login that asp.net uses
CREATE USER asp_net 
FOR LOGIN [NT AUTHORITY\NETWORK SERVICE] 
WITH DEFAULT_SCHEMA=[mySchema]
GO

--...and add them to the roles you need
EXEC sp_addrolemember N'db_execproc', 'asp_net';
EXEC sp_addrolemember N'db_datareader', 'asp_net';
EXEC sp_addrolemember N'db_datawriter', 'asp_net';
GO
````
