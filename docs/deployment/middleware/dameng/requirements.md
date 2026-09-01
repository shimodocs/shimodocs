# Dameng V8 Requirements

[← ShimoDocs Suite Deployment Documentation](../../README.md)

This document is intended to guide personnel who are implementing, maintaining, or integrating the Dameng database for the first time, step by step, to complete Dameng DM8 database initialization, MySQL compatibility mode configuration, service startup, and connection verification.

The examples in this document use the following plan:

| Item | Example Value |
| --- | --- |
| Database Installation Directory | `/opt/dmdbms` |
| Database Storage Directory | `/dmdata/data` |
| DATABASE_NAME | `DMTEST` |
| Instance Name | `DBSERVER` |
| Database Port | `5236` |
| Administrator Account | `SYSDBA` |
| Administrator PASSWORD | `<SYSDBA_PASSWORD>` |

> Note: `<SYSDBA_PASSWORD>` and `<SYSAUDITOR_PASSWORD>` are placeholders in the document. In actual operations, please replace them with real passwords that meet complexity requirements. PASSWORD complexity requirements.

## 1. Pre-Operation Confirmation

### 1. Confirm Dameng is Installed

Execute on the server:

```bash
ls /opt/dmdbms/bin
```

If you can see files like `dminit`, `dmserver`, `disql`, this indicates that the Dameng software has been installed.

You can also check the version:

```bash
/opt/dmdbms/bin/dmserver
```

The following content may appear in the output:

```text
dmserver V8
version: 03134284194-20240920-243574-20108
```

### 2. Confirm System User

Dameng usually runs the database using the `dmdba` user. Check if the user exists:

```bash
id dmdba
```

If it does not exist, it can be created by the user `root`:

```bash
groupadd dinstall
useradd -g dinstall -m -d /home/dmdba -s /bin/bash dmdba
passwd dmdba
```

### 3. Prepare the data directory

Use the following command to execute `root` user creation:

```bash
mkdir -p /dmdata/data
chown -R dmdba:dinstall /dmdata
chmod -R 775 /dmdata
```

The purpose of this step is to create a directory for storing database files and to grant permissions to the `dmdba` user.

## 2. Initialize the Database

Switch to the `dmdba` user to create:

```bash
su - dmdba
```

Execute the initialization command: 

```bash
/opt/dmdbms/bin/dminit \
  PATH=/dmdata/data \
  PAGE_SIZE=32 \
  EXTENT_SIZE=32 \
  CASE_SENSITIVE=0 \
  UNICODE_FLAG=1 \
  DB_NAME=DMTEST \
  INSTANCE_NAME=DBSERVER \
  PORT_NUM=5236 \
  SYSDBA_PWD=<SYSDBA_PASSWORD> \
  SYSAUDITOR_PWD=<SYSAUDITOR_PASSWORD>
```

If the initialization is successful, you will see output similar to this:

```text
create dm database success
```
After successful initialization, the database directory will be generated:

```text
/dmdata/data/DMTEST
```

The key configuration file among them is:

```text
/dmdata/data/DMTEST/dm.ini
```

## 3. Modify MySQL Compatibility Configuration

Edit the configuration file using the following method: `root` or `dmdba` created by the user:

```bash
vi /dmdata/data/DMTEST/dm.ini
```

Find and modify the following two configurations: 

```ini
COMPATIBLE_MODE = 4
ORDER_BY_NULLS_FLAG = 0
```

If these two configurations already exist in the file, you can directly modify the existing lines.

Do not add configurations with the same names at the end of the file, otherwise duplicate configurations may occur, causing the actual effective values to differ from the expected ones.

After completing the modifications, you can use the following command to check:

```bash
grep -Ein 'COMPATIBLE_MODE|ORDER_BY_NULLS_FLAG' /dmdata/data/DMTEST/dm.ini
```

Expected to see:

```text
COMPATIBLE_MODE = 4
ORDER_BY_NULLS_FLAG = 0
```

## 4. Register Database Service

Switch back to the `root` user to create:

```bash
exit
```

Register database service: 

```bash
/opt/dmdbms/script/root/dm_service_installer.sh \
  -t dmserver \
  -p DBSERVER \
  -dm_ini /dmdata/data/DMTEST/dm.ini
```

After successful registration, the service name is usually:

```text
DmServiceDBSERVER.service
```

Set up startup on boot and start the service: 

```bash
systemctl daemon-reload
systemctl enable DmServiceDBSERVER.service
systemctl start DmServiceDBSERVER.service
```

Check service status: 

```bash
systemctl status DmServiceDBSERVER.service --no-pager
```

If you see:

```text
Active: active (running)
```

Indicates that the database service has started. 

## 5. Verify if the database is available 

### 1. Check the port 

Execute:

```bash
ss -lntp | grep ':5236'
```

If you see `dmserver` listening on `5236`, it indicates that the database port is normal.

### 2. Local Login Test

Switch to the `dmdba` user to create:

```bash
su - dmdba
```

Log in to the database: 

```bash
/opt/dmdbms/bin/disql SYSDBA/<SYSDBA_PASSWORD>@127.0.0.1:5236
```

Execute after successful login:

```sql
select 1 as OK;
```

If return: 

```text
OK
-----------
1
```

Indicates that the database connection is normal. 

### 3. Verify if it is in MySQL compatibility mode

In `disql`: 

```sql
select para_name, para_value
from v$dm_ini
where para_name in (
  'COMPATIBLE_MODE',
  'ORDER_BY_NULLS_FLAG',
  'INSTANCE_NAME',
  'PORT_NUM'
);
```

Execution, Expected Result: 

```text
INSTANCE_NAME        DBSERVER
PORT_NUM             5236
COMPATIBLE_MODE      4
ORDER_BY_NULLS_FLAG  0
```

Among them:

```text
COMPATIBLE_MODE = 4
```

Indicates that the current database operating status has enabled MySQL compatibility mode. 

## Appendix 1, Detailed Description of Configuration Items 

### 1. `PATH` 

Example: 

```text
PATH=/dmdata/data
```

Meaning: 

`PATH` is the root directory of the database files. During the initialization process, Dameng will create the database directory under this directory.

If `DB_NAME=DMTEST`, the final directory is usually: 

```text
/dmdata/data/DMTEST
```

This directory will store data files, log files, control files, and the `dm.ini` configuration file.

Recommendations:

- It is recommended to place it on a data disk with sufficient capacity and stable performance in a production environment.
- It is not recommended to place it in a temporary directory, such as `/tmp`.
- Do not move the directory casually after initialization.

### 2. `DB_NAME`

Example:

```text
DB_NAME=DMTEST
```

Meaning: 

`DB_NAME` is the name DATABASE_NAME. It will affect the database directory name, log file name, etc.

For example, when `DB_NAME=DMTEST`, it usually generates:

```text
/dmdata/data/DMTEST
/dmdata/data/DMTEST/DMTEST01.log
/dmdata/data/DMTEST/DMTEST02.log
```

Suggestions:

- Use a single, clear DATABASE_NAME throughout the project.
- It is not recommended to change it after initialization.

### 3. `INSTANCE_NAME`

Example:

```text
INSTANCE_NAME=DBSERVER
```

Meaning: 

`INSTANCE_NAME` is the name of the database instance. It is usually used to generate the service name when registering the service.

For example: 

```text
INSTANCE_NAME=DBSERVER
```

The service name after registration is usually:

```text
DmServiceDBSERVER.service
```

Suggestions:

- For a single instance on a single machine, you can use `DBSERVER`.
- When deploying multiple instances on one machine, each instance name must be different.

### 4. `PORT_NUM`

Example:

```text
PORT_NUM=5236
```

Meaning: 

`PORT_NUM` is the database listening port. The application needs to access this port when connecting to the database.

The port entered on the program page must match this port:

```text
HOST:172.17.9.84
PORT:5236
```

Suggestions: 

- The default port of Dameng is usually `5236`. 
- If multiple Dameng instances exist on the same machine, ports cannot be duplicated. 
- After changing the port, the database service needs to be restarted. 

### 5. `PAGE_SIZE` 

Example: 

```text
PAGE_SIZE=32
```

Meaning:

`PAGE_SIZE` is the database page size, in KB. The database organizes data in units of pages when reading and writing data.

`PAGE_SIZE=32` indicates that each data page is 32KB.

Impact:

- It affects data storage, indexing, and I/O behavior.
- It is not recommended to modify it after initialization.
- If adjustment is needed, it usually requires reinitializing the database and migrating data.

Recommendation:

- If there is an SOP for this scenario, follow the SOP.
- Do not change it casually unless there are special requirements.

### 6. `EXTENT_SIZE`

Example:

```text
EXTENT_SIZE=32
```

Meaning:

`EXTENT_SIZE` is the cluster size, measured in pages. It can be understood as the basic unit of space allocation used by the database at a time.

If:

```text
PAGE_SIZE=32
EXTENT_SIZE=32
```

So a cluster is approximately:

```text
32KB * 32 = 1024KB
```

About 1MB. 

Impact:

- Will affect the granularity of data file space allocation.
- Not recommended to modify after initialization.

### 7. `CASE_SENSITIVE`

Example: 

```text
CASE_SENSITIVE=0
```

Meaning:

`CASE_SENSITIVE` indicates whether database object names are case-sensitive.

Common values:

```text
0:CASE_INSENSITIVE
1:CASE_SENSITIVE
```

For example, when case is not distinguished, the following two table names can be considered as the same object:

```text
user
USER
```

Impact: 

- Will affect the identification of table names, column names, and object names.
- For MySQL migration or MySQL-compatible scenarios, it is generally preferred to configure as `0`.
- It is not recommended to modify after initialization.

### 8. `UNICODE_FLAG`

Example: 

```text
UNICODE_FLAG=1
```

Meaning: 

`UNICODE_FLAG` is a character set configuration.

Common values: 

```text
0:GB18030
1:UTF-8
2:EUC-KR
```

`UNICODE_FLAG=1` indicates that the database uses the UTF-8 character set.

Recommendations:

- It is recommended to use UTF-8 for new systems.
- Better compatibility with Chinese, English, and multilingual characters.
- It is not recommended to modify after initialization.

### 9. `SYSDBA_PWD`

Example:

```text
SYSDBA_PWD=<SYSDBA_PASSWORD>
```

Meaning:

`SYSDBA_PWD` is the administrator account with PASSWORD `SYSDBA`.

`SYSDBA` is similar to a database super administrator, with advanced permissions.

Suggestions:

- Use a strong PASSWORD.
- Do not use simple passwords such as `SYSDBA`, `123456`, `password`.
- PASSWORDs are recommended to be at least 8 characters long and include letters and numbers.
- Do not write actual PASSWORDs in external documents.

### 10. `SYSAUDITOR_PWD`

Example:

```text
SYSAUDITOR_PWD=<SYSAUDITOR_PASSWORD>
```

Meaning: 

`SYSAUDITOR_PWD` is the audit administrator account of PASSWORD `SYSAUDITOR`.

`SYSAUDITOR` is mainly used for management functions related to auditing.

Recommendations:

- Use a PASSWORD different from `SYSDBA`.
- Use a strong PASSWORD that meets complexity requirements.

### 11. `COMPATIBLE_MODE`

Example:

```text
COMPATIBLE_MODE = 4
```

Meaning: 

`COMPATIBLE_MODE` is the compatibility mode configuration of the Dameng database, used to control which type of database the database conforms to in terms of syntax, functions, and certain behaviors. SQL syntax, functions, and certain behaviors.

Common value meanings:

```text
0:DEFAULT_MODE
1:SQL92
2:Oracle
3:MS SQL Server
4:MySQL
5:DM6
6:Teradata
7:PostgreSQL
8:DB2
```

This text is configured as: 

```text
COMPATIBLE_MODE = 4
```

Indicates enabling MySQL compatibility mode.

Function:

- Improves the compatibility of MySQL SQL syntax with Dameng.
- Reduces migration or adaptation efforts from MySQL to Dameng.

Note:

- This configuration does not mean Dameng supports the MySQL protocol.
- Programs still need to internally use the Dameng driver; if there is no driver configuration option on the page, users do not need to fill it in separately.
- A database service restart is required after modification.
- Whether it ultimately takes effect should be determined by the `v$dm_ini` query results.

### 12. `ORDER_BY_NULLS_FLAG`

Example:

```text
ORDER_BY_NULLS_FLAG = 0
```

Meaning:

`ORDER_BY_NULLS_FLAG` is used to control whether NULL values appear at the beginning or the end when sorting with NULL. `ORDER BY`.

Reason:

Different databases may have different default behaviors for sorting NULLs. When migrating an application from MySQL to Dameng, if the sort results depend on the position of NULLs, this parameter may affect the order of query results.

This article is configured as:

```text
ORDER_BY_NULLS_FLAG = 0
```

The purpose is to make the sorting behavior closer to MySQL usage habits.

Note:

- After modification, the database service needs to be restarted.
- If the business SQL has explicitly specified `NULLS FIRST` or `NULLS LAST`, the behavior specified in the SQL should take precedence.

## Appendix 2, Common Issues

### 1. Why can't I use the MySQL client even after setting MySQL compatible mode?

Because MySQL compatible mode only affects SQL syntax and certain database behaviors; it does not change Dameng's network protocol.

When applications or tools connect to Dameng, they still need to use the Dameng driver:

```text
dm.jdbc.driver.DmDriver
jdbc:dm://<host>:5236
```

Cannot use: 

```text
com.mysql.cj.jdbc.Driver
jdbc:mysql://<host>:5236
```

### 2. How to confirm that the configuration is indeed effective?

Do not just look at the `dm.ini` file; it is recommended to log in to the database to check the runtime status:

```sql
select para_name, para_value
from v$dm_ini
where para_name in ('COMPATIBLE_MODE', 'ORDER_BY_NULLS_FLAG');
```

The running status is considered effective only when the following results are seen:

```text
COMPATIBLE_MODE      4
ORDER_BY_NULLS_FLAG  0
```

### 3. Why didn't the changes take effect `dm.ini`?

Common reasons:

- The database service was not restarted after the changes.
- There are duplicate configuration items in the file.
- The modified file is not the one currently being used by the `dm.ini` instance.

You can check which configuration file the current instance is using through the service start command:

```bash
systemctl status DmServiceDBSERVER.service --no-pager
```

You will usually see something like the following in the output:

```text
dmserver path=/dmdata/data/DMTEST/dm.ini -noconsole
```

### 4. If I... what should I do if a complexity error occurs during PASSWORD initialization?

This indicates that the PASSWORD is too simple. Please switch to a more complex PASSWORD distribution package, for example:

```text
AT_LEAST 8 POSITION
CONTAINS_LETTERS_AND_NUMBERS
AVOID_USING_THE_ACCOUNT_NAME_ITSELF
```

### 5. Can these parameters be changed later?

No.

Initialization parameters are generally not recommended to be changed later, such as:
- 'PAGE_SIZE'
- 'EXTENT_SIZE'
- 'CASE_SENSITIVE'
- 'UNICODE_FLAG'
- 'DB_NAME'
- 'INSTANCE_NAME'

If these parameters are configured incorrectly, it is usually recommended to reinitialize the database and migrate the data again.

'dm.ini' parameters can be adjusted later, such as:
- 'COMPATIBLE_MODE'
- 'ORDER_BY_NULLS_FLAG'
- 'PORT_NUM'

However, after modification, it usually requires restarting the database service.

## Appendix 3: Final Checklist

- The '/dmdata/data' data directory has been created.
- The data catalog host is 'dmdba:dinstall'.
- The database has been successfully initialized.
- '/dmdata/data/DMTEST/dm.ini' exists.
- `COMPATIBLE_MODE = 4`.
- `ORDER_BY_NULLS_FLAG = 0`.
- The database service `DmServiceDBSERVER.service` is `active`.
- Port `5236` is being listened to.
- `SYSDBA` can log in to the database.
- In `v$dm_ini`, the runtime value `COMPATIBLE_MODE` is `4`.
