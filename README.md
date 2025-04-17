# XSS-CTF
2025-04-18 XSS-CTF  Walkthrough

## Scan

```
gobuster dir -u http://[xss-ctf ip] -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x html,txt,php,bak
```
/index.php  /index.html  /list.php  /add.php

## aaaaaa

/index.php
!!!!!!!

view source code
!!!!!!!

/H10000.txt
!!!!!!!!
H10000 값


## /list.php

!!!!!!!!!
M1,M2,M3,M4 값이 중요하기 때문에 복붙

1번 게시글 삭제 후 /index.php 
!!!!!!!!!!
!!!!!!!!!!

## /add.php

!!!!!!!!!!

개발자 도구 -F12
<!- -MySQL 쿼리 입력 폼 -- > 주석 확인
type=”hidden” 2개 확인 가능

!!!!!!!!!!!!!!!!!!!!!

type=”hidden” 삭제

type=”hidden” -> type=”submit” 변경


Mysql 쿼리 입력 부분 출력됨
!!!!!!!!!!!!!!!!!!!!!!!!

SQL Injection 공격 가능 – userID DB 확인 가능
!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!

## XOR 연산

XOR 변환기

/list.php 에서 얻은 M1,M2,M3,M4 값과 /H10000 에서 얻은 H10000 값으로 XOR 연산




