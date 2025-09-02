# Presentación Módulo 3: Routing Estático y Dinámico
## Diseño Completo de Diapositivas con Fundamentos Teóricos

---

## **DIAPOSITIVA 1: Título**
```
MÓDULO 3: ROUTING ESTÁTICO Y DINÁMICO
Fundamentos Teóricos y Implementación Avanzada

Curso Avanzado MikroTik
Gestión Operativa y Seguridad de Redes

Profesor: [Nombre]
Duración: 2 semanas (8 horas)
Prerequisito: Módulo 2 completado (Switching y VLANs)
```

---

## **DIAPOSITIVA 2: Objetivos del Módulo**
```
🎯 COMPETENCIAS A DESARROLLAR

Al finalizar este módulo podrás:

✅ DISEÑAR topologías de routing estático y dinámico
   - Optimización de paths y redundancia
   - Load balancing y failover strategies
   - Administrative distance y route selection

✅ IMPLEMENTAR protocolos de routing dinámico
   - OSPF multi-área con ABRs optimizados
   - BGP inter-AS con policies avanzadas
   - Route redistribution y filtering

✅ OPTIMIZAR performance de routing
   - Route filtering y summarization
   - Policy-based routing (PBR)
   - Convergence optimization

🏆 DE TÉCNICO A NETWORK ARCHITECT
```

---

## **DIAPOSITIVA 3: Agenda del Módulo**
```
📅 ESTRUCTURA INTEGRAL

SEMANA 1: FUNDAMENTOS Y STATIC ROUTING
• Teoría de grafos aplicada al routing
• Route selection algorithms profundos
• Static routing con redundancia avanzada
• Load balancing y floating routes

SEMANA 2: DYNAMIC ROUTING PROTOCOLS  
• OSPF multi-área design y implementation
• BGP fundamentals y policy engineering
• Route optimization y troubleshooting
• Integration y advanced scenarios

🏠 HOMEWORK: GNS3 ISP simulation + Policy routing
📊 EVALUACIÓN: 45% examen práctico + 40% proyecto + 15% presentación
```

---

## **DIAPOSITIVA 4: El Problema Fundamental del Routing**
```
🧭 ¿POR QUÉ NECESITAMOS ROUTING?

SWITCHING vs ROUTING:
┌─────────────────┬─────────────────┐
│ SWITCHING (L2)  │ ROUTING (L3)    │
├─────────────────┼─────────────────┤
│ MAC addresses   │ IP addresses    │
│ Same network    │ Different nets  │
│ Learning-based  │ Algorithm-based │
│ Flood unknown   │ Route or drop   │
│ No path choice  │ Best path       │
└─────────────────┴─────────────────┘

EL ROUTING RESUELVE:
• Path selection entre múltiples networks
• Optimal route determination
• Network reachability across boundaries
• Scalable internetwork communication

💡 ROUTING = FINDING THE BEST PATH IN A GRAPH
```

---

## **DIAPOSITIVA 5: Teoría de Grafos en Networking**
```
📊 NETWORKS AS MATHEMATICAL GRAPHS

COMPONENTES DEL GRAFO:
┌─────────────────────────────────────┐
│    NODES (V): Routers/Networks      │
│      ●────────●────────●            │
│      │        │        │            │
│   EDGES (E): Links between nodes    │
│      │        │        │            │
│   WEIGHTS: Metrics (cost, delay)    │
└─────────────────────────────────────┘

SHORTEST PATH PROBLEM:
├─ Input: Weighted directed graph
├─ Goal: Find minimum cost path
├─ Algorithms: Dijkstra, Bellman-Ford
└─ Output: Optimal routing table

REAL NETWORK EXAMPLE:
Router A → Router B: Cost 10 (100Mbps link)
Router A → Router C: Cost 1 (1Gbps link)
Router C → Router B: Cost 1 (1Gbps link)
BEST PATH A→B: A→C→B (Total cost: 2 vs 10)
```

---

## **DIAPOSITIVA 6: Route Selection Process**
```
⚖️ CÓMO LOS ROUTERS ELIGEN RUTAS

MULTI-STEP SELECTION PROCESS:
```
1. LONGEST PREFIX MATCH
   ├─ 192.168.10.50 matches:
   ├─ 0.0.0.0/0 (default)
   ├─ 192.168.0.0/16 (/16 prefix)  
   ├─ 192.168.10.0/24 (/24 prefix)
   └─ 192.168.10.48/28 (/28 = WINNER)

2. ADMINISTRATIVE DISTANCE
   ├─ Connected: 0 (most trusted)
   ├─ Static: 1
   ├─ OSPF: 110
   ├─ RIP: 120
   └─ Lower = Better

3. METRIC COMPARISON
   └─ Protocol-specific cost calculation

4. LOAD BALANCING
   └─ Equal cost paths → traffic distribution
```

---

## **DIAPOSITIVA 7: Routing Estático - Fundamentos**
```
🎯 STATIC ROUTING DEEP DIVE

CARACTERÍSTICAS:
┌─────────────────────────────────────┐
│ VENTAJAS           │ DESVENTAJAS    │
├─────────────────────┼────────────────│
│ ✓ Control total     │ ✗ No adapta   │
│ ✓ No overhead       │ ✗ Maintenance │
│ ✓ Predecible        │ ✗ Escala mal  │
│ ✓ Seguro            │ ✗ Manual      │
│ ✓ Recursos mínimos  │ ✗ Error-prone │
└─────────────────────┴────────────────┘

CUÁNDO USAR STATIC ROUTING:
• Small networks (< 10 routers)
• Hub-and-spoke topologies  
• Backup/default routes
• Security-critical environments
• Bandwidth-constrained links

CONFIGURACIÓN BÁSICA:
/ip route add dst-address=192.168.10.0/24 gateway=10.0.0.2
```

---

## **DIAPOSITIVA 8: Load Balancing Strategies**
```
⚖️ DISTRIBUCIÓN DE TRÁFICO

EQUAL COST MULTI-PATH (ECMP):
```
┌─────────┐    Path 1 (Cost=1)    ┌─────────┐
│Router A │────────────────────────│Router B │
│         │    Path 2 (Cost=1)    │         │
│         │────────────────────────│         │
└─────────┘                       └─────────┘
          Traffic split 50/50

ALGORITMOS DE LOAD BALANCING:
├─ Per-Packet: Perfect distribution, packet reordering
├─ Per-Flow: Same flow uses same path (RECOMMENDED)
├─ Per-Destination: Based on destination IP
└─ PCC (Per Connection Classifier): RouterOS method

MIKROTIK PCC EXAMPLE:
/ip firewall mangle add per-connection-classifier=both-addresses:2/0
→ 50% traffic path 1, 50% path 2
```

---

## **DIAPOSITIVA 9: Floating Routes y Redundancy**
```
🔄 AUTOMATIC FAILOVER

FLOATING ROUTE CONCEPT:
```
Primary:   0.0.0.0/0 → ISP1 (distance=1)
Backup:    0.0.0.0/0 → ISP2 (distance=10)

NORMAL OPERATION:
└─ Primary route active, backup route standby

LINK FAILURE:
├─ Primary route becomes unreachable
├─ Backup route automatically activates
└─ Traffic switches to backup path

RECOVERY:
├─ Primary link restored
├─ Primary route reinstalled  
└─ Traffic returns to primary path

CONFIGURATION:
/ip route add dst-address=0.0.0.0/0 gateway=203.0.113.1 distance=1
/ip route add dst-address=0.0.0.0/0 gateway=203.0.113.5 distance=10
```

---

## **DIAPOSITIVA 10: Dynamic Routing Classification**
```
🔄 PROTOCOLOS DINÁMICOS TAXONOMY

BY ALGORITHM:
```
DISTANCE VECTOR          LINK STATE
├─ Bellman-Ford         ├─ Dijkstra SPF
├─ Distance + Direction ├─ Complete topology
├─ Periodic updates     ├─ Event-driven updates  
├─ Slow convergence     ├─ Fast convergence
├─ Examples: RIP        ├─ Examples: OSPF, IS-IS
└─ Count-to-infinity    └─ Higher memory/CPU

BY SCOPE:
IGP (Interior Gateway)     EGP (Exterior Gateway)
├─ Within Autonomous System├─ Between Autonomous Systems
├─ Optimize performance    ├─ Optimize policy
├─ Examples: OSPF, EIGRP   ├─ Examples: BGP
└─ Metrics: BW, delay      └─ Metrics: AS-path, policy
```

---

## **DIAPOSITIVA 11: OSPF Architecture Deep Dive**
```
🏗️ OSPF HIERARCHICAL DESIGN

ÁREA-BASED TOPOLOGY:
```
        ┌─────────────────────────────┐
        │     AREA 0 (BACKBONE)      │
        │    ●────────●────────●     │
        └──────┬──────────────┬──────┘
               │              │
    ┌──────────▼───┐      ┌───▼──────────┐
    │   AREA 1     │      │   AREA 2     │  
    │ ●────●────●  │      │ ●────●────●  │
    └──────────────┘      └──────────────┘

DESIGN PRINCIPLES:
├─ BACKBONE AREA (0): Central hub, contiguous
├─ REGULAR AREAS: Connected to backbone via ABRs
├─ ABRs: Area Border Routers connect areas
├─ SCALABILITY: Localized flooding, summarization
└─ CONVERGENCE: Hierarchical SPF calculation

BENEFITS:
• Reduced LSA flooding scope
• Faster convergence (local changes)
• Route summarization opportunities
• Resource optimization per area
```

---

## **DIAPOSITIVA 12: OSPF Packet Types y Neighbor States**
```
📦 OSPF COMMUNICATION MECHANISM

PACKET TYPES:
```
1. HELLO (Type 1):
   ├─ Neighbor discovery/maintenance  
   ├─ Every 10s (LAN), 30s (WAN)
   └─ Contains: Router-ID, Area-ID, timers

2. DATABASE DESCRIPTION (Type 2):
   ├─ Database synchronization
   └─ LSA headers exchange

3. LINK STATE REQUEST (Type 3):
   └─ Request specific LSAs

4. LINK STATE UPDATE (Type 4):
   ├─ Carries actual LSAs
   └─ Reliable flooding mechanism

5. LINK STATE ACK (Type 5):
   └─ Acknowledge LSU receipt

NEIGHBOR STATE MACHINE:
DOWN → INIT → 2-WAY → ExSTART → EXCHANGE → LOADING → FULL
                ↑                                        ↑
          DR/BDR Election                         Fully Adjacent
```

---

## **DIAPOSITIVA 13: OSPF LSA Types Deep Dive**
```
🗂️ LINK STATE ADVERTISEMENTS

LSA TYPE HIERARCHY:
```
TYPE 1 - ROUTER LSA:
├─ Generated by: Each router
├─ Scope: Within area only
├─ Content: Router's direct links
└─ Flooding: Intra-area

TYPE 2 - NETWORK LSA:  
├─ Generated by: Designated Router (DR)
├─ Scope: Within area only
├─ Content: Multi-access network info
└─ Purpose: Reduce mesh complexity

TYPE 3 - SUMMARY LSA:
├─ Generated by: Area Border Routers (ABRs)
├─ Scope: Between areas (not intra-area)
├─ Content: Inter-area network advertisements
└─ Purpose: Provide inter-area reachability

TYPE 5 - EXTERNAL LSA:
├─ Generated by: ASBRs  
├─ Scope: Throughout OSPF domain
├─ Content: External routes (non-OSPF)
└─ Types: E1 (metric adds), E2 (metric constant)
```

---

## **DIAPOSITIVA 14: BGP Fundamentals**
```
🌐 BORDER GATEWAY PROTOCOL

BGP = INTERNET'S ROUTING PROTOCOL
```
CARACTERÍSTICAS ÚNICAS:
├─ Path Vector Protocol (not distance vector)
├─ Policy-based routing (not metric-based)
├─ Slow convergence by design (stability)
├─ Rich attribute system for control
└─ Scales to 800,000+ Internet routes

BGP vs IGPs COMPARISON:
┌─────────────────┬─────────────────┐
│ BGP (EGP)       │ IGP (OSPF/RIP) │
├─────────────────┼─────────────────┤
│ Policy control  │ Performance opt │
│ AS-path metrics │ Bandwidth/delay │
│ Manual control  │ Automatic conv  │
│ Minutes to conv │ Seconds to conv │
│ Administrative  │ Technical focus │
└─────────────────┴─────────────────┘

SESSION TYPES:
• eBGP: Between different ASes (AD=20)
• iBGP: Within same AS (AD=200)
```

---

## **DIAPOSITIVA 15: BGP Attributes y Path Selection**
```
🎛️ BGP PATH SELECTION ALGORITHM

WELL-KNOWN MANDATORY:
```
1. ORIGIN: IGP (i) > EGP (e) > Incomplete (?)
2. AS_PATH: Shorter path preferred
3. NEXT_HOP: IP address of next router

WELL-KNOWN DISCRETIONARY:
1. LOCAL_PREFERENCE: Higher preferred (local significance)
2. ATOMIC_AGGREGATE: Route summarization indicator

OPTIONAL ATTRIBUTES:
1. MED: Multi-Exit Discriminator (lower preferred)
2. COMMUNITY: Route tagging (32-bit values)

BGP DECISION PROCESS (ORDER):
1. Highest Weight (Cisco proprietary)
2. Highest Local Preference
3. Locally originated routes
4. Shortest AS_PATH
5. Lowest Origin code
6. Lowest MED (same AS only)
7. eBGP over iBGP
8. Lowest IGP metric to next-hop
9. Oldest route (stability)
10. Lowest Router-ID
```

---

## **DIAPOSITIVA 16: LABORATORIO 1 - Static Routing**
```
🧪 LABORATORIO PRÁCTICO 1

STATIC ROUTING CON REDUNDANCIA AVANZADA

OBJETIVOS:
• Implementar static routing con multiple paths
• Configurar load balancing ECMP
• Testing de floating routes para failover
• Performance optimization y monitoring

TOPOLOGÍA:
```
Grupo A ←→ Grupo B ←→ Grupo C
   ↕                    ↑
   └──── Backup Path ───┘

TÉCNICAS A IMPLEMENTAR:
├─ Equal Cost Multi-Path (ECMP)
├─ Floating routes con different AD
├─ PCC para intelligent load balancing
├─ Route monitoring y failover testing
└─ Performance measurement y optimization

DURACIÓN: 45 minutos
FOCUS: Practical redundancy y high availability
```

---

## **DIAPOSITIVA 17: LABORATORIO 2 - OSPF Multi-Área**
```
🧪 LABORATORIO PRÁCTICO 2

OSPF HIERARCHICAL IMPLEMENTATION

OBJETIVOS:
• Diseñar y implementar OSPF multi-área
• Configurar ABRs (Area Border Routers)
• Optimizar convergence y LSA propagation
• Advanced troubleshooting methodology

DISEÑO DE ÁREAS:
```
AREA 0 (BACKBONE):
├─ Routers A y B (ABRs + Backbone)
├─ Core routing functionality
└─ Inter-area LSA generation

AREA 1 (REGULAR):
├─ Routers A y C (A=ABR, C=Internal)  
├─ Local traffic optimization
└─ Summary LSAs from backbone

ADVANCED FEATURES:
├─ Authentication (MD5)
├─ Cost optimization
├─ Area summarization
├─ Stub area configuration (optional)
└─ Failover testing y convergence measurement
```

---

## **DIAPOSITIVA 18: LABORATORIO 3 - BGP Implementation**
```
🧪 LABORATORIO PRÁCTICO 3

BGP INTER-AS ROUTING

OBJETIVOS:
• Establecer BGP sessions entre ASes
• Implementar routing policies avanzadas
• Configure path manipulation techniques
• Test BGP convergence y stability

AS DESIGN:
```
AS 65001 (Customer) ←→ AS 65002 (Transit) ←→ AS 65003 (Customer)

POLICIES TO IMPLEMENT:
├─ LOCAL_PREFERENCE manipulation
├─ AS-PATH prepending  
├─ MED (Multi-Exit Discriminator)
├─ BGP Communities tagging
├─ Route filtering y security
└─ Prefix aggregation

ADVANCED FEATURES:
├─ Route Reflector concepts
├─ BGP Graceful Restart
├─ Community-based policies
├─ Traffic engineering
└─ Performance monitoring
```

---

## **DIAPOSITIVA 19: Route Selection Algorithm Demo**
```
📊 PRACTICAL ROUTE SELECTION

SCENARIO WALKTHROUGH:
```
Destination: 192.168.10.50

ROUTING TABLE ENTRIES:
├─ 0.0.0.0/0 via 10.0.0.1 (Static, AD=1)
├─ 192.168.0.0/16 via 10.0.0.2 (OSPF, AD=110)
├─ 192.168.10.0/24 via 10.0.0.3 (OSPF, AD=110, Metric=20)
├─ 192.168.10.0/24 via 10.0.0.4 (OSPF, AD=110, Metric=10)
└─ 192.168.10.48/28 via 10.0.0.5 (Static, AD=1)

SELECTION PROCESS:
1. LONGEST PREFIX MATCH:
   └─ /28 prefix más específico → 192.168.10.48/28

2. SELECTED ROUTE:
   └─ 192.168.10.48/28 via 10.0.0.5

💡 Prefix length trumps administrative distance!
```

---

## **DIAPOSITIVA 20: Administrative Distance Table**
```
📏 ROUTE TRUSTWORTHINESS HIERARCHY

ROUTEROS ADMINISTRATIVE DISTANCES:
```
┌─────────────────────┬────────┬─────────────────┐
│ Route Source        │   AD   │ Trust Level     │
├─────────────────────┼────────┼─────────────────┤
│ Directly Connected  │   0    │ Absolute        │
│ Static Routes       │   1    │ Highest         │
│ eBGP               │   20   │ Very High       │
│ OSPF Internal      │  110   │ High            │
│ RIP                │  120   │ Medium          │
│ iBGP               │  200   │ Low             │
│ Unknown            │  255   │ Never Used      │
└─────────────────────┴────────┴─────────────────┘

PRACTICAL IMPLICATIONS:
• Static routes override dynamic protocols
• eBGP preferred over IGPs (by design)
• iBGP has low preference (requires IGP for next-hop)
• Custom AD can be configured for specific needs

DESIGN PRINCIPLE: Lower AD = More Trusted
```

---

## **DIAPOSITIVA 21: Convergence Comparison**
```
⚡ PROTOCOL CONVERGENCE CHARACTERISTICS

CONVERGENCE TIME ANALYSIS:
```
┌─────────────┬─────────────┬─────────────┬─────────────┐
│ Protocol    │ Detection   │ Propagation │ Total Time  │
├─────────────┼─────────────┼─────────────┼─────────────┤
│ Static      │ Manual      │ Instant     │ Manual      │
│ RIP         │ 180s        │ 30s/hop     │ Minutes     │
│ OSPF        │ 40s (Dead)  │ Sub-second  │ <1 minute   │
│ BGP         │ 180s (Hold) │ Seconds     │ 2-5 minutes │
└─────────────┴─────────────┴─────────────┴─────────────┘

OPTIMIZATION TECHNIQUES:
OSPF:
├─ Reduce hello/dead timers
├─ Fast hello (sub-second)
├─ BFD (Bidirectional Forwarding Detection)
└─ Interface fast convergence

BGP:
├─ Reduce keepalive/hold timers
├─ BGP fast external fallover
├─ BGP graceful restart
└─ Route dampening tuning

NETWORK DESIGN IMPACT:
• Critical applications → OSPF preferred
• Internet routing → BGP required
• Simple networks → Static sufficient
```

---

## **DIAPOSITIVA 22: Route Filtering Strategies**
```
🔒 SECURITY Y PERFORMANCE FILTERING

FILTERING PURPOSES:
```
SECURITY FILTERING:
├─ Block private networks in BGP
├─ Prevent default route from customers
├─ AS-path filtering for trusted sources
└─ Community-based access control

PERFORMANCE FILTERING:
├─ Reduce routing table size
├─ Minimize update overhead
├─ Aggregate routes (summarization)
└─ Optimize convergence times

ROUTEROS FILTERING MECHANISMS:
┌─────────────────────────────────────┐
│ /routing filter add chain=bgp-out   │
│   prefix=10.0.0.0/8                │
│   action=discard                    │
│   comment="Block RFC1918"          │
└─────────────────────────────────────┘

COMMON FILTER PATTERNS:
• Prefix-length based: /24 to /32 only
• AS-path regex: ^65001$ (direct from AS 65001)
• Community matching: 65001:100 (customer routes)
• Combine multiple criteria for precision
```

---

## **DIAPOSITIVA 23: Policy-Based Routing (PBR)**
```
🎯 ADVANCED TRAFFIC ENGINEERING

PBR = ROUTING BEYOND DESTINATION
```
TRADITIONAL ROUTING:
└─ Decision based ONLY on destination IP

POLICY-BASED ROUTING:
├─ Source IP address
├─ Application/protocol
├─ Time of day
├─ User authentication
├─ Quality of Service marking
└─ Packet size/characteristics

MIKROTIK PBR IMPLEMENTATION:
┌─────────────────────────────────────┐
│ 1. MANGLE: Mark traffic             │
│ /ip firewall mangle add             │
│   src-address=192.168.10.0/24       │
│   action=mark-routing                │
│   new-routing-mark=admin-traffic     │
│                                     │
│ 2. ROUTE: Create policy route       │
│ /ip route add gateway=ISP1          │
│   routing-mark=admin-traffic        │
└─────────────────────────────────────┘

USE CASES:
• Load balancing por user groups
• QoS enforcement
• Backup path activation
• Traffic engineering
```

---

## **DIAPOSITIVA 24: Network Design Hierarchy**
```
🏗️ SCALABLE NETWORK ARCHITECTURE

THREE-TIER MODEL APPLIED TO ROUTING:
```
CORE LAYER:
├─ High-speed routing between sites
├─ OSPF backbone area typically
├─ BGP for inter-domain
├─ Minimal policy enforcement
└─ Maximum reliability y redundancy

DISTRIBUTION LAYER:
├─ Route summarization points
├─ Policy enforcement boundaries
├─ OSPF regular areas
├─ Redistribution control points
└─ Security filtering implementation

ACCESS LAYER:
├─ Default routes typically sufficient
├─ Static routing often appropriate
├─ Minimal routing complexity
├─ Host connectivity focus
└─ Basic security policies

ROUTING PROTOCOL PLACEMENT:
• CORE: BGP + OSPF backbone
• DISTRIBUTION: OSPF areas + redistribution
• ACCESS: Static routes + defaults
```

---

## **DIAPOSITIVA 25: Route Redistribution**
```
🔄 PROTOCOL INTEGRATION

REDISTRIBUTION = IMPORTING ROUTES BETWEEN PROTOCOLS
```
WHY REDISTRIBUTE?
├─ Legacy network integration
├─ Different administrative domains
├─ Optimal protocol per network segment
└─ Gradual migration strategies

CHALLENGES:
┌─────────────────────────────────────┐
│ METRIC INCOMPATIBILITY:             │
│ ├─ OSPF uses cost (bandwidth-based) │
│ ├─ RIP uses hop count               │
│ ├─ BGP uses path attributes         │
│ └─ Need metric conversion           │
│                                     │
│ ROUTING LOOPS:                      │
│ ├─ Mutual redistribution risks     │
│ ├─ Administrative distance issues   │
│ └─ Route feedback prevention       │
└─────────────────────────────────────┘

BEST PRACTICES:
• Single redistribution point preferred
• Route tagging for loop prevention
• Careful metric assignment
• Extensive filtering
• One-way redistribution when possible
```

---

## **DIAPOSITIVA 26: OSPF Optimization Techniques**
```
⚡ OSPF PERFORMANCE TUNING

AREA OPTIMIZATION:
```
AREA DESIGN PRINCIPLES:
├─ BACKBONE contiguous y central
├─ REGULAR AREAS: 50-100 routers max
├─ STUB AREAS: No external routes
├─ TOTALLY STUBBY: Minimal routing table
└─ NSSA: Controlled external connectivity

TIMER OPTIMIZATION:
┌─────────────────────────────────────┐
│ HELLO/DEAD INTERVALS:               │
│ ├─ Default: 10s/40s (LAN)          │
│ ├─ Fast: 1s/4s (critical links)    │
│ ├─ Slow: 30s/120s (WAN)            │
│ └─ BFD: Sub-second detection        │
│                                     │
│ SPF TIMERS:                         │
│ ├─ spf-delay: Initial delay         │
│ ├─ spf-max-delay: Maximum delay     │
│ └─ Prevents SPF thrashing           │
└─────────────────────────────────────┘

SUMMARIZATION:
/routing ospf area range add area=area1 
  prefix=192.168.0.0/16 advertise=yes

AUTHENTICATION:
/routing ospf area set backbone 
  auth-type=md5 auth-key="SecureKey123"
```

---

## **DIAPOSITIVA 27: BGP Advanced Features**
```
🚀 BGP ENTERPRISE FEATURES

ROUTE REFLECTOR:
```
PROBLEM: iBGP full mesh doesn't scale
├─ N routers = N(N-1)/2 sessions
├─ 10 routers = 45 sessions
├─ 100 routers = 4,950 sessions!
└─ Administrative nightmare

SOLUTION: Route Reflector
┌─────────────────────────────────────┐
│        Route Reflector              │
│             ●                       │
│         ╱   │   ╲                   │
│       ╱     │     ╲                 │
│     ●       ●       ●               │
│   Client  Client  Client            │
│                                     │
│ SESSIONS: N clients = N sessions    │
│ (vs N(N-1)/2 full mesh)            │
└─────────────────────────────────────┘

COMMUNITIES:
32-bit values for route tagging:
├─ 65001:100 → Customer routes
├─ 65001:200 → Internal routes
├─ NO_EXPORT → Don't advertise to eBGP
└─ NO_ADVERTISE → Don't advertise at all

GRACEFUL RESTART:
• Maintain forwarding during BGP restart
• Reduces network disruption
• Critical for high availability
```

---

## **DIAPOSITIVA 28: Troubleshooting Methodology**
```
🔧 SYSTEMATIC NETWORK TROUBLESHOOTING

LAYERED APPROACH:
```
LAYER 1 (PHYSICAL):
├─ /interface print where running=no
├─ Cable continuity testing
├─ LED status indicators
└─ Power y connectivity verification

LAYER 2 (DATA LINK):
├─ /interface bridge host print
├─ ARP table verification
├─ VLAN configuration check
└─ Switching functionality test

LAYER 3 (NETWORK):
├─ /ip route print where active=no
├─ Routing table analysis
├─ /tool traceroute destination
└─ ICMP reachability testing

ROUTING PROTOCOLS:
├─ /routing ospf neighbor print
├─ /routing bgp peer print status
├─ LSA database verification
├─ Convergence time measurement
└─ Policy verification

DOCUMENTATION APPROACH:
1. Identify symptoms clearly
2. Isolate problem domain  
3. Test systematically
4. Document findings
5. Verify solution
```

---

## **DIAPOSITIVA 29: Performance Metrics**
```
📊 ROUTING PERFORMANCE MEASUREMENT

KEY PERFORMANCE INDICATORS:
```
CONVERGENCE METRICS:
├─ Detection time: Link failure → protocol awareness
├─ Propagation time: Update distribution
├─ Calculation time: New routing table
└─ Installation time: Routes → forwarding table

RESOURCE UTILIZATION:
┌─────────────────────────────────────┐
│ MEMORY USAGE:                       │
│ ├─ OSPF: ~1KB per LSA              │
│ ├─ BGP: ~500 bytes per route       │
│ └─ Total = Base + (Routes × Size)   │
│                                     │
│ CPU UTILIZATION:                    │  
│ ├─ SPF calculation frequency        │
│ ├─ Route update processing          │
│ └─ Neighbor maintenance overhead    │
│                                     │
│ NETWORK OVERHEAD:                   │
│ ├─ Hello packets bandwidth          │
│ ├─ Update message frequency         │
│ └─ Database synchronization traffic │
└─────────────────────────────────────┘

OPTIMIZATION TARGETS:
• Convergence < 1 second (critical)
• Memory < 1GB (enterprise)
• CPU < 10% (normal operation)
• Bandwidth overhead < 1%
```

---

## **DIAPOSITIVA 30: Homework Assignment**
```
🏠 PROYECTO INDIVIDUAL AVANZADO

GNS3 ISP SIMULATION PROJECT

REQUERIMIENTOS TÉCNICOS:
• 5+ routers simulando ISP infrastructure
• Multiple autonomous systems (3+ ASes)
• OSPF multi-área within each AS
• BGP connections entre ASes
• Customer connections con policy routing
• Advanced routing policies implementation

TOPOLOGÍA MÍNIMA:
```
Customer A ── ISP1 ── Transit Provider ── ISP2 ── Customer B
    │           │            │             │          │
    AS 65001    AS 65100    AS 65000      AS 65200   AS 65002

FEATURES TO IMPLEMENT:
├─ Route filtering y security policies
├─ Traffic engineering con BGP attributes
├─ Redundancy y failover testing
├─ Performance monitoring y optimization
├─ Policy-based routing scenarios
└─ Comprehensive documentation

ANÁLISIS REQUERIDO:
• Convergence time measurements
• Traffic flow analysis
• Policy effectiveness testing
• Failure scenario documentation
• Performance optimization report
```

---

## **DIAPOSITIVA 31: Project Deliverables**
```
📋 ENTREGABLES DEL PROYECTO

DOCUMENTACIÓN TÉCNICA:
```
1. NETWORK DESIGN DOCUMENT:
   ├─ Topology diagrams con AS boundaries
   ├─ IP addressing scheme completo
   ├─ Routing protocol selection justification
   └─ Policy design rationale

2. CONFIGURATION GUIDE:
   ├─ Complete router configurations
   ├─ Step-by-step implementation guide
   ├─ Verification procedures
   └─ Rollback procedures

3. TESTING REPORT:
   ├─ Convergence time measurements
   ├─ Failover scenario results
   ├─ Policy effectiveness analysis
   ├─ Performance benchmarks
   └─ Troubleshooting case studies

4. OPTIMIZATION ANALYSIS:
   ├─ Before/after performance comparison
   ├─ Resource utilization analysis
   ├─ Scaling considerations
   └─ Future enhancement recommendations

TECHNICAL ARTIFACTS:
• GNS3 project file (.gns3project)
• Router configuration exports (.rsc files)  
• Performance measurement logs
• Network simulation videos/screenshots
```

---

## **DIAPOSITIVA 32: Evaluación del Módulo**
```
📊 ASSESSMENT Y CALIFICACIÓN

DISTRIBUCIÓN DETALLADA:
```
EXAMEN PRÁCTICO ROUTING (45%):
├─ Static routing configuration (10%)
├─ OSPF multi-área implementation (15%)  
├─ BGP configuration y policies (15%)
└─ Troubleshooting scenarios (5%)

PROYECTO GNS3 COMPLEJO (40%):
├─ Network design quality (10%)
├─ Technical implementation (15%)
├─ Documentation completeness (10%)
└─ Innovation y advanced features (5%)

PRESENTACIÓN OPTIMIZATION (15%):
├─ Technical content accuracy (8%)
├─ Presentation delivery (4%)
└─ Q&A handling (3%)

COMPETENCY RUBRIC:
🟢 Expert (90-100%): Diseña redes ISP-grade independientemente
🔵 Advanced (80-89%): Implementa routing complejo con minimal guidance  
🟡 Competent (70-79%): Configura protocolos con occasional supervision
🔴 Developing (<70%): Requiere substantial support y remediation

BONUS OPPORTUNITIES:
• Advanced BGP features implementation (+5%)
• Original troubleshooting methodology (+3%)
• Performance optimization innovations (+2%)
```

---

## **DIAPOSITIVA 33: Industry Applications**
```
🏢 REAL-WORLD IMPLEMENTATIONS

ENTERPRISE NETWORKS:
```
LARGE CORPORATION:
├─ OSPF multi-área para campus networks
├─ BGP para multi-homing Internet connectivity
├─ Policy routing para QoS enforcement
├─ Route summarization para scalability
└─ Advanced troubleshooting for uptime

SERVICE PROVIDER:
├─ BGP as core protocol (eBGP/iBGP)
├─ MPLS VPN service delivery
├─ Traffic engineering y optimization
├─ Route reflectors para iBGP scaling
└─ Advanced routing policies

DATA CENTER:
├─ OSPF para internal connectivity
├─ BGP para external connectivity
├─ ECMP para load distribution
├─ Fast convergence requirements
└─ Automation y orchestration

CAREER PATHS:
• Network Engineer → Routing specialist
• Network Architect → Multi-protocol expert
• NOC Engineer → Troubleshooting expert
• Consultant → Design y optimization specialist
```

---

## **DIAPOSITIVA 34: Future Technologies**
```
🚀 EMERGING ROUTING TECHNOLOGIES

NEXT-GENERATION PROTOCOLS:
```
SEGMENT ROUTING (SR):
├─ Source routing with MPLS/IPv6
├─ Simplified network operations
├─ Traffic engineering built-in
└─ SDN integration ready

BGP FLOWSPEC:
├─ Distribute traffic filtering rules
├─ DDoS mitigation automation  
├─ Granular traffic control
└─ Security policy distribution

ROUTING AUTOMATION:
├─ Intent-based networking (IBN)
├─ ML-driven route optimization
├─ Automated policy generation
└─ Self-healing networks

SOFTWARE-DEFINED ROUTING:
├─ Centralized route computation
├─ OpenFlow y P4 integration
├─ Programmable forwarding planes
└─ Dynamic topology adaptation

PREPARATION STRATEGIES:
• Master fundamentals thoroughly
• Understand automation principles
• Practice with simulation tools
• Stay current with standards evolution
```

---

## **DIAPOSITIVA 35: Certification Path**
```
🎓 PROFESSIONAL DEVELOPMENT PATH

MIKROTIK CERTIFICATIONS:
```
MTCNA (Network Associate):
├─ Routing fundamentals covered ✅
├─ Prerequisites: This course
├─ Focus: Basic routing y switching
└─ Industry recognition: Entry level

MTCRE (Routing Engineer):
├─ Advanced routing concepts
├─ Prerequisites: MTCNA + experience
├─ Focus: Complex routing scenarios
└─ Industry recognition: Professional level

MTCTCE (Traffic Control Engineer):
├─ QoS y traffic engineering
├─ Prerequisites: MTCRE
├─ Focus: Performance optimization
└─ Industry recognition: Expert level

COMPLEMENTARY CERTIFICATIONS:
├─ Cisco CCNP Enterprise (routing focus)
├─ Juniper JNCIP-ENT (enterprise routing)
├─ Arista ACE (data center routing)
└─ Industry vendor-neutral: BGP/OSPF specialty

CONTINUOUS LEARNING:
• Follow routing protocol RFCs
• Participate in network engineering communities
• Practice with diverse vendor platforms
• Stay updated with Internet routing trends
```

---

## **DIAPOSITIVA 36: Resources y Support**
```
📚 LEARNING RESOURCES EXTENDED

OFFICIAL DOCUMENTATION:
```
🌐 help.mikrotik.com/docs/spaces/ROS/pages/routing
📖 wiki.mikrotik.com/wiki/Manual:Routing
💬 forum.mikrotik.com → Routing sections

ADVANCED REFERENCES:
📚 "Internet Routing Architectures" - Halabi/McPherson
📚 "OSPF: Anatomy of an Internet Routing Protocol" - Moy  
📚 "BGP4: Inter-Domain Routing in the Internet" - Zhang/Bartell
📚 "Routing TCP/IP, Volume I y II" - Doyle/Carroll

HANDS-ON PRACTICE:
🔍 GNS3 + RouterOS CHR images
📊 Packet Tracer para conceptual understanding  
🖥️ EVE-NG para advanced simulations
🌐 Internet2 topology references

COMMUNITY SUPPORT:
💬 Reddit r/networking, r/mikrotik
🐦 Twitter routing community (#BGP #OSPF)
📺 YouTube channels: Ivan Pepelnjak, David Bombal
🎓 Network engineering Discord servers

REAL-TIME SUPPORT:
📧 [instructor email] para technical questions
⏰ Office hours: [schedule]
💬 Course forum para peer discussion
🔧 Lab session support during class time
```

---

## **DIAPOSITIVA 37: Final Q&A Session**
```
❓ COMPREHENSIVE Q&A

TECHNICAL QUESTIONS WELCOME:
```
PROTOCOL-SPECIFIC:
• OSPF area design decisions?
• BGP path selection clarifications?
• Static routing best practices?
• Convergence optimization techniques?

IMPLEMENTATION:
• RouterOS-specific configurations?
• Troubleshooting methodology questions?
• Performance tuning approaches?
• Integration challenges?

PROJECT SUPPORT:
• GNS3 setup y configuration help?
• Topology design guidance?
• Documentation requirements clarification?
• Timeline y milestone questions?

CAREER GUIDANCE:  
• Certification path recommendations?
• Industry application insights?
• Skills development priorities?
• Advanced learning directions?

📧 CONTACT INFORMATION:
• Email: [instructor contact]
• Office Hours: [schedule]  
• Forum: [course discussion platform]
• Emergency contact: [backup support]

¡Excellent work mastering routing fundamentals! 🎉
NEXT: Apply knowledge in real networks y continue learning!
```

---

## **Notas para el Docente**

### **Timing Sugerido por Sección:**
- **Introducción y objetivos (1-3):** 8 minutos
- **Fundamentos teóricos profundos (4-11):** 45 minutos  
- **Static routing concepts (7-9):** 20 minutos
- **Dynamic protocols theory (10-15):** 40 minutos
- **Laboratorios overview (16-18):** 15 minutos
- **Advanced concepts (19-27):** 35 minutos
- **Troubleshooting y optimization (28-29):** 15 minutos
- **Project y evaluation (30-37):** 15 minutos

**Total: ~3.2 horas presentation + 4.8 horas lab time = 8 hours module**

### **Puntos Críticos para Enfatizar:**

1. **Teoría de Grafos Foundation:** Understanding matemático del routing
2. **Route Selection Process:** Multi-step algorithm comprehension
3. **Protocol Convergence:** Time characteristics y optimization
4. **BGP Policy Control:** Administrative vs performance routing
5. **Troubleshooting Methodology:** Systematic layer-by-layer approach
6. **Real-world Applications:** Industry relevance y career preparation

### **Demostraciones en Vivo Recomendadas:**
- **Route selection process** con multiple paths (8 min)
- **OSPF LSA database** exploration (10 min)  
- **BGP path attributes** manipulation (12 min)
- **Convergence timing** measurement (8 min)
- **Troubleshooting workflow** systematic approach (10 min)

### **Assessment Integration Points:**
- **Formative:** Real-time protocol status checking
- **Practical:** Hands-on configuration verification
- **Analytical:** Route selection process explanation
- **Creative:** Network design justification
- **Collaborative:** Peer troubleshooting scenarios

Esta presentación proporciona education integral que combina mathematical foundations, protocol mechanics, practical implementation, y real-world application preparing students para advanced networking careers.

