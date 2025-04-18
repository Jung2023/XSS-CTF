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
gobuster dir -u http://192.168.56.143 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x html,txt,php,bak
```
![Image](https://github.com/user-attachments/assets/bd1a298b-82bf-4f81-88a9-02edc6157fcf)

/index.php  /index.html  /list.php  /add.php


## /index.php

들어가보았지만 Not Found가 뜬다. 그래서 web source페이지를 보았다.

![Image](https://github.com/user-attachments/assets/aa4d56d4-28ef-4e82-9b66-baba5fad9edc)

![Image](https://github.com/user-attachments/assets/520576de-79c4-4553-8a39-dfd7805ed9fb)

/H10000.txt

![Image](https://github.com/user-attachments/assets/432de6fe-9e2c-43fd-85b5-cea0166608b3)

$H_{10000}$ 값


## /list.php

index.php에서 Not Found가 떠서 list.php를 들어가면 다음과 같은 글이 작성되어있다.

1번 게시글 삭제 후 /index.php 를 다시 들어가면 정상적인 페이지가 뜬다는 것을 확인할 수 있다.

![Image](https://github.com/user-attachments/assets/3662b62c-6cd9-4126-a512-355c599517ab)

![Image](https://github.com/user-attachments/assets/e761c310-7b48-4307-bef6-122f69671eff)

/index.php

![Image](https://github.com/user-attachments/assets/47239fa6-b91f-4f44-a8a0-5117113147f2)


## /add.php

http://192.168.56.143/add.php 에서 View Source Code를 들어가서 보면 쿼리 작성폼이라는 주석이 있는 줄에 쿼리 작성 폼과 ID 조회하는 코드에 type=”hidden”이 되어 있는 것을 알 수 있다.

![Image](https://github.com/user-attachments/assets/0a49c75c-76f1-428e-806e-f37223e4c611)

개발자 도구 -F12
<!- -MySQL 쿼리 입력 폼 -- > 주석 확인
type=”hidden” 2개 확인 가능

![Image](https://github.com/user-attachments/assets/e5ea83a6-c3fd-469f-bcb7-70803c2e2c4a)

type=”hidden” 삭제

type=”hidden” -> type=”submit” 변경

![Image](https://github.com/user-attachments/assets/5ef4b06f-da65-4cf9-b137-ae34d2fe3ee5)

Mysql 쿼리 입력 부분 출력된다.

![Image](https://github.com/user-attachments/assets/892fc8e3-3334-4420-b366-18da9c542498)

SQL Injection 공격 가능 – userID DB 확인 가능

![Image](https://github.com/user-attachments/assets/d5de20bf-3eb1-43bb-9e74-860b97a817c5)

![Image](https://github.com/user-attachments/assets/b771484d-4fb4-4428-99e6-3ff44d8853ad)


## XOR 연산

XOR 변환기 http://kor.pe.kr/util/4/xor_convert.htm

/list.php 에서 얻은 M1,M2,M3,M4 값과 /H10000.txt 에서 얻은 $H_{10000}$ 값으로 XOR 연산

이 과정은 총 2가지로 풀 수 있다.

1번 방법: 

192.168.56.143/index.php에서 다음과 같은 식을 제공하였다. 이때 P=Password다. 

$M_{1}=userID ⨁ K ⨁ H_{9999}$

$M_{2}=serverID ⨁ K ⨁ H_{9999}$

$M_{3}=H_{10000} ⨁ P$

$M_{4}=H_{9999} ⨁ P$

$P=H_{10000}⨁M_{3}$를 통해 $Password$=df93ef64167b2334664ff59a5b8185f5값을 구하면 $M_{4}$를 통해 $H_{9999}$를 구할 수 있다. $H_{9999}=M_{4}⨁P$ 이후 $M_{1}$을 이용하여 $K$를 구할 수 있다. 이때 $userID$는 위의 SQL Injection으로 찾아낸 $userID$ 중 하나씩 다 대입하면 $userID$=Ocean777Timewarp를 넣어서 계산하다보면 그럴듯한 $K$=secretpassword12값이 나온다. 그 다음 $M_{2}$에서 $serverID=M_{2}⨁H_{9999}$를 구할 수 있다. $serverID$=cr0sss1tescr1pt9 이제 서버의 ID와 Password를 전부 찾았다.

2번 방법: 

$M_{1}=userID ⨁ K ⨁ H_{9999}$

$M_{2}=serverID ⨁ K ⨁ H_{9999}$

$M_{3}=H_{10000} ⨁ P$

$M_{4}=H_{9999} ⨁ P$

$P=H_{10000}⨁M_{3}$를 통해 $Password$=df93ef64167b2334664ff59a5b8185f5를 구한 후 $M_{1}⨁M_{2}=userID⨁serverID$가 나오고 SQL Injection으로 찾아낸 $userID$를 하나씩 대입하면 $serverID$= cr0sss1tescr1pt9가 나온다. 이렇게 하면 굳이 $H_{9999}$값과 $K$값을 구하지 않아도 $serverID$값을 구할 수 있다.


## SSH 접속

userID :
cr0sss1tescr1pt9

password :
df93ef64167b2334664ff59a5b8185f5

(공백 없음)

![Image](https://github.com/user-attachments/assets/93817ac5-e880-4dd2-bf48-1dcaf8cbc4ac)


## User Flag
user flag 획득

![Image](https://github.com/user-attachments/assets/67b27143-5b03-4bef-ae07-28b84f7f9b08)


## Root Flag

root로 권한 상승 - sudoedit

![Image](https://github.com/user-attachments/assets/860113b8-fe28-4892-b37d-67c51934209d)

https://github.com/blasty/CVE-2021-3156  참조

- brute.sh , hax.c , lib.c 파일 생성

- cr0sss1tescr1pt9 사용자는 vi,vim,nano 사용할 수 없다.

- kali-root 계정에서 /var/www/html/ 디렉터리에 파일 생성 후, cr0sss1tescr1pt9 사용자 계정에서 wget으로 파일을 다운로드한다.

![Image](https://github.com/user-attachments/assets/55b9071e-acd5-4125-a93a-0b9bf4691f41)

![Image](https://github.com/user-attachments/assets/efbcb372-cd7d-44e4-a68f-a7b37b068949)

![Image](https://github.com/user-attachments/assets/a66c7d11-fd0d-44d6-928b-d18b5c20d8f6)

![Image](https://github.com/user-attachments/assets/1e57f253-9b9a-4791-a059-c80f5fb96e8a)


https://github.com/blasty/CVE-2021-3156 - Makefile 참고

root flag

![Image](https://github.com/user-attachments/assets/10a28bff-12d4-4aed-808a-d153e2604233)







