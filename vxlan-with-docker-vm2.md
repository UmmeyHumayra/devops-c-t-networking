## VM2 commands only
ummey_poney@vx-test-vm2:~$ 
```
ip a
sudo apt update
sudo apt install -y docker.io
sudo docker network create --subnet 172.20.0.0/16 my-vxlan
sudo docker network ls
sudo docker run -d --net my-vxlan --ip 172.20.0.12 ubuntu sleep 3000
sudo docker ps
sudo docker inspect ee | grep IPAddress
sudo docker exec -it ee bash
```
root@ee141795b9f8:/# 
```
apt-get update
apt-get install net-tools
apt-get install -y iputils-ping
ping 172.20.0.1 -c 2
exit
```
ummey_poney@vx-test-vm2:~$ 
```
brctl show
sudo ip link add my-vxlan-demo type vxlan id 1000 remote 172.18.0.4 dstport 4789 dev ens4
ip a | grep vxlan
sudo ip link set my-vxlan-demo up
sudo brctl addif br-0f506a16a90a my-vxlan-demo
sudo apt install net-tools
route -n
sudo docker ps
sudo docker exec -it ee bash
```
root@ee141795b9f8:/# 
```
ping 172.20.0.11 -c 4
exit
```
ummey_poney@vx-test-vm2:~$ 
