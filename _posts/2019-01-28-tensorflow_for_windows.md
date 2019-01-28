---
layout: post
title: "딥러닝을 위한 환경설정법"
tags: [딥러닝, 환경설정]
comments: true
---

'윈도우즈 운영체제에서 텐서플로우 활용을 위한 아나콘다 가상환경 구축' 방법

#### step 1) cuda 9.0 설치
- https://developer.nvidia.com/cuda-90-download-archive 접속
- 내 컴퓨터 사양으로 선택하고 install type은 local로 선택한다.
- 다운로드 및 설치

#### step2) cuDnn 설치
- https://developer.nvidia.com/rdp/cudnn-download 접속
- (만약에 nvidia계정이 없으면 생성해야함)
- cuda9.0 선택, os선택
- 다운로드 및 압축해제
- bin폴더, include폴더, lib폴더, NVIDIA_SLA_cuDNN_Support.txt파일 복사해서 cuda설치경로로 붙어넣기.

경로예시) C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v9.0


#### step 3) 명령프롬프트(관리자권한) 실행

#### step 4) "conda create -n 가상환경이름 python=3.6" 명령어 실행
- 파이썬 3.6버전을 사용할 수 있는 가상환경이 생성된다
ex) conda create -n VE python=3.6

#### step 5) "activate 가상환경이름" 명령어를 실행하여 생성한 가상환경을 활성화 한다.
ex) activate VE

- "conda info --envs" 명령어는 내가 생성한 가상환경이 어떤게 있는지 확인할 수 있는 명령어이다.
- "deactivate"는 가상환경을 비활성화 시키는 명령어이다.
- 가상환경 삭제는 'conda remove --name 가상환경이름 --all'

ex) conda remove --name VE --all

- 아래는 예시

![1](https://user-images.githubusercontent.com/41605276/51814348-6307bd80-22fe-11e9-9e3e-255f34736206.png)

#### step 6) 'tensorflow' 패키지 설치

- 가상환경을 활성화한다.
- 'pip install tensorflow' 명령어를 이용하여 패키지를 설치한다.
- (만약에 내 딥러닝 연산 시 gpu 안쓰려면 저명령어에 -gpu뺀것을 입력한다.)
- 패키지 설치가 완료되면 다음과 같이 실행하여 문제가 없는지 확인한다.

![2](https://user-images.githubusercontent.com/41605276/51814351-6ac76200-22fe-11e9-8b70-c1cd751b6b96.png)

#### step 7) 'keras', 'theano'설치

- 'conda install keras' 명령어로 케라스 설치
- 'conda install theano' 명령어로 띠아노 설치

#### step 8) '주피터 노트북' 다중커널 설정

- 'ipython' 패키지 설치 
- 'ipykernel' 패키지 설치
- 'ipython kernel install --user' 명령어로 새로운 주피터 커널을 생성한다.
- C:\Users\사용자이름\Anaconda3\share\jupyter\kernels 밑에 가상개발환경을 위한 커널폴더를 만들고 kernel.json 파일을 생성한다.

기본적으로 python3 폴더가 있고 그안에 kernel.json파일이 있다. kernel.json파일에는 주피터 노트북에서 기본으로 사용할 수 있는 Python3 노트북의 설정이 여기 있는데, 그 내용은 다음과 같다.

{

"argv": [

"C:\\Users\\사용자이름\\Anaconda3\\python.exe",

"-m",

"ipykernel_launcher",

"-f",

"{connection_file}"
],

"display_name": "Python 3",

"language": "python"

}

- 가상개발환경을 위한 노트북용 폴더(폴더이름이 가상개발환경의 이름과 일치하지 않아도 된다)를 하나 만들고 python3 폴더 내에 kernel.json 파일을 복사해서 가상환경을 위한 노트북용 폴더 안에 붙어넣어준다.

- kernel.json 파일 편집

kernel.json의 세 번째 행은 파이썬 실행파일이 있는 경로와 파일명이다. 보통 가상개발환경은 C:\Users\사용자이름\Anaconda3\envs 아래에 만들어 진다. 그리고 가상개발환경 폴더에 python.exe가 있다. 그리고 "display_name" 에는 주피터 노트북 이름을 적어준다, 이름은 가상개발환경의 그것과 달라도 된다.

{

"argv": [

"C:\\Users\\사용자이름\\Anaconda3\\envs\\가상개발환경을 위한 노트북용 폴더이름\\python.exe",

"-m",

"ipykernel_launcher",

"-f",

"{connection_file}"

],

"display_name": "가상환경이름",

"language": "python"
}

- 주피터 노트북 실행 후 아래와 같은 그림처럼 가상환경의 커널이 추가되었는지 확인한다.

![3](https://user-images.githubusercontent.com/41605276/51814357-7581f700-22fe-11e9-93c3-c9e4928e332b.png)

#### step 9) 아래와 같이 텐서플로우 사용을 위한 패키지들을 아나콘다 클라우드에서 검색하여 인스톨 명령어로 설치한다.

- 아나콘다 클라우드 URL : https://anaconda.org/
- 텐서플로우 사용을 위한 패키지 목록

matplotlib, seaborn, scikit-learn
