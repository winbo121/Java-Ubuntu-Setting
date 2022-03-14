
## KiceCore Unbuntu 18.04 Deploy Guide

**Install 목록** 
1. vagrant 설치
2. tomcat 9 설치
3. jdk1.8 설치
4. nginx 설치
5.  Jenkins 설치 (선택사항)

## Install

1. vagrant 설치 (https://www.vagrantup.com/downloads)

### Vagrant 기본 명령어

Vagrant를 사용하기 위해서 반드시 알아야 하는 기본 명령어들이 있다. 다음을 기억하고 넘어가도록 한다.

-   **vagrant init**
    -   베이그런트를 프로비저닝 하기 위한 Vagrantfile을 생성한다.
-   **vagrant up**
    -   작성된 Vagrantfile을 바탕으로 프로비저닝을 진행한다.
-   **vagrant halt**
    -   베이그런트에서 관리하는 가상 머신을 종료한다.
-   **vagrant destroy** (조심  -> 박스 사라짐)
    -   베이그런트에서 관리하는 가상 머신을 삭제한다.
-   **vagrant ssh**
    -   베이그런트에서 생성된 가상 머신에 ssh로 접속한다.
-   **vagrant provision**
    -   베이그런트에서 관리하는 가상 머신의 설정을 변경하고 적용한다.


#### vagrant 설치후 해당 폴더에서
```
vagrant init
```

#### vagrantFile 포트포워딩  관련 사용중인 포트확인

cmd open
```
netstat -ano
```


vagrantFile
```
# -*- mode: ruby -*-
# vi: set ft=ruby :

MEMORY = 4096
CPU_COUNT = 2

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  # config.disksize.size = '20GB'

  config.vm.network "private_network", ip: "196.168.33.2"

  config.vm.network "forwarded_port", guest: 8080, host: 4086
  config.vm.network "forwarded_port", guest: 9000, host: 4087

  config.vm.synced_folder  ".", "/vagrant", disabled: false

  config.vm.provider "virtualbox" do |vb|
      vb.customize ["modifyvm", :id, "--memory", MEMORY.to_s]
      vb.customize ["modifyvm", :id, "--cpus", CPU_COUNT.to_s]
  end
end
```


Vagrant 시작
```
vagrant up
```

Vagrant ssh 접속
```
vagrant ssh
```