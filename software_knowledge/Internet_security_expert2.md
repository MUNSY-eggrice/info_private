# 로그 분석 및 파일 관리
## 해킹에 이용된 계정(root 권한을 갖는 계정 찾기)

1. cat /etc/passwd | more
2. git bit가 0으로 설정된 것을 찾는다. root그룹은 항상 0이기 때문.
```
root:x:0:0:root:/root:/bin/bash

```
* 필드 1 : 사용자명
* 필드 2 : 패스워드(/etc/shadow 파일에 암호화 되어있다.)
* 필드 3 : 사용자 계정 uid (User Id)
* 필드 4 : 사용자 계정 gid (Group Id)
* 필드 5 : 사용자 계정 이름 (정보)
* 필드 6 : 사용자 계정 홈 디렉토리
* 필드 7 : 사용자 계정 로그인 쉘

## 해킹에 사용된 파일들(해커의 계정이 소유한 파일들)의 위치 찾기

find / -user (해커의 계정) 2> /dev/null

자세한 내용은 find명령어 정리 참고

## 공격자의 흔적을 지우는데 사용된 파일 찾기

1. 위에서 찾은 해커 소유의 파일들을 cat과 more 명령어를 통해 확인  
ex) cat /tmp/.outkit | more

2. 해커가 root 권한을 얻은 뒤에 수행한 명령어 로그를 확인  
ex) cat ~root/.bash_history

관련 내용은 <로그 파일 경로>에 정리해 두었다.

## 침입자가 권한 상승을 위해 사용한 파일 위치 찾기

위에서 찾은 해커 소유의 파일 중에 cat과 more을 통해  
setreuid(geteuid(), geteuid()); 와 같이 권한 변경 관련 코드가 있는 파일을 찾아낸다.

1. setuid : 프로세스의 ruid, euid, suid를 변경한다(관리자가 root일때만)
2. seteuid : 프로세스의 euid를 변경한다.

핵심: find . -user (유저명) | xargs grep -n setreuid 2> /dev/null
### 참고
* 1>/dev/null은 표준출력을 /dev/null로 redirection하고 (정상적인 메시지를 null로)
* 2>/dev/null은 표준에러를 /dev/null로 redirection 한다 (에러메시지를 null로)

## 인가되지 않은(잘 알려지지 않은) 리모트 백도어 서비스 찾기
1. cd /etc/xinetd.d/
2. ls
3. 일반적으로 쓰이지 않는 서비스가 있는지 확인한다.
chargen, cups, daytime, echo, finger, rexec, rsh, servers, services, sgi_fam, telnet, tftp, time-udp, xadmin
위 목록과 관계없어 보이고 뜬금없다면 그것이 정답일 가능성이 높다.

## 서로 다른 두 파일을 비교하여 변경된 사항 확인하는 방법
diff 또는 cmp를 사용한다.

## 해커의 ip찾기

lastlog -u (계정)

ex> lastlog -u hacker

또는 -t (기간) 명령어를 통해 특정 기간 내의 로그만 확인

ex> lastlog -u hacker -t 3 (최근 3일 동안 hacker계정 접속 로그 출력)

# 계정 설정
## 사용자 계정의 유효기간을 30일로 설정하는 방법

usermod -f 30 cuha(cuha 계정의 유효기간을 30일로 설정)

## 패스워드 변경법

passwd cuha (cuha 계정의 패스워드 변경, 명령어 입력시 새 비밀번호 입력 필드가 생긴다.)

## root 계정에 대한 자동 로그아웃 시간을 50초로 설정하는 법
vi /etc/profile

TMOUT 항목의 값을 50으로 변경, 없다면 인서트 모드로 들어가
```
TMOUT=50
```
을 추가하고 나오기.

## PW 최소 글자 수를 8글자 이상으로 설정하는 방법

vi /etc/login.defs

PASS_MIN_LEN 항목 값을 8로 변경

## PW 유효기간을 30일로 설정하는 방법

vi /etc/login.defs

PASS_MAX_DAYS 항목 값을 30으로 변경

# 네트워크 및 방화벽 설정
## VSFTP 설정 중 anonymous(익명 사용자) 사용 못하게(차단) 하는 설정법

```
vi /etc/vsftpd.conf
anonymous_enable=NO
service vsftp restart(시험엔 나올지 모르겠다, 사용 안할 가능성 높다.)
```
## FTP 인증 실패 시 차단 횟수 설정법
```
vi /etc/ftpaccess
loginfails 3
service vsftp restart(마찬가지로 시험에서는 작성 안해도 될 것 같다.)