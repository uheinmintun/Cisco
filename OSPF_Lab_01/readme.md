# ospf overall lab

![](./images/img-1.png)

lab for
- virtual link
- redistribute
- create special area
- ospf summarization

Task
1. assign ip add in all routers
2. configure ospf in R1 to R9
3. create virtual links between R1 and R3
4. configure RIPv2 in R11 and R9
5. configure EIGRP in R8 and R10
6. configure redistribute in R8 and R9
7. configure area 30 as stub
8. configure area 20 as totally stub
9. configure area 40 as nssa
10. configure area 50 as totally nssa
11. configure ospf summarization on R1, R2, R3
12. configure ospf summarization on R8, R10

Task-1. assign ip add in all routers
```
host R2
int e0/0
ip add 12.0.0.2 255.255.255.0
no shut
int e0/1
ip add 23.0.0.2 255.255.255.0
no shut
```
    
Task-2. configure ospf in R1 to R9 (R2#)
```
router-id 0.0.0.2
router ospf 1
network 12.0.0.0 0.0.0.255 area 10
network 23.0.0.0 0.0.0.255 area 10
```

Task-3. create virtual links between R1 and R3
```
R1#
router ospf 1
area 10 virtual-link 0.0.0.3
    
R2#
router ospf 1
area 10 virtual-link 0.0.0.1
```
Show command: <br>
`show ip ospf virtual-links`

Task-4. configure RIPv2 in R11 and R9
```
router rip
version 2
no auto-summary
network 119.0.0.0
```

Task-5. configure EIGRP in R8 and R10
```
router eigrp 108
no auto-summary
network 108.0.0.0 0.0.0.255
```

Task-6. configure redistribute in R8 and R9
```
R8#
router ospf 1
redistribute eigrp 108 metric 20 subnets
router eigrp 108
redistribute ospf 1 metric 10000 10 255 10 1500
```
Show command: <br>
`show ip ospf database`

Task-7. configure area 30 as stub (R4 and R6)
```
router ospf 1
area 30 stub
```

Task-8. configure area 20 as totally stub (R4 and R5)
```
R5#
router ospf 1
area 20 stub
    
R4#
router ospf 1
area 20 stub no-summary
Note:
```

Task-9. configure area 40 as nssa (R7 and R9)
```
router ospf 1
area 40 nssa
```

Task-10. configure area 50 as totally nssa (R7 and R8)
```
R8#
router ospf 1
area 50 nssa
    
R7#
router ospf 1
area 50 nssa no-summary
```

Task-11. configure ospf summarization on R1, R2, R3
```
R2#
router ospf 1
network 192.168.0.0 0.0.3.255 area 10
    
R1 and R3#
router ospf 1
area 10 range 192.168.0.0 255.255.252.0
```

Task-12. configure ospf summarization on R8, R10
```
R10#
router eigrp 108
network 172.16.0.0 0.0.3.255
    
R8#
router ospf 1
summary-address 172.16.0.0 255.255.252.0
```


#### <u>Show commands</u> 
`# clear ip ospf process` <br>
`# show ip ospf neighbor` <br>
`# ip ospf priority 200` <br>
`# ip ospf priority 0` <br>
`# show ip ospf inter e0/0` <br>
`# ip ospf network point-to-multipoint` <br>
`# show ip ospf database` <br>
`# show ip ospf virtual-links` <br>


