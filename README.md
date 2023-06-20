## Devops-c-t-networking

### Assignment: 1 objectives: 
1. Create two namespaces -red and green
2. Connect the namespaces using with a veth cable
3. Ping from one namespace to the other one

### Steps: 
#### 1. Add two namespaces called "red" and "green"
```
sudo ip netns add red
sudo ip netns add green

ip netns list
```
#### 2. Create a veth cable
```
sudo ip link add r-eth type veth peer name g-eth
```
#### 3. Attach the r-eth interface with red netns and g-eth with the green netns
```
sudo ip link set r-eth netns red
sudo ip link set g-eth netns green
```
#### 4. Set ip addresses to both interfaces
```
sudo ip netns exec red ip addr add 10.10.1.1/24 dev r-eth
sudo ip netns exec green ip addr add 10.10.1.2/24 dev g-eth 
```
#### 5. Bring the interfaces up
```
sudo ip netns exec red ip link set r-eth up
sudo ip netns exec green ip link set g-eth up
```
#### 6. Ping the other interface and verify connectivity
```
sudo ip netns exec red bash 
ping 10.10.1.2
exit

sudo ip netns exec green bash 
ping 10.10.1.1
exit
```


### Assignment: 2 objectives: 
1. Create two namespaces -red and green
2. Create a bridge
3. Connect the namespaces using the bridge
4. Verify connectivity by pinging to the other name space

### Steps: 
#### 1. Add two namespaces called "red" and "green"
```
sudo ip netns add red
sudo ip netns add green

ip netns list
```
#### 2. Create the bridge "bridge" and bring it up
```
sudo ip link add br0 type bridge
sudo ip link set br0 up
```
#### 3. Create veth cables
```
sudo ip link add r-eth1 type veth peer name br0-eth1
sudo ip link add g-eth2 type veth peer name br0-eth2
```
#### 4. Attach the interfaces of veth cables with relevant interfaces
```
sudo ip link set r-eth1 netns red
sudo ip link set g-eth2 netns green
sudo ip link set br0-eth1 master br0
sudo ip link set br0-eth2 master br0
```
#### 5. Set ip addresses to the interfaces of network namespaces
```
sudo ip netns exec red ip addr add 10.10.1.1/24 dev r-eth1
sudo ip netns exec green ip addr add 10.10.1.2/24 dev g-eth2 
```
#### 6. Bring all the interfaces up
```
sudo ip netns exec red ip link set r-eth1 up
sudo ip netns exec green ip link set g-eth2 up
sudo ip link set br0-eth1 up
sudo ip link set br0-eth2 up
```
#### 7. Ping the other interface and verify connectivity
```
sudo ip netns exec red bash 
ping 10.10.1.2
exit

sudo ip netns exec green bash 
ping 10.10.1.1
exit
```