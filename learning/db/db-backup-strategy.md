**실무에서 가장 보편적으로 쓰이는 논리 덤프 백업 방법**과 함께, **추가 팁**까지 포함한 전체 정리

## PostgreSQL

* **도구**: `pg_dump`
* **포맷**: 커스텀 포맷 (`-F c`) → `.dump`
* **백업 스크립트 예시** (`/usr/local/bin/backup_pg.sh`):

```bash
#!/bin/bash
export PGPASSWORD="your_pg_password"
TIMESTAMP=$(date +%F)
BACKUP_DIR="/var/backups/postgres"
DB_NAME="mydb"

mkdir -p "${BACKUP_DIR}"
pg_dump -h localhost -U postgres \
  -F c -Z 9 \
  -f "${BACKUP_DIR}/${DB_NAME}_${TIMESTAMP}.dump" \
  "${DB_NAME}"
```

* **복원 명령어**:

```bash
pg_restore -h localhost -U postgres \
  -d restored_db \
  /var/backups/postgres/mydb_2025-07-01.dump
```

* **크론 등록 (매일 02:00)**:

```
0 2 * * * /usr/local/bin/backup_pg.sh >> /var/log/backup_pg.log 2>&1
```

---

## MySQL

* **도구**: `mysqldump`
* **포맷**: 플레인 SQL → `.sql` (압축 시 `.sql.gz`)
* **백업 스크립트 예시** (`/usr/local/bin/backup_mysql.sh`):

```bash
#!/bin/bash
MYSQL_USER="root"
MYSQL_PASSWORD="your_mysql_password"
MYSQL_HOST="localhost"
DB_NAME="mydb"
TIMESTAMP=$(date +%F)
BACKUP_DIR="/var/backups/mysql"

mkdir -p "${BACKUP_DIR}"
mysqldump -h "${MYSQL_HOST}" \
  -u "${MYSQL_USER}" -p"${MYSQL_PASSWORD}" \
  --single-transaction --routines --events \
  "${DB_NAME}" > "${BACKUP_DIR}/${DB_NAME}_${TIMESTAMP}.sql"

gzip "${BACKUP_DIR}/${DB_NAME}_${TIMESTAMP}.sql"
```

* **복원 명령어**:

```bash
gunzip < /var/backups/mysql/mydb_2025-07-01.sql.gz \
  | mysql -h localhost -u root -p your_mysql_password mydb
```

* **크론 등록 (매일 03:00)**:

```
0 3 * * * /usr/local/bin/backup_mysql.sh >> /var/log/backup_mysql.log 2>&1
```

---

## Oracle

* **도구**: Data Pump (`expdp`, `impdp`)
* **포맷**: 바이너리 덤프 → `.dmp`
* **백업 스크립트 예시** (`/usr/local/bin/backup_oracle.sh`):

```bash
#!/bin/bash
export ORACLE_SID=ORCLCDB
export ORACLE_HOME=/u01/app/oracle/product/19.0.0/dbhome_1
export PATH=$ORACLE_HOME/bin:$PATH

TIMESTAMP=$(date +%F)
BACKUP_DIR="/var/backups/oracle"
DUMPFILE="full_${TIMESTAMP}.dmp"
LOGFILE="expdp_${TIMESTAMP}.log"

mkdir -p "${BACKUP_DIR}"
expdp system/YourSysPass@ORCLCDB FULL=Y \
  DIRECTORY=DATA_PUMP_DIR \
  DUMPFILE="${DUMPFILE}" \
  LOGFILE="${LOGFILE}" \
  COMPRESSION=ALL

mv $ORACLE_BASE/admin/*/dpdump/${DUMPFILE} "${BACKUP_DIR}/"
mv $ORACLE_BASE/admin/*/dpdump/${LOGFILE}  "${BACKUP_DIR}/"
```

* **복원 명령어**:

```bash
impdp system/YourSysPass@ORCLCDB FULL=Y \
  DIRECTORY=DATA_PUMP_DIR \
  DUMPFILE="full_2025-07-01.dmp" \
  LOGFILE="impdp_2025-07-01.log"
```

* **크론 등록 (매일 04:00)**:

```
0 4 * * * /usr/local/bin/backup_oracle.sh >> /var/log/backup_oracle.log 2>&1
```

---

## 추가 팁

### 1. 로그 회전 및 보존

* `logrotate` 또는 `find` 명령어로 오래된 백업 자동 삭제

```bash
find $BACKUP_DIR -type f -mtime +7 -delete
```

* 주간/월간 백업 보존 정책을 병행 설정

---

### 2. 보안

* 평문 비밀번호 대신 아래 방법 활용:

| DB         | 권장 방식                        |
| ---------- | ---------------------------- |
| PostgreSQL | `~/.pgpass`                  |
| MySQL      | `~/.my.cnf`                  |
| Oracle     | Oracle Wallet 또는 외부 Vault 연동 |

* 백업 디렉토리 권한 제한:

```bash
chmod 700 /var/backups/*
```

---

### 3. 모니터링 및 알림

* 크론에 `MAILTO=user@example.com` 설정
* Slack 또는 Webhook 알림 스크립트 연동 가능

---

### 4. 압축 및 암호화

* 압축: `gzip`, `bzip2`, `xz` 중 환경에 맞게 선택
* 암호화: `gpg` 활용 예시

```bash
gpg -c mydb_2025-07-01.sql.gz
```

---

### 5. 증분 백업

* MySQL: Binary log 및 `--master-data` 옵션
* PostgreSQL: WAL 아카이빙 설정
* Oracle: RMAN의 증분 백업 기능 활용

---

### 6. 정기 복원 테스트

* 실제 복원 절차 수행하여 백업 무결성 검증
* 샘플 쿼리 실행으로 데이터 정확성 확인

---

### 7. 파일명 규칙

* 일관된 파일명 패턴으로 관리 효율성 확보

```
<DB이름>_<YYYY-MM-DD>.<포맷>.<확장자>
```

* 예시:

  * `mydb_2025-07-01.dump`
  * `mydb_2025-07-01.sql.gz`
  * `full_2025-07-01.dmp`

---

## 정리

* PostgreSQL, MySQL, Oracle 모두 논리 덤프 방식이 가장 널리 사용됨
* 복원 유연성을 고려해 포맷은 상황에 따라 선택
* 자동화(크론), 보안, 테스트까지 포함하여 백업 전략 구성
* 필요 시 물리 백업 및 클러스터 백업도 병행 고려

---

필요 시 이 내용을 팀 위키나 운영 문서에 바로 붙여 넣어도 손색없는 형식입니다. 다른 형식(HTML, PDF 등)으로도 변환이 필요하다면 알려주세요.
