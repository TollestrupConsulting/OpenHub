# Test Database Connectivity

Often it is needed to test a connection to a SQL Server database. This small PowerShell function can do the trick if you con’t have installed SQL Server Management Studio on your client computer.

The TestDatabaseConnectivity.ps1 script file contains the PowerShell function Test-SQLConnection.

Usage:
```
Test-SQLConnection "Data Source=localhost;database=myDB;User ID=myUser;Password=myPassword;"
```

Since the function use a connection-string as input parameter, the function can be used to test both SQL Authentication users and Active Directory integrated users.
