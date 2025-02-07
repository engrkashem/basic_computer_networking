# Create network namespaces
ip netns add ns0
ip netns add ns1
ip netns add router-ns

# Create veth pairs
ip link add veth-ns0 type veth peer name veth-br0
ip link add veth-ns1 type veth peer name veth-br1
ip link add veth-rt1 type veth peer name br0-rt
ip link add veth-rt2 type veth peer name br1-rt

# Assign veth pairs to namespaces
ip link set veth-ns0 netns ns0
ip link set veth-ns1 netns ns1
ip link set veth-rt1 netns router-ns
ip link set veth-rt2 netns router-ns

# Create bridges on the host
ip link add name br0 type bridge
ip link add name br1 type bridge

# Attach veth ends to bridges
ip link set veth-br0 master br0
ip link set veth-br1 master br1
ip link set br0-rt master br0
ip link set br1-rt master br1

# Bring up bridges and veth ends on the host
ip link set br0 up
ip link set br1 up
ip link set veth-br0 up
ip link set veth-br1 up
ip link set br0-rt up
ip link set br1-rt up

# Configure namespaces
# ns0
ip netns exec ns0 ip addr add 10.11.0.6/24 dev veth-ns0
ip netns exec ns0 ip link set veth-ns0 up
ip netns exec ns0 ip route add default via 10.11.0.1

# ns1
ip netns exec ns1 ip addr add 10.11.0.7/24 dev veth-ns1
ip netns exec ns1 ip link set veth-ns1 up
ip netns exec ns1 ip route add default via 10.11.0.2

# router-ns
ip netns exec router-ns ip addr add 10.11.0.4/24 dev veth-rt1
ip netns exec router-ns ip addr add 10.11.0.5/24 dev veth-rt2
ip netns exec router-ns ip link set veth-rt1 up
ip netns exec router-ns ip link set veth-rt2 up
ip netns exec router-ns ip route add default via 10.11.0.1

# Enable IP forwarding on the router namespace
ip netns exec router-ns sysctl -w net.ipv4.ip_forward=1

# Set up default routes on bridges
ip addr add 10.11.0.1/24 dev br0
ip addr add 10.11.0.2/24 dev br1
ip link set br0 up
ip link set br1 up

# Firewall rules set for bridge
#br0
sudo iptables --append FORWARD --in-interface br0 --jump  ACCEPT
sudo iptables --append FORWARD --out-interface br0 --jump  ACCEPT

#br1
sudo iptables --append FORWARD --in-interface br1 --jump  ACCEPT
sudo iptables --append FORWARD --out-interface br1 --jump  ACCEPT

# fib:
# 	# Create namespaces
# 	sudo ip netns add node1
# 	sudo ip netns exec node1 ip link set lo up

# 	sudo ip netns add node2
# 	sudo ip netns exec node2 ip link set lo up

# 	sudo ip netns add router
# 	sudo ip netns exec router ip link set lo up

# 	# Create bridge1
# 	sudo ip link add bridge1 type bridge
# 	sudo ip link set dev bridge1 up
# 	sudo ip addr add 10.11.0.1/24 dev bridge1 

# 	# Create veth for node1
# 	sudo ip link add v-red-ns type veth peer name v-red 
# 	sudo ip link set dev v-red-ns netns node1
# 	sudo ip link set dev v-red master bridge1
# 	sudo ip netns exec node1 ip address add 10.13.0.1/24 dev v-red-ns
# 	sudo ip netns exec node1 ip link set dev v-red-ns up
# 	sudo ip link set dev v-red up

# 	# Create bridge2
# 	sudo ip link add bridge2 type bridge
# 	sudo ip link set dev bridge2 up
# 	sudo ip addr add 10.12.0.1/24 dev bridge2 

# 	# Create veth for node2
# 	sudo ip link add v-blue-ns type veth peer name v-blue 
# 	sudo ip link set dev v-blue-ns netns node2 
# 	sudo ip link set dev v-blue master bridge2
# 	sudo ip netns exec node2 ip address add 10.14.0.1/24 dev v-blue-ns
# 	sudo ip netns exec node2 ip link set dev v-blue-ns up
# 	sudo ip link set dev v-blue up

# 	# Create virtual ethernet cables for router connections to bridges 
# 	sudo ip link add v1-router type veth peer name v1-bridge 
# 	sudo ip link add v2-router type veth peer name v2-bridge

# 	# Connect router to bridge1 
# 	sudo ip link set dev v1-router netns router 
# 	sudo ip link set dev v1-bridge master bridge1 
# 	sudo ip netns exec router ip addr add 10.15.0.1/24 dev v1-router 
# 	sudo ip netns exec router ip link set dev v1-router up 
# 	sudo ip link set dev v1-bridge up

# 	# Connect router to bridge2 
# 	sudo ip link set dev v2-router netns router 
# 	sudo ip link set dev v2-bridge master bridge2 
# 	sudo ip netns exec router ip addr add 10.15.0.2/24 dev v2-router 
# 	sudo ip netns exec router ip link set dev v2-router up 
# 	sudo ip link set dev v2-bridge up

# 	# Enable IP forwarding
# 	sudo sysctl -w net.ipv4.ip_forward=1

# 	# Add default routes for node1 and node2 
# 	sudo ip netns exec node1 ip route add default via 10.13.0.1
# 	sudo ip netns exec node2 ip route add default via 10.14.0.1

# cleanup:
# 	# Delete network namespaces
# 	sudo ip netns del node1
# 	sudo ip netns del node2
# 	sudo ip netns del router

# 	# Delete bridges
# 	sudo ip link del bridge1
# 	sudo ip link del bridge2
