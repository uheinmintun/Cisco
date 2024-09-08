1. Basic config
2. Configure eigrp with AS number 1
	2.1 R2-R4 static neighbor
	2.3 R5 named mode config
3. Passive interface in R4 and R5
4. Configure summarization in R4 and R5
5. Unequal loadbalancing
	5.1 change bandwidth of e0/0 in R1 and R2 to 5000 kbit/s
	5.2 configure unequal loadbalancing in R2 to reach 13.0.0.0 subnet.
6. Verify config
	# show ip protocols
	# show ip eigrp neighbors
	# show ip route eigrp
	# show ip eigrp topology
	# show ip eigrp interface
	# show ip eigrp topology all-links
	# show ip eigrp traffic
	
---
#### 1. Basic config (Assign IP address)
> _example: R1_
```
R1#
	int e0/0
	ip add 12.0.0.1 255.255.255.252
	no shut
	int e0/1
	ip add 13.0.0.1 255.255.255.252
	no shut
	int lo0
	ip add 4.4.4.1 255.255.255.255
```
2. Configure eigrp with AS number 1 (R1 to R5)
> _example: R1_ 
```
R1#
	router eigrp 1
	 network 4.4.4.1 0.0.0.0
	 network 12.0.0.0 0.0.0.3
	 network 13.0.0.0 0.0.0.3
	 no auto
```





	2.1 R2-R4 static neighbor
	2.3 R5 named mode config
3. Passive interface in R4 and R5
4. Configure summarization in R4 and R5
5. Unequal loadbalancing
	5.1 change bandwidth of e0/0 in R1 and R2 to 5000 kbit/s
	5.2 configure unequal loadbalancing in R2 to reach 13.0.0.0 subnet.
6. Verify config
	# show ip protocols
	# show ip eigrp neighbors
	# show ip route eigrp
	# show ip eigrp topology
	# show ip eigrp interface
	# show ip eigrp topology all-links
	# show ip eigrp traffic