# Dameng V8 요구 사항

[← ShimoDocs Suite 배포 문서](../../README.md)

이 문서는 데이터베이스를 처음 구현, 유지 관리 또는 통합하는 담당자가 완료할 수 있도록 안내하기 위한 것입니다. Dameng  Dameng DM8 데이터베이스 초기화, MySQL 호환성 모드 구성, 서비스 시작 및 연결 확인 단계별.

이 문서의 예제에서는 다음과 같은 계획을 사용합니다:

| 항목 | 예제 값 |
| --- | --- |
| 데이터베이스 설치 디렉토리 | `/opt/dmdbms` |
| 데이터베이스 저장 디렉토리 | `/dmdata/data` |
| DATABASE_NAME | `DMTEST` |
| 인스턴스 이름 | `DBSERVER` |
| 데이터베이스 포트 | `5236` |
| 관리자 계정 | `SYSDBA` |
| 관리자 PASSWORD | `<SYSDBA_PASSWORD>` |

> 참고: `<SYSDBA_PASSWORD>` 그리고 `<SYSAUDITOR_PASSWORD>` 문서의 내용은 자리 표시자입니다. 실제 작업 시에는 복잡성 요구 사항을 충족하는 실제 비밀번호로 교체하십시오. PASSWORD 복잡성 요구 사항을 충족하는 실제 비밀번호로 교체하십시오.

## 1. 사전 작업 확인

### 1. 다음을 확인합니다 Dameng 이미 설치되어 있는 경우

서버에서 실행:

```bash
ls /opt/dmdbms/bin
```

파일과 같은 것을 볼 수 있다면 `dminit`, `dmserver`, `disql`, 이는 Dameng 소프트웨어가 이미 설치되었습니다.

버전을 확인할 수도 있습니다:

```bash
/opt/dmdbms/bin/dmserver
```

이와 같은 내용이 출력에 나타날 수 있습니다:

```text
dmserver V8
version: 03134284194-20240920-243574-20108
```

### 2. 시스템 사용자 확인

Dameng 보통 데이터베이스를 사용자로 실행합니다. `dmdba` 사용자가 존재하는지 확인합니다:

```bash
id dmdba
```

존재하지 않으면, 사용자가 생성할 수 있습니다: `root` 사용자:

```bash
groupadd dinstall
useradd -g dinstall -m -d /home/dmdba -s /bin/bash dmdba
passwd dmdba
```

### 3. 데이터 디렉토리 준비

실행 사용: `root` 사용자:

```bash
mkdir -p /dmdata/data
chown -R dmdba:dinstall /dmdata
chmod -R 775 /dmdata
```

이 단계의 목적은 데이터베이스 파일을 저장할 디렉토리를 생성하고 사용자에게 권한을 부여하는 것입니다. `dmdba` 사용자.

## 2. 데이터베이스 초기화

으로 전환: `dmdba` 사용자:

```bash
su - dmdba
```

초기화 명령 실행: 

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

초기화가 성공하면 다음과 유사한 출력이 나타납니다: 

```text
create dm database success
```
성공적으로 초기화한 후, 데이터베이스 디렉토리가 생성됩니다: 

```text
/dmdata/data/DMTEST
```

그 중 핵심 구성 파일은 다음과 같습니다:

```text
/dmdata/data/DMTEST/dm.ini
```

## 3. 수정 MySQL 호환성 구성

구성 파일을 사용하여 편집합니다 `root` 또는 `dmdba` 사용자:

```bash
vi /dmdata/data/DMTEST/dm.ini
```

다음 두 가지 구성을 찾아 수정합니다: 

```ini
COMPATIBLE_MODE = 4
ORDER_BY_NULLS_FLAG = 0
```

파일에 이미 이 두 구성이 있는 경우, 기존 줄을 직접 수정할 수 있습니다.

파일 끝에 같은 이름의 다른 구성을 추가하지 마십시오. 그렇지 않으면 구성 중복이 발생하여 실제 유효 값이 예상 값과 다를 수 있습니다.

수정이 완료되면 다음 명령어를 사용하여 확인할 수 있습니다:

```bash
grep -Ein 'COMPATIBLE_MODE|ORDER_BY_NULLS_FLAG' /dmdata/data/DMTEST/dm.ini
```

다음과 같이 보일 것으로 예상됩니다:

```text
COMPATIBLE_MODE = 4
ORDER_BY_NULLS_FLAG = 0
```

## 4. 데이터베이스 서비스 등록

으로 다시 전환 `root` 사용자:

```bash
exit
```

데이터베이스 서비스 등록: 

```bash
/opt/dmdbms/script/root/dm_service_installer.sh \
  -t dmserver \
  -p DBSERVER \
  -dm_ini /dmdata/data/DMTEST/dm.ini
```

등록 성공 후 서비스 이름은 일반적으로 다음과 같습니다:

```text
DmServiceDBSERVER.service
```

부팅 시 시작하도록 설정하고 서비스를 시작합니다: 

```bash
systemctl daemon-reload
systemctl enable DmServiceDBSERVER.service
systemctl start DmServiceDBSERVER.service
```

서비스 상태 확인: 

```bash
systemctl status DmServiceDBSERVER.service --no-pager
```

다음을 보면: 

```text
Active: active (running)
```

데이터베이스 서비스가 시작되었음을 나타냅니다. 

## 5. 데이터베이스 사용 가능 여부 확인 

### 1. 포트 확인 

실행: 

```bash
ss -lntp | grep ':5236'
```

다음을 보면 `dmserver` 에서 대기 중 `5236`, 데이터베이스 포트가 정상임을 나타냅니다.

### 2. 로컬 로그인 테스트

으로 전환: `dmdba` 사용자:

```bash
su - dmdba
```

데이터베이스에 로그인: 

```bash
/opt/dmdbms/bin/disql SYSDBA/<SYSDBA_PASSWORD>@127.0.0.1:5236
```

로그인 성공 후 실행:

```sql
select 1 as OK;
```

다음과 같이 반환되면: 

```text
OK
-----------
1
```

데이터베이스 연결이 정상임을 나타냅니다. 

### 3. 호환 모드 여부 확인 MySQL 호환 모드

에서 실행 `disql`: 

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

예상 결과: 

```text
INSTANCE_NAME        DBSERVER
PORT_NUM             5236
COMPATIBLE_MODE      4
ORDER_BY_NULLS_FLAG  0
```

그 중:

```text
COMPATIBLE_MODE = 4
```

현재 데이터베이스 런타임 상태에서 MySQL 호환 모드가 활성화되었음을 나타냅니다. 



## 부록 1, 구성 항목 상세 설명 

### 1. `PATH` 

예시: 

```text
PATH=/dmdata/data
```

의미: 

`PATH` 데이터베이스 파일의 루트 디렉터리입니다. 초기화 중에, Dameng 이 디렉터리 아래에 데이터베이스 디렉터리를 생성합니다.

설치 중 `DB_NAME=DMTEST`, 최종 디렉터리는 일반적으로: 

```text
/dmdata/data/DMTEST
```

이 디렉터리는 데이터 파일, 로그 파일, 제어 파일 및 `dm.ini` 설정 파일을 저장합니다.

권장 사항:

- 운영 환경에서는 충분한 용량과 안정적인 성능을 가진 데이터 디스크에 배치하는 것이 권장됩니다.
- 임시 디렉토리와 같은 곳에 두는 것은 권장되지 않습니다. `/tmp`.
- 초기화 후에 디렉토리를 임의로 이동하지 마십시오.

### 2. `DB_NAME`

예:

```text
DB_NAME=DMTEST
```

의미: 

`DB_NAME` 의 이름은 DATABASE_NAME. 이것은 데이터베이스 디렉토리 이름, 로그 파일 이름 등에 영향을 미칩니다. 

예를 들어, `DB_NAME=DMTEST`보통 다음과 같이 생성됩니다: 

```text
/dmdata/data/DMTEST
/dmdata/data/DMTEST/DMTEST01.log
/dmdata/data/DMTEST/DMTEST02.log
```

권장 사항:

- 프로젝트 전체에서 단일 명확한 것을 사용하십시오. DATABASE_NAME 프로젝트 전체에서 사용합니다.
- 초기화 후 변경하는 것은 권장되지 않습니다.

### 3. `INSTANCE_NAME`

예:

```text
INSTANCE_NAME=DBSERVER
```

의미: 

`INSTANCE_NAME` 은 데이터베이스 인스턴스 이름입니다. 서비스 등록 시 서비스 이름을 생성하는 데 보통 사용됩니다.

예를 들어: 

```text
INSTANCE_NAME=DBSERVER
```

등록 후 서비스 이름은 일반적으로 다음과 같습니다:

```text
DmServiceDBSERVER.service
```

권장 사항: 

- 단일 인스턴스가 있는 단일 서버의 경우 다음을 사용할 수 있습니다 `DBSERVER`.
- 하나의 서버에 여러 인스턴스를 배포할 경우 각 인스턴스 이름은 달라야 합니다.

### 4. `PORT_NUM`

예시: 

```text
PORT_NUM=5236
```

의미: 

`PORT_NUM` 는 데이터베이스가 수신 대기하는 포트입니다. 애플리케이션은 데이터베이스에 연결할 때 이 포트에 접속해야 합니다. 

프로그램 페이지에 입력한 포트는 이 포트와 일치해야 합니다: 

```text
HOST:172.17.9.84
PORT:5236
```

권장 사항: 

- 의 기본 포트는 Dameng 일반적으로 `5236`. 
- 동일한 서버에 여러 Dameng 인스턴스가 있는 경우 포트는 중복될 수 없습니다. 
- 포트를 변경한 후에는 데이터베이스 서비스를 재시작해야 합니다. 

### 5. `PAGE_SIZE` 

예시: 

```text
PAGE_SIZE=32
```

의미: 

`PAGE_SIZE` 는 데이터베이스 페이지 크기(KB)입니다. 데이터베이스가 데이터를 읽고 쓸 때 페이지 단위로 데이터를 구성합니다. 

`PAGE_SIZE=32` 는 각 데이터 페이지가 32KB임을 의미합니다. 

영향: 

- 데이터 저장, 인덱싱, IO 동작에 영향을 미칩니다. 
- 초기화 후 수정하는 것은 권장되지 않습니다. 
- 조정이 필요할 경우, 일반적으로 데이터베이스를 재초기화하고 데이터를 이전해야 합니다. 

권장 사항: 

- 만약에 SOP 시나리오에 대한 SOP. 
- 특별한 요구 사항이 없으면 임의로 변경하지 마십시오. 

### 6. `EXTENT_SIZE` 

예시: 

```text
EXTENT_SIZE=32
```

의미: 

`EXTENT_SIZE` 은 페이지 단위로 측정되는 클러스터 크기입니다. 한 번에 데이터베이스가 사용하는 기본 공간 할당 단위로 이해할 수 있습니다.

만약: 

```text
PAGE_SIZE=32
EXTENT_SIZE=32
```

그러면 클러스터는 약: 

```text
32KB * 32 = 1024KB
```

약 1MB입니다. 

영향: 

- 데이터 파일 공간 할당의 세분성에 영향을 미칩니다. 
- 초기화 후 수정하는 것은 권장되지 않습니다. 

### 7. `CASE_SENSITIVE` 

예시: 

```text
CASE_SENSITIVE=0
```

의미: 

`CASE_SENSITIVE` 데이터베이스 객체 이름이 대소문자를 구분하는지를 나타냅니다.

일반적인 값: 

```text
0:CASE_INSENSITIVE
1:CASE_SENSITIVE
```

예를 들어, 대소문자를 구분하지 않을 경우, 다음의 두 테이블 이름은 동일한 객체로 간주될 수 있습니다:

```text
user
USER
```

영향: 

- 테이블 이름, 필드 이름 및 객체 이름 인식에 영향을 미칩니다. 
-  MySQL 마이그레이션 또는 MySQL-호환 시나리오에서는 일반적으로 다음과 같이 구성하는 것이 선호됩니다: `0`. 
- 초기화 후 수정하는 것은 권장되지 않습니다. 

### 8. `UNICODE_FLAG` 

예시: 

```text
UNICODE_FLAG=1
```

의미: 

`UNICODE_FLAG` 은 문자 집합 구성입니다.

일반적인 값: 

```text
0:GB18030
1:UTF-8
2:EUC-KR
```

`UNICODE_FLAG=1` 데이터베이스가 UTF-8 문자 집합을 사용함을 나타냅니다.

권장 사항:

- 계산 기준으로 사용하는 것이 권장됨 UTF신규 시스템에는 -8을 사용합니다.
- 중국어, 영어 및 다국어 문자와의 호환성이 더 좋습니다.
- 초기화 후 수정하는 것은 권장되지 않습니다.

### 9. `SYSDBA_PWD`

예:

```text
SYSDBA_PWD=<SYSDBA_PASSWORD>
```

의미: 

`SYSDBA_PWD` 은 PASSWORD 관리자 계정을 위한 `SYSDBA` 입니다.

`SYSDBA` 데이터베이스 슈퍼 관리자와 유사하며 고급 권한을 가집니다.

권장 사항: 

- 강력한 PASSWORD.
- 를 사용하십시오. 단순한 PASSWORD는 사용하지 마십시오. `SYSDBA`, `123456`, `password`.
- PASSWORD 길이는 최소 8자 이상으로 하고, 문자와 숫자를 포함하는 것이 권장됩니다.
- 실제 PASSWORD 를 외부 문서에 작성하지 마십시오.

### 10. `SYSAUDITOR_PWD`

예시: 

```text
SYSAUDITOR_PWD=<SYSAUDITOR_PASSWORD>
```

의미: 

`SYSAUDITOR_PWD` 은 PASSWORD 의 `SYSAUDITOR` 감사 관리자 계정.

`SYSAUDITOR` 주로 감사 관련 관리 기능에 사용됩니다.

권장 사항:

- 을 사용하십시오 PASSWORD 와 다른 `SYSDBA`.
- 강력한 PASSWORD 복잡성 요구 사항을 충족하는

### 11. `COMPATIBLE_MODE`

예:

```text
COMPATIBLE_MODE = 4
```

의미: 

`COMPATIBLE_MODE` 는 데이터베이스의 호환성 모드 구성이며, 사용되는 데이터베이스가 Dameng 문법, 함수 및 특정 동작 측면에서 어떤 유형의 데이터베이스와 일치하는지 제어합니다. SQL 

일반적인 값 의미: 

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

이 텍스트는 다음과 같이 구성됩니다: 

```text
COMPATIBLE_MODE = 4
```

활성화를 나타냅니다 MySQL 호환 모드가 활성화되었음을 나타냅니다. 

기능: 

- 문법 호환성을 향상시킵니다 MySQL SQL  Dameng. 
- 에서 마이그레이션할 때 변환 비용을 줄입니다 MySQL 또는 에 적응할 때 Dameng. 

참고: 

- 이 구성은 Dameng 가 MySQL 프로토콜을 지원한다는 의미가 아닙니다. 
- 프로그램은 여전히 사용할 필요가 있습니다 Dameng 드라이버는 내부적으로 처리됩니다; 페이지에 드라이버 설정 옵션이 없으면 사용자가 별도로 입력할 필요가 없습니다. 
- 수정 후 데이터베이스 서비스 재시작이 필요합니다. 
- 최종적으로 효과가 있는지는 기반으로 해야 합니다. `v$dm_ini` 쿼리 결과. 

### 12. `ORDER_BY_NULLS_FLAG` 

예시: 

```text
ORDER_BY_NULLS_FLAG = 0
```

의미: 

`ORDER_BY_NULLS_FLAG` 사용 여부를 제어하는 데 사용됩니다 NULL 정렬 시 값이 처음이나 끝에 나타나는지 여부 `ORDER BY`. 

중요한 이유: 

데이터베이스마다 NULL 정렬에 대한 기본 동작이 다를 수 있습니다. 애플리케이션을 MySQL 부터 Dameng까지, 정렬 결과가 NULL의 위치에 따라 달라지는 경우, 이 매개변수가 쿼리 결과의 순서에 영향을 줄 수 있습니다. 

이 문서는 다음과 같이 구성되어 있습니다: 

```text
ORDER_BY_NULLS_FLAG = 0
```

목적은 정렬 동작을 사용 습관에 더 가깝게 만드는 것입니다. MySQL 사용 습관.

참고:

- 수정 후에는 데이터베이스 서비스 재시작이 필요합니다.
- 업무가 SQL 이미 명시적으로 지정한 경우 `NULLS FIRST` 또는 `NULLS LAST`, 에서 지정한 동작이 우선해야 합니다. SQL .

## 부록 2, 자주 묻는 질문

### 1. 왜 연결할 수 없나요 MySQL 클라이언트 MySQL 호환 모드?

호환 모드는 MySQL 호환성 모드만 영향을 미치기 때문에 SQL 구문과 일부 데이터베이스 동작에는 영향을 미치지만, Dameng의 네트워크 프로토콜은 변경하지 않습니다.

응용 프로그램이나 도구가 연결할 때 Dameng, 여전히 Dameng 드라이버를 사용해야 합니다:

```text
dm.jdbc.driver.DmDriver
jdbc:dm://<host>:5236
```

사용할 수 없습니다: 

```text
com.mysql.cj.jdbc.Driver
jdbc:mysql://<host>:5236
```

### 2. 설정이 실제로 적용되었는지 확인하는 방법은 무엇입니까?

단순히 `dm.ini` 파일을 보는 것만으로는 충분하지 않으며, 데이터베이스에 로그인하여 런타임 상태를 확인하는 것이 좋습니다:

```sql
select para_name, para_value
from v$dm_ini
where para_name in ('COMPATIBLE_MODE', 'ORDER_BY_NULLS_FLAG');
```

다음 결과가 나타날 때만 실행 상태가 적용된 것입니다: 

```text
COMPATIBLE_MODE      4
ORDER_BY_NULLS_FLAG  0
```

### 3. 수정 후에도 적용되지 않는 이유는 무엇입니까? `dm.ini`?

일반적인 이유:

- 수정 후 데이터베이스 서비스를 다시 시작하지 않았습니다.
- 파일에 중복된 구성 항목이 있습니다.
- 수정된 파일이 아닙니다 `dm.ini` 현재 인스턴스에서 사용 중입니다.

현재 인스턴스가 어떤 구성 파일을 사용하고 있는지는 서비스 시작 명령을 통해 확인할 수 있습니다:

```bash
systemctl status DmServiceDBSERVER.service --no-pager
```

출력에서 일반적으로 다음과 같은 것을 보게 될 것입니다:

```text
dmserver path=/dmdata/data/DMTEST/dm.ini -noconsole
```

### 4. 내가 ~라면 무엇을 해야 하나요 PASSWORD 복잡성 오류가 발생합니까?

이것은 PASSWORD 이 너무 단순함을 나타냅니다. 더 복잡하게 변경해 주세요. PASSWORD예를 들어:

```text
AT_LEAST 8 POSITION
CONTAINS_LETTERS_AND_NUMBERS
AVOID_USING_THE_ACCOUNT_NAME_ITSELF
```

### 5. 이러한 매개변수는 나중에 변경할 수 있나요? 

아니요. 

초기화 매개변수는 일반적으로 나중에 변경하는 것이 권장되지 않습니다. 예를 들어: 
- 'PAGE_SIZE'
- 'EXTENT_SIZE'
- 'CASE_SENSITIVE'
- 'UNICODE_FLAG'
- 'DB_NAME'
- 'INSTANCE_NAME'

이 매개변수가 잘못 구성된 경우, 일반적으로 데이터베이스를 다시 초기화하고 데이터를 다시 마이그레이션하는 것이 권장됩니다. 

'dm.ini' 매개변수는 나중에 조정할 수 있습니다. 예를 들어: 

- 'COMPATIBLE_MODE'
- 'ORDER_BY_NULLS_FLAG'
- 'PORT_NUM'

그러나 수정 후에는 일반적으로 데이터베이스 서비스를 재시작해야 합니다. 

## 부록 3: 최종 점검표 


- '/dmdata/data' 데이터 디렉터리가 생성되었습니다. 
- 데이터 디렉터리 호스트는 'dmdba:dinstall'입니다. 
- 데이터베이스가 성공적으로 초기화되었습니다. 
- '/dmdata/data/DMTEST/dm.ini'가 존재합니다. 
- `COMPATIBLE_MODE = 4`. 
- `ORDER_BY_NULLS_FLAG = 0`. 
- 데이터베이스 서비스 `DmServiceDBSERVER.service` 가 `active`. 
- 포트 `5236` 리스닝 중입니다. 
- `SYSDBA` 데이터베이스에 로그인할 수 있습니다. 
- 에서 `v$dm_ini`, 런타임 값 `COMPATIBLE_MODE` 가 `4`.
