---
layout: post
title: "local provisioner 사용 실습"
tags: [Provisioning, terraform, AWS]
comments: true
---

.

#### '패스트캠퍼스'에서 공부한 내용을 필기노트 형식으로 정리한 것으로 일부 주관적이며 오류가 있을 수 있습니다.

- 패스트캠퍼스 : https://www.fastcampus.co.kr

#### step1) ec2 생성후 생성된 public ip를 ip_address.txt로 저장

#### step2) 앞서 생성했던 keypair와 security_group을 다시 생성

#### step3) 디렉토리 생성 및 이동

$ mkdir -p ~/python3/provisioning/06_local_provisioner

$ cd ~/python3/provisioning/06_local_provisioner

#### step4) main.tf 생성

provider "aws" {

region = "ap-northeast-2" # Seoul

}

data "aws_security_group" "security_dss" {

name = "security_dss"

}

resource "aws_instance" "dss_local_provisioner" {

ami = "ami-06e7b9c5e0c4dd014"

instance_type = "t2.nano"

key_name = "dss_key"

vpc_security_group_ids = [

"${data.aws_security_group.security_dss.id}"

]

provisioner "local-exec" {

command =

"echo \

${aws_instance.dss_local_provisioner.public_ip} \

`> public_ip.txt"`

}

tags {

Name = "dss_local_provisioner"

}

}

#### ** 위 코드에서 참고사항

- 여러개의 커멘트를 실행해야 하는 경우 아래와 같이 수정 후 실행

provisioner "local-exec" {

command = <<EOF

echo ${aws_instance.dss-ec2.public_ip} > public_ip.txt

echo ${aws_instance.dss-ec2.private_ip} > private_ip.txt

EOF

}

- terraform.tfstate 파일 삭제후 실행해야 수정된 내용이 적용

#### step5) .tf 스크립트 확인 및 플러그인 설치, 인스턴스 실제 생성

$ terraform init

$ terraform plan

$ terraform apply -auto-approve

#### step6) 로컬에 ip 주소 파일이 생성되었는지 확인

$ cat public_ip.txt

(생성된 아이피 주소 출력)

#### step7) 테라폼 콘솔로 변수 확인

$ terraform console

`> aws_instance.dss-ec2.public_ip`

(생성된 아이피 주소 출력)

#### step 8) 생성한 인스턴스 삭제

terraform destroy -auto-approve
