## Devops-c-t-networking: VxLAN with docker

### Assignment: 2 objectives: 
1. Create two containers and connect them using vxlan

Block diagram: 
![Project Diagram](https://github.com/UmmeyHumayra/devops-c-t-networking/blob/assignment/Vxlan_lab_docker.jpg)

### Steps: 
####  Update linux ubuntu VM
```
sudo apt update
```
#### Install docker
```
sudo apt install -y docker.io # I had to follow the installation procedure from website
docker --version
```

## VM1 (Kali Linux)

#### Create a separate docker bridge network
```
sudo docker network create --subnet 172.18.0.0/16 vxlan-net
```
Note the vxlan-net network ID which will be needed later. 
┌──(kali㉿kali)-[~]
└─$ sudo docker network create --subnet 172.18.0.0/16 vxlan-net
6a715a63a5bf19bec0ff9903003cae8a10572f48d26fb57cf4031a39a0db8ce5

#### list all networks in docker: 
┌──(kali㉿kali)-[~]
└─$ sudo docker network ls                                     
NETWORK ID     NAME        DRIVER    SCOPE
0948b1415da3   bridge      bridge    local
03a0bb1fce3e   host        host      local
f091ec770831   none        null      local
6a715a63a5bf   vxlan-net   bridge    local

#### Check interfaces
```
┌──(kali㉿kali)-[~]
└─$ ip a 
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:05:83:1a brd ff:ff:ff:ff:ff:ff
    inet 192.168.183.130/24 brd 192.168.183.255 scope global dynamic noprefixroute eth0
       valid_lft 915sec preferred_lft 915sec
    inet6 fe80::c0ec:3f2f:2df1:5be6/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default 
    link/ether 02:42:27:7a:52:5a brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:27ff:fe7a:525a/64 scope link 
       valid_lft forever preferred_lft forever
6: br-6a715a63a5bf: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default 
    link/ether 02:42:10:7f:a4:f1 brd ff:ff:ff:ff:ff:ff
    inet 172.18.0.1/16 brd 172.18.255.255 scope global br-6a715a63a5bf
       valid_lft forever preferred_lft forever
```
## VM2 (Ubuntu)
```
ubuntu@ubuntu:~$ sudo docker network create --subnet 172.18.0.0/16 vxlan-net
ce41dce63ab3618db6c44c61f4e32cc1199b617d65de4bd2f9c275c8521a8c11
ubuntu@ubuntu:~$ sudo docker network ls
NETWORK ID     NAME        DRIVER    SCOPE
96a0287af59e   bridge      bridge    local
ce9e77b185d2   host        host      local
0dfe1c94f35b   none        null      local
ce41dce63ab3   vxlan-net   bridge    local
ubuntu@ubuntu:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:06:ee:52 brd ff:ff:ff:ff:ff:ff
    altname enp2s1
    inet 192.168.183.128/24 brd 192.168.183.255 scope global dynamic noprefixroute ens33
       valid_lft 1666sec preferred_lft 1666sec
    inet6 fe80::beb0:3292:6a38:667a/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default 
    link/ether 02:42:ae:8e:0e:c6 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
    inet6 fe80::42:aeff:fe8e:ec6/64 scope link 
       valid_lft forever preferred_lft forever
6: br-ce41dce63ab3: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default 
    link/ether 02:42:a1:bc:21:3a brd ff:ff:ff:ff:ff:ff
    inet 172.18.0.1/16 brd 172.18.255.255 scope global br-ce41dce63ab3
       valid_lft forever preferred_lft forever

```
#### Step 2: Run docker container on top of newly created docker bridge network and try to ping the docker bridge

## VM1 (Kali Linux)
#### running ubuntu container with "sleep 3000" and a static ip
```
┌──(kali㉿kali)-[~]
└─$ sudo docker run -d --net vxlan-net --ip 172.18.0.11 ubuntu sleep 3000
[sudo] password for kali: 
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
3153aa388d02: Pull complete 
Digest: sha256:0bced47fffa3361afa981854fcabcd4577cd43cebbb808cea2b1f33a3dd7f508
Status: Downloaded newer image for ubuntu:latest
0fdc6e82c55bb9e6a157cbd5496a0ee037bac36175eec44c030474e50998d7dd

```
#### Check the container running or not
```
┌──(kali㉿kali)-[~]
└─$ sudo docker ps                                                       
CONTAINER ID   IMAGE     COMMAND        CREATED          STATUS          PORTS     NAMES
0fdc6e82c55b   ubuntu    "sleep 3000"   43 seconds ago   Up 38 seconds             bold_antonelli

#### Check the IP Address to make sure that the IP assigned properly 

┌──(kali㉿kali)-[~]
└─$ sudo docker inspect 0f | grep IPAddress
            "SecondaryIPAddresses": null,
            "IPAddress": "",
                    "IPAddress": "172.18.0.11",
```
#### ping the docker bridge ip to see whether the traffic can pass
```                                  
┌──(kali㉿kali)-[~]
└─$ ping 172.18.0.1 -c 2                                                          
PING 172.18.0.1 (172.18.0.1) 56(84) bytes of data.
64 bytes from 172.18.0.1: icmp_seq=1 ttl=64 time=115 ms
64 bytes from 172.18.0.1: icmp_seq=2 ttl=64 time=0.056 ms

--- 172.18.0.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1007ms
rtt min/avg/max/mdev = 0.056/57.663/115.271/57.607 ms
```
## VM2 (Ubuntu)

#### running ubuntu container with "sleep 3000" and a static ip
```
ubuntu@ubuntu:~$ sudo docker run -d --net vxlan-net --ip 172.18.0.12 ubuntu sleep 3000
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
3153aa388d02: Pull complete 
Digest: sha256:0bced47fffa3361afa981854fcabcd4577cd43cebbb808cea2b1f33a3dd7f508
Status: Downloaded newer image for ubuntu:latest
c8be45cff52cc6295beed6d5dbf40fc4c491ecd1a99dd943f7d4a6b23206e6f0

```
#### Check the container running or not
```
ubuntu@ubuntu:~$ sudo docker ps
CONTAINER ID   IMAGE     COMMAND        CREATED          STATUS         PORTS     NAMES
c8be45cff52c   ubuntu    "sleep 3000"   10 seconds ago   Up 6 seconds             epic_snyder

```
#### Check the IP Address to make sure that the IP assigned properly 

```
ubuntu@ubuntu:~$ sudo docker inspect c8 | grep IPAddress
            "SecondaryIPAddresses": null,
            "IPAddress": "",
                    "IPAddress": "172.18.0.12",

```
#### ping the docker bridge ip to see whether the traffic can pass
```
ubuntu@ubuntu:~$ ping 172.18.0.1 -c 2
PING 172.18.0.1 (172.18.0.1) 56(84) bytes of data.
64 bytes from 172.18.0.1: icmp_seq=1 ttl=64 time=0.054 ms
64 bytes from 172.18.0.1: icmp_seq=2 ttl=64 time=0.050 ms

--- 172.18.0.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1003ms
rtt min/avg/max/mdev = 0.050/0.052/0.054/0.002 ms

```

#### Step 3: Now access the running container and try to ping anothe hosts running container via IP Address. Though hosts can communicate each other, container communication should fail because there is no tunner or anything to carry the traffic.

## From VM1 (Kali Linux)

#### Enter the running container using exec
```
──(kali㉿kali)-[~]
└─$ sudo docker exec -it 0f bash           
[sudo] password for kali: 
```
#### Now we are inside running container
#### Update the package and install net-tools and ping tools
```
root@0fdc6e82c55b:/# apt-get update
root@0fdc6e82c55b:/# apt-get install net-tools
root@0fdc6e82c55b:/# apt-get install iputils-ping
```
#### Now ping the other container
```
root@0fdc6e82c55b:/# ping 172.18.0.12 -c 2
PING 172.18.0.12 (172.18.0.12) 56(84) bytes of data.
From 172.18.0.11 icmp_seq=1 Destination Host Unreachable
From 172.18.0.11 icmp_seq=2 Destination Host Unreachable

--- 172.18.0.12 ping statistics ---
2 packets transmitted, 0 received, +2 errors, 100% packet loss, time 1117ms
pipe 2
root@0fdc6e82c55b:/# 
```
## From VM2 (Ubuntu)

#### Enter the running container using exec
```
ubuntu@ubuntu:~$ sudo docker exec -it c8 bash

```
#### Now we are inside running container
#### Update the package and install net-tools and ping tools
```
root@c8be45cff52c:/# apt-get update
root@c8be45cff52c:/# apt-get install net-tools
root@c8be45cff52c:/# apt-get install iputils-ping
```
#### Now ping the other container
```
root@c8be45cff52c:/# ping 171.18.0.11 -c 2
PING 171.18.0.11 (171.18.0.11) 56(84) bytes of data.

--- 171.18.0.11 ping statistics ---
2 packets transmitted, 0 received, 100% packet loss, time 1026ms

```

#### Step 4: Now create a VxLAN tunnel to establish communication between two hosts running containers. Then attach the VxLAN to the docker bridge. Make sure the VNI ID is the same for both hosts.  

## VM1 (Kali Linux)
#### Install bridge utility in VM
```
──(kali㉿kali)-[~]
└─$ sudo apt install -y bridge-utils
```
#### check the bridge list on the host
```                                          
┌──(kali㉿kali)-[~]
└─$ brctl show
bridge name     bridge id               STP enabled     interfaces
br-6a715a63a5bf         8000.0242107fa4f1       no              vethabfd25f
docker0         8000.0242277a525a       no
```
#### create a VxLAN
#### "vxlan-demo" is the name of the interface, type should be vxlan
#### VNI ID is 1000
#### destination port should be 4789 which is a udp port for vxlan communication
#### 192.168.183.128 is the ip of the other VM (ubuntu)
``` 
┌──(kali㉿kali)-[~]
└─$ sudo ip link add vxlan-demo type vxlan id 1000 remote 192.168.183.128 dstport 4789 dev eth0

``` 
#### Check interface list if the vxlan interface was created
``` 
┌──(kali㉿kali)-[~]
└─$ ip a | grep vxlan
9: vxlan-demo: <BROADCAST,MULTICAST> mtu 1450 qdisc noop state DOWN group default qlen 1000

``` 
#### Bring up the interface
```                                                
┌──(kali㉿kali)-[~]
└─$ sudo ip link set vxlan-demo up                                                             
                                                 
┌──(kali㉿kali)-[~]
└─$ ip a | grep vxlan             
9: vxlan-demo: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UNKNOWN group default qlen 1000
## Unknown / up indicates interface is up                              
``` 
#### now attach the newly created vxlan interface to the docker bridge we created
```
┌──(kali㉿kali)-[~]
└─$ sudo brctl addif br-6a715a63a5bf vxlan-demo

```
#### check the route table to ensure everything is okay. Note the route for destination "172.18.0.0"
```
┌──(kali㉿kali)-[~]
└─$ route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.183.2   0.0.0.0         UG    100    0        0 eth0
172.17.0.0      0.0.0.0         255.255.0.0     U     0      0        0 docker0
172.18.0.0      0.0.0.0         255.255.0.0     U     0      0        0 br-6a715a63a5bf
192.168.183.0   0.0.0.0         255.255.255.0   U     100    0        0 eth0
```
## VM2 (Ubuntu)
#### Install bridge utility in VM
```
ubuntu@ubuntu:~$ sudo apt install -y bridge-utils
```
#### check the bridge list on the host
```                                          
ubuntu@ubuntu:~$ brctl show
bridge name	bridge id		STP enabled	interfaces
br-ce41dce63ab3		8000.0242a1bc213a	no		
docker0		8000.0242ae8e0ec6	no		

```
#### create a VxLAN
#### "vxlan-demo" is the name of the interface, type should be vxlan
#### VNI ID is 1000
#### destination port should be 4789 which is a udp port for vxlan communication
#### 192.168.183.130 is the ip of the other VM (kali linux); note that the interface name in ubuntu is ens33
``` 
ubuntu@ubuntu:~$ ip a  | grep 192.168
    inet 192.168.183.128/24 brd 192.168.183.255 scope global dynamic noprefixroute ens33

ubuntu@ubuntu:~$ sudo ip link add vxlan-demo type vxlan id 1000 remote 192.168.183.130 dstport 4789 dev ens33

``` 
#### Check interface list if the vxlan interface was created
``` 
ubuntu@ubuntu:~$ ip a | grep vxlan
9: vxlan-demo: <BROADCAST,MULTICAST> mtu 1450 qdisc noop state DOWN group default qlen 1000

``` 
#### Bring up the interface
```                                                
ubuntu@ubuntu:~$ sudo ip link set vxlan-demo up

ubuntu@ubuntu:~$ ip a | grep vxlan
9: vxlan-demo: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UNKNOWN group default qlen 1000

## Unknown / up indicates interface is up                              
``` 
#### now attach the newly created vxlan interface to the docker bridge we created
```
ubuntu@ubuntu:~$ sudo brctl addif br-ce41dce63ab3 vxlan-demo

```
#### check the route table to ensure everything is okay. Note the route for destination "172.18.0.0"
```
ubuntu@ubuntu:~$ sudo apt install net-tools
ubuntu@ubuntu:~$ route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.183.2   0.0.0.0         UG    100    0        0 ens33
169.254.0.0     0.0.0.0         255.255.0.0     U     1000   0        0 ens33
172.17.0.0      0.0.0.0         255.255.0.0     U     0      0        0 docker0
172.18.0.0      0.0.0.0         255.255.0.0     U     0      0        0 br-ce41dce63ab3
192.168.183.0   0.0.0.0         255.255.255.0   U     100    0        0 ens33

```
#### Step 5: Now test the connectivity. It should work now. A VxLAN Overlay Network Tunnel has been established. 

## VM1 (Kali Linux)
```
## My docker got deleted, had to redo the docker start previous steps. 
ubuntu@ubuntu:~$ sudo docker run -d --net vxlan-net --ip 172.18.0.12 ubuntu sleep 3000
86db8b86411854811573de1f3a6ca3b42103fd6c373b1cc1b220ed5a44826a43
ubuntu@ubuntu:~$ sudo docker ps
CONTAINER ID   IMAGE     COMMAND        CREATED          STATUS         PORTS     NAMES
86db8b864118   ubuntu    "sleep 3000"   10 seconds ago   Up 5 seconds             suspicious_dhawan
ubuntu@ubuntu:~$ sudo docker exec -it 86 bash
root@86db8b864118:/# apt-get update
root@86db8b864118:/# apt-get install net-tools
root@86db8b864118:/# apt-get install iputils-ping
root@86db8b864118:/# ping 172.18.0.11 -c 2
PING 172.18.0.11 (172.18.0.11) 56(84) bytes of data.
64 bytes from 172.18.0.11: icmp_seq=1 ttl=64 time=2.46 ms
64 bytes from 172.18.0.11: icmp_seq=2 ttl=64 time=1.09 ms

--- 172.18.0.11 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 1.087/1.773/2.460/0.686 ms

```

