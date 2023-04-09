# setup and check
env:
	sudo ip netns add ns1
	sudo ip netns add ns2
	sudo ip link set ens4   netns ns1
	sudo ip link set ens4d1 netns ns2
	sudo ip netns exec ns1 ip a add 192.168.1.1/24 dev ens4
	sudo ip netns exec ns2 ip a add 192.168.1.2/24 dev ens4d1
	sudo ip netns exec ns1 ip l set up dev ens4
	sudo ip netns exec ns2 ip l set up dev ens4d1

check:
	sudo ip netns exec ns1 ip -br a
	sudo ip netns exec ns2 ip -br a

check_detail:
	sudo ip netns exec ns1 ethtool ens4
	sudo ip netns exec ns1 ethtool -i ens4
	sudo ip netns exec ns1 ethtool -g ens4
	sudo ip netns exec ns1 ethtool -k ens4
	sudo ip netns exec ns1 ethtool -S ens4

# counter
counter_server:
	watch -n 1 -d 'sudo ip netns exec ns1 ip -s a show dev ens4'

counter_nic_server:
	watch -n 1 -d 'sudo ip netns exec ns1 ethtool -S ens4 | grep -v ": 0"'

counter_client:
	watch -n 1 -d 'sudo ip netns exec ns2 ip -s a show dev ens4d1'

counter_nic_client:
	watch -n 1 -d 'sudo ip netns exec ns2 ethtool -S ens4d1 | grep -v ": 0"'

# exec iperf
server:
	sudo ip netns exec ns1 iperf3 -s

client:
	sudo ip netns exec ns2 iperf3 -c 192.168.1.1

# clean up
clean:
	sudo ip netns del ns1
	sudo ip netns del ns2
