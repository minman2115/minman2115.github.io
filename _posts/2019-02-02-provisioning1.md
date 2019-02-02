---
layout: post
title: "프로비저닝을 위한 테라폼 환경설정 실습정리"
tags: [Provisioning, terraform, AWS]
comments: true
---

.

#### '패스트캠퍼스'에서 공부한 내용을 필기노트 형식으로 정리한 것으로 일부 주관적이며 오류가 있을 수 있습니다.

- 패스트캠퍼스 : https://www.fastcampus.co.kr

### # awscli 설치

- 선행작업 : 파이썬3 설치
- 'conda install -c conda-forge awscli' 명령어를 이용하여 'AWS CLI' 패키지 설치
- 'AWS CLI' 설치확인

-> 터미널에서 'aws --version' 명령어 입력 시 버전출력

### # terraform 설치

- https://www.terraform.io/downloads.html 접속
- 윈도우 64버전으로 다운로드 및 설치

### # Key 등록

#### step1) IAM으로 키 발급

- AWS 콘솔화면에서 IAM 메뉴로 접속
- 좌측에 사용자 탭으로 접속 및 상단의 '사용자 추가' 파란색단추 버튼 클릭
- 프로그래밍 방식 access와 'AWS Management Console' access 모두 체크하고 비밀번호를 임의로 설정하여 계정을 생성
- .csv 파일을 다운 받아서 파일을 확인하면 발급된 Access Key와 Secret Access Key 확인(별도의 공간에 저장하고 삭제하지 않도록 한다.)
- 생성한 계정의 권한에 AmazonEC2FullAccess 권한 추가

#### step2) AWS CLI에 Key 등록

- 다음과 같은 과정으로 key를 등록한다.

1) 리눅스 서버 접속

2) 다음과 같이 입력

$ aws configure

AWS Access Key ID [None]: (본인의 Access Key)

AWS Secret Access Key [None]: (본인의 Secret Access Key)


Default region name [None]: ap-northeast-2 (서울지역을 말한다)

Default output format [None]: json

### # keypair 생성

#### step1) 인스턴스 접속 시 필요한 키페어를 생성


1) 디렉토리 생성 및 이동 

$ mkdir -p ~/python3/provisioning/01_keypair

$ cd ~/python3/provisioning/01_keypair

2) key 생성

- `./ssh` 디렉토리에 `dss_key`(비밀키)와 `dss_key.pub`(공개키) 파일 생성

`$ ssh-keygen -t rsa -b 4096 -C "(이메일주소)" -f "$HOME/.ssh/dss_key" -N ""`

3) 생성확인

$ ls ~/.ssh

`authorized_keys dss_key dss_key.pub`

#### step2) `main.tf`생성

1) 01_keypair 폴더로 이동

2) vi main.tf 입력

3) 아래와 같은 코드 입력

provider "aws" {

region = "ap-northeast-2" # Seoul

}

resource "aws_key_pair" "dss_key" {

key_name = "dss_key"

public_key = "${file("~/.ssh/dss_key.pub")}"

}

4) .tf 스크립트 확인 및 플러그인 설치

- $ terraform init 입력

5) 실행 계획 확인

- $ terraform plan 입력

6) 실제 실행

- AmazonEC2FullAccess 권한이 있어야 생성과 삭제가 가능

- 실행 후 키페어가 생성되었는지 확인

$ terraform apply 입력

- 중간에 yes를 입력

7) 인스턴스 삭제

- $ terraform destroy 입력

- 중간에 yes를 입력해야 삭제가 됨
- 실행 후 폴더 내에 키가 잘 삭제되었는지 확인

### # security group 생성

1) 디렉토리 생성 및 이동

$ mkdir -p ~/python3/provisioning/02_security_group

$ cd ~/python3/provisioning/02_security_group

2) 인스턴스 생성 시 ssh 접속을 위한 시큐리티 그룹 생성

- vi main.tf 입력

- 서울지역에 22,80,8888,8080 포트를 여는 security group을 생성하는 코드를 아래와 같이 작성한다. 

provider "aws" {

region = "ap-northeast-2"

}

resource "aws_security_group" "dss-terraform" {

name = "security_dss"

description = "dss group"

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

3) 실행확인 및 인스턴스 삭제

- $ terraform init

- $ terraform plan

- $ terraform apply

- $ terraform destroy
