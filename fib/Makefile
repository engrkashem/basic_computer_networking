fib:
	# 1. Create Bridges (idempotent)
	sudo ip link add name br0 type bridge 
	sudo ip link add name br1 type bridge 

	# Configure Bridges (using distinct IPs)
	sudo ip addr add 10.11.0.1/24 dev br0 
	sudo ip addr add 10.12.0.1/24 dev br1 
	sudo ip link set br0 up 
	sudo ip link set br1 up 

	# 2. Create Network Namespaces (idempotent)
	sudo ip netns add ns1 
	sudo ip netns add ns2 
	sudo ip netns add router-ns 

	# 3. Create Veth Pairs and Connect (idempotent)
	sudo ip link add veth-ns1 type veth peer name veth-br0 
	sudo ip link add veth-ns2 type veth peer name veth-br1 
	sudo ip link add veth-rt0 type veth peer name br0-rt 
	sudo ip link add veth-rt1 type veth peer name br1-rt 

	sudo ip link set veth-ns1 netns ns1 
	sudo ip link set veth-ns2 netns ns2 
	sudo ip link set veth-rt0 netns router-ns 
	sudo ip link set veth-rt1 netns router-ns 

	sudo ip link set veth-br0 master br0 
	sudo ip link set veth-br1 master br1 
	sudo ip link set br0-rt master br0 
	sudo ip link set br1-rt master br1 

	sudo ip link set veth-br0 up 
	sudo ip link set veth-br1 up 
	sudo ip link set br0-rt up 
	sudo ip link set br1-rt up 


	# 4. Configure IP Addresses
	# ns1
	sudo ip netns exec ns1 ip addr add 10.11.0.10/24 dev veth-ns1  
	sudo ip netns exec ns1 ip link set veth-ns1 up 
	sudo ip netns exec ns1 ip route add default via 10.11.0.1  

	# ns2
	sudo ip netns exec ns2 ip addr add 10.12.0.10/24 dev veth-ns2  
	sudo ip netns exec ns2 ip link set veth-ns2 up 
	sudo ip netns exec ns2 ip route add default via 10.12.0.1  

	# router-ns (using distinct IPs for router interfaces)
	sudo ip netns exec router-ns ip addr add 10.11.0.2/24 dev veth-rt0   
	sudo ip netns exec router-ns ip addr add 10.12.0.2/24 dev veth-rt1  

	sudo ip netns exec router-ns ip link set veth-rt0 up 
	sudo ip netns exec router-ns ip link set veth-rt1 up 

	# 5. Crucial: Set up Routing in router-ns (SIMPLIFIED - NO CHECKS NEEDED)
	sudo ip netns exec router-ns ip route add 10.11.0.0/24 dev veth-rt0 metric 100  
	sudo ip netns exec router-ns ip route add 10.12.0.0/24 dev veth-rt1 metric 100  

	# 6. Enable IP Forwarding in router-ns (idempotent)
	sudo ip netns exec router-ns sysctl -w net.ipv4.ip_forward=1 


cleanup:
		sudo ip netns del ns1 
		sudo ip netns del ns2 
		sudo ip netns del router-ns 
		sudo ip link del br0 
		sudo ip link del br1 
		sudo ip link del veth-ns1 
		sudo ip link del veth-ns2 
		sudo ip link del veth-rt0 
		sudo ip link del veth-rt1 
		sudo ip link del veth-br0 
		sudo ip link del veth-br1 
		sudo ip link del br0-rt 
		sudo ip link del br1-rt 

