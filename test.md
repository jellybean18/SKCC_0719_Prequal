# Exaplme
```
1. Check vm.swappiness on all your nodes
swap 확인
sysctl vm.swappiness

영구 적용 위해 /etc/sysctl.conf 추가 필요
vi /etc/sysctl.conf --> vm.swappiness = 1 추가

2. Show the mount attributes of your volume(s)
아래 명령어로 확인
df -h
3. If you have ext-based volumes, list the reserve space setting
아래 명령어로 확인
fsck -a /dev/nvme0n1p1
4. Disable transparent hugepage support
cat /sys/kernel/mm/transparent_hugepage/enabled
[always] madvise never --> 이렇게 나오면 enabled 상태
always madvise [never] --> 이렇게 나와야 disabled 상태

/etc/rc.d/rc.local vi로 실행해서 아래 명령어 입력

echo never > /sys/kernel/mm/transparent_hugepage/enabled
echo never > /sys/kernel/mm/transparent_hugepage/defrag
권한 변경
chmod +x /etc/rc.d/rc.local 

/etc/default/grub vi로 실행해서 아래 명령어 입력
transparent_hugepage=never

아래 명령어 실행
grub2-mkconfig -o /boot/grub2/grub.cfg

5. List your network interface configuration
ip addr 명령어 실행

6. Show that forward and reverse host lookups are correctly resolved
centos 계정 비밀번호 설정
sudo passwd centos --> admin으로 통일

ssh 설정 변경
sudo vi /etc/ssh/sshd_config 열기
PasswordAuthentication yes 주석 풀기
PasswordAuthentication no 주석 처리

hostname 설정
sudo hostnamectl set-hostname 노드번호.team3.com
ex) nd1.team3.com

/etc/hosts 설정
172.31.0.147 nd1.team3.com nd1
172.31.2.54 nd2.team3.com nd2
172.31.7.7 nd3.team3.com nd3
172.31.2.235 nd4.team4.com nd4
172.31.4.97 nd5.team5.com nd5

위와 같이 작성

sudo reboot 로 재시작

재부팅 후 ssh로 각 노드들 접속해보기

7. Show the nscd service is running
sudo yum install nscd -y
sudo systemctl start nscd --> 서비스 시작
systemctl status nscd -> 서비스 상태 확인

8. Show the ntpd service is running
sudo yum install ntp -y
sudo systemctl start ntpd --> 서비스 시작
systemctl status ntpd --> 서비스 상태 확인
```
https://www.cloudera.com/documentation/enterprise/5-15-x/topics/install_cm_server.html

# b.
```
- Configure the repository for CM 5.15.2
  $ sudo yum install -y wget
  $ sudo wget https://archive.cloudera.com/cm5/redhat/7/x86_64/cm/cloudera-manager.repo -P /etc/yum.repos.d/

sudo yum install oracle-j2sdk1.7 --> jdk 설치

jdbc
wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.47.tar.gz
tar zxvf mysql-connector-java-5.1.47.tar.gz
sudo mkdir -p /usr/share/java/
cd mysql-connector-java-5.1.47
sudo cp mysql-connector-java-5.1.47-bin.jar /usr/share/java/mysql-connector-java.jar
```
