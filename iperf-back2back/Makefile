env:
	sudo ip netns add ns1
	sudo ip netns add ns2
	sudo ip link set ens4   netns ns1
	sudo ip link set ens4d1 netns ns2
	sudo ip netns exec ns1 ip a add 192.168.1.1/24 dev ens4
	sudo ip netns exec ns2 ip a add 192.168.1.2/24 dev ens4d1
	sudo ip netns exec ns1 ip l set up dev ens4
	sudo ip netns exec ns2 ip l set up dev ens4d1

server:
	sudo ip netns exec ns1 iperf3 -s

client:
	sudo ip netns exec ns2 iperf3 -c 192.168.1.1

clean:
	sudo ip netns del ns1
	sudo ip netns del ns2
