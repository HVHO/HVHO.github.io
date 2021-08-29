---
layout: post
title: "[ Terraform Up&Running ] 테라폼 기본 사용 방법 - 단일 웹 서버 배포하기" 
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
<br>

---

# Terraform 의 기본 작업 LifeCycle

Terraform 으로 인프라를 프로비저닝 하는 일련의 작업은 보통 계획(Plan)-적용(Apply)-삭제(Destroy) 의 과정을 거칩니다.
<br>

## 계획(Plan)

테라폼을 통해 인프라 작업을 하면, 직접 콘솔을 통해 인프라를 변경시키는 것에 비해서 코드를 실제 환경하기 적용하기 전에 **"검증"** 단계를 거칠 수 있는 장점이 있습니다. ( 콘솔로 작업하면 인프라가 바로 변경되지만, 코드로 작업하면 해당 코드를 환경에 적용하기 전 static validation 할 수 있습니다 )

해당 단계를 테라폼은 **계획** 단계라고 하며, 해당 단계는 현재 적용되어 있는 리소스들의 상태와 코드를 적용 시켰을 때 변경될 리소스의 상태의 차이를 보여줍니다. ( git status 와 비슷하다고 생각하시면 될 것 같습니다. )  
  
코드를 실제 환경에 적용하기 전, 코드의 변경점이 내가 의도한 변경사항이 맞는지 확인해 보는 단계이고, 해당 명령어는 실제 환경에 영향을 주지 않기 때문에 내가 정확히 원하는 결과를 얻을 때 까지 반복해서 확인해 볼 수 있습니다.

## 적용(Apply)

해당 단계는 코드의 변경 사항을 실제 환경에 적용시키는 단계입니다.  

적용이 성공한다면 변경 ( 추가 / 변경 / 삭제 ) 된 리소스의 정보가 출력되고, 만약 실패한다면 실패한 이유가 나옵니다.  
  
여기서 테라폼의 장점을 알 수 있는데, **적용** 명령은 **멱등성(idempotent)** 을 가지기 때문에 여러번 적용하여도 작성한 코드와 동일한 상태를 보장합니다. ( 리소스 여러개를 생성하다 중간에 실패했을 경우에도 코드를 고쳐 다시 적용하면 이전에 만들어졋던 리소스는 알아서 처리됩니다. )  

![magic](https://user-images.githubusercontent.com/25953981/128623582-d6d6bd51-586b-4215-ac8b-f3069571e6da.png){:width="35%" height="35%"}


## 삭제(Destory)

해당 단계는 현재 관리되고 있는 모든 인프라를 삭제하는 명령어 입니다.

서비스를 아예 내려 버릴때가 아니면 거의 사용할 일이 없는 명령어이며, 실행하면 정말 실행할 것인지 한번 더 물어봅니다.  
물론 코드가 사라지는 것은 아니기 때문에, Apply 를 통해서 동일한 인프라를 바로 다시 올릴 수 있습니다.  
  
업무에서 해당 명령어를 사용하실 때 조심하세요!

---

# AWS 계정 설정 

Terraform 을 사용하기 위해서는 적절한 권한을 가진 IAM 이 필요합니다.   
이 문서에서는 EC2/S3/DynamoDB/RDS/CloudWatch/IAM 의 접근 권한이 있는 IAM 을 생성합니다. ( AWS 계정은 이미 준비되어 있다고 가정합니다. )

1. [AWS Console](https://aws.amazon.com/ko/console/) 접속
2. Region 선택 ( 이 글에서는 ap-northeast-2 Seoul 을 기준으로 설명 합니다. )
3. **IAM** 메뉴 접속 
4. 새로운 유저 추가 ( 프로그래밍 방식 액세스 선택 )
5. 새로운 유저에 적절한 정책 추가 ( 관리하려는 리소스에 따라 적절한 권한이 필요합니다. )
	- AmazonEC2FullAccess
	- AmazonS3FullAccess
	- AmazonDynamoDBFullAccess
	- AmazonRDSFullAccess
	- CloudWatchFullAccess
	- IAMFullAccess
6. 생성된 유저 토큰 정보를 안전하게 저장 ( 텍스트로 저장하는 것 보다는, Bitwarden / Keybase / KeyChain 등의 안전한 Passowrd 보관 유틸리티에 저장하는 것을 권장합니다. )
7. 토큰 정보를 테라폼에서 사용할 수 있게 환경 변수로 export  
```export AWS_ACCESS_KEY_ID=${위에서 생성한 ACCESS KEY}```  
```export AWs_SECRET_ACCESS_KEY=${위에서 생성한 SECRET ACCESS KEY}```

---

# Terraform 설치

[Terraform 공식 다운로드 페이지](https://www.terraform.io/downloads.html) 에서 원하는 버전을 다운 받으실 수 있습니다.

- Linux  
<code>sudo apt install terraform</code>

- MacOS  
<code>brew install terraform</code>

---

# 단일 웹 서버 배포

이 단계를 끝내고 나면 아래와 같은 구조의 인프라 요소를 얻게 됩니다.

![image](https://user-images.githubusercontent.com/25953981/131239676-5d61a250-2c68-4d87-8b1e-b8bd9aecea6d.png)


## 웹 서버를 위한 배시 스크립트 준비

실제 프로덕션 환경에서는 스프링, 장고 등과 같은 서버용 프레임워크를 사용하지만,
이 실습에서는 단순 응답만을 위하여 bash script 를 사용합니다.

{% highlight bash %}

#!/bin/bash
echo "Hello, World" > index.html
nohup busybox httpd -f -p 8080 &

{% endhighlight %}

위 스크립트는 index.html 에 "Hello, World" 라는 문자열을 입력하여 저장하고,
8080 포트를 listening 하고 있는 busybox 유틸리티를 백그라운드로 실행합니다.

## EC2 인스턴스를 위한 tf 파일 준비

해당 스크립트를 실행하는 웹 서버를 실행하기 위해서, 우리는 1개의 ```EC2``` 인스턴스, 1개의 ```Security Group```이 필요합니다.

먼저 EC2 인스턴스를 위한 테라폼 코드입니다.

{% highlight terraform %}

resource "aws_instance" "example" {
	ami	= "ami-0ba5cd124d7a79612"
	instance_type = "t2.micro"
	vpc_security_group_ids = [aws_security_group.instance.id]

	user_data = <<-EOF
	#!/bin/bash
	echo "Hello, World" > index.html
	nohup busybox httpd -f -p 8080 &
	EOF
	
	tags = {
		Name = "terraform-example" 
	}
}

{% endhighlight %}

- ```ami``` 는 ```amazon linux``` ( ami-04876f29fd3a5e8ba ) - ami 는 같은 타입의 이미지여도 리즌별로 아이디가 다르니 조심해야 합니다.
- ```instance_type``` 은 ```t2.micro```
- ```user_data``` 는 위에서 작성했던 web server 을 띄우는 bash script
- ```tag``` 중 해당 인스턴스의 이름은 ```terraform-example```
- ```vpc_security_gropu_ids``` 는 인스턴스를 아래에 기술할 ```security group``` 에 속하게 설정합니다.

으로 설정하는 정보가 담겨 있습니다.

기본적으로 AWS 의 EC2 는 기본적으로 ingress traffic 을 허용하지 않기 때문에, 별도로 이를 허용해 주는 보안그룹(Security Group) 설정도 필요합니다.

{% highlight terraform %}
resource "aws_security_group" "instance" {
	name = "terraform-example-instance"

	ingress {
		from_port = 8080
		to_port = 8080
		protocol = "tcp"
		cidr_bolcks = ["0.0.0.0/0"]
	}

}
{% endhighlight %}

이 테라폼 코드는 AWS security group 리소스를 정의하고 있으며,
- 어느 IP 에서 들어오는 요청이든 ( CIDR 0.0.0.0/0)
- 8080 포트로 들어오는 tcp 요청을 허용합니다.

<br>
테라폼 파일은 위처럼 리소스에 대한 정의를 코드로 할 수 있게 만들어 주며, 해당 리소스 간의 **관계** 도 표현할 수 있습니다. ( 상대참조 id 로 ```EC2``` 인스턴스와 ```security group``` 의 관계 정의 )  
리소스간의 관계가 생기면, 이에 따라 리소스간의 디펜던시가 발생하게 되는데, 테라폼은 해당 디펜던시 관계에 따라 리소스의 생성 순서를 알아서 관리해 줍니다. ( 해당 디펜던시 정보는 ```terraform graph``` 명령어로 확인할 수 있습니다. )  
또한, 한번에 병렬로 생성할 수 있는 ( 서로 디펜던시가 없는 ) 최대의 리소스를 생성함으로써, 최단시간에 리소스가 생성되도록 만들어져 있습니다.
![magic](https://user-images.githubusercontent.com/25953981/128623582-d6d6bd51-586b-4215-ac8b-f3069571e6da.png){:width="35%" height="35%"}*너무 좋죠!*

## 계획 ( Plan )

이제 위에서 열심히 작업한 테라폼 코드를 적용하기 전, 계획을 하는 단계입니다.  
- 위에서 작성한 리소스에 대한 정보를 ```main.tf``` 파일에 저장합니다. ( [여기](https://gist.github.com/HVHO/e6a40f6a795d5cad26732ed184362297) 에서 보실 수 있습니다.)
- 해당 디렉토리에서 ```terraform init``` 을 실행하여 초기화 시킵니다.
- ```terraform plan``` 을 실행해 주면 적용 시 변경되게 될 사항에 대한 정보를 미리 볼 수 있습니다.

{% highlight bash %}
terry  ~/workspace/study/tmp  terraform plan                                                   ✔
provider.aws.region
  The region where AWS operations will take place. Examples
  are us-east-1, us-west-2, etc.

  Enter a value: ap-northeast-2


Terraform used the selected providers to generate the following execution plan. Resource actions are
indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_instance.example will be created
  + resource "aws_instance" "example" {
      + ami                                  = "ami-04876f29fd3a5e8ba"
      + arn                                  = (known after apply)
      + associate_public_ip_address          = (known after apply)
      + availability_zone                    = (known after apply)
      + cpu_core_count                       = (known after apply)
      + cpu_threads_per_core                 = (known after apply)
      + disable_api_termination              = (known after apply)
      + ebs_optimized                        = (known after apply)
      + get_password_data                    = false
      + host_id                              = (known after apply)
      + id                                   = (known after apply)
      + instance_initiated_shutdown_behavior = (known after apply)
      + instance_state                       = (known after apply)
      + instance_type                        = "t2.micro"
      + ipv6_address_count                   = (known after apply)
      + ipv6_addresses                       = (known after apply)
      + key_name                             = (known after apply)
      + monitoring                           = (known after apply)
      + outpost_arn                          = (known after apply)
      + password_data                        = (known after apply)
      + placement_group                      = (known after apply)
      + primary_network_interface_id         = (known after apply)
      + private_dns                          = (known after apply)
      + private_ip                           = (known after apply)
      + public_dns                           = (known after apply)
      + public_ip                            = (known after apply)
      + secondary_private_ips                = (known after apply)
      + security_groups                      = (known after apply)
      + source_dest_check                    = true
      + subnet_id                            = (known after apply)
      + tags                                 = {
          + "Name" = "terraform-example"
        }
      + tags_all                             = {
          + "Name" = "terraform-example"
        }
      + tenancy                              = (known after apply)
      + user_data                            = "6353bc3b1ca0bfbc35fbf0b4593f4fe97f713126"
      + user_data_base64                     = (known after apply)
      + vpc_security_group_ids               = (known after apply)

      + capacity_reservation_specification {
          + capacity_reservation_preference = (known after apply)

          + capacity_reservation_target {
              + capacity_reservation_id = (known after apply)
            }
        }

      + ebs_block_device {
          + delete_on_termination = (known after apply)
          + device_name           = (known after apply)
          + encrypted             = (known after apply)
          + iops                  = (known after apply)
          + kms_key_id            = (known after apply)
          + snapshot_id           = (known after apply)
          + tags                  = (known after apply)
          + throughput            = (known after apply)
          + volume_id             = (known after apply)
          + volume_size           = (known after apply)
          + volume_type           = (known after apply)
        }

      + enclave_options {
          + enabled = (known after apply)
        }

      + ephemeral_block_device {
          + device_name  = (known after apply)
          + no_device    = (known after apply)
          + virtual_name = (known after apply)
        }

      + metadata_options {
          + http_endpoint               = (known after apply)
          + http_put_response_hop_limit = (known after apply)
          + http_tokens                 = (known after apply)
        }

      + network_interface {
          + delete_on_termination = (known after apply)
          + device_index          = (known after apply)
          + network_interface_id  = (known after apply)
        }

      + root_block_device {
          + delete_on_termination = (known after apply)
          + device_name           = (known after apply)
          + encrypted             = (known after apply)
          + iops                  = (known after apply)
          + kms_key_id            = (known after apply)
          + tags                  = (known after apply)
          + throughput            = (known after apply)
          + volume_id             = (known after apply)
          + volume_size           = (known after apply)
          + volume_type           = (known after apply)
        }
    }

  # aws_security_group.instance will be created
  + resource "aws_security_group" "instance" {
      + arn                    = (known after apply)
      + description            = "Managed by Terraform"
      + egress                 = (known after apply)
      + id                     = (known after apply)
      + ingress                = [
          + {
              + cidr_blocks      = [
                  + "0.0.0.0/0",
                ]
              + description      = ""
              + from_port        = 8080
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "tcp"
              + security_groups  = []
              + self             = false
              + to_port          = 8080
            },
        ]
      + name                   = "terraform-example-instance"
      + name_prefix            = (known after apply)
      + owner_id               = (known after apply)
      + revoke_rules_on_delete = false
      + tags_all               = (known after apply)
      + vpc_id                 = (known after apply)
    }

Plan: 2 to add, 0 to change, 0 to destroy.

────────────────────────────────────────────────────────────────────────────────────────────────────

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly
these actions if you run "terraform apply" now.
{% endhighlight %}

결과를 분석하면 ```EC2``` 하나, ```Security Group``` 하나 총 2개의 리소스를 정상적으로 add 할 것임을 볼 수 있습니다.  
일부 값들은 적용 전에는 알 수 없어 ```known after apply``` 으로 나오는 것을 볼 수 있네요.

## 적용 ( Apply )

이제 실제로 인프라를 띄워 보기 위해서 적용하는 단계만 남았습니다.
긴 말 할것 없이 바로 해봅시다.

{% highlight bash %}
 terry  ~/workspace/study/tmp  terraform apply                                                  ✔
provider.aws.region
  The region where AWS operations will take place. Examples
  are us-east-1, us-west-2, etc.

  Enter a value: ap-northeast-2

  ..생략

Plan: 2 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_security_group.instance: Creating...
aws_security_group.instance: Creation complete after 2s [id=sg-01523266bcf57adc9]
aws_instance.example: Creating...
aws_instance.example: Still creating... [10s elapsed]
aws_instance.example: Still creating... [20s elapsed]
aws_instance.example: Still creating... [30s elapsed]
aws_instance.example: Creation complete after 37s [id=i-0e466823518641f52]
{% endhighlight %}

```Apply``` 를 하기 전 마지막으로 ````Plan``` 을 확인하는 과정을 거치며,  
```i-0e466823518641f52``` EC2,```sg-01523266bcf57adc9``` SG 가 생성된 것을 알 수 있습니다.  

이는 AWS 콘솔에서도 바로 확인해 볼 수 있습니다.

![EC2](https://user-images.githubusercontent.com/25953981/131240764-edbe0ac4-6776-4e82-902a-3b24c959ee52.png)*정상적으로 실행된  EC2*
![EC2 description](https://user-images.githubusercontent.com/25953981/131241904-500bd894-3340-4db4-b2b2-ca7049ff70f1.png)*세부 정보에서 public ip 를 확인 할 수 있다*

## 웹서버 접속

웹서버가 정상적으로 동작하는지 확인하기 위해 AWS 콘솔에서 확인한 EC2 인스턴스의 퍼블릭 IP로 접속해 봅니다.

![curl](https://user-images.githubusercontent.com/25953981/131241820-683c4598-581a-43e2-903d-7bcb896fe137.png)*접속 성공!*


## 삭제 ( Desotroy )

리소스들을 삭제하지 않고 띄워두면 과금이 되니, 항상 연습 후에는 ```Destroy``` 해 주어야 합니다.  


{% highlight bash %}
..생략
Plan: 0 to add, 0 to change, 2 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

aws_instance.example: Destroying... [id=i-0049635af0af13df7]
aws_instance.example: Still destroying... [id=i-0049635af0af13df7, 10s elapsed]
aws_instance.example: Still destroying... [id=i-0049635af0af13df7, 20s elapsed]
aws_instance.example: Still destroying... [id=i-0049635af0af13df7, 30s elapsed]
aws_instance.example: Still destroying... [id=i-0049635af0af13df7, 40s elapsed]
aws_instance.example: Destruction complete after 40s
aws_security_group.instance: Destroying... [id=sg-01523266bcf57adc9]
aws_security_group.instance: Destruction complete after 0s

Destroy complete! Resources: 2 destroyed.
{% endhighlight %}

```Destory``` 과정도 ```Apply``` 과정과 거의 동일합니다.  
명령을 실행하기 전 ```Plan``` 과정을 거치며, 해당 변경사항을 확인 후 yes 를 입력하면 실제 삭제가 이루어 집니다.

---

# 글을 마치며..

이번 글에서는 실제로 ```Terraform``` 을 이용하여 접근 가능한 웹 서버 인프라를 구성하는 실습을 해 보았습니다.  
```Terraform``` 을 이용하면 파일을 준비하는 것은 직접 인프라를 띄우는 것보다 복잡하지만, 한번 구성을 해 놓기만 하면 **신뢰성 있게** 인프라를 관리 할 수 있다는 큰 장점이 있습니다. ( 더이상 콘솔에서 이 리소스는 뭐지? 라는 고민을 안하셔도 됩니다! )
    
다음 글은 ```Terraform``` 의 상태 관리에 대한 내용을 작성할 예정입니다.

# 함께 읽으면 좋은 글 
- [테라폼(Terraform) 기초 튜토리얼](https://www.44bits.io/ko/post/terraform_introduction_infrastrucute_as_code)
- [[ Terraform Up&Running ] 왜 테라폼을 써야할까?](https://hvho.github.io/2021-07-25/terraform-up-and-running-1)


