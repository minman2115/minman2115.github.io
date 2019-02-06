---
layout: post
title: "프로비저닝을 이용한 EC2 인스턴스 생성 실습 3-1"
tags: [Provisioning, terraform, AWS]
comments: true
---

.

#### '패스트캠퍼스'에서 공부한 내용을 필기노트 형식으로 정리한 것으로 일부 주관적이며 오류가 있을 수 있습니다.

- 패스트캠퍼스 : https://www.fastcampus.co.kr

### # 간단한 EC2 인스턴스 생성

#### step1) 디렉토리 생성 및 이동

$ mkdir -p ~/python3/provisioning/03_simple_ec2

$ cd ~/python3/provisioning/03_simple_ec2

#### step2) 앞서 생성했던 keypair와 security_group을 다시 생성

#### step3) main.tf 생성

- vi main.tf 입력 후 아래와 같이 코드를 작성

provider "aws" {

region = "ap-northeast-2" # Seoul

}

data "aws_security_group" "security_dss" {

name = "security_dss"

}

resource "aws_instance" "dss-ec2" {

ami = "ami-06e7b9c5e0c4dd014"

instance_type = "t2.nano"

key_name = "dss_key"

vpc_security_group_ids = [

"${data.aws_security_group.security_dss.id}"

]

tags {

Name = "dss_instance"

}

}

#### step4) .tf 스크립트 확인 및 플러그인 설치

$ terraform init

$ terraform plan 

#### step5) 실제 실행 : 인스턴스 생성

- 아래와 같이 명령어를 입력한다.

terraform apply -auto-approve

참고로 위와 같이 auto-approve 옵션을 추가하면 인스턴스 생성시 yes 를 입력하지 않아도 된다.

- 중간에 yes를 입력해야 생성된다.
- 실행 후 인스턴스가 생성되었는지 확인
- 인스턴스에 dss_key가 적용되었는지 확인
- 인스턴스에 default security_group이 적용되었는지 확인

#### step6) 생성한 인스턴스에 접속해보기

'ssh -i ~/.ssh/dss_key ubuntu@(생성한 인스턴스 아이피)' 명령어 실행

#### step7) 생성한 인스턴스 삭제

'terraform destroy -auto-approve' 명령어 입력 후 실행
