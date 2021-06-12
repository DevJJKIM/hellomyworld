# AWS 서버리스 웹 애플리케이션 구축하기

## Overall

AWS의 서버리스 제품인 [AWS Lambda](https://aws.amazon.com/lambda/), [Amazon API Gateway](https://aws.amazon.com/api-gateway/), [Amazon DynamoDB](https://aws.amazon.com/dynamodb/), [Amazon Cognito](https://aws.amazon.com/cognito/), [AWS Amplify Console](https://aws.amazon.com/amplify/console/)활용하여 웹페이지, API인증, Back-end, 데이터 저장소를 연결하는 간단한 서버리스 웹 애플리케이션을 생성하여 보겠습니다.

예상 소요시간은 **약 1시간** 입니다.

실습에서 활용되는 소스는 모두 AWS에서 제공하는 Sample 소스를 활용합니다.  
모든 실습은 **미국 서부\(오레곤\) us-west-2**에서 진행하고, 다른 리전에서 진행해도 무방합니다.

![&#xC560;&#xD50C;&#xB9AC;&#xCF00;&#xC774;&#xC158; &#xC544;&#xD0A4;&#xD14D;&#xCC98; &#xAD6C;&#xC131;&#xB3C4;](../../.gitbook/assets/image%20%2864%29.png)

## 제품소개

### **1. AWS Amplify**

## Task1. 정적 웹사이트 호스팅

📌브라우저에 로드되는 정적 웹 리소스를 호스팅하기 위해 AWS Amplify를 활용하겠습니다.

![AWS Amplify &#xC5F0;&#xB3D9; &#xC2DC;&#xB098;&#xB9AC;&#xC624;](../../.gitbook/assets/image%20%2842%29.png)

### 1. 소스저장소 생성\(Repository\)

📌소스 코드를 관리를 위해서  [AWS CodeCommit](https://aws.amazon.com/codecommit) 또는 [GitHub](http://github.com/) 이 이용 가능하나, 실습에서는 GitHub을 사용하도록 하겠습니다.

A. Github에서 Private Repository로 저장소를 생성하겠습니다. Repository 이름은 `wildrydes-site`으로 설정합니다.

![](../../.gitbook/assets/image%20%2858%29.png)

B. 생성된 Git Repository의 주소를 로컬PC로 클론합니다.  
Private 저장소로 설정하였으므로 USER\_ID와 PW를 입력 합니다.

```
git clone https://github.com/[USER_ID]/wildrydes-site.git
```

C. 클론한 디렉토리로 이동후 샘플소스를 받아옵니다.

```bash
cd wildrydes-site/
aws s3 cp s3://wildrydes-us-east-1/WebApplication/1_StaticWebHosting/website ./ --recursive
```

D. 저장된 소스파일을 Remote 저장소로 업로드합니다.

```bash
#git 파일 커밋하기
git add .
git commit -m "first commit"
git push
```

E. Push가 완료되 아래와 같이 결과를 확인할수 있습니다.

![](../../.gitbook/assets/image%20%2854%29.png)

### 2. AWS Amplify로 웹 호스팅 활성

\*\*\*\*📌 AWS Amplify를 사용하여 방금 Commit한 웹사이트를 배포하도록 하겠습니다.

A. Amplify Console에서 웹사이트 배포를 위해 Deliver의 ![](../../.gitbook/assets/image%20%2852%29.png)를 클릭합니다.

![](../../.gitbook/assets/image%20%2859%29.png)

B. GitHub를 Repository로 활용하고 있으므로, GitHub을 클릭 후 Authorization 승인하여 줍니다.  
     이용가능 Repository는 Github외에도 다양한 저장소를 이용할 수 있습니다.

![](../../.gitbook/assets/image%20%2850%29.png)

C. 권한 승인 후 Commit한 Repository를 선택 후 ![](../../.gitbook/assets/image%20%2860%29.png) 을 클릭합니다.  
     브랜치는 master 브랜치를 선택합니다.

![](../../.gitbook/assets/image%20%2843%29.png)

E. "빌드 설정 구성"페이지에서 모든 기본 값을 그대로 두고 ![](../../.gitbook/assets/image%20%2860%29.png) 을 선택합니다.

F. "검토"페이지에서 ![](../../.gitbook/assets/image%20%2840%29.png) 를 선택합니다

G. 필요한 리소스를 생성하고 코드를 배포하는 데 몇 분 정도 걸립니다.

H. 사이트 이미지를 클릭하여 배포 결과를 확인합니다.  
\(개가 말위에 타고있는 모습을 확인하시면 정상입니다.😀 \)

![](../../.gitbook/assets/image%20%2863%29.png)

I. master 링크를 클릭하면 빌드 및 배포에 대 세부 정보와 다양한 장치에서 앱의 스크린 샷이 표시됩니다.

![](../../.gitbook/assets/image%20%2857%29.png)

### 3. 사이트 수정

📌 연결된 리포지토리의 변경 사항을 기록하여 AWS Amplify에서 자동 감지하여 재배포하는지 확인해 보겠습니다.

A. `index.html` 페이지를 열고 제목을 다음과 같이 수정합니다: `Wild Rydes - Rydes of the Future!`

B. 수정한 파일을 저장하고, Remote 저장소로 Push 합니다.

```bash
git add index.html
git commit -m "updated index.html"
git push
```

소스 변경을 감지하면 아래와 같이 자동으로 다시 빌드 배포를 시작합니다.

![](../../.gitbook/assets/image%20%2869%29.png)

C. 완료되면 Wild Rydes 사이트를 다시 열고 수정한 제목을 확인합니다.

![](../../.gitbook/assets/image%20%2867%29.png)

## Task2. 사용자관리

📌 Amazon Cognito 를 활용하여 사용자를 풀을 만들어 사용자로 등록하고, 이메일 주소를 확인하고, 사이트에 로그인할 수 있는 페이지를 배포해 보겠습니다.

📌 사용자가 등록 신청을 제출하면 Amazon Cognito에서 확인 코드가 담긴 확인 이메일을 해당 주소로 보내 줍니다. 사용자는 계정 확인을 위해 사이트로 돌아가서 자신의 이메일 주소와 이메일로 받은 확인 코드를 입력합니다. 

{% hint style="info" %}
 Amazon Cognito에는 두 가지 사용자 인증 메커니즘이 있습니다. Cognito 사용자 풀을 사용하여 애플리케이션에 등록 및 로그인 기능을 추가하거나, Cognito Identity Pools를 사용하여 Facebook, Twitter, Amazon 같은 소셜 자격 증명 공급자를 통해 사용자를 인증하거나 SAML 자격 증명 솔루션 또는 자체 자격 증명 시스템을 사용할 수 있습니다. 이 모듈에서는 제시된 등록 및 로그인 페이지의 백엔드로 사용자 풀을 사용하겠습니다.
{% endhint %}

### 1. Amazon Cognito 사용자 풀 생

A. Console에서 Cognito를 검색하여 클릭한 후, **사용자 풀 관리**를 선택합니다.

![](../../.gitbook/assets/image%20%2849%29.png)

B. **사용자 풀 생성**을 선택 후 풀의 이름은 `WildRydes`으로 입력하고 **기본값 검토**를 진행합니다.

![](../../.gitbook/assets/image%20%2847%29.png)

C. 검토 페이지에서 **풀 생성**을 클릭합니다. \(여기서 인증관련 다양한 옵션을 선택할 수 있습니다.\)

![](../../.gitbook/assets/image%20%2861%29.png)

D. 이렇게 많들어진 풀ID와 ARN은 으로 활용 됩니다.

![](../../.gitbook/assets/image%20%2846%29.png)

### 2. 사용자 풀에 앱 추가

📌 사용자 풀에 접속할 앱 클라이언트를 지정해 줍니다.

A.  탐색 모음의 왼쪽 **일반 설정** 섹션에 있는 **앱 클라이언트**를 선택하고 **앱 클라이언트 추가**​를 선택합니다.

![](../../.gitbook/assets/image%20%2851%29.png)

B. 앱 클라이언트 이름은 WildRydesWebApp 으로 입력하고, 보안키 생성은 체크를 해제한 후 **앱 클라이언트 생성**을 선택합니다.​

![](../../.gitbook/assets/image%20%2868%29.png)

C. 사용자 풀에 액세스할 수 있는 고유한 ID와 선택적 보안키를 받게 됩니다.

![](../../.gitbook/assets/image%20%2855%29.png)

### 3. 웹 사이트 Config 업데이트

📌 /js/config.js 파일에는 사용자 풀 ID, 앱 클라이언트 ID 및 리전에 대한 설정이 들어 있습니다. 이전 단계에서 사용자 풀의 정보로 업데이트 하겠습니다.

A. 텍스트 편집기로 `wild-ryde-site/js/config.js`를 열고, 이전에 생성했던 정보들로 풀ID와 클라이언트ID를 업데이트하여 줍니다.

```bash
window._config = {
    cognito: {
        userPoolId: 'us-west-2_KnjHFuijS', // Pool ID
        userPoolClientId: '6c8t2ml5tsdopi5ofg3t4i6hfp', // 앱 클라이언트 ID
        region: 'us-west-2' // e.g. us-east-2
    },
    api: {
        invokeUrl: ''
    }};
```

B. Remote 저장소로 Push하여 줍니다.

```bash
git add config.js
git commit -m "updated config.js"
git push
```

### 4. 구현 테스트

A.  **Giddy Up!\(가자!\)** 버튼을 선택하고, 가입을 진행합니다.

![](../../.gitbook/assets/image%20%2856%29.png)

{% hint style="success" %}
Cognito의 Default 설정때문에 최소한 대문자 하나, 숫자 하나, 특수문자 하나가 포함된 암호를 선택하여야 합니다.
{% endhint %}

B. 입력한 이메일 주소에서 Verification Code를 입력합니다.

![](../../.gitbook/assets/image%20%2845%29.png)

C. 입력이 정상적으로 완료되면 Pop-up이 뜨고, Login page로 redirection 됩니다.

![](../../.gitbook/assets/image%20%2853%29.png)

D. Cognito에서 실제 사용자 인증이 완료되었는지 확인 합니다.

![](../../.gitbook/assets/image%20%2865%29.png)

E. 가입한 이메일주소와 패스워드로 로그인을 진행하면, 성공적으로 인증이 완료되었다는 메시지와 함께 API가 구성되지 않았다는 알림이 나타납니다.

![](../../.gitbook/assets/image%20%2862%29.png)

## Task3. 서버리스 백엔드 구축

📌 AWS Lambda와 Amazon DynamoDB를 사용하여 웹 애플리케이션에 대한 요청을 처리하기 위한 백엔드 프로세스를 만들도록 하겠습니다.

📌 사용자가  요청할 때마다 호출될 Lambda 함수를 구현하겠습니다. 이 함수는 Amazon API Gateway를 사용하여 브라우저에서 호출됩니다.

### 1. Amazon DynamoDB 테이블 만들기

A.  Console에서  **DynamoDB**를 선택한 후 \[**Create table**\]을 선택합니다.



A. 테이블에 `Rides`라는 이름을 지정하고, 형식이 문자열\(String\)인 `RideId`라는 파티션 키를 부여합니다.다른 모든 설정에는 기본값을 사용합니다.



{% hint style="info" %}
테이블 이름과 파티션 키는 대/소문자를 구분합니다. 제공된 ID를 정확하게 사용해야 합니다. 
{% endhint %}







Once you're strong enough, save the world:

{% code title="hello.sh" %}
```bash
# Ain't no coder that yet, sorry
echo 'You got to trust me on this, I savee world'
```
{% endcode %}



