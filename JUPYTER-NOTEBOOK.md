# 주피터 노트북 설치 및 설정

## 주피터 설치 

sudo apt-get update

sudo apt-get -y install python2.7 python-pip python-dev

python -version

pip -version

sudo pip install --upgrade pip

sudo pip install jupyter

## 주피터 설정

jupyter notebook

jupyter notebook password

jupyter notebook --generate-config

## R커널 추가 설치

sudo apt-get install r-base r-base-dev

R

install.packages('IRkernel')

IRkernel::installspec()

IRkernel::installspec(user = FALSE)

## 참고

http://yhzion.tistory.com/17

https://github.com/jupyter/jupyter/wiki/Jupyter-kernels