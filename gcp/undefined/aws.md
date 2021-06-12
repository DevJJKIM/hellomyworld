# AWS 서버리스 웹 애플리케이션 구축하기

## Overall

AWS의 서버리스 제품인 [AWS Lambda](https://aws.amazon.com/lambda/), [Amazon API Gateway](https://aws.amazon.com/api-gateway/), [Amazon DynamoDB](https://aws.amazon.com/dynamodb/), [Amazon Cognito](https://aws.amazon.com/cognito/), [AWS Amplify Console](https://aws.amazon.com/amplify/console/)활용하여 웹페이지, API인증, Back-end, 데이터 저장소를 연결하는 간단한 서버리스 웹 애플리케이션을 생성하여 보겠습니다.

예상 소요시간은 **약 1시간** 입니다.

실습에서 활용되는 소스는 모두 AWS에서 제공하는 Sample 소스를 활용합니다.  
모든 실습은 **미국 서부\(오레곤\) us-west-2**에서 진행하고, 다른 리전에서 진행해도 무방합니다.

![&#xC560;&#xD50C;&#xB9AC;&#xCF00;&#xC774;&#xC158; &#xC544;&#xD0A4;&#xD14D;&#xCC98; &#xAD6C;&#xC131;&#xB3C4;](../../.gitbook/assets/image%20%2848%29.png)

## 제품소개

### **1. AWS Amplify**

## Task1. 정적 웹사이트 호스팅

브라우저에 로드되는 정적 웹 리소스를 호스팅하기 위해 ****AWS Amplify를 활용하겠습니다.

![AWS Amplify &#xC5F0;&#xB3D9; &#xC2DC;&#xB098;&#xB9AC;&#xC624;](../../.gitbook/assets/image%20%2840%29.png)

### 1. 소스저장소 생성\(Repository\)

소스 코드를 관리를 위해서  [AWS CodeCommit](https://aws.amazon.com/codecommit) 또는 [GitHub](http://github.com/) 이 이용 가능하나, 실습에서는 GitHub을 사용하도록 하겠습니다.

github에서 Private Repository로 저장소를 생성하겠습니다. Repository 이름은 `wildrydes-site`으로 설정합니다.

![](../../.gitbook/assets/image%20%2846%29.png)

생성된 Git Repository의 주소를 로컬PC로 클론합니다.  
Private 저장소로 설정하였으므로 USER\_ID와 PW를 입력 합니다.

```
git clone https://github.com/[USER_ID]/wildrydes-site.git
```

클론한 디렉토리로 이동후 샘플소스를 받아옵니다.

```bash
cd wildrydes-site/
aws s3 cp s3://wildrydes-us-east-1/WebApplication/1_StaticWebHosting/website ./ --recursive
```

저장된 소스파일을 Remote 저장소로 업로드합니다.

```bash
#git 파일 커밋하기
git add .
git commit -m "first commit"
git push
```

Push가 완료되 아래와 같이 결과를 확인할수 있습니다.

![](../../.gitbook/assets/image%20%2845%29.png)

### 2. AWS Amplify로 웹 호스팅 활성

AWS Amplify를 사용하여 방금 Commit한 웹사이트를 배포하도록 하겠습니다.

 Amplify Console에서 웹사이트 배포를 위해 Deliver의 ![](../../.gitbook/assets/image%20%2844%29.png)를 클릭합니다.

![](../../.gitbook/assets/image%20%2847%29.png)

GitHub를 Repository로 활용하고 있으므로, GitHub을 클릭 후 Authorization 승인하여 줍니다.

![](../../.gitbook/assets/image%20%2843%29.png)

GiGitdddd

![](../../.gitbook/assets/image%20%2841%29.png)



{% hint style="info" %}
 Super-powers are granted randomly so please submit an issue if you're not happy with yours.
{% endhint %}

Once you're strong enough, save the world:

{% code title="hello.sh" %}
```bash
# Ain't no coder that yet, sorry
echo 'You got to trust me on this, I savee world'
```
{% endcode %}



