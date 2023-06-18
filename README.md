## Devops-c-t-networking

### Assignment objectives: 
1. Create two namespaces
2. Connect them with veth cable
3. Ping from one namespace to the other one

### Steps: 
#### 1. Add two namespaces called "red" and "green"
```
sudo ip netns add red
sudo ip netns add green

ip netns list
```
#### 2a. Create a veth cable
```
sudo ip link add r-eth type veth peer name g-eth
```
#### 2b. Attach the r-eth interface with red netns and g-eth with the green netns
```
sudo ip link set r-eth netns red
sudo ip link set g-eth netns green
```
#### 2c. Set ip address to red interface
```
sudo ip netns exec red ip addr add 10.10.1.1/24 dev r-eth
sudo ip netns exec green ip addr add 10.10.1.2/24 dev g-eth 
```
#### 2d. Bring the interfaces up
```
sudo ip netns exec red ip link set r-eth up
sudo ip netns exec green ip link set g-eth up
```
#### 2e. Ping the other interface and verify connectivity
```
sudo ip netns exec red bash 
ping 10.10.1.2
exit

sudo ip netns exec green bash 
ping 10.10.1.1
exit
```
