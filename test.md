# Exaplme
```
1. Create a CDH Cluster on AWS

============================================================================================================================================================
1) Linux Setup
============================================================================================================================================================
- System connection (GitBash)
호스트1		ssh -i /c/Users/SKCC/Desktop/bigdata/SKCC.pem centos@13.124.2.159
호스트2		ssh -i /c/Users/SKCC/Desktop/bigdata/SKCC.pem centos@13.125.3.194
호스트3		ssh -i /c/Users/SKCC/Desktop/bigdata/SKCC.pem centos@13.125.63.129
호스트4		ssh -i /c/Users/SKCC/Desktop/bigdata/SKCC.pem centos@13.125.85.171
호스트5		ssh -i /c/Users/SKCC/Desktop/bigdata/SKCC.pem centos@13.209.130.88

- Setup /etc/hosts (All nodes)
  $ sudo vi /etc/hosts
172.31.4.133	nd1.com nd1
172.31.13.112	nd2.com nd2
172.31.3.199	nd3.com nd3
172.31.6.244	nd4.com nd4
172.31.15.131	nd5.com nd5

- add user (All nodes) 
	$ sudo useradd training -u 3800
	$ sudo passwd training
	$ sudo groupadd skcc
	$ sudo usermod -a -G skcc training

>>>>> 1.a.linux setup - add user.PNG	

- Create a password for user “centos” (All nodes)
	$ sudo passwd centos

- Modify sshd_config to allow password login (All nodes)
	$ sudo vi /etc/ssh/sshd_config
		변경 -> PasswordAuthentication yes

- Restart the sshd.service (All nodes)
  $ sudo service sshd restart

- Change the hostname (each of the 5 nodes)
  $ sudo hostnamectl set-hostname nd1.com
  $ sudo hostnamectl set-hostname nd2.com
  $ sudo hostnamectl set-hostname nd3.com
  $ sudo hostnamectl set-hostname nd4.com
  $ sudo hostnamectl set-hostname nd5.com

  $ hostname 
  
  $ getent group skcc
  $ getent passwd training

>>>>> 1.a.linux setup - commands.PNG	  
  

- Configure the repository for CM 5.15.2
  $ sudo yum install -y wget
  $ sudo wget https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/cloudera-manager.repo -P /etc/yum.repos.d/

  $ sudo vi /etc/yum.repos.d/cloudera-manager.repo
  repo 파일의 baseurl 내용을 아래로 변경
  baseurl=https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/5.15.2/

- Install JDK on each of the hosts
  $ sudo yum install oracle-j2sdk1.7
  
>>>>> 1.a.linux setup - Install JDK.PNG	    

- yum Install CM
  $ sudo yum install -y cloudera-manager-daemons cloudera-manager-server
  
>>>>> 1.a.linux setup - yum Install CM.PNG


============================================================================================================================================================
2) Install a MySQL server
============================================================================================================================================================
- Install and enable Maria DB (or a DB of your choice)
- Don’t forget to secure your DB installation
  $ sudo yum install -y mariadb-server
  $ sudo systemctl enable mariadb
  $ sudo systemctl start mariadb
  
>>>>> 1.b.Install a MySQL server - maria yum install and start.PNG
  
  $ sudo /usr/bin/mysql_secure_installation

		[...]
		Enter current password for root (enter for none):
		OK, successfully used password, moving on...
		[...]
		Set root password? [Y/n] Y
		New password:
		Re-enter new password:
		[...]
		Remove anonymous users? [Y/n] Y
		[...]
		Disallow root login remotely? [Y/n] N
		[...]
		Remove test database and access to it [Y/n] Y
		[...]
		Reload privilege tables now? [Y/n] Y
		[...]
		All done!  If you've completed all of the above steps, your MariaDB
		installation should now be secure.
		
>>>>>  1.b.Install a MySQL server - mysql_secure_installation.PNG		

-	Install the mysql connector or mariadb connector (모든host에 설치)
	$ wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.46.tar.gz
	$ tar zxvf mysql-connector-java-5.1.46.tar.gz
    $ sudo mkdir -p /usr/share/java/
	$ cd mysql-connector-java-5.1.46
	$ sudo cp mysql-connector-java-5.1.46-bin.jar /usr/share/java/mysql-connector-java.jar

- Create the necessary users and dbs in your database
- Grant them the necessary rights
  $ mysql -u root -p

>>>>>  1.b.Install a MySQL server - Install the mysql connector or mariadb connector.PNG	


CREATE DATABASE scm DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
CREATE DATABASE amon DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
CREATE DATABASE rman DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
CREATE DATABASE hue DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
CREATE DATABASE metastore DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
CREATE DATABASE sentry DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
CREATE DATABASE nav DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
CREATE DATABASE navms DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
CREATE DATABASE oozie DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;

GRANT ALL ON scm.* TO 'scm'@'%' IDENTIFIED BY 'password';
GRANT ALL ON amon.* TO 'amon'@'%' IDENTIFIED BY 'password';
GRANT ALL ON rman.* TO 'rman'@'%' IDENTIFIED BY 'password';
GRANT ALL ON hue.* TO 'hue'@'%' IDENTIFIED BY 'password';
GRANT ALL ON metastore.* TO 'hive'@'%' IDENTIFIED BY 'password';
GRANT ALL ON sentry.* TO 'sentry'@'%' IDENTIFIED BY 'password';
GRANT ALL ON nav.* TO 'nav'@'%' IDENTIFIED BY 'password';
GRANT ALL ON navms.* TO 'navms'@'%' IDENTIFIED BY 'password';
GRANT ALL ON oozie.* TO 'oozie'@'%' IDENTIFIED BY 'password';

FLUSH PRIVILEGES;

SHOW DATABASES;
SELECT VERSION();

>>>>>  1.b.Install a MySQL server - db setting.PNG	

EXIT;		

- Setup the CM database
	$ sudo /usr/share/cmf/schema/scm_prepare_database.sh mysql scm scm password
	$ sudo rm /etc/cloudera-scm-server/db.mgmt.properties
	$ sudo systemctl start cloudera-scm-server

- nd1 에서 db 실행 후 training 계정 생성
  $ mysql -u root -p
  비밀번호 : password 입력

  GRANT ALL ON *.* TO 'training'@'%' IDENTIFIED BY 'training';
  FLUSH PRIVILEGES;
  EXIT;
  
>>>>>  1.b.Install a MySQL server - db training user setting.PNG	

- Setup the CM database
  $ sudo /usr/share/cmf/schema/scm_prepare_database.sh mysql scm scm password
  $ sudo rm /etc/cloudera-scm-server/db.mgmt.properties
  

============================================================================================================================================================
3) Install Cloudera Manager
============================================================================================================================================================
- 리눅스 셋팅 시 jdk 설치 후 기 설치 함. (sudo yum install -y cloudera-manager-daemons cloudera-manager-server)
- CM 구동
  $ sudo systemctl start cloudera-scm-server

- prepare to install the cluster through the CM GUI installation process
	http://13.124.2.159:7180/cmf/login  admin / admin

>>>>> 1.c.Install Cloudera Manager - CM GUI~7.PNG

-	host 명 등록
	nd1.com,	nd2.com,	nd3.com,	nd4.com,	nd5.com

>>>>> 1.c.Install Cloudera Manager - CM GUI-Main.PNG

- parcel(package) 에서 Kafka 패키지 "다운로드" -> "배포" -> "활성" 수행  
  cluster 메뉴에서 Kafka 서비스 추가
  -> 브로커는 데이터노드에, 게이트웨이는 전체 노드에
  
>>>>>   1.c.Install Cloudera Manager - CM GUI-KAFKA.PNG

- sqoop
cluster 메뉴에서 sqoop 1 client 서비스 추가

>>>>>   1.c.Install Cloudera Manager - CM GUI-sqoop.PNG

- Spark
cluster 메뉴에서 Spark 서비스 추가
role 설정후 마직막에 클러스터 재구성 및 재시작 수행
>>>>>   1.c.Install Cloudera Manager - CM GUI-Spark.PNG

============================================================================================================================================================
4) Install Cloudera Manager
============================================================================================================================================================





```
