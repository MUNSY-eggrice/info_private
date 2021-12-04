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
```
## IPTABLES 에서 들어오는 패킷 중 icmp 패킷 모두 차단 설정법

iptables -A INPUT -p icmp -j DROP

## sendmail 설정 중 거부하고 싶은 ip address 설정법
```
vi /etc/mail/access
123.123.123.123 REJECT (123.123.123.123 차단)
cd /etc/mail
makemap hash access < access
```
참고로 아이피뒤에 붙이는 옵션은 다음과 같다.
OK : 모두 허용.
RELAY : 수신/발신 허용.
REJECT : 수신/발신 거부.
DISCARD : /etc/sendmail.cf에 지정된 곳으로 메일을 폐기함 (스팸 메일함으로 이동)

## http.conf 설정으로 index파일이 없는 경우 홈 디렉토리가 디렉토리 형태로 안보이게 설정하는 법
```
find / -name httpd.conf 2> /dev/null
vi /위에서 찾은 경로/httpd.conf
Options Indexes FollowSymLinks  -> Options FollowSymLinks
```
# 포트 설정 및 권한 설정
## 인가되지 않은(Well-known 포트가 아닌) 백도어 포트 번호 찾기

1. Well-Known 포트는 1~1024번 포트이다.
2. netstat 명령어를 사용한다. $netstat -nat
3. 포트번호에서 1~1024가 아닌 포트가 비인가 포트이다.
4. netstat -nap 명령을 사용하면 해당 포트를 사용중인 프로세스의 정보를 얻을 수 있다.

## 4000~5000번대 사이의 TCP포트를 열어 LISTEN하고 있다. 해당 포트 찾기

1. netstat -nat | grep LISTEN 명령으로 연결된 모든 TCP 포트 번호를 출력한다.
2. 4000과 5000번 사이의 포트 번호를 찾아 답안에 기술하면 된다.

## 권한 설정하는 방법
chmod 명령어를 사용한다

* r : read : 4
* w : write : 2
* x : excute : 1

위 숫자들을 더한 값으로 사용할 수 있다.

chmod 644 (rw_r__r__) 소유자는 읽기 쓰기 가능, 다른 그룹과 사용자는 읽기만 가능하다.

## /etc/services 파일 설정법

이 파일은 리눅스 서버에서 사용하는 모든 포트들에 대한 정의가 설정되어있다.

설정을 통해 기본 사용 포트를 변경하여 알려진(Well-known)포트를 통해 기본적인 해킹 툴들을 차단할 수 있다.

ex)
서비스명 	 포트/프로토콜 	 별칭  
ftp                21/tcp			* 별칭은 반드시 정해야 하는 것은 아님  
fsp                21/udp 	 fspd  

## 아파치 서버(웹서버)에서 1.2.3.4 ip만을 차단하려고 한다.

1. find / -name httpd.conf 2>/dev/null
2. vi /1에서 찾은 경로/httpd.conf
3. <Directory/>
    Order deny, allow
    Deny from 1.2.3.4
    Allow from all
    </Directory>
4. service httpd restart

## 현재 서버가 sniffing 당하고 있다. 그 근거와 차단 방법은?

