## VM1 commands and output 
```
ummey_poney@vx-test-vm1:~$ ip a
```
2: ens4: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1460 qdisc mq state UP group default qlen 1000
    link/ether 42:01:ac:12:00:04 brd ff:ff:ff:ff:ff:ff
    altname enp0s4
    inet 172.18.0.4/32 scope global dynamic ens4
```
ummey_poney@vx-test-vm1:~$ sudo apt update
ummey_poney@vx-test-vm1:~$ sudo apt install -y docker.io
ummey_poney@vx-test-vm1:~$ sudo docker network create --subnet 172.20.0.0/16 my-vxlan
```
00ad686c1835f3a66747ae414045e7a660055b7e157f550f031e9c0590818a3d
```
ummey_poney@vx-test-vm1:~$ sudo docker network ls
```
NETWORK ID     NAME       DRIVER    SCOPE
cf2c45f63bb8   bridge     bridge    local
c30c55adcc11   host       host      local
00ad686c1835   my-vxlan   bridge    local
fc11df3bcd7b   none       null      local
```
ummey_poney@vx-test-vm1:~$ sudo docker run -d --net my-vxlan --ip 172.20.0.11 ubuntu sleep 3000
```
4e06b863f5bfd0f6c082a93914c98569262e7cd0a2c66ce7e2b6f4943bf33c0c
```
ummey_poney@vx-test-vm1:~$ sudo docker ps
```
CONTAINER ID   IMAGE     COMMAND        CREATED          STATUS          PORTS     NAMES
4e06b863f5bf   ubuntu    "sleep 3000"   26 seconds ago   Up 25 seconds             busy_fermat
```
ummey_poney@vx-test-vm1:~$ sudo docker inspect 4e | grep IPAddress
```
            "SecondaryIPAddresses": null,
            "IPAddress": "",
                    "IPAddress": "172.20.0.11",
```
ummey_poney@vx-test-vm1:~$ sudo docker exec -it 4e bash
root@4e06b863f5bf:/# apt-get update
root@4e06b863f5bf:/# apt-get install net-tools
root@4e06b863f5bf:/# apt-get install iputils-ping
root@4e06b863f5bf:/# ping 172.20.0.1 -c 2
```
PING 172.20.0.1 (172.20.0.1) 56(84) bytes of data.
64 bytes from 172.20.0.1: icmp_seq=1 ttl=64 time=0.102 ms
64 bytes from 172.20.0.1: icmp_seq=2 ttl=64 time=0.077 ms

--- 172.20.0.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1031ms
rtt min/avg/max/mdev = 0.077/0.089/0.102/0.012 ms
```
root@4e06b863f5bf:/# exit
```
exit
```
ummey_poney@vx-test-vm1:~$ brctl show
```
bridge name     bridge id               STP enabled     interfaces
br-ca62e0c9155f         8000.0242b7f06890       no              veth5b6f61b
docker0         8000.024256057fd6       no
```
ummey_poney@vx-test-vm1:~$ sudo ip link add my-vxlan-demo type vxlan id 1000 remote 172.18.0.5 dstport 4789 dev ens4
ummey_poney@vx-test-vm1:~$ ip a | grep vxlan
```
8: my-vxlan-demo: <BROADCAST,MULTICAST> mtu 1410 qdisc noop state DOWN group default qlen 1000
```
ummey_poney@vx-test-vm1:~$ sudo ip link set my-vxlan-demo up
ummey_poney@vx-test-vm1:~$ sudo brctl addif br-ca62e0c9155f my-vxlan-demo
ummey_poney@vx-test-vm1:~$ sudo apt install net-toolsummey_poney@vx-test-vm1:~$ route -n
```
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         172.18.0.1      0.0.0.0         UG    100    0        0 ens4
172.17.0.0      0.0.0.0         255.255.0.0     U     0      0        0 docker0
172.18.0.1      0.0.0.0         255.255.255.255 UH    100    0        0 ens4
172.20.0.0      0.0.0.0         255.255.0.0     U     0      0        0 br-ca62e0c9155f
```
ummey_poney@vx-test-vm1:~$ sudo docker ps
```
CONTAINER ID   IMAGE     COMMAND        CREATED          STATUS          PORTS     NAMES
4e06b863f5bf   ubuntu    "sleep 3000"   12 minutes ago   Up 12 minutes             busy_fermat
```
ummey_poney@vx-test-vm1:~$ sudo docker exec -it 4e bash
root@4e06b863f5bf:/# ping 172.20.0.12
```
PING 172.20.0.12 (172.20.0.12) 56(84) bytes of data.
64 bytes from 172.20.0.12: icmp_seq=1 ttl=64 time=0.939 ms
64 bytes from 172.20.0.12: icmp_seq=2 ttl=64 time=0.514 ms
64 bytes from 172.20.0.12: icmp_seq=3 ttl=64 time=0.433 ms
64 bytes from 172.20.0.12: icmp_seq=4 ttl=64 time=0.505 ms
64 bytes from 172.20.0.12: icmp_seq=5 ttl=64 time=0.437 ms
^C
--- 172.20.0.12 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4050ms
rtt min/avg/max/mdev = 0.433/0.565/0.939/0.189 ms
```
root@4e06b863f5bf:/# exit
```
exit
```
ummey_poney@vx-test-vm1:~$
```
