
# *Unbuntu Deploy Guide*

### Docker 설치방법

1. Docker 설치(윈도우)

```
사이트 (https://www.docker.com/products/docker-desktop/)
```
2. Docker 설치(우분투)

```
명령어(apt-get install docker.io)
```

### Docker 기본 명령어

1. 우분투 이미지 PULL받기
```
docker pull ubuntu:16.04 
```
2. 컨테이너 생성하기 
```
docker run -d --privileged -p{포워딩할 임의 포트}:8080 -p{포워딩할 임의 포트}:3306 --name {컨테이너 이름} ubuntu:16.04 /sbin/init
docker exec -it {컨테이너 이름} /bin/bash
```


## vagrant Install (virtualBox 있어야 사용가능 https://www.virtualbox.org/)

1. vagrant 설치 (https://www.vagrantup.com/downloads)

### Vagrant 기본 명령어

Vagrant를 사용하기 위해서 반드시 알아야 하는 기본 명령어들이 있다. 다음을 기억하고 넘어가도록 한다.

-   **vagrant init**
    -   베이그런트를 프로비저닝 하기 위한 Vagrantfile을 생성한다.
-   **vagrant up**
    -   작성된 Vagrantfile을 바탕으로 프로비저닝을 진행한다.
-   **vagrant reload**
    -   베이그란트에서 설정바꾼것을 적용한다.
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



#### VagrantFile에 복사 붙이기
```
# -*- mode: ruby -*-
# vi: set ft=ruby :

MEMORY = 4096
CPU_COUNT = 2

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  # config.disksize.size = '20GB'

  config.vm.network "private_network", ip: "196.168.33.2"
  config.ssh.username ="vagrant"
  config.ssh.password ="vagrant"
  config.ssh.insert_key = false
  config.vm.network "private_network", ip: "196.168.33.2"

  config.vm.network "forwarded_port", guest: 22, host: 4085
  config.vm.network "forwarded_port", guest: 8080, host: 4086
  config.vm.network "forwarded_port", guest: 9000, host: 4087
  config.vm.network "forwarded_port", guest: 3306, host: 4088

  config.vm.synced_folder  ".", "/vagrant", disabled: false

  config.vm.provider "virtualbox" do |vb|
      vb.customize ["modifyvm", :id, "--memory", MEMORY.to_s]
      vb.customize ["modifyvm", :id, "--cpus", CPU_COUNT.to_s]
  end
end
```


#### Vagrant 시작
```
vagrant up
vagrant plugin install vagrant-winnfsd (NFS가 없어서 Vagrant 안되는 경우 )
```

#### Vagrant ssh 접속
```
vagrant ssh
```

#### 우분투 Download 속도 올리는 방법 (카카오버전)
```
apt-get update
apt-get install vim
cp /etc/apt/sources.list /etc/apt/sources.list_back
vi /etc/apt/sources.list
:%s/archive.ubuntu.com/ftp.daumkakao.com (명령어 편집기 모드에서) (카카오모드 우분투)
```

#### 우분투 ssh 접근가능 명령어
```
vi /etc/ssh/sshd_config
PasswordAuthentication을 yes로 수정 그리고 저장
/etc/init.d/ssh restart
```

## JAVA 및 TOMCAT 설치

#### Java1.8 JDK JRE 다운로드
```
apt-get install openjdk-8-jre-headless

apt-get install openjdk-8-jdk

java -version
```

#### 톰캣8 설치
```
apt-get install tomcat8
```



#### 톰캣9 설치

/opt / tomcat에 홈 디렉토리가있는 새 시스템 사용자 및 그룹을 만들기
```
sudo apt install default-jdk
sudo useradd -m -U -d /opt/tomcat -s /bin/false tomcat
```
톰캣을 설치하기 위한  unzip wget다운로드 
```
apt install unzip wget
```
/tmp 디렉토리로 이동하고 wget으로 톰캣zip 파일을 다운
```
cd /tmp

wget http://apache.rediris.es/tomcat/tomcat-9/v9.0.10/bin/apache-tomcat-9.0.10.zip (안될경우 해당사이트가서 우클릭해서 주소링크복사하기)
```
톰캣 아이디추가
```
useradd -m -U -d /opt/tomcat -s /bin/false tomcat
```

다운로드가 완료되면 zip 파일의 압축을 풀고 / opt / tomcat 디렉토리로 이동합니다.

```
unzip apache-tomcat-*.zip
mv apache-tomcat-*/ /opt/tomcat/
```
버전 및 업데이트를 더 잘 제어 할 수 있습니다, 설치 디렉토리를 가리키는 심볼릭 링크를 생성합니다.
```
ln -s /opt/tomcat/apache-tomcat-* /opt/tomcat/latest
```

이전에 설정 한 Tomcat 사용자는 Tomcat 9 디렉토리에 액세스. 디렉토리의 소유권을 tomcat의 사용자 및 그룹으로 변경해야합니다.
```
chown -R tomcat: /opt/tomcat
```
bin 디렉토리 내의 스크립트를 실행 가능하게 만들 것입니다.
```
chmod +x /opt/tomcat/latest/bin/*.sh
```

Tomcat을 서비스로 실행하려면 **tomcat.service라는 새 유닛 파일을 생성합니다.** 저장해야합니다 **/ etc / systemd / system / 디렉토리 내부** 다음 내용으로
```
[Unit]

Description=Tomcat 9 servlet container

After=network.target

[Service]

Type=forking

User=tomcat

Group=tomcat

Environment="JAVA_HOME=/usr/lib/jvm/default-java"

Environment="JAVA_OPTS=-Djava.security.egd=file:///dev/urandom"

Environment="CATALINA_BASE=/opt/tomcat/latest"

Environment="CATALINA_HOME=/opt/tomcat/latest"

Environment="CATALINA_PID=/opt/tomcat/latest/temp/tomcat.pid"

Environment="CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC"

ExecStart=/opt/tomcat/latest/bin/startup.sh

ExecStop=/opt/tomcat/latest/bin/shutdown.sh

[Install]

WantedBy=multi-user.target
```

#### 새 단위 파일을 생성했음을 systemd에 알립니다. 
```
systemctl daemon-reload
systemctl start tomcat
systemctl status tomcat
systemctl enable tomcat
ufw allow 8080/tcp
```

## Mysql 설치 및 설정

#### Mysql 설치
```
apt-get install -y mysql-server ufw
```
#### Mysql 로컬주소 바인딩 주석
```
vim /etc/mysql/mysql.conf.d/mysqld.cnf

#bind-address 127.0.0.1 <-- 주석처리

service mysql restart
```

#### Mysql 접속 및 권한주기
```
mysql -u root -p

show databases;

use [데이터베이스 명<스키마 명>]

create user 'root'@'%' identified by '123456';

grant all privileges on *.* to 'root'@'%' with grant option;

create user 'kice'@'%' identified by '123456';

grant all privileges on *.* to 'kice'@'%' with grant option;

flush privileges;

service mysql restart

ufw allow out 3306/tcp

ufw allow in 3306/tcp

service mysql restart
```

#### Mysql 비밀번호 재설정
```
mysql_secure_installation

```

#### Mysql 덤프 

```
create schema [데이터베이스 명] (데이터베이스<스키마> 만들기)

mysqldump(mysql) -u [자신아이디] -p [스키마명] > [파일명].sql (데이터베이스 export하기)
mysqldump(mysql) -u [자신아이디] -p [스키마명] < [파일명].sql (데이터베이스 import하기)

```

## GitHub key받기


![1](https://user-images.githubusercontent.com/39364010/187109474-377adc11-2c99-4936-882e-809cb2508204.png)

![2](https://user-images.githubusercontent.com/39364010/219526631-9a1de815-6cbf-4fc3-93f2-e8d4b1aee447.png)

![3](https://user-images.githubusercontent.com/39364010/219526645-945ed22d-7ee4-4cda-9c4f-be45f7fb8e29.png)

![4](https://user-images.githubusercontent.com/39364010/219526653-676f4892-5f77-45c3-8626-776b67685a48.png)

![5](https://user-images.githubusercontent.com/39364010/219526667-f1a02648-9fc5-4bfd-be76-88468f71dd24.png)

## Jenkins 설치 및 구동

#### Jenkins 설치 하기전 
```
apt-get install -y sudo apt-transport-https ca-certificates

wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | apt-key add - 

(wget --no-check-certificate  -vO - https://pkg.jenkins.io/debian-stable/jenkins.io.key | apt-key add -) <- 위의것이 안될경우

sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'

apt-get update
```

#### Jenkins 설치
```
apt-get install jenkins  

service jenkins status
```

#### Jenkins 포워딩 포트 변경
```
vi /etc/default/jenkins

HTTP_PORT=8080 -> HTTP_PORT=9000 (변경)
```

#### Jenkins 접속 권한 변경
```
vi /etc/sudoers

root ALL=(ALL:ALL) ALL 밑에 -> jenkins ALL=(ALL:ALL) NOPASSWD: ALL 추가

service jenkins restart
```

#### Jenkins 비밀번호 확인 (사이트 들어가자 마자 바로 넣을 비번)
```
cat /var/lib/jenkins/secrets/initialAdminPassword
```

#### Jenkins 구동을 위한 shell스크립트 작성
```
cd /home/

vi deploy.sh

------------------------------ (작성)
echo "start kice core deploy"

cd /var/lib/jenkins/workspace/test5/test5/

mvn clean install

cd /var/lib/jenkins/workspace/test5/test5/target/

mv test5-1.0.0.war test5.war

mv test5.war /var/lib/tomcat8/webapps/

echo "end kice core deploy"
------------------------------ (작성)


--------------------------------------<<쉘 스크립트 부가설명>>------------------------------------------

echo "start kice core deploy"

cd /var/lib/jenkins/workspace/test5/test5/ (pom.xml 있는 안쪽 프로젝트까지 들어가기)

mvn install (maven 에서 제공하는 명령어 이고 pom.xml이 있어야 작동된다) 

cd /var/lib/jenkins/workspace/test5/test5/target/ (mvn 인스톨시 타겟 파일 안에 war파일이 생긴다) 

ex) 해당 프로젝트에 pom.xml에 들어가면 그룹 아이디에 war파일 생기는 방식 
###################################
<groupId>test5</groupId>
	<artifactId>test5</artifactId>
	<packaging>war</packaging>
	<version>1.0.0</version>
	<name>test5</name>
<url>http://www.egovframe.go.kr</url>
------> test5-1.0.0.war ( artifactId-version.war 형식)
###################################


mv test5-1.0.0.war test5.war (파일명 변경)

mv test5.war /var/lib/tomcat8/webapps/ (톰캣으로 파일을 옮기기)

echo "end kice core deploy"

---------------------------------------------------------------------------------------------------------

```



