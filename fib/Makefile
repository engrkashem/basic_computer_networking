ns:
	sudo ip netns add red
	sudo ip netns add blue
	sudo ip netns add router

br1:
	sudo ip link add dev br1 type bridge
	sudo ip link set dev br1 up
	sudo ip addr add 10.11.0.1/24 dev br1

br2:
	sudo ip link add dev br2 type bridge
	sudo ip link set dev br2 up
	sudo ip addr add 10.12.0.1/24 dev br2

interface-red-br1:
	sudo ip link add v-red-ns type veth peer name v-red
	sudo ip link set v-red-ns netns red
	sudo ip link set v-red master br1
	sudo ip netns exec red ip addr add 10.11.0.2/24 dev v-red-ns
	sudo ip netns exec red ip link set v-red-ns up
	sudo ip link set v-red up

interface-br1-router:
	sudo ip link add vr-red type veth peer name vr-red-ns
	sudo ip link set vr-red-ns netns router
	sudo ip link set vr-red master br1
	sudo ip netns exec router ip addr add 10.11.0.3/24 dev vr-red-ns
	sudo ip netns exec router ip link set vr-red-ns up
	sudo ip link set vr-red up

interface-router-br2:
	sudo ip link add vr-blue-ns type veth peer name vr-blue
	sudo ip link set vr-blue-ns netns router
	sudo ip link set vr-blue master br2
	sudo ip netns exec router ip addr add 10.12.0.3/24 dev vr-blue-ns
	sudo ip netns exec router ip link set vr-blue-ns up
	sudo ip link set vr-blue up

cleanup:
	sudo ip netns del red
	sudo ip netns del blue
	sudo ip netns del router
	sudo ip link del br1
	sudo ip link del br2