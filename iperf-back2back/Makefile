# params
PORT1 = ens4
PORT2 = ens4d1
NS1 = ns1
NS2 = ns2

.PHONY: env check check_detail counter_server counter_nic_server counter_client counter_nic_client server client clean

# setup and check
env:
	sudo ip netns add $(NS1)
	sudo ip netns add $(NS2)
	sudo ip link set $(PORT1) netns $(NS1)
	sudo ip link set $(PORT2) netns $(NS2)
	sudo ip netns exec $(NS1) ip a add 192.168.1.1/24 dev $(PORT1)
	sudo ip netns exec $(NS2) ip a add 192.168.1.2/24 dev $(PORT2)
	sudo ip netns exec $(NS1) ip l set up dev $(PORT1)
	sudo ip netns exec $(NS2) ip l set up dev $(PORT2)

check:
	sudo ip netns exec $(NS1) ip -br a
	sudo ip netns exec $(NS2) ip -br a

check_detail:
	@echo "-------- $(NS1) of $(PORT1) --------"
	sudo ip netns exec $(NS1) ethtool $(PORT1)
	sudo ip netns exec $(NS1) ethtool -i $(PORT1)
	sudo ip netns exec $(NS1) ethtool -g $(PORT1)
	sudo ip netns exec $(NS1) ethtool -k $(PORT1)
	sudo ip netns exec $(NS1) ethtool -S $(PORT1)
	@echo "-------- $(NS2) of $(PORT2) --------"
	sudo ip netns exec $(NS2) ethtool $(PORT2)
	sudo ip netns exec $(NS2) ethtool -i $(PORT2)
	sudo ip netns exec $(NS2) ethtool -g $(PORT2)
	sudo ip netns exec $(NS2) ethtool -k $(PORT2)
	sudo ip netns exec $(NS2) ethtool -S $(PORT2)

# counter
counter_server:
	watch -n 1 -d 'sudo ip netns exec $(NS1) ip -s a show dev $(PORT1)'

counter_nic_server:
	watch -n 1 -d 'sudo ip netns exec $(NS1) ethtool -S $(PORT1) | grep -v ": 0"'

counter_client:
	watch -n 1 -d 'sudo ip netns exec $(NS2) ip -s a show dev $(PORT2)'

counter_nic_client:
	watch -n 1 -d 'sudo ip netns exec $(NS2) ethtool -S $(PORT2) | grep -v ": 0"'

# exec iperf
server:
	sudo ip netns exec $(NS1) iperf3 -s

client:
	sudo ip netns exec $(NS2) iperf3 -c 192.168.1.1

# clean up
clean:
	sudo ip netns del $(NS1)
	sudo ip netns del $(NS2)
