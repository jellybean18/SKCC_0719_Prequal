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

6. 
```
