## **Introduction**

This lab simulates a real-world deployment of **DMVPN Phase 3 (Dynamic Multipoint VPN)** using a fictional scenario involving the Moroccan transportation company **ONCF**. The goal is to provide hands-on experience with configuring a scalable and secure WAN solution over the Internet, utilizing technologies such as **GRE, NHRP, IPSec, and OSPF**.





> **Disclaimer:** The ONCF company is used strictly as an example in this educational context. This lab has no affiliation with ONCF and does not reflect any real infrastructure or implementation by the organization.



This lab is intended for networking students or anyone interested in learning how DMVPN Phase 3 works in a multi-branch topology using a central hub and spoke routers. The configuration is tested in **EVE-NG** and includes **full tunnel, IPSec encryption, and OSPF dynamic routing**.







**Author:** _abderrafik (th3poet)

**Date:** _April 2025_









## **Scenario**

You have been hired by **ONCF**, the Moroccan metro company operating in several major cities. The organization aims to implement a **secure, scalable WAN** over the **Internet** using **DMVPN Phase 3**, allowing secure communication between branches while maintaining centralized control from the headquarters

**Your Mission:**
You are tasked with **configuring and setting up DMVPN Phase 3** using the following requirements:

### **Network Topology**

**Hub (Headquarters):** Casablanca (CASA)  
**Spokes (Branches):** Tangier, Fes, Marrakech, Rabat  
**Transport:** Internet (via ISP)

```
Site        WAN IP       Tunnel IP      LAN Network
--------------------------------------------------
CASA       11.0.0.2/30  172.16.0.1     192.168.1.0/24 
TANGER     12.0.0.2/30  172.16.0.2     192.168.2.0/24 
FES        13.0.0.2/30  172.16.0.3     192.168.3.0/24 
MARRAKECH  14.0.0.2/30  172.16.0.4     192.168.4.0/24 
RABAT      15.0.0.2/30  172.16.0.5     192.168.5.0/24 
```

---

## **Lab Setup Instructions**

1. Download Lab Files

```
Clone the repository:

~$ git clone https://github.com/0xth3poet/DMVPN-IPsec-Setup.git

Or download manually:

https://github.com/0xth3poet/DMVPN-IPsec-Setup/blob/main/_Exports_unetlab_export-20250430-212957.zip

```

![image-20250430230141152](https://github.com/0xth3poet/DMVPN-TP/blob/main/Untitled%20design.png)











## Upload  lab to EVE-NG

![image-20250430230313020](https://github.com/0xth3poet/DMVPN-TP/blob/main/1.png)















## Et Voilà 

![image-20250430230118748](https://github.com/0xth3poet/DMVPN-TP/blob/main/image-20250430230118748.png)

## **Configuration time**

### Default Route Configuration:

We are using a default route with the ISP router (already configured in the lab)

```
CASA_HUB(config)#ip route 0.0.0.0 0.0.0.0 fa0/0

SPOKE_TANGER(config)#ip route 0.0.0.0 0.0.0.0 fa0/1

SPOKE_FES(config)#ip route 0.0.0.0 0.0.0.0 fa0/0

SPOKE_MARRAKECH(config)#ip route 0.0.0.0 0.0.0.0 fa0/0

SPOKE_RABAT(config)#ip route 0.0.0.0 0.0.0.0 fa0/0


```

## Phase 3 Configuration

**HUB Configuration (R-CASA)**

```
interface Tunnel0 
ip address 172.16.0.1 255.255.255.0 
tunnel source fa0/0 
tunnel mode gre multipoint 
ip nhrp network-id 123 
ip nhrp authentication pass123 
ip nhrp map multicast dynamic 
ip ospf network point-to-multipoint  
ip nhrp redirect 
```

**Spoke Configuration (R-TANGER)**

```
interface tunnel0
ip address 172.16.0.2 255.255.255.0
tunnel source fa0/1
tunnel mode gre multipoint
ip nhrp network-id 123
ip nhrp map 172.16.0.1 11.0.0.2 
ip nhrp nhs 172.16.0.1 
ip nhrp authentication pass123
ip nhrp map multicast 11.0.0.2 
ip ospf network point-to-multipoint 
ip nhrp shortcut 
```

**Spoke Configuration (R-FES)**

```
interface tunnel0
ip address 172.16.0.3 255.255.255.0
tunnel source fa0/0
tunnel mode gre multipoint
ip nhrp network-id 123
ip nhrp map 172.16.0.1 11.0.0.2
ip nhrp nhs 172.16.0.1
ip nhrp authentication pass123
ip nhrp map multicast 11.0.0.2
ip ospf network point-to-multipoint
ip nhrp shortcut
```

**Spoke Configuration (R-MARRAKECH)**

```
interface tunnel0
ip address 172.16.0.4 255.255.255.0
tunnel source fa0/0
tunnel mode gre multipoint
ip nhrp network-id 123
ip nhrp map 172.16.0.1 11.0.0.2
ip nhrp nhs 172.16.0.1
ip nhrp authentication pass123
ip nhrp map multicast 11.0.0.2
ip ospf network point-to-multipoint
ip nhrp shortcut
```

**Spoke Configuration (R-RABAT)**

```
interface tunnel0
ip address 172.16.0.5 255.255.255.0
tunnel source fa0/0
tunnel mode gre multipoint
ip nhrp network-id 123
ip nhrp map 172.16.0.1 11.0.0.2
ip nhrp nhs 172.16.0.1
ip nhrp authentication pass123
ip nhrp map multicast 11.0.0.2
ip ospf network point-to-multipoint
ip nhrp shortcut
```

## Verification DMVPN

```
show dmvpn
```

## IPSec Configuration

> NOTE: implement this configuration of IPSEC on all routers except ISP router

```
crypto isakmp  policy 10
 authentication pre-share       
 encryption aes 256             
 hash sha                        
 group 5                         
 exit

crypto isakmp key cisco123 address 0.0.0.0           

crypto ipsec transform-set dmvpn esp-3des esp-sha-hmac 
mod tunnel            
exit

crypto ipsec profile prof-dmvpn 
 set transform-set dmvpn 

interface tunnel0 
 tunnel protection ipsec profile prof-dmvpn 
```

##  **Verification Commands**

```
show crypto ipsec sa
show crypto ipsec profile
```

## **Routing with OSPF**

**HUB Configuration (R-CASA)**

```
router ospf 1
 network 172.16.0.0 0.0.0.255 area 0
 network 192.168.1.0 0.0.0.255 area 0
```

**SPOKE Configuration (R-TANGER)**

```
router ospf 1
network 172.16.0.0 0.0.0.255 area 0
network 192.168.2.0 0.0.0.255 area 0
```

**SPOKE Configuration (R-FES)**

```
router ospf 1
network 172.16.0.0 0.0.0.255 area 0
network 192.168.3.0 0.0.0.255 area 0
```

**SPOKE Configuration (R-MARRAKECH)**

```
router ospf 1
network 172.16.0.0 0.0.0.255 area 0
network 192.168.4.0 0.0.0.255 area 0
```

**SPOKE Configuration (R-RABAT)**

```
router ospf 1
network 172.16.0.0 0.0.0.255 area 0
network 192.168.5.0 0.0.0.255 area 0
```

## **Final Verification**

```
From FES, ping RABAT LAN: 

SPOKE_FES(config)#do ping 192.168.5.1

From RABAT, ping TANGER LAN:

SPOKE_RABAT(config)#do ping 192.168.2.1
```

## **Traffic Analysis**

Use **Wireshark** to analyze tunnel traffic, IPSec encryption, and dynamic spoke-to-spoke communication.
