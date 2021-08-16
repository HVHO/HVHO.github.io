---
layout: post
title: "[ Terraform Up&Running ] 테라폼 기본 사용 방법" 
description: 테라폼의 기본 사용 방법을 알아보자
comments: true
tags: Terraform
---

> 이 글은 Terraform Up&Running 책을 읽고 쓴 글입니다.  
> 더 자세한 내용을 원하시면 [해당도서](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791186710661&orderClick=LEa&Kc=) 구매를 추천드립니다.

# 시작하기에 앞서..

## 용어

### Provider

Terraform 은 AWS, GCP, AZURE 등 여러 클라우드 인프라 환경을 지원하는데, 이렇게 클라우드 인프라 공급자 들을 Provider 이라고 부릅니다.

### Resource

인프라를 구성하는 여러 구성요소들,  
예를 들어 EC2 instance, LB( Load Balencer ), DNS( Domain Name Server ), RDS 등의 각 요소들을 Resource 라고 부릅니다.

### HCL

HCL ( Hashicorp Configuration Languate ) 는 Terraform 을 만든 제작사인 hashicorp 에서 만든 Terraform 전용 DSL ( Domain Specific Language ) 입니다.  
Terraform 의 설정 파일들은 HCL 로 기술되며, .tf 의 파일 확장자를 가집니다.

## Terraform 의 기본 작업 LifeCycle

Terraform 으로 인프라를 프로비저닝 하는 일련의 작업은 보통 계획-적용-삭제 의 과정을 거칩니다.

### 계획(Plan)

테라폼을 통해 인프라 작업을 하면, 직접 콘솔을 통해 인프라를 변경시키는 것에 비해서 코드를 실제 환경하기 적용하기 전에 **"검증"** 단계를 거칠 수 있는 장점이 있습니다. ( 콘솔로 작업하면 인프라가 바로 변경되지만, 코드로 작업하면 해당 코드를 환경에 적용하기 전 static validation 할 수 있습니다 )

해당 단계를 테라폼은 **계획** 단계라고 하며, 해당 단계는 현재 적용되어 있는 리소스들의 상태와 코드를 적용 시켰을 때 변경될 리소스의 상태의 차이를 보여줍니다. ( git status 와 비슷하다고 생각하시면 될 것 같습니다. )  
  
코드를 실제 환경에 적용하기 전, 코드의 변경점이 내가 의도한 변경사항이 맞는지 확인해 보는 단계이고, 해당 명령어는 실제 환경에 영향을 주지 않기 때문에 내가 정확히 원하는 결과를 얻을 때 까지 반복해서 확인해 볼 수 있습니다.

### 적용(Apply)

해당 단계는 코드의 변경 사항을 실제 환경에 적용시키는 단계입니다.  

적용이 성공한다면 변경 ( 추가 / 변경 / 삭제 ) 된 리소스의 정보가 출력되고, 만약 실패한다면 실패한 이유가 나옵니다.  
  
여기서 테라폼의 장점을 알 수 있는데, **적용** 명령은 **멱등성(idempotent)** 을 가지기 때문에 여러번 적용하여도 작성한 코드와 동일한 상태를 보장합니다. ( 리소스 여러개를 생성하다 중간에 실패했을 경우에도 코드를 고쳐 다시 적용하면 이전에 만들어졋던 리소스는 알아서 처리됩니다. )  

![magic](https://user-images.githubusercontent.com/25953981/128623582-d6d6bd51-586b-4215-ac8b-f3069571e6da.png){:width="35%" height="35%"}


### 삭제(Destory)

해당 단계는 현재 관리되고 있는 모든 인프라를 삭제하는 명령어 입니다.

서비스를 아예 내려 버릴때가 아니면 거의 사용할 일이 없는 명령어이며, 실행하면 정말 실행할 것인지 한번 더 물어봅니다.  
물론 코드가 사라지는 것은 아니기 때문에, Apply 를 통해서 동일한 인프라를 바로 다시 올릴 수 있습니다.  
  
업무에서 해당 명령어를 사용하실 때 조심하세요!

---

# AWS 계정 설정 

Terraform 을 사용하기 위해서는 적절한 권한을 가진 IAM 이 필요합니다.   
이 문서에서는 EC2/S3/DynamoDB/RDS/CloudWatch/IAM 의 접근 권한이 있는 IAM 을 생성합니다. 

1. [AWS Console](https://aws.amazon.com/ko/console/) 접속
2.  Region 선택 ( 이 문서에서는 ap-northeast-2 seoul 을 기준으로 설명 합니다. )
3. **IAM** 메뉴 접속 
4. 



