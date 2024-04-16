---
title: "ec2에 mariaDB를 설치해보자"
date: 2024-04-16 15:40:00 +09:00
categories: [ 인프라 ]
tags:
  [
    DB, mariaDB, mysql, RDB
  ]
---

### MariaDB 설치

```shell
sudo apt-get update
sudo apt-get install mariadb-server
```

### MariaDB 보안 설정
```shell
sudo mysql_secure_installation
```
* root 암호를 설정하고, 불필요한 사용자 및 테스트 데이터베이스를 제거

### 외부접속 허용
#### 내부설정
MariaDB의 기본 구성에서는 외부에서의 접속이 허용안됨, 외부에서 접속할 수 있도록 MariaDB 설정 파일을 편집 <br>
`sudo vim /etc/mysql/mariadb.conf.d/50-server.cnf`
```shell
#bind-address            = 127.0.0.1
bind-address            = 0.0.0.0
```

#### 외부 접속 권한 추가
```sql
GRANT ALL ON *.* to root@'%' IDENTIFIED BY 'Password';
FLUSH PRIVILEGES;
```
* GRANT ALL: 사용자에 대해 모든 권한을 부여. 데이터베이스를 완전히 관리하고 모든 작업을 수행할 수 있는 권한부여.
* ON *.*: 모든 데이터베이스와 테이블에 대한 권한을 부여. `*.*`는 모든 데이터베이스를 의미합니다. 첫 번째 *는 데이터베이스 이름을, 두 번째 *는 테이블 이름을 나타냄.
* TO root@'%': 권한을 부여할 사용자를 지정합니다. 여기서 `root는` 사용자 이름이고, `'%'`는 `모든 호스트`를 나타냅니다. 따라서 이 권한은 모든 호스트에서 root 사용자에게 적용됩니다.
  * 이설정을 해줘야 원격에서 접속 가능


#### ec2-보안그룹 설정
ec2 보안그룹에서 3306 포트를 open


### MariaDB 재시작
```shell
sudo systemctl restart mariadb
```
