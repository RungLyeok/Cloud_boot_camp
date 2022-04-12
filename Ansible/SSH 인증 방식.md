# SSH



### 📌INDEX

- [SSH 파일](ssh-파일)
- [패스워드 기반 SSH 인증](#패스워드-기반-ssh-인증)
- [키 기반 SSH 인증](#키-기반-ssh-인증)
- [SSH 키 생성](#ssh-키-생성)
- [SSH 키 미리받기](#ssh-키-미리-받기)
- [서버에 키 등록](#서버에-키-등록)
- [SSH 접속](#ssh-접속)

<br>

<br>

## SSH 파일

**/etc/ssh/<Key_Pair>**

- 서버의 키 쌍 : public키, private키



**/etc/ssh/config_ssh** 

- ssh 서비스 설정파일(클라이언트)



**/etc/ssh/config_sshd** 

- sshd 서비스 설정파일(서버)
- 패스워드 인증방식 비활성화가 default 

```shell
PasswordAuthentication no
```

- 키 기반 인증방식은 활성화가 default

```shell
GSSAPIAuthentication yes
```



**~/.ssh/<Key_Pair>**

- Client(계정)의 키 쌍



**~/.ssh/known_hosts**

- 접속했던 호스트를 기록(핑거프린트)
- 한번 기록되고 나면 이후 접속할 때 물어보지 않음
- **계정을 바꿔서 접속하면 다시 물어봄** -> 홈 디렉토리가 바뀌기 때문



**~/.ssh/authorized_keys**

- 상대방의 공개키를 저장



**~/.ssh/config**

- ssh 로그인 시 옵션을 사전 세팅 
- 등록해놓으면 window에서 `ssh` 명령을 통해 매우 간편하게 접속
- 예시

```shell
Host controller
    HostName 192.168.100.10
    User vagrant
    IdentityFile C:\Users\Playdata\vagrant\ansible\.vagrant\machines\controller\virtualbox\private_key

Host node1
    HostName 192.168.100.11
    User vagrant
    IdentityFile C:\Users\Playdata\vagrant\ansible\.vagrant\machines\node1\virtualbox\private_key

Host node2
    HostName 192.168.100.12
    User vagrant
    IdentityFile C:\Users\Playdata\vagrant\ansible\.vagrant\machines\node2\virtualbox\private_key
```

```shell
ssh controller
```

```shell
ssh node1
```

```shell
ssh node2
```



- 파일 수정 후에는 변경사항이 반영되도록 sshd 서비스 재시작

```shell
sudo systemctl restart sshd
```



<br>
<br>

## 패스워드 기반 SSH 인증

A(Client) ---SSH---> B(Server)



1. A는 B의 공개키 수령
   - `/etc/ssh/ssh_host_<Algorithm>.pub`
   - Algoritm
     - RSA
     - DSA
     - ECDSA
2. (B 시스템 최초 접속 시) A 시스템 사용자에게 B의 공개키(지문)이 맞는지 확인
3. A의 `~/.ssh/known_hosts`에 B의 공개키 등록
4. ID/PW 인증

<br>

<br>

## 키 기반 SSH 인증

A(Client) ---SSH---> B(Server)



1. A에서 (인증용) 키 쌍 생성 
   - `ssh-keygen` 
     - `~/.ssh/id_rsa`: 개인키
     - `~/.ssh/id_rsa.pub`: 공개키

2. B에 A의 공개키를 등록
   - B의 `~/.ssh/authorized_keys`에 A의 공개키 등록
   - EC2 인스턴스 : A에서 지정한 A의 공개키 등록
   - BM/VM : `ssh-copy-id` 명령을 통해 공개키 복사
     - B에 패스워드 인증방식이 활성화되어있어야함

3. (B 시스템에 최초 접속시) A 시스템의 사용자에게 B의 공개키(지문) 맞는지 확인
4. A의 `~/.ssh/known_hosts` 파일에 B의 공개키 등록
   - B의 IP/Domain
   - B의 공개키
5. A의 개인키로 인증



**기본 로그인 사용자**

- Amazon Linux: ec2-user
- Ubuntu: ubuntu
- Debian: debian
- Centos: centos
- RHEL: cloud-user
- vagrant: vagrant
- ...

<br>

<br>

## SSH 키 생성

- **`ssh-keygen [옵션]`**

- **ssh key를 생성, 관리 및 변경하는 명령어**
- private 키는 default로 ``~/.ssh/id_rsa` 생성
  - public 키는 같은 위치에 id_rsa.pub로 생성
- 옵션이 없으면 키를 생성
  - ``~/.ssh/id_<Algorithm>`` 키 생성
- 옵션
  - `-t` : (type) 알고리즘을 지정
  - `-f` : (file) 키 파일의 파일 이름 지정
  - `-l` : (list) 지정한 공개 키 파일의 지문을 표시
  - `-b` : (bit) 키의 크기 지정
    - default : 2048
    - 크기를 늘릴 수록 보안이 강화됨

- 생성 후에 `passphrase`를 설정하라고함
  - 보안을 위해서 설정하는 것이 좋음



예시

- ecdsa 알고리즘의 키쌍 생성

```shell
ssh-keygen -t ecdsa
```

- **(서버의)`/etc/ssh/ssh_host_ecdsa_key.pub` 공개키 지문 확인**⭐
  - 미리 준비된 서버의 경우 이렇게 지문을 확인하는 것이 best
  - But, EC2 인스턴스의 경우 만들어질 때 key가 생성되기 때문에 미리 확인할 방법이 없음

```shell
ssh-keygen -l -f /etc/ssh/ssh_host_ecdsa_key.pub
```

<br>

<br>

## SSH 키 미리 받기

- **`ssh-keyscan [옵션] [대상IP]`**
- **대상의 공개키를 미리 받아볼 수 있는 명령어**
- 맹점 : 네트워크를 통해 전달받는 것이기 때문에 중간에 해커가 가로채서 잘못될 정보를 줄 수도 있음

- 옵션
  - `-t` : 알고리즘 타입 지정해서 확인



예시

- 서버(IP)의 rsa 알고리즘의 공개키 확인

```shell
ssh-keyscan -t rsa 192.168.100.11
```

- 파이프라인(|)을 활용하여, 서버(IP)의 **핑커프린트(지문) 바로 확인**⭐
  - 파이프라인 앞 명령의 출력이 파이프라인 뒤 '`-`'로 들어감

```shell
ssh-keyscan -t ecdsa 192.168.100.11 | ssh-keygen -l -f -
```

- **미리 서버의 공개키 `known_hosts`에 미리 등록하기**⭐
  - 핑거프린트를 물어보지 않음
  - but, 등록해도 서버가 암호인증(`sshd_config`)을 활성화해둬야 접속됨
    - 키를 등록하기 위해서는 암호 인증이 가능해야함

```shell
ssh-keyscan -t ecdsa 192.168.100.11 >> ~/.ssh/Known_hosts
```

```shell
ssh-keyscan -t ecdsa 192.168.100.12 >> ~/.ssh/known_hosts
```

<br>

<br>

## 서버에 키 등록

- **`ssh-copy-id [대상 IP]`**
- 클라이언트(Client)의 공개키가 서버의 `~/.ssh/authorized_keys`에 등록됨
- 등록 후에는 패스워드를 물어보지 않음

```shell
ssh-copy-id 192.168.100.11
```

- 계정을 지정해주지 않으면 클라이언트의 계정을 그대로 사용
- 명확하게 하기 위해서는 계정을 지정해주는 것이 좋음

```shell
ssh-copy-id vagrant@192.168.100.11
```



<br>

<br>

## SSH 접속

- `ssh`로 접속할 때 계정을 지정하지 않으면, 현재 클라이언트의 계정을 그대로 사용
- 서버에 접속할 때, 계정의 패스워드를 물어봄
- 계정을 바꿔서 접속하면 핑거프린트 다시 물어봄
  - 홈 디렉토리가 바뀌기 때문



#### Windows -> Vagrant SSH 접근

- vagrant를 이용하면 자동으로 키 기반 인증
  - .vagrant 파일은 절대 수동으로 변경,조작,삭제하면 안됨
  - 키 위치(클라이언트의 키) : `.vagrant/machines/[vm명]/virtualbox/private_key`
  - 아래 두개의 명령어는 동일한 명령어 : vagrant가 간편하게 처리해주는 것

```
vagrant ssh controller 
```

```shell
ssh -i .\vagrant\machines\controller\virtualbox\private_key vagrant@192.168.100.10
```



참고) 만약 ssh 키가 노출, 유출된 경우 서버쪽 키 파일을 모두 지우고 재부팅하면 키 파일들 새롭게 만들어짐

<br>

<br>

