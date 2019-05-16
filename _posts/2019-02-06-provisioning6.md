---
layout: post
title: "remote provisioner 사용실습 2-1"
tags: [terraform]
comments: true
---

.

#### 그림, 실습코드 등 학습자료 출처 : https://gitlab.com/radajin
	
#### # user_data를 이용한 로컬 프로비저너 사용예시

#### step1) 디렉토리 생성 및 이동

$ mkdir -p ~/python3/provisioning/07_remote_provisioner

$ cd ~/python3/provisioning/07_remote_provisioner

#### step2) 생성한 키와 보안그룹이 있다면 다 삭제

#### step3) main.tf 작성

- default security_group을 추가해줘야 user_data 사용가능

- 생성한 security_group에 22번 포트가 있어야 접속가능

data "aws_security_group" "default" {

name = "default"

}

data "aws_security_group" "security_dss" {

name = "security_dss"

}

provider "aws" {

region = "ap-northeast-2"

}

resource "aws_instance" "dss_remote_provisioner" {

ami = "ami-06e7b9c5e0c4dd014"

instance_type = "t2.nano"

key_name = "dss_key"

vpc_security_group_ids = [

"${data.aws_security_group.default.id}",

"${aws_security_group.security_dss.id}"

]

`# root 권한으로 실행됨 (최상위 디렉토리에서 실행)`

user_data = <<EOF

`#!/bin/bash`

echo "Hello, World" > index.txt

EOF

tags {

Name = "dss_remote_provisioner" `# 인스턴스 이름`

}

}

#### step4) output.tf 작성

output "public_ip" {

value = "${aws_instance.dss_remote_provisioner.public_ip}

}

#### step5) .tf 스크립트 확인 및 플러그인 설치, 인스턴스 실제 생성

$ terraform init

$ terraform plan

$ terraform apply -auto-approv

#### step6) 인스턴스 접속

$ ssh -i ~/.ssh/dss_key ubuntu@(생성한 인스턴스 아이피주소)

#### step7) index.txt 확인

$ cd /

$ cat index.txt
