# [Azure] Azure 기초 개념 및 실습



### 📌INDEX

- [리소스 그룹](#리소스-그룹)

- [공용 IP](#공용-ip)
- [사용자](#사용자)
- [가상 네트워크](#가상-네트워크)
- [가상 머신](#가상-머신)

<br>

<br>

## 리소스 그룹

[What is resource group?](https://docs.microsoft.com/ko-kr/azure/azure-resource-manager/management/manage-resource-groups-portal#what-is-a-resource-group)

가상 머신이나 가상 네트워크, 스토리지 계정과 같은 **리소스를 목적에 따라 논리적으로 그룹화한 것**(AWS에는 존재하지 않는 개념)

<br>

리소스와 시소스 그룹 간의 관계가 갖는 특성

- **리소스는 하나의 리소스 그룹에만 존재**해야함
- 리소스와 리소스 그룹의 이름은 변경할 수 없음
- **리로스 그룹과 리소스의 위치(지역)이 다를 수 있음** 
  - 단순히 어디서 관리할 것인가의 차이
  - 리소스 목록을 어디에서 관리할지는 상관 없음
- 한 리소스 그룹의 리소스가 다른 리소스 그룹의 리소스와 상호작용할 수 있음
- 한 리소스 그룹의 리소스를 다른 리소스 그룹으로 이동할 수 있음

미리 리소스를 만들어둔 뒤, 나중에 리소스 그룹에 배치할 수 있음

**계층 구조 : 리소스 그룹 ⊂ 그룹 ⊂ 구독**

<br>

**💻 실습: 리소스 그룹 만들기**

![image-20220426065154542](https://raw.githubusercontent.com/na3150/typora-img/main/img/image-20220426065154542.png)

![image-20220426065248430](https://raw.githubusercontent.com/na3150/typora-img/main/img/image-20220426065248430.png)

<br>

<br>

## 공용 IP

[공용 IP 주소](https://docs.microsoft.com/ko-kr/azure/virtual-network/ip-services/public-ip-addresses)

공용 IP 주소를 통해 인터넷 리소스가 Azure 리소스에 대한 인바운드와 통신할 수 있음

AWS의 elastic ip에 해당되는 것

<br>

**💻 실습: 공용 ip 주소 생성해보기**

[계층] - [전역] : (global) azure 전체를 의미

![image-20220426070014418](https://raw.githubusercontent.com/na3150/typora-img/main/img/image-20220426070014418.png)

리소스 그룹에서 리소스 추가된 것을 확인할 수 있음

![image-20220426070504050](https://raw.githubusercontent.com/na3150/typora-img/main/img/image-20220426070504050.png)

<br>

<br>

## 사용자

[Azure RBAC](https://docs.microsoft.com/ko-kr/azure/role-based-access-control/)

Azure RBAC란 Azure 리소스에 대한 세밀한 액세스 관리를 제공하는 시스템

[Azure 외부 게스트](https://docs.microsoft.com/ko-kr/azure/role-based-access-control/role-assignments-external-users)

**이메일 형태**로 게스트를 초대

외부 게스트가 필요한 경우

- 이메일 계정만 있는 외부 자영업자가 프로젝트에 대한 Azure 리소스에 액세스할 수 있도록 허용
- 외부 파트너가 특정 리소스 또는 전체 구독을 관리할 수 있도록 허용
- 고객 조직에 속하지 않은 지원 엔지니어(예: Microsoft 지원)가 문제 해결을 위해 일시적으로 고객의 Azure 리소스에 액세스할 수 있도록 허용

<br>

**게스트 사용자에게 역할 할당**

[리소스 그룹] - [액세스 제어(IAM)] - [역할 할당] 클릭 후 역할을 선택한 뒤 사용자, 그룹 또는 서비스 주체를 선택

![image-20220426071857080](https://raw.githubusercontent.com/na3150/typora-img/main/img/image-20220426071857080.png)

<br>

| 역할   | 설명                                                         |
| ------ | ------------------------------------------------------------ |
| 소유자 | Azure RBAC에서 역할을 할당하는 기능을 포함하여 모든 리소스를 관리할 수 있는 모든 권한을 부여 |
| 기여자 | 모든 리소스를 관리할 수 있는 모든 권한을 부여하지만, 역할 할당, 할당 관리 또는 이미지 갤러리 공유를 허용하지 않음 |
| 독자   | 모든 리소스를 볼 수 있지만 변경할 수는 없음                  |

<br>

<br>

## 가상 네트워크

[Azure Virtual Network란?](https://docs.microsoft.com/ko-kr/azure/virtual-network/virtual-networks-overview)

Azure Virtual Network(VNet)는 Azure의 **프라이빗 네트워크의 기본 구성 요소**로, Azure VM(Virtual Machines)과 같은 다양한 형식의 Azure 리소스가 서로, 인터넷 및 특정 온-프레미스 네트워크와 안전하게 통신할 수 있음

- Azure는 internet gateway가 자동으로 생성
- nat gateway가 없어도 인터넷 통신이 가능함(그러나 nat gateway를 사용하는 것을 권장함)

- 서브넷이 리전 전체에 걸쳐서 생성되기 때문에, 서브넷을 만들 때 별도의 영역을 지정하지 않음

<br>

**💻 실습: 가상 네트워크 생성하기**

![image-20220426072707737](https://raw.githubusercontent.com/na3150/typora-img/main/img/image-20220426072707737.png)

default 서브넷 삭제하고 서브넷 생성해보기

NAT 게이트웨이는 `없음`을 체크

![image-20220426072843160](https://raw.githubusercontent.com/na3150/typora-img/main/img/image-20220426072843160.png)

나머지는 default 설정으로 진행

생성 확인

![image-20220426073211552](https://raw.githubusercontent.com/na3150/typora-img/main/img/image-20220426073211552.png)

<br>

<br>

## 가상 머신

[Azure Virtual Machine](https://docs.microsoft.com/ko-kr/azure/virtual-machines/)

[Azure Linux Virtual Machine](https://docs.microsoft.com/ko-kr/azure/virtual-machines/linux/overview)

Azure Virtual Machines(VM)는 Azure에서 제공하는 여러 유형의 [확장성 있는 주문형 컴퓨팅 리소스](https://docs.microsoft.com/ko-KR/azure/architecture/guide/technology-choices/compute-decision-tree) 중 하나

한번이라도 vm을 만들면, 네트워크를 감시할 때 사용하기 위한 `NeetworkWatcherRG`  리소스 그룹이 자동으로 생성됨 => 특별히 관리할 필요는 없음

<br>

**💻 실습: 가상 머신 생성하기**

가상 머신 선택

![image-20220426073719340](https://raw.githubusercontent.com/na3150/typora-img/main/img/image-20220426073719340.png)

구독과 리소스 그룹 선택 후 가상 머신 이름 입력

![image-20220426073832047](https://raw.githubusercontent.com/na3150/typora-img/main/img/image-20220426073832047.png)

글쓴이는 기존 퍼블릭 키를 사용

AMI에 따라 사용자 이름이 정해져있는 AWS와 달리, **Azure는 사용자 이름을 사용자가 설정**할 수 있음

주의❗) 퍼블릭 키를 붙여넣을 때, 마지막에 사용자 지정하는 것은 제거해야함(예: `vagrant@controller`) => 붙이면, 그것이 계정이 됨

![image-20220426074026217](https://raw.githubusercontent.com/na3150/typora-img/main/img/image-20220426074026217.png)

가상 네트워크 선택

![image-20220426074622594](https://raw.githubusercontent.com/na3150/typora-img/main/img/image-20220426074622594.png)

나머지는 default 설정으로 두고, 접속확인

```shell
[vagrant@controller ~]$ ssh azureuser@[퍼블릭IP]
...
azureuser@test-vm:~$
```

<br>

**💻 실습: 암호 인증 방식의 프라이빗 가상 머신 생성하기**

참고)인증 형식에서 암호를 선택하기 전에 [SSH 공개 키] - [Azure에 저장된 기존 키 사용]을 선택해보자

바로 위 실습에서 기존 퍼블릭 키 사용 방식을 이용했음에도, **리소스가 남지 않는 것**을 확인할 수 있음

**Azure는 기존 퍼블릭 키를 사용하면 리소스가 만들어지지 않음**

![image-20220426075536752](https://raw.githubusercontent.com/na3150/typora-img/main/img/image-20220426075536752.png)

암호 인증 형식 선택 후 내용 입력

- Azure는 퍼블릭 클라우드 중 유일하게 패스워드 인증을 지원함

![image-20220426075203775](https://raw.githubusercontent.com/na3150/typora-img/main/img/image-20220426075203775.png)

공용 IP 없음 체크

![image-20220426075355666](https://raw.githubusercontent.com/na3150/typora-img/main/img/image-20220426075355666.png)

나머지는 default 설정으로 구성 후, 위 실습에서 생성한 인스턴스를 통해 연결 확인

```shell
azureuser@test-vm:~$ ssh azure@10.0.0.5
...
azure@10.0.0.5's password:
...
azure@test-vm2:~$
```

**NAT 게이트웨이를 사용하지 않았음에도 기본적으로 인터넷이 가능**한 것을 확인할 수 있음

- 그러나 보안상 NAT 게이트웨이를 사용하는 것이 좋음

```
azure@test-vm2:~$ curl ifconfig.me
20.41.114.245
```

<br>
**Bastion Host**

Azure에서는 Bastion Host를 관리형 서비스로 사용 가능함

**가상 네트워크를 만들 때 [보안] - [BastionHost] - [사용]을 체크해두면, 네트워크에 자동으로 Bastion Host가 배포**됨

<br>

`사용 안함`을 선택한 경우, 나중에 추가할 수 있음

![image-20220426080428728](https://raw.githubusercontent.com/na3150/typora-img/main/img/image-20220426080428728.png)

Bastion Host 생성 후, 연결할 VM을 선택할 수 있음

![image-20220426080845421](https://raw.githubusercontent.com/na3150/typora-img/main/img/image-20220426080845421.png)

VM 연결 시 설정한 인증으로 **웹에서 바로 Bastion Host를 통해 접속**할 수 있음

![image-20220426081200459](https://raw.githubusercontent.com/na3150/typora-img/main/img/image-20220426081200459.png)

![image-20220426081237819](https://raw.githubusercontent.com/na3150/typora-img/main/img/image-20220426081237819.png)