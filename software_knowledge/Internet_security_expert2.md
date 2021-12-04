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