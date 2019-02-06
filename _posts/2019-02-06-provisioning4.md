---
layout: post
title: "프로비저닝을 이용한 EC2 인스턴스 생성 실습 3-3"
tags: [Provisioning, terraform, AWS]
comments: true
---

variable과 output을 이용하여 인스턴스 생성해보기

#### '패스트캠퍼스'에서 공부한 내용을 필기노트 형식으로 정리한 것으로 일부 주관적이며 오류가 있을 수 있습니다.

- 패스트캠퍼스 : https://www.fastcampus.co.kr

#### step1) 디렉토리 생성 및 이동

$ mkdir -p ~/python3/provisioning/05_vars_output

$ cd ~/python3/provisioning/05_vars_output

#### step2) vars.tf 생성 및 코드 작성

variable "server_port" {

description = "server_port"

default = 8080

}

variable "ami" {

type = "map"

default = {

ubuntu = "ami-06e7b9c5e0c4dd014"

}

}

#### step3) terraform console에서 선언된 변수 확인

$ terraform console

-> var.server_port

8080

-> var.ami["ubuntu"]

ami-06e7b9c5e0c4dd014

#### step4) vars.tf 생성 및 코드 작성

provider "aws" {

region = "ap-northeast-2"

}

resource "aws_security_group" "dss_sg" {

name = "dss_sg"

ingress {

from_port = "${var.server_port}"

to_port = "${var.server_port}"

protocol = "tcp"

cidr_blocks = [

"0.0.0.0/0",

]

}

}

resource "aws_instance" "vars_output" {

ami = "${var.ami["ubuntu"]}"

instance_type = "t2.nano"

vpc_security_group_ids = [

"${aws_security_group.dss_sg.id}"

]

tags {

Name = "dss_vars_output"

}

}

#### step5) output.tf 생성 및 코드 작성

output "public_ip" {

value = "${aws_instance.vars_output.public_ip}"

}

#### step6) .tf 스크립트 확인 및 플러그인 설치, 인스턴스 실제 생성

$ terraform init

$ terraform plan

$ terraform apply -auto-approve

#### step 7) 인스턴스 생성 후 output 변수가 잘 출력되었는지 확인

#### step 8) 생성한 인스턴스 삭제

terraform destroy -auto-approve
