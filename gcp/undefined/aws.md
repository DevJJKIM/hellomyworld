# AWS 서버리스 웹 애플리케이션 구축하기

## Overall

AWS의 서버리스 제품인 [AWS Lambda](https://aws.amazon.com/lambda/), [Amazon API Gateway](https://aws.amazon.com/api-gateway/), [Amazon DynamoDB](https://aws.amazon.com/dynamodb/), [Amazon Cognito](https://aws.amazon.com/cognito/), [AWS Amplify Console](https://aws.amazon.com/amplify/console/)활용하여 웹페이지, API인증, Back-end, 데이터 저장소를 연결하는 간단한 서버리스 웹 애플리케이션을 생성하여 보겠습니다.

예상 소요시간은 **약 1시간** 입니다.

실습에서 활용되는 소스는 모두 AWS에서 제공하는 Sample 소스를 활용합니다.  
모든 실습은 **미국 서부\(오레곤\) us-west-2**에서 진행하고, 다른 리전에서 진행해도 무방합니다.

![&#xC560;&#xD50C;&#xB9AC;&#xCF00;&#xC774;&#xC158; &#xC544;&#xD0A4;&#xD14D;&#xCC98; &#xAD6C;&#xC131;&#xB3C4;](../../.gitbook/assets/image%20%2895%29.png)

## 제품소개

### **1. AWS Amplify**

모바일 및 프런트 엔드 웹 개발자가 AWS에서 구동되는 안전하고 확장 가능한 풀 스택 애플리케이션을 개발하도록 지원하는 도구 및 서비스 집합입니다.

### **2. AWS Cognito**

\*\*\*\*

## Task1. 정적 웹사이트 호스팅

📌브라우저에 로드되는 정적 웹 리소스를 호스팅하기 위해 AWS Amplify를 활용하겠습니다.

![AWS Amplify &#xC5F0;&#xB3D9; &#xC2DC;&#xB098;&#xB9AC;&#xC624;](../../.gitbook/assets/image%20%2845%29.png)

### 1. 소스저장소 생성\(Repository\)

📌소스 코드를 관리를 위해서  [AWS CodeCommit](https://aws.amazon.com/codecommit) 또는 [GitHub](http://github.com/) 이 이용 가능하나, 실습에서는 GitHub을 사용하도록 하겠습니다.

A. Github에서 Private Repository로 저장소를 생성하겠습니다. Repository 이름은 `wildrydes-site`으로 설정합니다.

![](../../.gitbook/assets/image%20%2884%29.png)

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

![](../../.gitbook/assets/image%20%2877%29.png)

### 2. AWS Amplify로 웹 호스팅 활성

\*\*\*\*📌 AWS Amplify를 사용하여 방금 Commit한 웹사이트를 배포하도록 하겠습니다.

A. Amplify Console에서 웹사이트 배포를 위해 Deliver의 ![](../../.gitbook/assets/image%20%2875%29.png)를 클릭합니다.

![](../../.gitbook/assets/image%20%2885%29.png)

B. GitHub를 Repository로 활용하고 있으므로, GitHub을 클릭 후 Authorization 승인하여 줍니다.  
     이용가능 Repository는 Github외에도 다양한 저장소를 이용할 수 있습니다.

![](../../.gitbook/assets/image%20%2873%29.png)

C. 권한 승인 후 Commit한 Repository를 선택 후 ![](../../.gitbook/assets/image%20%2887%29.png) 을 클릭합니다.  
     브랜치는 master 브랜치를 선택합니다.

![](../../.gitbook/assets/image%20%2846%29.png)

E. "빌드 설정 구성"페이지에서 모든 기본 값을 그대로 두고 ![](../../.gitbook/assets/image%20%2887%29.png) 을 선택합니다.

F. "검토"페이지에서 ![](../../.gitbook/assets/image%20%2843%29.png) 를 선택합니다

G. 필요한 리소스를 생성하고 코드를 배포하는 데 몇 분 정도 걸립니다.

H. 사이트 이미지를 클릭하여 배포 결과를 확인합니다.  
\(개가 말위에 타고있는 모습을 확인하시면 정상입니다.😀 \)

![](../../.gitbook/assets/image%20%2894%29.png)

I. master 링크를 클릭하면 빌드 및 배포에 대 세부 정보와 다양한 장치에서 앱의 스크린 샷이 표시됩니다.

![](../../.gitbook/assets/image%20%2881%29.png)

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

![](../../.gitbook/assets/image%20%28103%29.png)

C. 완료되면 Wild Rydes 사이트를 다시 열고 수정한 제목을 확인합니다.

![](../../.gitbook/assets/image%20%28100%29.png)

## Task2. 사용자관리

📌 Amazon Cognito 를 활용하여 사용자를 풀을 만들어 사용자로 등록하고, 이메일 주소를 확인하고, 사이트에 로그인할 수 있는 페이지를 배포해 보겠습니다.

📌 사용자가 등록 신청을 제출하면 Amazon Cognito에서 확인 코드가 담긴 확인 이메일을 해당 주소로 보내 줍니다. 사용자는 계정 확인을 위해 사이트로 돌아가서 자신의 이메일 주소와 이메일로 받은 확인 코드를 입력합니다. 

{% hint style="info" %}
 Amazon Cognito에는 두 가지 사용자 인증 메커니즘이 있습니다. Cognito 사용자 풀을 사용하여 애플리케이션에 등록 및 로그인 기능을 추가하거나, Cognito Identity Pools를 사용하여 Facebook, Twitter, Amazon 같은 소셜 자격 증명 공급자를 통해 사용자를 인증하거나 SAML 자격 증명 솔루션 또는 자체 자격 증명 시스템을 사용할 수 있습니다. 이 모듈에서는 제시된 등록 및 로그인 페이지의 백엔드로 사용자 풀을 사용하겠습니다.
{% endhint %}

### 1. Amazon Cognito 사용자 풀 생

A. Console에서 Cognito를 검색하여 클릭한 후, **사용자 풀 관리**를 선택합니다.

![](../../.gitbook/assets/image%20%2870%29.png)

B. **사용자 풀 생성**을 선택 후 풀의 이름은 `WildRydes`으로 입력하고 **기본값 검토**를 진행합니다.

![](../../.gitbook/assets/image%20%2859%29.png)

C. 검토 페이지에서 **풀 생성**을 클릭합니다. \(여기서 인증관련 다양한 옵션을 선택할 수 있습니다.\)

![](../../.gitbook/assets/image%20%2889%29.png)

D. 이렇게 많들어진 풀ID와 ARN은 으로 활용 됩니다.

![](../../.gitbook/assets/image%20%2852%29.png)

### 2. 사용자 풀에 앱 추가

📌 사용자 풀에 접속할 앱 클라이언트를 지정해 줍니다.

A.  탐색 모음의 왼쪽 **일반 설정** 섹션에 있는 **앱 클라이언트**를 선택하고 **앱 클라이언트 추가**​를 선택합니다.

![](../../.gitbook/assets/image%20%2874%29.png)

B. 앱 클라이언트 이름은 WildRydesWebApp 으로 입력하고, 보안키 생성은 체크를 해제한 후 **앱 클라이언트 생성**을 선택합니다.​

![](../../.gitbook/assets/image%20%28101%29.png)

C. 사용자 풀에 액세스할 수 있는 고유한 ID와 선택적 보안키를 받게 됩니다.

![](../../.gitbook/assets/image%20%2878%29.png)

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

![](../../.gitbook/assets/image%20%2880%29.png)

{% hint style="success" %}
Cognito의 Default 설정때문에 최소한 대문자 하나, 숫자 하나, 특수문자 하나가 포함된 암호를 선택하여야 합니다.
{% endhint %}

B. 입력한 이메일 주소에서 Verification Code를 입력합니다.

![](../../.gitbook/assets/image%20%2849%29.png)

C. 입력이 정상적으로 완료되면 Pop-up이 뜨고, Login page로 redirection 됩니다.

![](../../.gitbook/assets/image%20%2876%29.png)

D. Cognito에서 실제 사용자 인증이 완료되었는지 확인 합니다.

![](../../.gitbook/assets/image%20%2897%29.png)

E. 가입한 이메일주소와 패스워드로 로그인을 진행하면, 성공적으로 인증이 완료되었다는 메시지와 함께 API가 구성되지 않았다는 알림이 나타납니다.

![](../../.gitbook/assets/image%20%2893%29.png)

## Task3. 서버리스 백엔드 구축

📌 AWS Lambda와 Amazon DynamoDB를 사용하여 웹 애플리케이션에 대한 요청을 처리하기 위한 백엔드 프로세스를 만들도록 하겠습니다.

📌 사용자가  요청할 때마다 호출될 Lambda 함수를 구현하겠습니다. 이 함수는 Amazon API Gateway를 사용하여 브라우저에서 호출됩니다.

### 1. Amazon DynamoDB 테이블 만들기

A.  Console에서  **DynamoDB**를 선택한 후 \[**테이블만들기**\]을 선택합니다.

B. 테이블에 `Rides`라는 이름을 지정하고, 형식이 문자열\(String\)인 `RideId`라는 파티션 키를 부여합니다.다른 모든 설정에는 기본값을 사용합니다. 생성을 클릭합니다.

![](../../.gitbook/assets/image%20%2896%29.png)

{% hint style="info" %}
테이블 이름과 파티션 키는 대/소문자를 구분합니다. 제공된 ID를 정확하게 사용해야 합니다. 
{% endhint %}

### 2. Lambda 함수를 위한 IAM 역할 만들기

📌 각 Lambda 함수에는 IAM 역할이 연결되어 있습니다. 이 역할은 기능이 상호 작용하도록 허용된 다른 AWS 서비스를 정의합니다. 

 📌 Amazon CloudWatch Logs에 로그를 쓸 수 있는 권한과 DynamoDB 테이블에 항목을 쓸 수 있는 액세스 권한을 Lambda 함수에 부여하는 IAM 역할을 만들도록 하겠습니다.

A.   Console에서 **IAM**을 선택하고,  **역할** 탭에서 **역할 만들기**를 선택합니다.

![](../../.gitbook/assets/image%20%2850%29.png)

B. AWS 서비스에서 Lambda를 선택하고 ![](../../.gitbook/assets/image%20%2871%29.png) 클릭합니다.

![](../../.gitbook/assets/image%20%2840%29.png)

C. 정책 필터에  `AWSLambdaBasicExecutionRole` 를 입력하고 ![](../../.gitbook/assets/image%20%2862%29.png) 클릭합니다.

![](../../.gitbook/assets/image%20%2865%29.png)

D. 태그 생성은 넘어가고,  역할 이름에  `WildRydesLambda` 입력 후 **\[역할 만들기\]**를 클릭합니다.

![](../../.gitbook/assets/image%20%2888%29.png)

E. WildRydesLambda 요약페이지에서 **인라인 정책 추가**를 선택합니다.

![](../../.gitbook/assets/image%20%2882%29.png)

F. 서비스에서 **DynamoDB**를 선택하고, 작업 필터링 검색 상자에 **PutItem**을 입력합니다.

![](../../.gitbook/assets/image%20%28107%29.png)

G. 리소스 탭에서 **ARN 추가**를 클릭합니다.

![](../../.gitbook/assets/image%20%2851%29.png)

H. DynamoDB생성했던 ARN정보를 입력합니다.

![](../../.gitbook/assets/image%20%2841%29.png)

I. 정책검토를 클릭  정책이름에  `DynamoDBWriteAccess`를 입력하고 **정책 생성**을 선택합니다.

![](../../.gitbook/assets/image%20%2869%29.png)

### 3. Lambda 함수 만들기

함수를 만들기 위한 코드는 아래 코드를 사용합니다.  [requestUnicorn.js](https://webapp.serverlessworkshops.io/serverlessbackend/lambda/requestUnicorn.js)

```bash
// Copyright Amazon.com, Inc. or its affiliates. All Rights Reserved.
// SPDX-License-Identifier: MIT-0

const randomBytes = require('crypto').randomBytes;

const AWS = require('aws-sdk');

const ddb = new AWS.DynamoDB.DocumentClient();

const fleet = [
    {
        Name: 'Bucephalus',
        Color: 'Golden',
        Gender: 'Male',
    },
    {
        Name: 'Shadowfax',
        Color: 'White',
        Gender: 'Male',
    },
    {
        Name: 'Rocinante',
        Color: 'Yellow',
        Gender: 'Female',
    },
];

exports.handler = (event, context, callback) => {
    if (!event.requestContext.authorizer) {
      errorResponse('Authorization not configured', context.awsRequestId, callback);
      return;
    }

    const rideId = toUrlString(randomBytes(16));
    console.log('Received event (', rideId, '): ', event);

    // Because we're using a Cognito User Pools authorizer, all of the claims
    // included in the authentication token are provided in the request context.
    // This includes the username as well as other attributes.
    const username = event.requestContext.authorizer.claims['cognito:username'];

    // The body field of the event in a proxy integration is a raw string.
    // In order to extract meaningful values, we need to first parse this string
    // into an object. A more robust implementation might inspect the Content-Type
    // header first and use a different parsing strategy based on that value.
    const requestBody = JSON.parse(event.body);

    const pickupLocation = requestBody.PickupLocation;

    const unicorn = findUnicorn(pickupLocation);

    recordRide(rideId, username, unicorn).then(() => {
        // You can use the callback function to provide a return value from your Node.js
        // Lambda functions. The first parameter is used for failed invocations. The
        // second parameter specifies the result data of the invocation.

        // Because this Lambda function is called by an API Gateway proxy integration
        // the result object must use the following structure.
        callback(null, {
            statusCode: 201,
            body: JSON.stringify({
                RideId: rideId,
                Unicorn: unicorn,
                UnicornName: unicorn.Name,
                Eta: '30 seconds',
                Rider: username,
            }),
            headers: {
                'Access-Control-Allow-Origin': '*',
            },
        });
    }).catch((err) => {
        console.error(err);

        // If there is an error during processing, catch it and return
        // from the Lambda function successfully. Specify a 500 HTTP status
        // code and provide an error message in the body. This will provide a
        // more meaningful error response to the end client.
        errorResponse(err.message, context.awsRequestId, callback)
    });
};

// This is where you would implement logic to find the optimal unicorn for
// this ride (possibly invoking another Lambda function as a microservice.)
// For simplicity, we'll just pick a unicorn at random.
function findUnicorn(pickupLocation) {
    console.log('Finding unicorn for ', pickupLocation.Latitude, ', ', pickupLocation.Longitude);
    return fleet[Math.floor(Math.random() * fleet.length)];
}

function recordRide(rideId, username, unicorn) {
    return ddb.put({
        TableName: 'Rides',
        Item: {
            RideId: rideId,
            User: username,
            Unicorn: unicorn,
            UnicornName: unicorn.Name,
            RequestTime: new Date().toISOString(),
        },
    }).promise();
}

function toUrlString(buffer) {
    return buffer.toString('base64')
        .replace(/\+/g, '-')
        .replace(/\//g, '_')
        .replace(/=/g, '');
}

function errorResponse(errorMessage, awsRequestId, callback) {
  callback(null, {
    statusCode: 500,
    body: JSON.stringify({
      Error: errorMessage,
      Reference: awsRequestId,
    }),
    headers: {
      'Access-Control-Allow-Origin': '*',
    },
  });
}
```

A. Console에서 **Lambda**를 선택하고 **함수 생성**을 클릭합니다.

![](../../.gitbook/assets/image%20%2857%29.png)

B. 함수 이름은  `RequestUnicorn`을 입력하고, `기존 역할` 드롭다운 메뉴에서 `WildRydesLambda`를 선택한 후 함수 생성을 클릭합니다.

![](../../.gitbook/assets/image%20%2842%29.png)

C. index.js파일을 위의 샘플소스로 교체하고 Deploy 클릭합니다.

### 4. 구현 테스트

📌 AWS Lambda 콘솔을 사용하여 만든 함수를 테스트하겠습니다.

A. 테스트 앱에서 이름필드에  `TestRequestEvent`을 입력하고 변경 사항 저장을 클릭합니다.

![](../../.gitbook/assets/image%20%2858%29.png)

B. 저장된 이벤트로 테스트를 진행합니다. 아래의 결과로 나오면 정상입니다.

![](../../.gitbook/assets/image%20%2861%29.png)

## Task4. RESTFul API 배포

📌 Amazon API Gateway를 사용하여 이전 모듈에서 구축한 Lambda 함수를 RESTFul API로 공개하도록 하겠습니다.

📌 이 API는 이전 모듈에서 생성한 Amazon Cognito 사용자 풀을 사용하여 보호됩니다.

### 1. REST API 생성

A. Console에서 API Gateway를 선택하고, REST API 구축을 클릭합니다.

![](../../.gitbook/assets/image%20%2891%29.png)

B. **New API\(새 API\)**를 선택하고 **API Name\(API 이름\)**에 `WildRydes`를 입력합니다. 엔드포인트 유형은 `Edge optimized(최적화된 엣지)`를 선택합니다.

![](../../.gitbook/assets/image%20%2872%29.png)

{% hint style="info" %}
인터넷에서 퍼블릭 서비스에 액세스하려면 Edge optimized\(최적화된 엣지\)가 가장 좋습니다.   
일반적으로 리전 엔드포인트는 같은 AWS 리전 내에서 주로 액세스하는 API에 사용됩니다.
{% endhint %}

### 2. Cognito 사용자 풀 권한 부여자 생성

📌 Amazon API Gateway는 Cognito 사용자 풀에서 반환된 JWT 토큰을 사용하여 API 호출을 인증합니다.

📌 기존에 생성한 사용자 풀을 사용하도록 API에 대한 권한 부여자를 구성하도록 하겠습니다.

A. **권한 부여자** 탭에서 **새로운 권한 부여자 생성**을 클릭합니다.

* 권한 부여자 이름에 `WildRydes`를 입력합니다.
* 유형에 **Cognito**를 선택하고, **Token Source\(토큰 원본\)**에 `Authorization`을 입력 후 생성을 클릭합니다.

![](../../.gitbook/assets/image%20%2853%29.png)

B. 사이트의 `/ride.html`에서 Auth Token정보를 복사 한 후 권한 부여자의 카드 하단에 있는 **Test\(테스트\)**를 클릭합니다.

C. Auth 토큰을 팝업 대화 상자에 있는 **Authorization Token\(권한 부여 토큰\)** 필드에 붙여 넣고, **Test\(테스트\)**를 클릭합니다. 아래와 같이 응답코드가 200이면 성공입니다.

![](../../.gitbook/assets/image%20%2892%29.png)

### 3.  리소스 및 메서드 생성

📌 API 내부에 POST 메서드로 /ride라는 새 리소스를 생성하고 Lambda와 통 구성합니다.

A. **리소스 탭**에서 **리소스 생성**을 클릭합니다.

![](../../.gitbook/assets/image%20%2867%29.png)

B.  리소스이름은`ride`로 입력하고 API Gateway CORS 체크하고 **리소스 생성**을 클릭합니다.

![](../../.gitbook/assets/image%20%2879%29.png)

C. 생성된 ride리소스에서 메서드를 생성합니다.

![](../../.gitbook/assets/image%20%2883%29.png)

D. 드롭다운에서 `POST`를 선택한 다음, **확인 표시를 클릭**합니다.

E. ****Lambda 프록시 통합 사용 **체크박스를 선택**하고 `RequestUnicorn` 함수 이름을 입력한 후 **저장**을 클릭합니다. 함수를 호출할 수 있는 Amazon API Gateway 권한을 부여하라는 메시지가 나타나면 **OK\(확인\)**를 선택합니다.

![](../../.gitbook/assets/image%20%2847%29.png)

F. **Method Request\(메서드 요청\)** 카드를 선택합니다.

![](../../.gitbook/assets/image%20%2868%29.png)

G. **승인필드**에서 WildRydes Cognito 사용자 풀을 선택하고 확인 표시를 클릭합니다.

![](../../.gitbook/assets/image%20%2856%29.png)

### 4. API 배포

📌구성한 API를 배포하겠습니다.

A.  **Actions\(작업\)** 드롭다운 목록에서 **Deploy API\(API 배포\)**를 선택합니다.

* **Deployment stage\(배포 단계\)** 드롭다운 목록에서 **\[New Stage\]\(새 단계\)**를 선택합니다.
* **Stage Name\(단계 이름\)**에 `prod`를 입력후 **배포**를 클릭합니다.

![](../../.gitbook/assets/image%20%2899%29.png)

### 5. API Gateway연동을 위한 Config수정

A. 편집기로 config.js를 연후 Amazon API Gateway 콘솔에 있는 단계 편집기 페이지의 상단에서 호출 URL을 직접 복사하여 invokeUrl키에 업데이트 합니다.

```bash
window._config = {
    cognito: {
        userPoolId: 'us-west-2_KnjHFuijS', // e.g. us-east-2_uXboG5pAb
        userPoolClientId: '6c8t2ml5tsdopi5ofg3t4i6hfp', // e.g. 25ddkmj4v6hfsfvruhpfi7n4hv
        region: 'us-west-2' // e.g. us-east-2
    },
    api: {
        invokeUrl: 'https://wzbh99u02b.execute-api.us-west-2.amazonaws.com/prod' 
    }
};
```

B. Remote 저장소로 Push하여 줍니다

```bash
git add config.js
git commit -m "invokUrl updated"
git push
```

## Task6. 결과검증

📌현재 있는 웹페이즈는 닫고 Amplify에 접속하여 새롭게 사이트를 열겠습니다.

A. 웹 사이트 도메인 아래 있는 `/ride.html`로 이동합니다.

**📌Request Unicorn\(유니콘 요청\)**을 선택합니다. 오른쪽 사이드바에 유니콘이 오는 중이라는 알림이 표시된 다음 유니콘 아이콘이 픽업 위치로 날아가는 것을 확인되면 완료 입니다.

![](../../.gitbook/assets/image%20%28102%29.png)



![](../../.gitbook/assets/image%20%2864%29.png)

## Task7. 모든 리소스 삭제

실습 이후 추가적인 비용이 들지 않도록 사용한 모든 리스는 삭제하여 줍니다.

### 1. Amplify 앱 삭제

* 앱 랜딩 페이지에서 ‘액션&gt; 앱 삭제’를 선택합니다. 

#### 2. Amazon Cognito 사용자 풀 삭제 <a id="Amazon_Cognito_&#xC0AC;&#xC6A9;&#xC790;_&#xD480;_&#xC0AD;&#xC81C;"></a>

* **사용자 풀 관리**에서 생성한 **WildRydes** 사용자 풀을 삭제합니다.

### 3.  **Lambda 함수 삭제**

* 생성한 `RequestUnicorn` 함수를 삭제 합니다.

### 4.  **IAM 역할 삭**

*  역할 탭에서 `WildRydesLambda` 역할을 삭제합니다. ****

**5.**  **DynamoDB 테이블 삭제**

*  생성한 **Rides** 테이블 삭제합니다.

**6. API 삭제**

*   **API Gateway**에서 생성한 API를 삭제합니다.

### 7. CloudWatch Log 삭제

*  **CloudWatch**를 선택하고, 로그그룹상에 있는 모든 로그들을 삭제합니다.

## Reference

[https://aws.amazon.com/ko/getting-started/hands-on/build-serverless-web-app-lambda-apigateway-s3-dynamodb-cognito/](https://aws.amazon.com/ko/getting-started/hands-on/build-serverless-web-app-lambda-apigateway-s3-dynamodb-cognito/)

