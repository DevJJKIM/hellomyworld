# AWS의 고가용성 Web Service 구성하기

## Overall

본 실습은  VPC 내 Private subnet들에 Auto Scaling Group을 이용하여 웹 서비스 인스턴스를 배포합니다.  
이를 통해 고가용성을 확보할 수 있는 인프라환경에 Web Browser를 통하여 Sample Web Page에 접근할 수 있도록 구성합니다.

![Web Service &#xCD5C;&#xC885; &#xC544;&#xD0A4;&#xD14D;&#xCC98;](../../.gitbook/assets/image%20%28152%29.png)

## Task 1. 네트워크 환경 구성

VPC Wizard를 통하여 Public Subnet 및 Private Subnet을 2개의 가용영역\(AZ-a, AZ-c\)에 각각 하나씩 생성하고, 하나의 Public Subnet에 NAT 게이트웨이를 구성합니다.   
그리고 라우팅 테이블을 설정하여 트래픽 흐름을 정의하는 작업을 진행합니다.

### 1. VPC 생성

 **Amazon Virtual Private Cloud\(Amazon VPC\)는 사용자가 정의한 가상 네트워크**로 On-premise 데이터 센터에서 운영하는 기존 네트워크와 매우 유사합니다.

A.  VPC Dashboard 좌측 메뉴에서 **Elastic IPs\(탄력적 IP\)** 를 클릭하고,  **Allocate Elastic IP Address\(새 주소 할당\)\)** 을 클릭 합니다. 

![](../../.gitbook/assets/image%20%28110%29.png)

B. NAT Gateway에 사용할 고정된 Public IP를 생성하기 위하여 우측 하단의 **Allocate\(할당\)** 을 클릭합니다.

![](../../.gitbook/assets/image%20%28116%29.png)

{% hint style="info" %}
이것은 VPC Wizard를 통하여 생성할 NAT Gateway에 할당하기 위하여 미리 생성하는 것입니다.

탄력적 IP\(Elastic IP\)는 사용자 계정\(Account\)에 고정적으로 할당되며, 인스턴스의 상태에 관계 없이 지속적으로 할당되는 IP를 의미하며, Public IP이므로 외부에서 접근이 가능한 IP입니다.
{% endhint %}

C. 사용자 계정\(Account\)에 할당된 새로운 탄력적 IP\(Elastic IP\)를 확인 할 수 있습니다.

![&#xD0C4;&#xB825;&#xC801; IP &#xC0DD;&#xC131;&#xACB0;&#xACFC;](../../.gitbook/assets/image%20%28145%29.png)

D.  **VPC Dashboard\(대시보드\)**에서 **Launch VPC Wizard\(VPC 마법사 시작\)** 을 클릭하고,  2번째 Option인 **퍼블릭 및 프라이빗 서브넷이 있는 VPC** 를 선택합니다.

![](../../.gitbook/assets/image%20%28133%29.png)

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

![](../../.gitbook/assets/image%20%28160%29.png)

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

![](../../.gitbook/assets/image%20%28120%29.png)

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

![](../../.gitbook/assets/image%20%28146%29.png)

* 목적지가 _**10.0.0.0/16\(VPC 내부\)**_ 인 경우 **로컬 게이트웨이\(local\)** 로 트래픽을 라우팅 합니다.
* 모든 목적지\(0.0.0.0/0\)의 트래픽을 **인터넷 게이트웨이\(igw-xxx\)** 로 라우팅 합니다.
* **인터넷과 바로 통신** 이 가능한 라우팅 구성이므로, **퍼블릭 서브넷** 들에 적용되어야 하는 라우팅 테이블을 확인할 수 있습니다.

B.  해당 라우트 테이블 조건이 연결되어 있는 서브넷을 확인하기 위하여 **서브넷 연결** 탭을 선택하고, **서브넷 연결 편집**을  클릭합니다.

![](../../.gitbook/assets/image%20%28149%29.png)

* `Public subnet C` 역시 해당 라우트 테이블의 규칙에 따라 **0.0.0.0/0** 으로의 트래픽을 인터넷 게이트웨이로 보내야 합니다.
* **Edit subnet associations** 을 눌러 `Public subnet C` 도 해당 라우트 테이블에 연결합니다.

C.  혼선을 방지하기 위해, 라우트 테이블의 **Name** 필드를 눌러 `Public route` 이름을 붙여 줍니다.

![](../../.gitbook/assets/image%20%28115%29.png)

D. 프라이빗 서브넷들을 위한 라우 테이블도 퍼블릭 서브넷과 같이 라우팅 테이블을 설정합니다.

![](../../.gitbook/assets/image%20%28113%29.png)

E.  **Name** 필드를 눌러 `Private route` 라고 라우트 테이블의 이름을 지정해 줍니다.

F. 현재까지 생성 결과

![](https://documents.lucid.app/documents/50fa5fc2-60ef-406f-aa97-443bd4d4258b/pages/0_0?a=383&x=220&y=239&w=1320&h=902&store=1&accept=image%2F*&auth=LCA%20d511d5403f15e15b516f192169b1b0b5dc66193b-ts%3D1624371116)

## Task 2. Web Service 구성

### 1. Web Server 인스턴스 생성

A.  AWS Management Console 좌측 상단의 _**Services**_ 메뉴를 클릭하고 EC2 를 선택하고,  **EC2 Dashboard**에서  **인스턴스 시작**을 선택합니다.

![](../../.gitbook/assets/image%20%28117%29.png)

B. _**\[단계 1: Amazon Machine Image\(AMI\) 선택\]**_에서 `Amazon Linux 2 AMI`를 선택하십시오.

![](../../.gitbook/assets/image%20%28143%29.png)

C. 인스턴스 타입은 `t2.micro` 를 선택하고 ![](../../.gitbook/assets/image%20%28130%29.png) 를 선택합니다.

D. _**\[단계 3: 인스턴스 세부 정보 구성\]**_에서 아래의 정보들을 입력합니다.

| Key | Value |
| :--- | :--- |
| Network |  `VPC-Lab` 태그가 붙어 있는 VPC를 선택합니다. |
| Subnet |  `Public subnet A`를 찾아 선택합니다. |
| Auto-assign Public IP | 활성화\(Enable\) |

![](../../.gitbook/assets/image%20%28144%29.png)

E. 고급 세부정보에 Sample webpage를 위한 스크립트를 적용 합니다.

![](../../.gitbook/assets/image%20%28111%29.png)

```text
#include https://kr-id-general.workshop.aws/sh/start.sh
```

F. 태그를 추가하여 줍니다.

| Key | Value |
| :--- | :--- |
| Name | Web server for custom AMI |

![](../../.gitbook/assets/image%20%28139%29.png)

G. 보안 그룹을 생성합니다. 보안 그룹은 방화벽 정책으로 허용하고자 하는 프로토콜과 주소를 지정하게 됩니다.

![](../../.gitbook/assets/image%20%28155%29.png)

H. key pair 생성은 생략하고 진행합니다.

![](../../.gitbook/assets/image%20%28162%29.png)

I.  새로운 웹 브라우저 탭을 열고 URL 주소 입력하는 영역에, EC2 인스턴스의 **퍼블릭 DNS 또는 IPv4 퍼블릭 IP를 입력**하십시오. 아래와 같이 페이지가 보여지면 웹 서버 인스턴스가 정상적으로 구성된 것입니다.

![](../../.gitbook/assets/image%20%28163%29.png)

![](../../.gitbook/assets/image%20%28154%29.png)

### 2. 커스텀 AMI 생성

생성된 인스턴스를 이용하여 이미지를 만들고, 추후 인스턴스 생성 시 이 이미지를 사용할 수 있습니다. 이를 커스텀 AMI라고 부릅니다.

A.  EC2 콘솔에서 기 생성한 인스턴스를 선택하고, **Actions** -&gt; **Image and templates** -&gt; **Create Image** 를 선택합니다. 아래와 같이 입력하고 이미지를 생성합니다.

![](../../.gitbook/assets/image%20%28134%29.png)

B. AMI 생성 결과를 확인 합니다.  **Available** 상태가 확인 되면 정상입니다.

![](../../.gitbook/assets/image%20%28153%29.png)

{% hint style="info" %}
 _**방금 생성한 EC2 인스턴스를 이용하여 오토스케일링에 사용하기 위하여 커스텀 AMI\(골든 이미지\) 생성을 완료하였습니다.**_ 따라서, 현재 생성한 EC2 인스턴스는 더 이상 필요가 없어 졌으므로, 종료\(Termination\)합니다.
{% endhint %}

## Task 3. 오토 스케일링 웹 서비스 배포

### 1. Application Load Balancer 구성

AWS Elastic Load Balancing은 Application Load Balancer, Network Load Balancer, Classic Load Balancer의 세 가지 유형의 로드 밸런서를 지원합니다.

A.  **EC2 관리 콘솔**에서 **Load Balancing** 항목 아래 **Load Balancers** 를 클릭하고, 가운데 위의 **Create Load Balancer** 를 클릭합니다.

![](../../.gitbook/assets/image%20%28140%29.png)

B.  **Application Load Balance** 선택합니다.

![](../../.gitbook/assets/image%20%28126%29.png)

C. 환경설정 정보들을 입력하니다.

| Key | Value |
| :--- | :--- |
| Name | Web-ALB |
| VPC   | VPC-Lab |
|  Availability Zones | ap-northeast-2a\(Public subnet A\), ap-northeast-2c\(Public subnet C\) |

![](../../.gitbook/assets/image%20%28161%29.png)

![](../../.gitbook/assets/image%20%28151%29.png)

D. _**\[3단계: 보안 그룹 구성\]**_ 으로 넘어가서 새로운 보안 그룹을 생성합니다.  
 **Security group name** 에 `Web-ALB-SG` 를 입력하고 **Type** 드롭다운 메뉴에서 `HTTP`를 찾아 선택합니다. 

![](../../.gitbook/assets/image%20%28125%29.png)

E. \[4단계: 라우팅 구성\] 에서 위에서 설정한 리스너가 트래픽을 넘겨줄 대상 그룹을 설정합니다.  
아직 트래픽을 받아 처리해 줄 인스턴스가 없으므로  **Name**만 `Web-TG` 로 만들고 다음 단계로 넘어갑니다.

![](../../.gitbook/assets/image%20%28128%29.png)

F. 생성을 클릭하면 아래와 같이 생성결과를 확인할 수 있습니다.

![](../../.gitbook/assets/image%20%28150%29.png)

### 2. 보안 그룹 생성

생성되는 인스턴스들이 사용할 보안 그룹을 만듭니다.

A.  EC2 콘솔에서  **Security Groups**를 선택하고, **Create Security Group** 을 클릭합니다.

![](../../.gitbook/assets/image%20%28166%29.png)

B. 보안 그룹 생성을 위한 정보들을 입력합니다.

| Key | Value |
| :--- | :--- |
| Security Group Name | ASG-Web-Inst-SG |
| Description | HTTP Allow |
| VPC | VPC-Lab |

![](../../.gitbook/assets/image%20%28156%29.png)

C.  **ALB에서 들어오는 HTTP 트래픽만 받도록** Inbound Rule을 수정합니다.

![](../../.gitbook/assets/image%20%28118%29.png)

| Key | Value |
| :--- | :--- |
| Type | HTTP |
| Source | Web-ALB-SG \(클릭하고 나면 sg-xxxx 형태로 변경됨\) |

D. 인터넷에서 ALB를 통해 인스턴스로 들어오는 HTTP 연결\(TCP 80\)에 대해서만 트래픽을 허용하는 보안 그룹을 생성하였습니다.

![](../../.gitbook/assets/image%20%28137%29.png)

### 3. 시작 템플릿 생성

 Auto Scaling Group에서 시작할 Amazon EC2 인스턴스를 구성하려면 **시작 템플릿을** 사용 합니다.  
**시작 템플릿**은 한 리소스 내의 모든 시작 파라미터를 한꺼번에 구성하도록 되어 있어 인스턴스 생성에 필요한 단계 수를 줄일 수 있습니다.

A.  EC2 콘솔에서  **Launch Templates**의 **Create Launch Template**을 클릭합니다.

![](../../.gitbook/assets/image%20%28124%29.png)

B.  먼저 **Launch template name** 과 **Template version description** 을 아래와 같이 설정하고, Auto Scaling guidance의 **Provide guidance** 항목의 _**체크박스를 선택**_ 합니다. 이 체크박스를 선택하여 생성하는 템플릿이 Amazon EC2 Auto Scaling에서 활용되도록 설정합니다.

| Key | Value |
| :--- | :--- |
| Launch template name | Web |
| Template version description | Immersion Day Web Instances Template – Web only |
| Auto Scaling guidance |  **Provide guidance to help me set up a template that I can use with EC2 Auto Scaling** 체크 박스 클릭 |

![](../../.gitbook/assets/image%20%28147%29.png)

C.  **Amazon Machine Image\(AMI\)** 란에는 이전 EC2 실습에서 만든 AMI\(`Web Server v1`\)를 찾아 설정합니다. 인스턴스 타입은 `t2.micro`를 선택합니다.

![](../../.gitbook/assets/image%20%28129%29.png)

D. 네트워크 설정에서 기존에 만들었던 `ASG-Web-Inst-SG`를 선택합니다.

![](../../.gitbook/assets/image%20%28138%29.png)

E. Tag에는 아래와 같이 입력합니다.

![](../../.gitbook/assets/image%20%28136%29.png)

F. 시작템플릿이 정상 생성되었습니다.

![](../../.gitbook/assets/image%20%28165%29.png)

### 4. Auto Scaling Group 구성

![](../../.gitbook/assets/image%20%28158%29.png)

A. Auto Scaling 그룹의 이름을 지정합니다. `Web-ASG` 으로 이름을 지정하고, 템플릿은 방금 만든 Web을 지정합니다.

![](../../.gitbook/assets/image%20%28122%29.png)

B. 네트워크는 기존의 VPC와 Private Subnet으로 설정하여 인스턴스의 위치를 결정하여 줍니다.

![](../../.gitbook/assets/image%20%28112%29.png)

C. 로드 밸러싱은 기존 로드밸런서를 선택하고, 모니터링을 위해 CloudWatch로 지표 수집을 활성화 시킵니다.

![](../../.gitbook/assets/image%20%28148%29.png)

![](../../.gitbook/assets/image%20%28159%29.png)

D. Auto-scale의 그룹 크기를 설정합니다. 인스턴스 수를 평상시 2개로 유지하고, 정책에 따라 최소 2개, 최대 4개까지의 스케일링을 허용합니다.

![](../../.gitbook/assets/image%20%28114%29.png)

E. CPU 평균 사용률이 전체 30%가 유지될 수 있도록 인스턴스 수를 조정하는 정책을 입력합니다.

![](../../.gitbook/assets/image%20%28131%29.png)

F. 태그단계는  **Key**에 `Name`을, **Value**에 `ASG-Web-Instance`를 입력한 후 **다음**을 클릭하고, Auto-Scaling그룹을 생성합니다.

![](../../.gitbook/assets/image%20%28127%29.png)

G.Auto Scaling 그룹을 통해 생성된 인스턴스들은 EC2 인스턴스 메뉴에서도 확인할 수 있습니다.

![](../../.gitbook/assets/image%20%28123%29.png)

H. 현재까지의 아키텍처 구성

![](https://documents.lucid.app/documents/50fa5fc2-60ef-406f-aa97-443bd4d4258b/pages/0_0?a=539&x=220&y=239&w=1320&h=902&store=1&accept=image%2F*&auth=LCA%20082a11a218ca68b45fbc92e6b4b410fec45e97cc-ts%3D1624371116)

## Task 4. 웹서비스 확인 및 테스트

### 1. 웹 서비스 및 로드 밸런서 동작 확인

Application Load Balancer를 통해 웹서비스에 접속해봅니다.

![](../../.gitbook/assets/image%20%28141%29.png)



### 2. Auto Scaling Test

웹서비스의 기능을 활용하여 부하 발생시 인스턴스가 새로 생기는지 확인 합니다.

A.  상기 웹 페이지에서 **LOAD TEST** 메뉴를 클릭합니다. 화면이 바뀌고 가해진 부하가 보입니다. 

![](../../.gitbook/assets/image%20%28132%29.png)

B.  EC2 콘솔의 좌측 메뉴에서 **Auto Scaling Groups**에 들어가, **Monitoring** 탭을 누릅니다. 아래 **Enabled metrics**에서 **EC2**를 누르고, 우측 타임프레임을 **1시간**으로 설정해 줍니다. 이후 잠시 기다리면 **CPU Utilization \(Percent\)** 그래프가 변화하는 것을 보실 수 있습니다.

![](../../.gitbook/assets/image%20%28135%29.png)



C.  5분\(300초\) 정도 기다린 후 **Activity** 탭을 눌러 보면, 조정 정책에 따라 EC2 인스턴스를 추가 배치하는 것을 보실 수 있습니다.

![](../../.gitbook/assets/image%20%28142%29.png)

D.  **Instance** 탭을 클릭하, 두 개의 인스턴스가 추가적으로 생겨나 총 4개의 인스턴스가 동작중인 것을 볼 수 있습니다.

![](../../.gitbook/assets/image%20%28157%29.png)

## Reference

[https://general-immersionday.workshop.aws/ko/network.html](https://general-immersionday.workshop.aws/ko/network.html)

