# Requirement

Tensorflow는 python3에서 더 잘동작한다고 한다.  
따라서 python3를 python 기본버전으로 하여 사용하는것이 바람직하다.


# valid for this session only
~/.bashrc 에 아래 커맨드를 추가  
alias python=python3  

>sudo apt install python-dev  
>sudo apt install python3-pip

# Installation and Dependencies

## Virtualenv

python의 가상환경이란, 작은 python을 새로 설치해서 내가 원하는 모듈만 운용하는 바구니라고 생각하면 된다.  

>virtualenv ssd

위 작업이 완료되면 내가 지정했던 ssd라는 폴더가 생성되고 그 안에 새로 python이 설치되어있음을 알 수 있다.  

>source ~(ssd폴더 경로)/ssd/bin/activate  

위 명령어를 터미널에 입력시 가상환경을 activate 시킬수 있다. 간편하게 사용하기위해 bashrc에 alias를 등록하여 사용하도록 하자.  

>alias ssd='source ~/ml/virtualenv/ssd/bin/activate'  

가상환경을 activate 시키면

>(ssd) kdh@buzz ~/ml/virtualenv $ 

(ssd) 가 따라 붙어다니는것을 볼 수 있다.  
이제 가상환경에 필요한 python 모듈을 설치하면 된다.  
설치한 목록은 다음 명령어를 통해 정확한 리스트로 저장해두자.
>pip freeze > requirements.txt

이제 requirements.txt 파일에서 설치된 모듈과 그 버전이 리스트되어 저장되어 있음을 알 수 있다.

가상환경을 나가고 싶으면 간단히 deactivate라고 명령하면 된다.
>deactivate

## Tensorflow-gpu install
>pip3 install setuptools  
>pip3 install wheel

gpu version (recommend)
> pip3 install tensorflow-gpu

cpu version
> pip3 install tensorflow

## Libraries install
Protobuf 3.0.0, Python-tk , Pillow 1.0 & lxml 
>sudo apt-get install protobuf-compiler python-pil python-lxml python-tk

Cython
>pip3 install --user cython

Contextlib2
>pip3 install --user contextlib2

Jupyter
>pip3 install --user jupyter

Matplotlib
>pip3 install --upgrade pip==9.0.1
>pip3 install --user matplotlib 

Pandas
>pip3 install --user pandas

>pip3 install --user pycocotools

>pip3 install --user tensorflow-object-detection-api

## Download Tensorflow model repository
> git clone --quiet https://github.com/tensorflow/models.git 

# Protobuf Compilation
다운받은 repository 경로로 이동한다. 
>cd ~(받은경로)/models/research/

> protoc object_detection/protos/*.proto --python_out=.

## Manual Protobuf Compilation (Only if you had problem with above code)
컴파일시 에러가 나면 수동으로 컴파일한다.

> mkdir protoc_3.0
> cd protoc_3.0
> wget -O protobuf.zip https://github.com/google/protobuf/releases/download/v3.0.0/protoc-3.0.0-linux-x86_64.zip
> unzip protobuf.zip
> cd ..
> ./protoc_3.0/bin/protoc object_detection/protos/*.proto --python_out=.

## Add Libraries to PYTHONPATH
pythonpath에 models/research/, /slim 경로를 추가해야됨

>export PYTHONPATH=`$PYTHONPATH`:'pwd':'pwd'/slim  
>예시 : export PYTHONPATH=`$PYTHONPATH`:~/ml/models/research:~/ml/models/research/slim

# Testing the Installation
> python object_detection/builders/model_builder_test.py