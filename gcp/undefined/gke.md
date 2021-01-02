# GKE 뽀개기

## 1. GKE\(Google Kubernetes Engine\)란

GKE\(Google Kubernetes Engine\)는 Google Cloud Platform이 제공하는 Managed 서비스입니다.

## 2. GKE 시작하기

### Task1. GKE Cluster 생성하기

먼저 클러스터 생성을 위해서는 Google Kubernetes Engine API가 사용 설정이 필요합니다.  
GKE 메뉴에 접근하게 되면, 자동으로 enable 됩니다.

![GKE&#xD074;&#xB7EC;&#xC2A4;&#xD130; &#xC0DD;&#xC131;&#xD558;&#xAE30;](../../.gitbook/assets/image%20%284%29.png)

클러스터 생성화면에 들어가면, 3가지의 메뉴트리로 구성됩니다.

* **Cluster basis**   - 클러스터의 위치 및 버전 선택
* **Node Pools**
* **Cluster**

![](../../.gitbook/assets/image.png)

#### Cluster basics

**Location Type**

* **Zonal** -  Google Cloud의 데이터 센터에 master node가 하나의 Zone에만 존재하는 형태입니다.    따라서, Cluster master의 **고가용성\(High Availability\)**를 보장하지 않습니다. - 한국 리전은 3개의 Zone으로 구성되어 있습니다.
* **Regional** - 고가용성을 보장하기 위해 region 안에 zone 별로 하나씩 master node를 생성합니다.
* **Specify default node location** - Default node locations를 여러개 지정해서 multi-zonal cluster를 구성할 수도 있습니다.

**Master version**

* **Static version** - 버전을 고정으로 사용.
* **Release version** - Kubernetes 버전의 automatic upgrade를 지원받을 수 있습니다.
  * Rapid channel - 오픈 소스 Kubernetes가 GA\(General Availability\)된 뒤 몇 주 안에 릴리즈됩니다.
  * Regular channel - Rapid가 업데이트된 후 2~3개월 후에 릴리즈
  * Stable channel - Regular보다 다시 2~3월 후에 문제가 없다는 것이 확인된 후 릴리즈. \(상용환경을 구성 시 Stable Recommand\)

**Node Pools**

Node pool은 같은 종류의 노드의 집합을 의미합니다. EKS의 node group에 해당합니다.  
Node는 결국 GCE\(VM\) 이기 때문에 이미지 타입과 머신 타입 등을 설정하게 되어 있습니다.

**Node Pool details**

![](../../.gitbook/assets/image%20%283%29.png)

* Release version을 stable로 해놔서 Automation이 default로 enabled된 것을 확인할 수 있다.

**Surge Update**

Node의 Kubernetes version 업그레이드 시 node를 몇개까지 더 늘이고 줄일 수 있는지를 결정합니다.  
Max surge = 1 \(업그레이드 시 1개 node를 더 생성 함\)  
Max unavailable = 0\(사용 가능한 node 수가 업그레이드 전의 node 수보다 더 적게 되지는 않도록 함\)  
즉, 새 버전의 node 1개를 추가한 뒤 하나의 node를 종료하고, 다시 새 버전의 node 1개를 추가하기를 반복하면서 node를 업그레이드 하게 됩니다.



