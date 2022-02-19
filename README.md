# Copy Database from one Azure subscription to Another Azure Subscription

--Step# 1
--Create login and user in the master database of the source server.

```
CREATE LOGIN migrationuser WITH PASSWORD = 'XXXXXXXXXX'
GO
CREATE USER migrationuser FOR LOGIN migrationuser WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE dbmanager ADD MEMBER migrationuser;
GO
```

![image](https://user-images.githubusercontent.com/6815990/154799616-f7edaec4-7c20-4e87-a96c-1033d45e0d70.png)

--Step# 2
--Create the user in the source database and grant dbowner permission to the database.

```
CREATE USER migrationuser FOR LOGIN migrationuser WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE db_owner ADD MEMBER migrationuser;
GO
```

![image](https://user-images.githubusercontent.com/6815990/154799720-4205aeb0-0f66-4196-9a72-18a17f19774d.png)

--Step# 3
--Capture the SID of the user "loginname" from master database

```
SELECT [sid] FROM sysusers WHERE [name] = 'migrationuser';
```

Result - 0x0106000000000064000000000000000070189A02C0E2E446B0034C6FD13AC46F

--Step# 4
--Connect to Destination server.
--Create login and user in the master database, same as of the source server.

```

CREATE LOGIN migrationuser WITH PASSWORD = 'XXXXXXXX', SID = 0x0106000000000064000000000000000070189A02C0E2E446B0034C6FD13AC46F;
GO
CREATE USER migrationuser FOR LOGIN migrationuser WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE dbmanager ADD MEMBER migrationuser;
GO
```

--Step# 5
--Execute the copy of database script from the destination server using the credentials created
```
CREATE DATABASE source
AS COPY OF [copy-sql-source].[source];
```

![image](https://user-images.githubusercontent.com/6815990/154800685-97244788-91ff-4d6a-9126-1f491d6c7d56.png)
