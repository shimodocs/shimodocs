# Dameng V8 要求

[← ShimoDocs Suite 部署文档](../../README.md)

本文档旨在指导首次实施、维护或集成 Dameng 数据库的人员，逐步完成 Dameng DM8 数据库初始化、 MySQL 兼容模式配置、服务启动和连接验证步骤。

本文档中的示例使用以下规划：

| 项目 | 示例值 |
| --- | --- |
| 数据库安装目录 | `/opt/dmdbms` |
| 数据库存储目录 | `/dmdata/data` |
| DATABASE_NAME | `DMTEST` |
| 实例名称 | `DBSERVER` |
| 数据库端口 | `5236` |
| 管理员账户 | `SYSDBA` |
| 管理员 PASSWORD | `<SYSDBA_PASSWORD>` |

> 注意： `<SYSDBA_PASSWORD>` 以及 `<SYSAUDITOR_PASSWORD>` 在文档中是占位符。在实际操作中，请将其替换为符合复杂性要求的真实密码。 PASSWORD 复杂性要求。

## 1. 运营前确认

### 1. 确认 Dameng 已经安装

在服务器上执行：

```bash
ls /opt/dmdbms/bin
```

如果你能看到类似 `dminit`, `dmserver`, `disql`的文件，这表明 Dameng 软件已被安装。

你也可以检查版本：

```bash
/opt/dmdbms/bin/dmserver
```

输出中可能会出现如下内容：

```text
dmserver V8
version: 03134284194-20240920-243574-20108
```

### 2. 确认系统用户

Dameng 通常使用 `dmdba` 用户运行数据库。检查用户是否存在：

```bash
id dmdba
```

如果不存在，可以由 `root` 用户创建：

```bash
groupadd dinstall
useradd -g dinstall -m -d /home/dmdba -s /bin/bash dmdba
passwd dmdba
```

### 3. 准备数据目录

使用以下命令执行 `root` 用户创建：

```bash
mkdir -p /dmdata/data
chown -R dmdba:dinstall /dmdata
chmod -R 775 /dmdata
```

此步骤的目的是创建一个用于存储数据库文件的目录，并授予权限给 `dmdba` 用户。

## 2. 初始化数据库

切换到 `dmdba` 用户创建：

```bash
su - dmdba
```

执行初始化命令： 

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

如果初始化成功，您将看到类似的输出： 

```text
create dm database success
```
初始化成功后，将生成数据库目录： 

```text
/dmdata/data/DMTEST
```

其中的关键配置文件是：

```text
/dmdata/data/DMTEST/dm.ini
```

## 3. 修改 MySQL 兼容性配置

使用以下方式编辑配置文件 `root` 或 `dmdba` 用户创建：

```bash
vi /dmdata/data/DMTEST/dm.ini
```

查找并修改以下两个配置： 

```ini
COMPATIBLE_MODE = 4
ORDER_BY_NULLS_FLAG = 0
```

如果文件中已经有这两个配置，可以直接修改现有行。

不要在文件末尾添加与已有名称相同的配置，否则可能出现重复配置，导致实际生效值与预期不同。

修改完成后，可以使用以下命令检查：

```bash
grep -Ein 'COMPATIBLE_MODE|ORDER_BY_NULLS_FLAG' /dmdata/data/DMTEST/dm.ini
```

预期看到：

```text
COMPATIBLE_MODE = 4
ORDER_BY_NULLS_FLAG = 0
```

## 4. 注册数据库服务

切换回 `root` 用户创建：

```bash
exit
```

注册数据库服务： 

```bash
/opt/dmdbms/script/root/dm_service_installer.sh \
  -t dmserver \
  -p DBSERVER \
  -dm_ini /dmdata/data/DMTEST/dm.ini
```

注册成功后，服务名称通常为：

```text
DmServiceDBSERVER.service
```

设置开机启动并启动服务： 

```bash
systemctl daemon-reload
systemctl enable DmServiceDBSERVER.service
systemctl start DmServiceDBSERVER.service
```

检查服务状态： 

```bash
systemctl status DmServiceDBSERVER.service --no-pager
```

如果看到： 

```text
Active: active (running)
```

表示数据库服务已启动。 

## 5. 验证数据库是否可用 

### 1. 检查端口 

执行： 

```bash
ss -lntp | grep ':5236'
```

如果看到 `dmserver` 正在监听 `5236`，表示数据库端口正常。

### 2. 本地登录测试

切换到 `dmdba` 用户创建：

```bash
su - dmdba
```

登录数据库： 

```bash
/opt/dmdbms/bin/disql SYSDBA/<SYSDBA_PASSWORD>@127.0.0.1:5236
```

登录成功后执行：

```sql
select 1 as OK;
```

如果返回： 

```text
OK
-----------
1
```

表示数据库连接正常。 

### 3. 验证是否处于 MySQL 兼容模式

在 `disql`: 

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

执行，预期结果： 

```text
INSTANCE_NAME        DBSERVER
PORT_NUM             5236
COMPATIBLE_MODE      4
ORDER_BY_NULLS_FLAG  0
```

其中：

```text
COMPATIBLE_MODE = 4
```

表示当前数据库运行状态已开启 MySQL 兼容模式。 



## 附录1，配置项详细说明 

### 1. `PATH` 

示例： 

```text
PATH=/dmdata/data
```

含义： 

`PATH` 是数据库文件的根目录。在初始化过程中， Dameng 将在该目录下创建数据库目录。

如果 `DB_NAME=DMTEST`，最终目录通常为： 

```text
/dmdata/data/DMTEST
```

该目录将存储数据文件、日志文件、控制文件以及 `dm.ini` 配置文件。

建议：

- 建议在生产环境中将其放置在容量充足且性能稳定的数据盘上。
- 不建议将其放置在临时目录中，例如 `/tmp`.
- 初始化后不要随意移动目录。

### 2. `DB_NAME`

示例：

```text
DB_NAME=DMTEST
```

含义： 

`DB_NAME` 是的名称 DATABASE_NAME。它将影响数据库目录名、日志文件名等。 

例如，当 `DB_NAME=DMTEST`，它通常会生成： 

```text
/dmdata/data/DMTEST
/dmdata/data/DMTEST/DMTEST01.log
/dmdata/data/DMTEST/DMTEST02.log
```

建议：

- 在整个项目中使用单一明确的 DATABASE_NAME 。
- 初始化后不建议更改它。

### 3. `INSTANCE_NAME`

示例：

```text
INSTANCE_NAME=DBSERVER
```

含义： 

`INSTANCE_NAME` 是数据库实例名称。通常在注册服务时用于生成服务名称。

例如： 

```text
INSTANCE_NAME=DBSERVER
```

注册后的服务名称通常是：

```text
DmServiceDBSERVER.service
```

建议： 

- 对于单台机器上的单个实例，您可以使用 `DBSERVER`.
- 当在一台机器上部署多个实例时，每个实例名称必须不同。

### 4. `PORT_NUM`

示例： 

```text
PORT_NUM=5236
```

含义： 

`PORT_NUM` 是数据库监听端口。应用程序在连接数据库时需要访问此端口。 

程序页面上输入的端口必须与此端口一致： 

```text
HOST:172.17.9.84
PORT:5236
```

建议： 

- 的默认端口通常是 Dameng  `5236`. 
- 如果同一台机器上存在多个 Dameng 实例，端口不能重复。 
- 更改端口后，需要重启数据库服务。 

### 5. `PAGE_SIZE` 

示例： 

```text
PAGE_SIZE=32
```

含义： 

`PAGE_SIZE` 是数据库页大小，单位为KB。数据库在读写数据时以页为单位组织数据。 

`PAGE_SIZE=32` 表示每个数据页为32KB。 

影响： 

- 它会影响数据存储、索引和IO行为。 
- 初始化后不建议修改。 
- 如果需要调整，通常需要重新初始化数据库并迁移数据。 

建议： 

- 如果有一个 SOP 用于该场景，请根据 SOP. 
- 当没有特殊要求时，不要随意更改它。 

### 6. `EXTENT_SIZE` 

示例： 

```text
EXTENT_SIZE=32
```

含义： 

`EXTENT_SIZE` 是集群大小，以页为单位。可以理解为数据库一次使用的基本空间分配单位。

如果： 

```text
PAGE_SIZE=32
EXTENT_SIZE=32
```

那么一个集群大约是： 

```text
32KB * 32 = 1024KB
```

大约1MB。 

影响： 

- 将影响数据文件空间分配的粒度。 
- 初始化后不建议修改。 

### 7. `CASE_SENSITIVE` 

示例： 

```text
CASE_SENSITIVE=0
```

含义： 

`CASE_SENSITIVE` 表示数据库对象名称是否区分大小写。

常见值： 

```text
0:CASE_INSENSITIVE
1:CASE_SENSITIVE
```

例如，当不区分大小写时，以下两个表名可以被视为同一个对象：

```text
user
USER
```

影响： 

- 将影响表名、字段名和对象名的识别。 
- 对于 MySQL 迁移或 MySQL-兼容场景，通常优选配置为 `0`. 
- 初始化后不建议修改。 

### 8. `UNICODE_FLAG` 

示例： 

```text
UNICODE_FLAG=1
```

含义： 

`UNICODE_FLAG` 是字符集配置。

常见值： 

```text
0:GB18030
1:UTF-8
2:EUC-KR
```

`UNICODE_FLAG=1` 表示数据库使用 UTF-8 字符集。

建议：

- 建议使用 UTF-8 用于新系统。
- 对中文、英文及多语言字符具有更好的兼容性。
- 初始化后不建议修改。

### 9. `SYSDBA_PWD`

示例：

```text
SYSDBA_PWD=<SYSDBA_PASSWORD>
```

含义： 

`SYSDBA_PWD` 是 PASSWORD 为 `SYSDBA` 管理员账户。

`SYSDBA` 类似于数据库超级管理员，具有高级权限。

建议： 

- 使用强 PASSWORD.
- 不要使用简单的密码，如 `SYSDBA`, `123456`, `password`.
- PASSWORD 建议长度至少为8个字符，并包含字母和数字。
- 不要将实际 PASSWORD 写入外部文档。

### 10. `SYSAUDITOR_PWD`

示例： 

```text
SYSAUDITOR_PWD=<SYSAUDITOR_PASSWORD>
```

含义： 

`SYSAUDITOR_PWD` 是 PASSWORD 的 `SYSAUDITOR` 审计管理员账户。

`SYSAUDITOR` 主要用于与审计相关的管理功能。

建议：

- 使用一个 PASSWORD 不同于 `SYSDBA`.
- 使用强 PASSWORD 满足复杂性要求的

### 11. `COMPATIBLE_MODE`

示例：

```text
COMPATIBLE_MODE = 4
```

含义： 

`COMPATIBLE_MODE` 是 Dameng 数据库的兼容模式配置，用于控制数据库在语法、函数和某些行为上与哪种类型的数据库保持一致。 SQL 语法、函数和某些行为。

常见值含义： 

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

此文本配置为： 

```text
COMPATIBLE_MODE = 4
```

表示启用 MySQL 兼容模式。 

功能： 

- 提高 MySQL SQL 语法的兼容性 Dameng. 
- 减少从 MySQL 迁移或适应到 Dameng. 

注意： 

- 此配置并不意味着 Dameng 支持 MySQL 协议。 
- 程序仍然需要内部使用 Dameng 驱动；如果页面上没有驱动配置选项，用户无需单独填写。 
- 修改后需要重启数据库服务。 
- 最终是否生效应以 `v$dm_ini` 查询结果为准。 

### 12. `ORDER_BY_NULLS_FLAG` 

示例： 

```text
ORDER_BY_NULLS_FLAG = 0
```

含义： 

`ORDER_BY_NULLS_FLAG` 用于控制在使用 NULL 排序时，NULL值是出现在开头还是结尾。 `ORDER BY`. 

原因： 

不同数据库对排序NULL可能有不同的默认行为。当将应用程序从 MySQL 迁移到 Dameng时，如果排序结果依赖于NULL的位置，该参数可能会影响查询结果的顺序。 

本文配置为： 

```text
ORDER_BY_NULLS_FLAG = 0
```

目的是使排序行为更接近 MySQL 的使用习惯。

注意:

- 修改后需要重启数据库服务。
- 如果业务 SQL 已经明确指定 `NULLS FIRST` 或 `NULLS LAST`，则 SQL 中指定的行为应优先。

## 附录2，常见问题

### 1. 为什么我无法使用 MySQL 客户即使在设置后 MySQL 兼容模式？

因为 MySQL 兼容模式只影响 SQL 语法和某些数据库行为，它不会改变 Dameng的网络协议。

当应用程序或工具连接到 Dameng，仍然需要使用 Dameng 驱动程序：

```text
dm.jdbc.driver.DmDriver
jdbc:dm://<host>:5236
```

不能使用： 

```text
com.mysql.cj.jdbc.Driver
jdbc:mysql://<host>:5236
```

### 2. 如何确认配置确实生效？

不要只是查看 `dm.ini` 文件；建议登录数据库检查运行时状态：

```sql
select para_name, para_value
from v$dm_ini
where para_name in ('COMPATIBLE_MODE', 'ORDER_BY_NULLS_FLAG');
```

只有看到以下结果时，运行状态才算生效： 

```text
COMPATIBLE_MODE      4
ORDER_BY_NULLS_FLAG  0
```

### 3. 为什么修改后没有生效 `dm.ini`?

常见原因：

- 修改后数据库服务未重新启动。
- 文件中存在重复的配置项。
- 修改的文件不是 `dm.ini` 实例当前正在使用的文件。

您可以通过服务启动命令确认当前实例正在使用哪个配置文件：

```bash
systemctl status DmServiceDBSERVER.service --no-pager
```

你通常会在输出中看到类似以下的内容：

```text
dmserver path=/dmdata/data/DMTEST/dm.ini -noconsole
```

### 4. 如果我...我应该怎么办 PASSWORD 初始化期间发生复杂性错误？

表示该 PASSWORD 太简单了。请改为更复杂的 PASSWORD的分发包，例如：

```text
AT_LEAST 8 POSITION
CONTAINS_LETTERS_AND_NUMBERS
AVOID_USING_THE_ACCOUNT_NAME_ITSELF
```

### 5。这些参数以后可以更改吗？ 

不能。 

初始化参数一般不建议以后更改，例如： 
- 'PAGE_SIZE'
- 'EXTENT_SIZE'
- 'CASE_SENSITIVE'
- 'UNICODE_FLAG'
- 'DB_NAME'
- 'INSTANCE_NAME'

如果这些参数配置错误，通常建议重新初始化数据库并重新迁移数据。 

'dm.ini' 参数可以在以后调整，例如： 

- 'COMPATIBLE_MODE'
- 'ORDER_BY_NULLS_FLAG'
- 'PORT_NUM'

但是，修改后，一般需要重启数据库服务。 

## 附录 3：最终检查表 


- 已创建 '/dmdata/data' 数据目录。 
- 数据目录主机为 'dmdba:dinstall'。 
- 数据库已成功初始化。 
- '/dmdata/data/DMTEST/dm.ini' 存在。 
- `COMPATIBLE_MODE = 4`. 
- `ORDER_BY_NULLS_FLAG = 0`. 
- 数据库服务 `DmServiceDBSERVER.service` 正在 `active`. 
- 端口 `5236` 被监听。 
- `SYSDBA` 可以登录数据库。 
- 在 `v$dm_ini`中，运行时值为 `COMPATIBLE_MODE` 正在 `4`.
