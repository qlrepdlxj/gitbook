## 주피터 노트북 설치 및 설정

#### 주피터 설치 

sudo apt-get update

sudo apt-get -y install python2.7 python-pip python-dev

python -version

pip -version

sudo pip install --upgrade pip

sudo pip install jupyter

#### 주피터 설정

jupyter notebook

jupyter notebook password

jupyter notebook --generate-config

#### R커널 추가 설치

sudo apt-get install r-base r-base-dev

R

install.packages('IRkernel')

IRkernel::installspec()

IRkernel::installspec(user = FALSE)

#### 부팅시 주피터 자동시작 (우분투 18.04 기준)

pip3 install --user jupyter jupyterlab

sudo nano /etc/systemd/system/jupyter.service (*.service 이름은 사용하지 않는 이름으로 아무거나 생성가능)

```
[Unit]
Description=Jupyter Notebook Server

[Service]
Type=simple
User=<username>
ExecStart=/home/<username>/.local/bin/jupyter-notebook
WorkingDirectory=/your/working/dir

[Install]
WantedBy=multi-user.target
```
sudo chmod +x jupyter.service

sudo systemctl daemon-reload

sudo systemctl enable jupyterNotebook

sudo systemctl start jupyterNotebook

sudo systemctl status jupyterNotebook

sudo systemctl restart jupyterNotebook

sudo systemctl stop jupyterNotebook

sudo systemctl disable jupyterNotebook

#### 참고

http://yhzion.tistory.com/17

https://github.com/jupyter/jupyter/wiki/Jupyter-kernels

http://pinedance.github.io/blog/2017/09/12/Ubuntu-16.04-system-service-%EB%93%B1%EB%A1%9D%ED%95%98%EA%B8%B0
