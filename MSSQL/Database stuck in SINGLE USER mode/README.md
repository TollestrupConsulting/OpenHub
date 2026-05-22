# Database stuck in SINGLE USER mode

Sometimes a database is stuck in SINGLE USER mode after a restore process. To fix this issue we normally just change it to MULTI USER mode:

```sql
ALTER DATABASE dbname
SET MULTI_USER;
GO
```

But often another process or user is already logged in to the database and we can't change the user mode since in SINGLE USER mode, it can only handle a single user ;-)

## First kill user connections, the change user mode

The trick is to firstly kill all existing connections to the database, and then quickly change the user mode (before other connect to database again).

> [!NOTE]
> The following script only works on SQL Server 2012 and later.

```sql
-- Kill user connections
USE [master];

DECLARE @kill VARCHAR(8000) = '';
SELECT @kill = @kill + 'KILL ' + CONVERT(VARCHAR(5), session_id) + ';'
FROM sys.dm_exec_sessions
WHERE database_id = db_id('dbname')

EXEC(@kill)

-- Change user mode
ALTER DATABASE dbname
SET MULTI_USER;
GO
```