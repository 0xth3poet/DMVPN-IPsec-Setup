
> Note: don't forget to configure interfaces and use default route 

**Scenario:**
You have been hired by **ONCF**, a metro company that operates across several major cities in Morocco. The company has its **headquarters in Casablanca (Casa)** and multiple branches (domains) located in:

- Tangier (TANGE)
    
- Fes (FES)
    
- Marrakech
    
- Rabat

The ONCF company wants to **implement a secure and scalable WAN solution** over the Internet using **DMVPN Phase 3**. This will allow direct communication between branches while maintaining a central point of control.

**Your Mission:**

You are tasked with **configuring and setting up DMVPN Phase 3** using the following requirements:

- **Hub:** Casablanca (Casa)
    
- **Spokes:** Tangier, Fes, Marrakech, and Rabat
    
- **Transport:** Internet (via ISP)
    
Use the **topology provided** to complete this task.
```
Site        WAN IP       Tunnel IP      LAN Network
--------------------------------------------------
CASA       11.0.0.1/30  172.16.0.1     192.168.1.0/24
TANGER     12.0.0.1/30  172.16.0.2     192.168.2.0/24  
FES        13.0.0.1/30  172.16.0.3     192.168.3.0/24
MARRAKECH  14.0.0.1/30  172.16.0.4     192.168.4.0/24
RABAT      15.0.0.1/30  172.16.0.5     192.168.5.0/24
```

![[Pasted image 20250429074711.png]]
### Configuration routing Using default route:

```

ONCF_CASA(config)#ip route 0.0.0.0 0.0.0.0 fa0/0

ONCF_TANGER(config)#ip route 0.0.0.0 0.0.0.0 fa0/1

ONCF_FES(config)#ip route 0.0.0.0 0.0.0.0 fa0/0

ONCF_MARRAKECH(config)#ip route 0.0.0.0 0.0.0.0 fa0/0

ONCF_RABAT(config)#ip route 0.0.0.0 0.0.0.0 fa0/0


```

