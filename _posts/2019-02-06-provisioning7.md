---
layout: post
title: "remote provisioner 사용실습 2-2"
tags: [Provisioning, terraform, AWS]
comments: true
---

.

#### '패스트캠퍼스'에서 공부한 내용을 필기노트 형식으로 정리한 것으로 일부 주관적이며 오류가 있을 수 있습니다.

- 패스트캠퍼스 : https://www.fastcampus.co.kr

#### # connection 과 remote-exec 이용한 예시

#### step1) 디렉토리 생성 및 이동

$ mkdir -p ~/python3/provisioning/08_remote_provisioner_2

$ cd ~/python3/provisioning/08_remote_provisioner_2

#### step2) main.tf 생성

- dss_key 키페어와 security_dss 보안그룹이 생성되어 있는 상태에서 실행한다.

provider "aws" {

region = "ap-northeast-2"

}

data "aws_security_group" "security_dss" {

name = "security_dss"

}

resource "aws_instance" "remote_provisioner_2" {

ami = "ami-06e7b9c5e0c4dd014"

instance_type = "t2.nano"

key_name = "dss_key"

vpc_security_group_ids = [

"${data.aws_security_group.security_dss.id}"

]

connection {

user = "ubuntu"

type = "ssh"

private_key = "${file("~/.ssh/dss_key")}"

timeout = "1m"

}

provisioner "remote-exec" {

inline = [

"echo 'hello world' >> index.txt",

]

}

tags {

Name = "remote_provisioner_2"

}

}

#### step3) output.tf 생성

output "public_ip" {

value = "${aws_instance.remote_provisioner_2.public_ip}

}

#### step4) .tf 스크립트 확인 및 플러그인 설치, 인스턴스 실제 생성

$ terraform init

$ terraform plan

$ terraform apply -auto-approv

#### step5) 인스턴스 접속

$ ssh -i ~/.ssh/dss_key ubuntu@(생성한 인스턴스 아이피주소)

#### step6) index.txt 확인

$ cd /

$ cat index.txt

#### step7) 인스턴스 삭제

$ terraform destroy -auto-approve
