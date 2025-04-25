# OSPF Lab

![](./images/img-1.png)
---
![](./images/ip_table.png)
---
#### Lab for
- OSPF
- Virtual link
- Redistribute
- Create special area
- OSPF summarization
- Authentication

#### Task
1. [Assign IP address in all routers](#1-assign-ip-address-in-all-routers)
2. [Configure OSPF in R1 to R9](#2-configure-ospf-in-r1-to-r9)
3. [Create virtual links between R1 and R3](#3-create-virtual-links-between-r1-and-r3)
4. [Configure RIPv2 in R11 and R9](#4-configure-ripv2-in-r11-and-r9)
5. [Configure EIGRP in R8 and R10](#5-configure-eigrp-in-r8-and-r10)
6. [Configure redistribute in R8 and R9](#6-configure-redistribute-in-r8-and-r9)
7. [Configure area 30 as stub area](#7-configure-area-30-as-stub-area)
8. [Configure area 20 as totally stub area](#8-configure-area-20-as-totally-stub-area)
9. [Configure area 40 as nssa area](#9-configure-area-40-as-nssa-area)
10. [Configure area 50 as totally nssa area](#10-configure-area-50-as-totally-nssa-area)
11. [Configure ospf summarization on R1, R3, R8](#11-configure-ospf-summarization-on-r1-r3-r8)
12. [Configure Area 10 Virtual link SHA Authenticaiton](#12-configure-area-10-virtual-link-sha-authenticaiton)
13. [MD5 Authentication on Area 0 (R3-R4)](#13-md5-authentication-on-area-0-r3-r4)

---
#### 1. Assign IP address in all routers
>_example: R2_
```py
R2#
    int e0/0
    ip add 12.0.0.2 255.255.255.0
    no shut
    int e0/1
    ip add 23.0.0.2 255.255.255.0
    no shut
    int lo0
    ip add 192.168.0.1 255.255.255.0
    int lo1
    ip add 192.168.1.1 255.255.255.0
    int lo2
    ip add 192.168.2.1 255.255.255.0
```
    
#### 2. Configure OSPF in R1 to R9
> _example: R2_
```py
R2#
    router ospf 1
    router-id 0.0.0.2
    network 12.0.0.0 0.0.0.255 area 10
    network 23.0.0.0 0.0.0.255 area 10
    network 192.168.0.0 0.0.3.255 area 10
```
- 2.1. Verify

    ```py
    R7#sh ip route ospf
    O IA     12.0.0.0 [110/20] via 17.0.0.1, 00:31:12, Ethernet0/1
    O IA     23.0.0.0 [110/30] via 17.0.0.1, 00:31:12, Ethernet0/1
    O IA     192.168.0.1 [110/21] via 17.0.0.1, 00:31:12, Ethernet0/1
    O IA     192.168.1.1 [110/21] via 17.0.0.1, 00:31:12, Ethernet0/1
    O IA     192.168.2.1 [110/21] via 17.0.0.1, 00:31:12, Ethernet0/1
    ```

#### 3. Create virtual links between R1 and R3
```py
R1#
    router ospf 1
    area 10 virtual-link 0.0.0.3
```
```py
R3#
    router ospf 1
    area 10 virtual-link 0.0.0.1
```
- 3.1. Verify

    ```sh
    R1#sh ip ospf virtual-links 
    "Virtual Link OSPF_VL0 to router 0.0.0.3 is up"
    Run as demand circuit
    DoNotAge LSA allowed.
    Transit area 10, via interface Ethernet0/0
    Topology-MTID    Cost    Disabled     Shutdown      Topology Name
            0           20        no          no            Base
    Transmit Delay is 1 sec, State POINT_TO_POINT,
    Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
        Hello due in 00:00:08
        Adjacency State FULL (Hello suppressed)
        Index 1/1/2, retransmission queue length 0, number of retransmission 1
        First 0x0(0)/0x0(0)/0x0(0) Next 0x0(0)/0x0(0)/0x0(0)
        Last retransmission scan length is 1, maximum is 1
        Last retransmission scan time is 0 msec, maximum is 0 msec
    ```

#### 4. Configure RIPv2 in R11 and R9
```py
R9#
    router rip
    version 2
    no auto-summary
    network 119.0.0.0
```

#### 5. Configure EIGRP in R8 and R10
```py
R8#
    router eigrp 108
    no auto-summary
    network 108.0.0.0 0.0.0.255
```
```py
R10#
    router eigrp 108
    no auto
    network 108.0.0.0 0.0.0.255
    network 172.16.0.0 0.0.3.255
```

#### 6. Configure redistribute in R8 and R9
```py
R8#
    router eigrp 108
    redistribute ospf 1 metric 10000 100 255 10 1500
    router ospf 1
    redistribute eigrp 108 subnets metric-type 2
```
```py
R9#
    router rip
    redistribute ospf 1 metric 10
    router ospf 1
    redistribute rip metric 10 subnets
```
#### 7. Configure area 30 as stub area
```py
R4#
    router ospf 1
    area 30 stub
```
```py
R6#
    router ospf 1
    area 30 stub
```
- 7.1. Verify (no type 5 LSA, no ASBR)

    ```sh
    R6#sh ip ospf | sec Area
        Area 30
            Number of interfaces in this area is 1
    "       It is a stub area"
            Area has no authentication
            Area ranges are
    ```

#### 8. Configure area 20 as totally stub area
```py
R5#
    router ospf 1
    area 20 stub
```
```py
R4#
    router ospf 1
    area 20 stub no-summary
```
- 8.1 Verify (no type 5 LSA, no ASBR, no type 3 summary LSA)

    ```shell
    R4#sh ip ospf | sec Area
    "   Area BACKBONE(0)"
            Number of interfaces in this area is 1
        Area has no authentication
        Area ranges are
        Area 20
            Number of interfaces in this area is 1
   "        It is a stub area, no summary LSA in this area"
            Generates stub default route with cost 1
        Area has no authentication
        Area ranges are
        Area 30
            Number of interfaces in this area is 1
    "       It is a stub area"
            Generates stub default route with cost 1
        Area has no authentication
        Area ranges are
    ```
    ```sh
    R5#sh ip ospf data

                OSPF Router with ID (0.0.0.5) (Process ID 1)

            Router Link States (Area 20)

    Link ID         ADV Router      Age         Seq#       Checksum Link count
    0.0.0.4         0.0.0.4         1218        0x80000004 0x0001BB 1
    0.0.0.5         0.0.0.5         1211        0x80000004 0x00FBBE 1

            Net Link States (Area 20)

    Link ID         ADV Router      Age         Seq#       Checksum
    45.0.0.5        0.0.0.5         1225        0x80000001 0x008C6F

            Summary Net Link States (Area 20)

    Link ID         ADV Router      Age         Seq#       Checksum
    0.0.0.0         0.0.0.4         1268        0x80000001 0x0099A0
    ```

#### 9. Configure area 40 as nssa area
```py
R7#
    router ospf 1
    area 40 nssa default-information-originate
```
```py
R9#
    router ospf 1
    area 40 nssa
```
- 9.1 Verify (no type 5 LSA, ASBR allowed)

    ```sh
    R9#sh ip ospf database

                OSPF Router with ID (0.0.0.9) (Process ID 1)

            Router Link States (Area 40)

    Link ID         ADV Router      Age         Seq#       Checksum Link count
    0.0.0.7         0.0.0.7         190         0x80000003 0x001949 1
    0.0.0.9         0.0.0.9         150         0x80000003 0x00124B 1

            Net Link States (Area 40)

    Link ID         ADV Router      Age         Seq#       Checksum
    79.0.0.9        0.0.0.9         150         0x80000002 0x006859

            Summary Net Link States (Area 40)

    Link ID         ADV Router      Age         Seq#       Checksum
    12.0.0.0        0.0.0.7         1778        0x80000001 0x0031DE
    17.0.0.0        0.0.0.7         190         0x80000002 0x00898A
    23.0.0.0        0.0.0.7         1778        0x80000001 0x0006F4
    34.0.0.0        0.0.0.7         1261        0x80000001 0x00DA0B
    45.0.0.0        0.0.0.7         1261        0x80000001 0x00AF21
    46.0.0.0        0.0.0.7         1261        0x80000001 0x00A22D
    78.0.0.0        0.0.0.7         190         0x80000002 0x006D69
    192.168.0.1     0.0.0.7         355         0x80000001 0x001C94
    192.168.1.1     0.0.0.7         355         0x80000001 0x00119E
    192.168.2.1     0.0.0.7         355         0x80000001 0x0006A8

            Type-7 AS External Link States (Area 40)

    Link ID         ADV Router      Age         Seq#       Checksum Tag
    0.0.0.0         0.0.0.7         190         0x80000002 0x00E0C8 0
    119.0.0.0       0.0.0.9         150         0x80000002 0x00F0CB 0

    ```
#### 10. Configure area 50 as totally nssa area
```py
R8#
    router ospf 1
    area 50 nssa
```
```py
R7#
    router ospf 1
    area 50 nssa no-summary
```
- 10.1 Verify (no type 3 LSA, no type 5 LSA, ASBR allowed)

    ```sh
    R8#sh ip ospf data
            Summary Net Link States (Area 50)

    Link ID         ADV Router      Age         Seq#       Checksum
    0.0.0.0         0.0.0.7         59          0x80000001 0x000F20

            Type-7 AS External Link States (Area 50)

    Link ID         ADV Router      Age         Seq#       Checksum Tag
    108.0.0.0       0.0.0.8         985         0x80000001 0x006F5C 0
    172.16.1.0      0.0.0.8         985         0x80000001 0x00601A 0
    172.16.2.0      0.0.0.8         985         0x80000001 0x005524 0
    172.16.3.0      0.0.0.8         985         0x80000001 0x004A2E 0
    ```

#### 11. configure ospf summarization on R1, R3, R8
```py
R1#
    router ospf 1
    area 10 range 192.168.0.0 255.255.252.0
```
```py
R3#
    router ospf 1
    area 10 range 192.168.0.0 255.255.252.0
```
```py
R8#
    router ospf 1
    summary-address 172.16.0.0 255.255.252.0
```
- 11.1 Verify

    ```sh
    R7#sh ip route | sec 192.168    
    O IA  192.168.0.0/22 [110/21] via 17.0.0.1, 00:01:14, Ethernet0/1

    R7#sh ip route | sec 172.16
        172.16.0.0/22 is subnetted, 1 subnets
    O N2     172.16.0.0 [110/20] via 78.0.0.8, 00:00:38, Ethernet0/2
    ```
#### 12. Configure Area 10 Virtual link SHA Authenticaiton
```sh
R1#
 key chain A10-KEYCHAIN
  key 1
   key-string A10-KEY
   cryptographic-algorithm hmac-sha-512
 router ospf 1
  area 10 virtual-link 0.0.0.3 authentication key-chain A10-KEYCHAIN
```
```sh
R3#
 key chain A10-KEYCHAIN
  key 1
   key-string A10-KEY
   cryptographic-algorithm hmac-sha-512
 router ospf 1
  area 10 virtual-link 0.0.0.1 authentication key-chain A10-KEYCHAIN
```
- 12.1 Verify

    ```shell
    R1#sh ip ospf virtual-links 
    Virtual Link OSPF_VL0 to router 0.0.0.3 is up
    Run as demand circuit
    DoNotAge LSA allowed.
    Transit area 10, via interface Ethernet0/0
    Topology-MTID    Cost    Disabled     Shutdown      Topology Name
            0           20        no          no            Base
    Transmit Delay is 1 sec, State POINT_TO_POINT,
    Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
        Hello due in 00:00:06
        Adjacency State FULL (Hello suppressed)
        Index 2/3, retransmission queue length 0, number of retransmission 0
        First 0x0(0)/0x0(0) Next 0x0(0)/0x0(0)
        Last retransmission scan length is 0, maximum is 0
        Last retransmission scan time is 0 msec, maximum is 0 msec
"   Cryptographic authentication enabled"
"       Sending SA: Key 1, Algorithm HMAC-SHA-512 - key chain A10-KEYCHAIN"
    ```

#### 13. MD5 Authentication on Area 0 (R3-R4)
```sh
R3#
 key chain A0-KEYCHAIN
  key 1
   key-string A0-KEY
   cryptographic-algorithm md5
 int e0/0
  ip ospf authentication key-chain A0-KEYCHAIN
```
```sh
R4#
 key chain A0-KEYCHAIN
  key 1
   key-string A0-KEY
   cryptographic-algorithm md5
 int e0/0
  ip ospf authentication key-chain A0-KEYCHAIN
```
- 12.1 Verify

    ```shell
    R4#sh ip ospf int e0/0 | sec Crypto
    Cryptographic authentication enabled
        Sending SA: Key 1, Algorithm MD5 - key chain A0-KEYCHAIN
    ```