# GRE over IPsec
![](./images/topology.png "GRE over IPsec")
---
#### Lab for
- GRE over IPsec

#### Tasks
1. [Initial Config](#1-initial-config)
2. [IPsec Phrase 1 Config](#2-ipsec-phrase-1-config)
3. [IPsec Phrase 2 Config](#3-ipsec-phrase-2-config)
4. [GRE config](#4-gre-config)
5. [Configure Routing Protocol](#5-configure-routing-protocol)
6. Commands <br>
    `# clear crypto session remote [remote-IP]` <br>
    `# show crypto session` <br>
    `# show crypto ipsec transform-set` <br>
    `# show crypto isakmp sa` <br>
    `# show crypto ipsec sa`
---
#### 1. Initial Config

```py
R1#
    host R1
    
    int e0/0
    ip add 13.0.0.1 255.255.255.0
    no shut
    int e0/1
    ip add 192.168.0.1 255.255.255.0
    no shut
    
    ip route 0.0.0.0 0.0.0.0 13.0.0.3
```
```py
R2#
    host R2
    
    int e0/1
    ip add 23.0.0.2 255.255.255.0
    no shut
    int e0/0
    ip add 172.16.0.2 255.255.255.0
    no shut
    
    ip route 0.0.0.0 0.0.0.0 23.0.0.3
```

#### 2. IPsec Phrase 1 Config
```py
R1#
    crypto isakmp policy 10
     encr aes 256
     hash sha512
     authentication pre-share
     group 2
     
    crypto isakmp key OneTwo34 address 23.0.0.2  
```
```py
R2#
    crypto isakmp policy 10
     encr aes 256
     hash sha512
     authentication pre-share
     group 2
     
    crypto isakmp key OneTwo34 address 13.0.0.1  
```

#### 3. IPsec Phrase 2 Config
```py
R1#
    crypto ipsec transform-set MySet ah-sha512-hmac 
     mode transport
    
    ip access-list extended MyACL
     permit gre host 13.0.0.1 host 23.0.0.2
     
    crypto map MyMap 10 ipsec-isakmp 
     set peer 23.0.0.2
     set transform-set MySet 
     match address MyACL
     
    int e0/0  
     crypto map MyMap
```
```py
R2#
    crypto ipsec transform-set MySet ah-sha512-hmac 
     mode transport
    
    ip access-list extended MyACL
     permit gre host 23.0.0.2 host 13.0.0.1
     
    crypto map MyMap 10 ipsec-isakmp 
     set peer 13.0.0.1
     set transform-set MySet 
     match address MyACL
     
    int e0/1
     crypto map MyMap
```

#### 4. GRE config
```py
R1#
    interface Tunnel12
     ip address 100.0.0.1 255.255.255.0
     tunnel source 13.0.0.1
     tunnel destination 23.0.0.2
```
```py
R2#
    interface Tunnel12
     ip address 100.0.0.2 255.255.255.0
     tunnel source 23.0.0.2
     tunnel destination 13.0.0.1
```

#### 5. Configure Routing Protocol
```py
R1#
    router ospf 1
     network 100.0.0.0 0.0.0.255 area 0
     network 192.168.0.0 0.0.0.255 area 0
```
```py
R1#
    router ospf 1
     network 100.0.0.0 0.0.0.255 area 0
     network 172.16.0.0 0.0.0.255 area 0
```