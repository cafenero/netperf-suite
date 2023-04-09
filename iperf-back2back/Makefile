# params
PORT1 = ens4
PORT2 = ens4d1
NS1 = ns1
NS2 = ns2

.PHONY: env mtu mtu9k check check_detail counter_server counter_nic_server \
	counter_client counter_nic_client server client client_mss_ladder \
	client_mss_ladder_json clean

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

mtu:
	sudo ip netns exec $(NS1) ip l set mtu 1500 dev $(PORT1)
	sudo ip netns exec $(NS2) ip l set mtu 1500 dev $(PORT2)

mtu9k:
	sudo ip netns exec $(NS1) ip l set mtu 9000 dev $(PORT1)
	sudo ip netns exec $(NS2) ip l set mtu 9000 dev $(PORT2)

check:
	sudo ip netns exec $(NS1) ip a show dev $(PORT1)
	sudo ip netns exec $(NS2) ip a show dev $(PORT2)

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
	watch -n 1 -d 'sudo ip netns exec $(NS1) ip -s a show dev $(PORT1); sudo ip netns exec $(NS1) netstat -s -t'

counter_nic_server:
	watch -n 1 -d 'sudo ip netns exec $(NS1) ethtool -S $(PORT1) | grep -v ": 0"'

counter_client:
	watch -n 1 -d 'sudo ip netns exec $(NS2) ip -s a show dev $(PORT2); sudo ip netns exec $(NS1) netstat -s -t'

counter_nic_client:
	watch -n 1 -d 'sudo ip netns exec $(NS2) ethtool -S $(PORT2) | grep -v ": 0"'

# exec iperf
server:
	sudo ip netns exec $(NS1) iperf3 -s

client:
	sudo ip netns exec $(NS2) iperf3 -c 192.168.1.1 -V

client_mss_ladder:
	sudo ip netns exec $(NS2) iperf3 -c 192.168.1.1 -V --set-mss 88
	sudo ip netns exec $(NS2) iperf3 -c 192.168.1.1 -V --set-mss 200
	sudo ip netns exec $(NS2) iperf3 -c 192.168.1.1 -V --set-mss 400
	sudo ip netns exec $(NS2) iperf3 -c 192.168.1.1 -V --set-mss 800
	sudo ip netns exec $(NS2) iperf3 -c 192.168.1.1 -V --set-mss 1500
	sudo ip netns exec $(NS2) iperf3 -c 192.168.1.1 -V --set-mss 3000
	sudo ip netns exec $(NS2) iperf3 -c 192.168.1.1 -V --set-mss 6000
	sudo ip netns exec $(NS2) iperf3 -c 192.168.1.1 -V --set-mss 9000
	sudo ip netns exec $(NS2) iperf3 -c 192.168.1.1 -V --set-mss 9216

client_mss_ladder_json:
	test -d output || mkdir output
	sudo ip netns exec $(NS2) iperf3 -c 192.168.1.1 -J --set-mss 88   > output/mss88.json
	sudo ip netns exec $(NS2) iperf3 -c 192.168.1.1 -J --set-mss 200  > output/mss200.json
	sudo ip netns exec $(NS2) iperf3 -c 192.168.1.1 -J --set-mss 400  > output/mss400.json
	sudo ip netns exec $(NS2) iperf3 -c 192.168.1.1 -J --set-mss 800  > output/mss800.json
	sudo ip netns exec $(NS2) iperf3 -c 192.168.1.1 -J --set-mss 1500 > output/mss1500.json
	sudo ip netns exec $(NS2) iperf3 -c 192.168.1.1 -J --set-mss 3000 > output/mss3000.json
	sudo ip netns exec $(NS2) iperf3 -c 192.168.1.1 -J --set-mss 6000 > output/mss6000.json
	sudo ip netns exec $(NS2) iperf3 -c 192.168.1.1 -J --set-mss 9000 > output/mss9000.json
	sudo ip netns exec $(NS2) iperf3 -c 192.168.1.1 -J --set-mss 9216 > output/mss9216.json

# clean up
clean:
	sudo ip netns del $(NS1)
	sudo ip netns del $(NS2)
