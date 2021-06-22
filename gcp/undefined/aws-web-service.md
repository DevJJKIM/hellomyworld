# AWS의 고가용성 Web Service 구성하기

## Overall

본 실습은  VPC 내 Private subnet들에 Auto Scaling Group을 이용하여 웹 서비스 인스턴스를 배포합니다.  
이를 통해 고가용성을 확보할 수 있는 인프라환경에 Web Browser를 통하여 Sample Web Page에 접근할 수 있도록 구성합니다.

![Web Service &#xCD5C;&#xC885; &#xC544;&#xD0A4;&#xD14D;&#xCC98;](../../.gitbook/assets/image%20%28118%29.png)

## Task 1. 네트워크 환경 구성

VPC Wizard를 통하여 Public Subnet 및 Private Subnet을 2개의 가용영역\(AZ-a, AZ-c\)에 각각 하나씩 생성하고, 하나의 Public Subnet에 NAT 게이트웨이를 구성합니다.   
그리고 라우팅 테이블을 설정하여 트래픽 흐름을 정의하는 작업을 진행합니다.

### 1. VPC 생성

 **Amazon Virtual Private Cloud\(Amazon VPC\)는 사용자가 정의한 가상 네트워크**로 On-premise 데이터 센터에서 운영하는 기존 네트워크와 매우 유사합니다.

A.  VPC Dashboard 좌측 메뉴에서 **Elastic IPs\(탄력적 IP\)** 를 클릭하고,  **Allocate Elastic IP Address\(새 주소 할당\)\)** 을 클릭 합니다. 

![](../../.gitbook/assets/image%20%28109%29.png)

B. NAT Gateway에 사용할 고정된 Public IP를 생성하기 위하여 우측 하단의 **Allocate\(할당\)** 을 클릭합니다.

![](../../.gitbook/assets/image%20%28110%29.png)

{% hint style="info" %}
이것은 VPC Wizard를 통하여 생성할 NAT Gateway에 할당하기 위하여 미리 생성하는 것입니다.

탄력적 IP\(Elastic IP\)는 사용자 계정\(Account\)에 고정적으로 할당되며, 인스턴스의 상태에 관계 없이 지속적으로 할당되는 IP를 의미하며, Public IP이므로 외부에서 접근이 가능한 IP입니다.
{% endhint %}

C. 사용자 계정\(Account\)에 할당된 새로운 탄력적 IP\(Elastic IP\)를 확인 할 수 있습니다.

![&#xD0C4;&#xB825;&#xC801; IP &#xC0DD;&#xC131;&#xACB0;&#xACFC;](../../.gitbook/assets/image%20%28115%29.png)

D.  **VPC Dashboard\(대시보드\)**에서 **Launch VPC Wizard\(VPC 마법사 시작\)** 을 클릭하고,  2번째 Option인 **퍼블릭 및 프라이빗 서브넷이 있는 VPC** 를 선택합니다.

![](../../.gitbook/assets/image%20%28114%29.png)

{% hint style="info" %}
VPC 마법사가 화면상의 그림과 같이 Private Subnet에 있는 EC2 인스턴스가 Internet에 Access할 수 있도록 NAT Gateway를 자동으로 생성합니다.
{% endhint %}

E.  **IPv4 CIDR block, VPC name, Public subnet’s IPv4 CIDR, Availability Zone, Public subnet name, Private subnet’s IPv4 CIDR, Availability Zone, Private subnet name** 을 입력 합니다.  
NAT Gateway에 할당할 탄력적 IP 지정을 위하여 **Elastic IP Allocation ID** 를 선택합니다. NAT Gateway를 위하여 생성한 탄력적 IP\(Elastic IP\)의 할당된 ID\(Allocation ID\)를 선택합니다.

| Key | Value |
| :--- | :--- |
| IPv4 CIDR block | 10.0.0.0/16 |
| VPC name | VPC-Lab |
| Public subnet’s IPv4 CIDR | 10.0.10.0/24 |
| Availability Zone | ap-northeast-2a |
| Public subnet name | Public subnet A |
| Private subnet’s IPv4 CIDR | 10.0.100.0/24 |
| Availability Zone | ap-northeast-2a |
| Private subnet name | Private subnet A |

![](../../.gitbook/assets/image%20%28119%29.png)

F. 생성결

![&#xC544;&#xD0A4;&#xD14D;&#xCC98; &#xC0DD;&#xC131; &#xACB0;&#xACFC;](https://documents.lucid.app/documents/50fa5fc2-60ef-406f-aa97-443bd4d4258b/pages/xAeOPzHvTdJi?a=337&x=220&y=239&w=1320&h=902&store=1&accept=image%2F*&auth=LCA%202efa8d0853e1a310ec1049f5e0cfc8245005153c-ts%3D1624371116)

### 2. 서브넷 추가 생성

고가용성의 네트워크 서비스 구성을 위해 퍼블릭, 프라이빗 서브넷을 하나씩 더 추가 합니다.

A.  VPC 콘솔 왼쪽 화면의 **Subnet** 을 클릭하고, 상단의 **Create subnet** 버튼 클릭하고 아래 정보들을 입력하여 Puiblic subnet C를 성합니다.

| Key | Value |
| :--- | :--- |
| VPC |  VPC란을 클릭해서 `VPC-Lab` 태그가 설정된 VPC를 선택합니다. |
| Subnet name | Public subnet C |
| Availability Zone | ap-northeast-2c |
| IPv4 CIDR block | 10.0.20.0/24 |

![](../../.gitbook/assets/image%20%28112%29.png)

B.  프라이빗 서브넷도 하나 더 생성하기 위하여, **Create Subnet** 버튼을 클릭합니다. 그리고 아래의 값으로 지정하고, **Create** 버튼을 눌러 Private subnet C를 성합니다.

| Key | Value |
| :--- | :--- |
| VPC |  VPC란을 클릭해서 `VPC-Lab` 태그가 설정된 VPC를 선택합니다. |
| Subnet name | Private subnet C |
| Availability Zone | ap-northeast-2c |
| IPv4 CIDR block | 10.0.200.0/24 |

C. 생성결과

![&#xC544;&#xD0A4;&#xD14D;&#xCC98; &#xC0DD;&#xC131; &#xACB0;&#xACFC;](https://documents.lucid.app/documents/50fa5fc2-60ef-406f-aa97-443bd4d4258b/pages/0_0?a=354&x=220&y=239&w=1320&h=902&store=1&accept=image%2F*&auth=LCA%20b59dcbfb551109cbe23d032c1bd36ad664209e04-ts%3D1624371116)

### 3. 라우팅 테이블 수정

라우팅 테이블을 변경하거나 해당 라우팅 테이블에 Subnet을 연결하는 작업을 할 수 있습니다.

A. 인터넷 통신이 가능한 퍼블릭 서브넷의 라우팅 테이블을 연결합니다.

![](../../.gitbook/assets/image%20%28116%29.png)

* 목적지가 _**10.0.0.0/16\(VPC 내부\)**_ 인 경우 **로컬 게이트웨이\(local\)** 로 트래픽을 라우팅 합니다.
* 모든 목적지\(0.0.0.0/0\)의 트래픽을 **인터넷 게이트웨이\(igw-xxx\)** 로 라우팅 합니다.
* **인터넷과 바로 통신** 이 가능한 라우팅 구성이므로, **퍼블릭 서브넷** 들에 적용되어야 하는 라우팅 테이블을 확인할 수 있습니다.

B.  해당 라우트 테이블 조건이 연결되어 있는 서브넷을 확인하기 위하여 **서브넷 연결** 탭을 선택하고, **서브넷 연결 편집**을  클릭합니다.

![](../../.gitbook/assets/image%20%28117%29.png)

* `Public subnet C` 역시 해당 라우트 테이블의 규칙에 따라 **0.0.0.0/0** 으로의 트래픽을 인터넷 게이트웨이로 보내야 합니다.
* **Edit subnet associations** 을 눌러 `Public subnet C` 도 해당 라우트 테이블에 연결합니다.

