# EKS 뽀개기

### 1. EKS\(Elastic Kubernetes Service\)란

EKS\(Elastic Kubernetes Service\)는 AWS에서 Kubernetes를 실행하는 데 사용할 수 있는 Managed 서비스입니다.

EKS는 2가지 방법으로 클러스터를 생성할 때 사용할 수 있습니다.

* **eksctl** : EKS에서 클러스터를 생성하고 관리하기위한 간단한 CLI 도구로 [Weaveworks](https://www.weave.works/)가 제공하는 공식 AWS CloudFormation 템플릿 기반으로 작동하여 단 하나의 명령으로 몇 분 만에 기본 클러스터를 만들 수 있습니다.
* **AWS 콘솔 및 AWS CLI** : Amazon EKS 클러스터에 필요한 각 리소스를 수동으로 생성합니다.

#### eksctl을 활용하여 간단히 EKS 클러스터를 구성해보고, 애플리케이션을 배포해보도록 하겠습니다.

### 2. EKS 시작하기

### Task1. 환경 구성하기

시작하기 앞ㅇ서 eksctl을 사용하기 위해서는 다음과 같은 Tool을 설치하여 합니다.  
해당 설치 트랙은 Linux기반으로 진행되오니 Window10 환경의 유저분들은 아래 링크를 참조하시어 Linux환경을 설치하시 바랍니다.  [https://jjnomad.tistory.com/2](https://jjnomad.tistory.com/2)  
또는, **Cloud9 AWS WEB IDE**를 활용할 수도 있습니다. 콘솔에서 Cloud9 확인 할 수 있습니다.

### awscli

### eksctl

### kubectl

### 3. EKS 클러스터 생성하기

클러스터 생성하기 전 인스턴스에 접근하기 위한 키를 생성합니다. 해당 키를 통해 향후 인스턴스 ssh접근 시 활용하게 됩니다.

퍼블릭 key생성을 위해 `ssh-keygen`을 입력하고, 파일이름을 eks-demo로 정하겠습니다.   
그리고 enter를 두번 칩니다. 아래와 같이 결과가 나오는 것을 확인 할 수 있습니다.   
`ls` 명령어로 생성된 파일을 확인 합니다. 그럼 .pub으로 끝나는 공개키와, 비밀키를 확인 할 수 있습니다.

![](../../.gitbook/assets/image%20%2827%29.png)

이제 아래코드를 실행하면 EKS 클러스터가 생성해보도록 하겠습니다.

```text
eksctl create cluster \
--name my-cluster \
--version 1.18 \
--region us-west-2 \
--nodegroup-name linux-nodes \
--nodes 1 \
--nodes-min 1 \
--nodes-max 1 \
--ssh-access \
--ssh-public-key eks-demo.pub \
--managed \
--node-type t3.medium
```

eksctl은 flags를 활용하여 옵션값을 지정할 수 있습니다. 지정된 옵션은 다음과 같습니다.

* 클러스터이름 : my-cluster
* 쿠버네티스버전 : 1.18
* 리전 : us-west-2 미국서부\(오레곤\)
* 노드그룹이름 : linux-nodes
* node 개수 : 1
* 최소개수 : 1, 최대개수 1
* 노드타입 : t3.medium
* ssh-public-key : 방금전 생성한 퍼블릭키를 사용합니다.

### 4. CloudFormation 살펴보기

eksctl을 사용하면 CloudFormation에서 클러스터 생성에 필요한 여러 자원들을 자동으로 설치하여 줍니다.



### **5. EKS 살펴보기**

