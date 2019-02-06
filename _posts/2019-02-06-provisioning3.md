---
layout: post
title: ""프로비저닝을 이용한 EC2 인스턴스 생성 실습 3-2"
tags: [Provisioning, terraform, AWS]
comments: true
---

keypair, security group, instance를 동시에 생성해보기

#### '패스트캠퍼스'에서 공부한 내용을 필기노트 형식으로 정리한 것으로 일부 주관적이며 오류가 있을 수 있습니다.

- 패스트캠퍼스 : https://www.fastcampus.co.kr

### # keypair, security group, instance를 동시에 생성하기

#### step 1) 디렉토리 생성 및 이동

$ mkdir -p ~/python3/provisioning/04_keypair_security_group_ec2

$ cd ~/python3/provisioning/04_keypair_security_group_ec2

#### step 2) 앞서 생성했던 keypair와 security_group을 제거

step 3) main.tf 작성

provider "aws" {

region = "ap-northeast-2" # Seoul

}

** 아래 코드는 key_pair를 test_key라는 이름으로 생성한다는 의미

resource "aws_key_pair" "dss_key" {

key_name = "dss_key"

public_key = "${file("~/.ssh/dss_key.pub")}"

}

** 아래코드는 ssh security_group을 생성
resource "aws_security_group" "dss-terraform" {

name = "security_dss"

description = "Allow SSH port from all"

ingress {

from_port = 22

to_port = 22

protocol = "tcp"

cidr_blocks = ["0.0.0.0/0"]

}

ingress {

from_port = 80

to_port = 80

protocol = "tcp"

cidr_blocks = ["0.0.0.0/0"]

}

ingress {

from_port = 8888

to_port = 8888

protocol = "tcp"

cidr_blocks = ["0.0.0.0/0"]

}

ingress {

from_port = 8080

to_port = 8080

protocol = "tcp"

cidr_blocks = ["0.0.0.0/0"]

}

}

resource "aws_instance" "dss-ec2" {

ami = "ami-06e7b9c5e0c4dd014"

instance_type = "t2.nano"

key_name = "${aws_key_pair.dss_key.key_name}"

vpc_security_group_ids = [

** 아래 코드는 ssh security_group을 적용한다는 의미

"${aws_security_group.dss-terraform.id}"

]

tags {

** 아래 코드는 인스턴스 이름을 의미

Name = "dss_instance"

}
}

#### step4) .tf 스크립트 확인 및 플러그인 설치, 인스턴스 실제 생성

$ terraform init

$ terraform plan

$ terraform apply -auto-approve

#### step 5) 키페어, 보안그룹, ec2가 잘 생성되었는지 확인한다.

- ssh 접속으로 생성한 인스턴스 정상접속여부 확인

$ ssh -i ~/.ssh/dss_key ubuntu@(생성한 인스턴스 아이피주소)

#### step 6) 생성한 인스턴스 삭제

terraform destroy -auto-approve
