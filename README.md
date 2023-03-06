# Linux

## 개요
- 리눅스 페도라를 이용한 개인 서버 운영에 필요한 정보 모음

## 목차

1. [네트워크](1-네트워크)
2. [인프라](2-인프라)
3. [DB](2-오라클)

---

### 1. 네트워크
#### 1.1. 네트워크 트래픽 덤프 확인
- 요청 수신여부 확인을 위한 트래픽 덤프를 보는 방법
- tcpdump를 사용하여 실시간 트래픽을 확인 가능
```shell
root$ tcpdump -i [네트워크 인터페이스] host [출발지 IP 주소]
```
```shell
root$ tcpdump -i wlp2s0 src 8.8.8.8
```
### 2. 인프라
#### 1.1. 가상 메모리 설정
- ```$sudo free -m, $sudo swapon -s``` 명령어를 통해 사용중인 swap 내용이 있는지 확인
- ```$sudo swapoff -a``` 명령어를 통해 swap을 종료
- ```$sudo fallocate -l 4G /swapfile``` swapfile 생성
- ```$sudo chmod 600 /swapfile``` 시스템 전용 접근권한 설정
- ```$sudo mkswap /swapfile``` 활성화 준비
- ```$sudo swapon /swapfile``` 활성화
- ```$sudo vi /etc/fstab``` fstab 파일 수정을 통해 재기동시에도 변경된 swap이 적용되도록 변경
```shell
## fastab에 아래 내용을 추가한다
/swapfile swap  swap  defaults  0 0
```
#### 2.2. yum 사용
- ```$sudo yum list {package name}``` 명령어를 통해 설치 가능한 패키지 버전을 확인한다.
> ex) ```$sudo yum list java*jdk-devel``` : 설치 가능한 jdk 버전 확인
- ```$sudo yum install {package name}``` 명령어를 통해 패키지를 설치한다.
> ex) ```$sudo yum install java-11-openjdk-devel.x86_64``` : openjdk 11 설치 <br>
> ※권한이 불충분한 경우 리스트 조회와 설치가 불가능한 경우가 생기니 sudo 권한으로 실행할 것

### 3. 오라클
#### 3.1. ORA-12546 TNS:permission denied
> #### 문제
> - 오라클 리스너 구동/상태확인/종료 명령에 해당 요류가 발생하였다.
> ```
> TNS-12555: TNS:permission denied
>   TNS-12560: TNS:protocol adapter error
>     TNS-00525: Insufficient privilege for operation
>       Linux Error: 1: Operation not permitted
> ```
> ---
> #### 원인
> - ```/var/tmp/.oracle``` 디렉터리의 소유주와 소유그룹을 오라클 계정/그룹으로 지정되지 않아 발생하는 문제였다.
> ---
> #### 해결
> - 해당 디렉터리의 소유주를 변경하여 문제를 해결하였다.
> - 아래의 방법으로 소유주를 확인한다.
> ```shell
> root$ ll /var/tmp
> ...
> srwxrwxrwx. 1 root  root  0 7월  5 22:00 .oracle
> ... 
> ```
> - 소유주가 오라클 설치시 생성되는 계정이 아닐 경우 변경해준다. (```chown -R``` 옵션을 통해 하위 디렉터리까지 소유주 변경이 가능하다.)
> ```shell
> root$ chown -R oracle:dba /var/tmp/.oracle 
> ```
#### 3.2. Connection refused
> #### 문제
> - 오라클 리스너 상태를 확인할때에 발생하였다.
> - ```shell
>
> ```
> ```
> TNS-12541: TNS:no listener
>   TNS-12560: TNS:protocol adapter error
>     TNS-00511: No listener
>       Linux Error: 111: Connection refused
> ```
