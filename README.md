# ospf-adjacency-troubleshooting-fault-isolation
Three-router OSPF triangle topology featuring routers R1 (Router ID 1.1.1.1), R2 (Router ID 2.2.2.2), and R3 (Router ID 3.3.3.3) interconnected across point-to-point/broadcast transit links.

🌐 Quick Overview & Network Specs
🟢 Architecture & Topology: Three-router OSPF triangle topology featuring routers R1 (Router ID 1.1.1.1), R2 (Router ID 2.2.2.2), and R3 (Router ID 3.3.3.3) interconnected across point-to-point/broadcast transit links.

🔵 Core Routing Protocols: OSPFv2 dynamic routing protocol configured across all participant routers to establish full link-state database (LSDB) synchronization and achieve the FULL operational neighbor state.

🟣 Adjacency State Machine Requirements: Four fundamental configuration parameters must precisely match across adjacent interfaces for OSPF peering to successfully transition from INIT / 2-WAY to FULL:

Area ID: Both interfaces must belong to the same OSPF area (e.g., Area 0 backbone mismatch is the most frequent deployment error).

Hello & Dead Timers: Hello and dead intervals must match identically (default is 10s hello / 40s dead on broadcast links).

Subnet Mask: Connected interfaces must share the same network subnet mask down to the individual bit.

Authentication & MTU: Authentication keys/types must align, and interface MTU values must match to prevent routers from getting stuck in EXSTART / EXCHANGE.

💡 Key Technical Insights & Common Failure Scenarios
🔍 Mismatched Hello and Dead Timers: If hello timers differ (e.g., one router configured with ip ospf hello-interval 20), hello packets are dropped or ignored, preventing the formation of neighbor adjacencies.

⚡ Passive Interface Misconfiguration: Setting an interface as passive (passive-interface gigabitEthernet 0/1) stops the router from sending or listening to OSPF hello packets on that link, resulting in zero neighbors.

🛠️ Subnet Mask Mismatch: A subnet discrepancy (e.g., /24 on one router interface and /30 on the neighboring peer interface) breaks packet validation, stopping neighbor formation entirely.

🛡️ MTU Mismatched States (EXSTART/EXCHANGE Freeze): If interface MTU settings differ across peers, Database Descriptor (DBD) packet exchanges fail, leaving neighbor states permanently locked in the EXSTART or EXCHANGE states.

🛠️ Essential Cisco CLI Troubleshooting & Configuration Snippets
Fixing Timer Mismatch
Cisco CLI
R2(config)# interface gigabitEthernet 0/0
R2(config-if)# ip ospf hello-interval 10
R2(config-if)# ip ospf dead-interval 40
Resolving Passive Interface Errors
Cisco CLI
R3(config)# router ospf 1
R3(config-router)# no passive-interface gigabitEthernet 0/1
Correcting Subnet Mask Configurations
Cisco CLI
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# ip address 192.168.1.1 255.255.255.0
🧪 Verification & Audit Commands
Bash
# Verify active neighbor adjacencies, state (FULL), and router IDs
show ip ospf neighbor
show ip ospf neighbor detail

# Inspect OSPF interface parameters, area memberships, and timer intervals
show ip ospf interface brief
show ip ospf interface gigabitEthernet 0/0

# Audit Link-State Database (LSDB) contents and routing table entries
show ip ospf database
show ip route ospf
