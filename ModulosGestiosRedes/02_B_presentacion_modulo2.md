# Presentación Módulo 2: Configuración de Interfaces y Switching
## Versión Actualizada con Fundamentos Teóricos Profundos

## Estructura de Presentación (PowerPoint/Google Slides)

---

## **DIAPOSITIVA 1: Título**
```
MÓDULO 2: CONFIGURACIÓN DE INTERFACES Y SWITCHING
Fundamentos Teóricos y Implementación Práctica

Curso Avanzado MikroTik
Gestión Operativa y Seguridad de Redes

Profesor: [Nombre]
Duración: 2 semanas (8 horas)
Prerequisito: Módulo 1 completado
```

---

## **DIAPOSITIVA 2: Objetivos del Módulo**
```
🎯 COMPETENCIAS A DESARROLLAR

Al finalizar este módulo podrás:

✅ ENTENDER la teoría fundamental de switching y VLANs
   - Algoritmos de bridging y spanning tree
   - IEEE 802.1Q frame processing
   - Hardware vs software switching

✅ CONFIGURAR interfaces físicas y virtuales optimizando performance
   - Parámetros físicos ethernet
   - Bridge configuration avanzada
   - VLAN implementation methods

✅ IMPLEMENTAR switching empresarial con segmentación completa
   - VLAN design patterns
   - Inter-VLAN routing
   - Troubleshooting sistemático

🔧 BASE TEÓRICA SÓLIDA + IMPLEMENTACIÓN PRÁCTICA
```

---

## **DIAPOSITIVA 3: Arquitectura del Módulo**
```
📚 ESTRUCTURA INTEGRAL

FUNDAMENTOS TEÓRICOS (40%):
• Evolución del networking moderno
• Teoría de switching transparente
• Algoritmos STP/RSTP profundos
• Frame processing 802.1Q
• Metodologías troubleshooting

IMPLEMENTACIÓN PRÁCTICA (60%):
• Configuración interfaces MikroTik
• Bridge y hardware offload
• VLAN design patterns
• Performance optimization
• Troubleshooting hands-on

🎓 ENFOQUE: Engineering principles + Practical skills
📊 EVALUACIÓN: 50% lab + 35% proyecto + 15% teoría
```

---

## **DIAPOSITIVA 4: Evolución del Networking**
```
🏗️ DE HUBS A SWITCHING INTELIGENTE

HUB-BASED NETWORKS (Legacy):
🔴 Single collision domain
🔴 Half-duplex, shared bandwidth
🔴 CSMA/CD contention
🔴 Performance degradation con scale

SWITCH-BASED NETWORKS:
🟡 Collision domain per port
🟡 Full-duplex, dedicated bandwidth  
🟡 MAC address learning
🟡 Broadcast domain = entire switch

VLAN-AWARE SWITCHING (Modern):
🟢 Logical segmentation
🟢 Multiple broadcast domains
🟢 Policy enforcement granular
🟢 Security by design

CADA EVOLUCIÓN = MAYOR INTELIGENCIA + CONTROL
```

---

## **DIAPOSITIVA 5: Modelo OSI en Switching**
```
🔧 LAYERS RELEVANTES PARA SWITCHING

LAYER 1 (PHYSICAL):
• Auto-negotiation algorithms
• Link detection y fault tolerance
• Cable plant considerations
• Power over Ethernet (PoE)

LAYER 2 (DATA LINK):  
• MAC address learning automático
• Frame forwarding decisions
• VLAN tagging y processing
• Spanning Tree Protocol operation

LAYER 2.5 (VLAN/MPLS):
• VLAN membership determination
• 802.1Q tag processing
• Inter-VLAN routing preparation
• QoS marking y classification

💡 SWITCHING = INTELIGENCIA DISTRIBUIDA ACROSS LAYERS
```

---

## **DIAPOSITIVA 6: Teoría de Congestión y Control**
```
📊 BUFFER MANAGEMENT EN SWITCHES

TIPOS DE BUFFERING:
• Input Buffering → Buffers en puertos entrada
• Output Buffering → Buffers en puertos salida  
• Shared Buffering → Pool común memoria

FLOW CONTROL MECHANISMS:
┌─────────────────┐    PAUSE    ┌─────────────────┐
│   SWITCH A      │◄─────────────│   SWITCH B      │
│ (Buffer 90%)    │              │ (Send pause)    │
└─────────────────┘              └─────────────────┘

IEEE 802.3X PAUSE FRAMES:
• Temporary transmission halt
• Buffer overflow prevention  
• Automatic resume when space available
• Critical para preventing packet loss

MIKROTIK: Hardware-based flow control automático
```

---

## **DIAPOSITIVA 7: Fundamentos Ethernet Profundos**
```
⚡ ETHERNET THEORY DEEP DIVE

CSMA/CD ALGORITHM (Half-Duplex):
1. CARRIER SENSE → Listen before transmit
2. MULTIPLE ACCESS → Shared medium access
3. COLLISION DETECTION → Detect simultaneous tx
4. EXPONENTIAL BACKOFF → Retry with delay

AUTO-NEGOTIATION PROCESS:
┌─────────────┐    FLP Exchange    ┌─────────────┐
│  DEVICE A   │◄─────────────────►│  DEVICE B   │
│             │                   │             │
└─────────────┘                   └─────────────┘
      │                                 │
      ▼                                 ▼
Common Capabilities Analysis → Best Configuration

JERARQUÍA DE PREFERENCIA:
1000BASE-T FD > 1000BASE-T HD > 100BASE-TX FD > 100BASE-TX HD > 10BASE-T FD > 10BASE-T HD
```

---

## **DIAPOSITIVA 8: Performance Ethernet Real**
```
📈 OVERHEAD Y THROUGHPUT REAL

ETHERNET FRAME OVERHEAD:
┌──────────┬─────────┬──────────┬─────────┬─────────┬─────┐
│ Preamble │   DA    │    SA    │ Type/Len│  Data   │ FCS │
│ 8 bytes  │ 6 bytes │ 6 bytes  │ 2 bytes │ 46-1500 │ 4B  │
└──────────┴─────────┴──────────┴─────────┴─────────┴─────┘
           │◄──── 18 bytes overhead ────►│

CÁLCULOS REALES:
• 1 Gbps Ethernet = 1,000,000,000 bps
• Interframe Gap (IFG) = 96 bits obligatorio
• Preamble = 64 bits por frame
• Maximum throughput ≈ 94.7% de line rate

EJEMPLO: 64-byte frames
→ 1,488,095 frames/second máximo
→ Wire-speed = optimal switching goal
```

---

## **DIAPOSITIVA 9: Bridge Theory - Transparent Bridging**
```
🌉 ALGORITMO LEARNING BRIDGE

PRINCIPIOS FUNDAMENTALES:
1. TRANSPARENCY → Hosts no saben del bridge
2. LEARNING → Aprende MACs automáticamente
3. FORWARDING → Decisiones basadas en tabla
4. FLOODING → Broadcast si destino unknown
5. AGING → Elimina entradas obsoletas

ALGORITMO PASO A PASO:
```
Frame llega puerto P, MAC origen S, destino D:

LEARNING PHASE:
├─ Agregar/actualizar: S → Puerto P
├─ Timestamp última actividad  
└─ Tabla MAC updated

FORWARDING PHASE:
├─ SI D en tabla MAC:
│  ├─ Puerto destino ≠ puerto origen → FORWARD
│  └─ Puerto destino = puerto origen → FILTER
└─ SI D NO en tabla → FLOOD todos puertos

AGING PHASE:
└─ Eliminar entradas > aging time (default 5 min)
```

---

## **DIAPOSITIVA 10: Hardware vs Software Switching**
```
⚡ ASIC vs CPU PROCESSING

SOFTWARE SWITCHING (CPU PATH):
┌─────┐   ┌────────┐   ┌─────────────┐   ┌────────┐   ┌─────┐
│ NIC │──►│ Driver │──►│   Kernel    │──►│ Driver │──►│ NIC │
└─────┘   └────────┘   │ Switching   │   └────────┘   └─────┘
                       │   Logic     │
                       └─────────────┘
                       CPU-intensive

HARDWARE SWITCHING (ASIC PATH):  
┌─────┐   ┌─────────────────────────────────────┐   ┌─────┐
│ PHY │──►│         SWITCH ASIC                 │──►│ PHY │
└─────┘   │ ┌─────┬─────┬─────┬─────┬─────┐   │   └─────┘
          │ │Parse│Lookup│Mod │Queue│Sched│   │
          │ └─────┴─────┴─────┴─────┴─────┘   │
          └─────────────────────────────────────┘
          Wire-speed, hardware-accelerated
```

---

## **DIAPOSITIVA 11: ASIC Performance Metrics**
```
🚀 HARDWARE SWITCHING PERFORMANCE

LATENCY COMPARISON:
• Software switching: 50-500 microsegundos
  ├─ Context switches, interrupts
  ├─ Memory copies, cache misses
  └─ Variable según CPU load

• Hardware switching: 1-10 microsegundos  
  ├─ Dedicated silicon pipeline
  ├─ No OS overhead
  └─ Consistent, predictable

PACKET PROCESSING RATES:
┌─────────────┬─────────────┬─────────────┐
│ Frame Size  │ 1 Gbps PPS  │ 10 Gbps PPS│
├─────────────┼─────────────┼─────────────┤
│ 64 bytes    │ 1,488,095   │ 14,880,952  │
│ 256 bytes   │ 452,898     │ 4,528,985   │
│ 1518 bytes  │ 81,274      │ 812,743     │
└─────────────┴─────────────┴─────────────┘

MIKROTIK: Automatic hardware offload when possible
```

---

## **DIAPOSITIVA 12: Spanning Tree - El Problema del Loop**
```
⚠️ NETWORK LOOPS = CATASTROPHIC FAILURE

EL PROBLEMA:
┌─────────┐                    ┌─────────┐
│Switch A │◄──────────────────►│Switch B │
│         │                    │         │
│    └────┴──────────────────────┘       │
│    Redundant Link              │         
└─────────┘                    └─────────┘

SIN STP:
1. Host envía broadcast frame
2. Switch A flood → Switch B  
3. Switch B flood → Switch A
4. LOOP INFINITO → Network meltdown

BROADCAST STORM SYMPTOMS:
• 100% link utilization
• MAC table instability ("flapping")
• Network completely unusable
• Only solution: physical disconnection

STP = MATHEMATICAL SOLUTION TO GRAPH THEORY PROBLEM
```

---

## **DIAPOSITIVA 13: STP Algorithm Deep Dive**
```
🌳 SPANNING TREE PROTOCOL - IEEE 802.1D

OBJETIVO: Crear árbol sin ciclos conectando todos nodos

FASE 1: ROOT BRIDGE ELECTION
Bridge ID = [Priority 2 bytes] + [MAC Address 6 bytes]
┌─────────────────┬─────────────────────────────┐
│ Priority        │ MAC Address                 │
│ (0x8000 default)│ (único por device)         │
└─────────────────┴─────────────────────────────┘
             ↓
    Lowest Bridge ID = Root Bridge

FASE 2: ROOT PORT SELECTION (cada non-root bridge)
Path Cost acumulativo hacia root:
• 10 Mbps = 100    • 100 Mbps = 19
• 1 Gbps = 4       • 10 Gbps = 2

FASE 3: DESIGNATED PORT SELECTION (cada link)
Por cada enlace físico:
→ Un puerto designated (forwarding)
→ Selección basada en lowest path cost
```

---

## **DIAPOSITIVA 14: STP Port States y Timers**
```
⏱️ STP CONVERGENCE PROCESS

PORT STATES PROGRESSION:
┌─────────┐  Max Age   ┌──────────┐  Forward   ┌──────────┐  Forward   ┌───────────┐
│BLOCKING │────20s────►│LISTENING │────15s────►│LEARNING  │────15s────►│FORWARDING │
│         │            │          │            │          │            │           │
└─────────┘            └──────────┘            └──────────┘            └───────────┘
    │                      │                      │                        │
    ▼                      ▼                      ▼                        ▼
No Data              No Data                 Learn MACs              Normal Operation
Listen BPDUs         Process BPDUs          Process BPDUs           Process BPDUs
                     Send BPDUs              Send BPDUs              Send BPDUs

TIMERS CONFIGURATION:
• Hello Time: 2 segundos (BPDU frequency)
• Max Age: 20 segundos (BPDU timeout)  
• Forward Delay: 15 segundos (Learning + Listening)

TOTAL CONVERGENCE TIME: 30-50 SEGUNDOS (Problema!)
```

---

## **DIAPOSITIVA 15: RSTP - Rapid Spanning Tree**
```
🚀 RSTP - CONVERGENCIA SUB-2 SEGUNDOS

MEJORAS FUNDAMENTALES:
┌─────────────────┬─────────────┬─────────────┐
│ Característica  │ STP (802.1D)│ RSTP (802.1w)│
├─────────────────┼─────────────┼─────────────┤
│ Convergence     │ 30-50 seg   │ <2 segundos │
│ Port States     │ 5 states    │ 3 states    │
│ BPDU Processing │ Timer-based │ Handshake   │
│ Edge Ports      │ No          │ Sí          │
│ Backup Paths    │ Calculated  │ Pre-computed│
└─────────────────┴─────────────┴─────────────┘

PROPOSAL/AGREEMENT MECHANISM:
Switch A ──► "PROPOSAL" ──► Switch B
         ◄── "AGREEMENT" ◄──
              │
              ▼
         Immediate Forwarding
         
EDGE PORT CONCEPT:
• Puerto conecta HOST (no switch)  
• Transición inmediata a Forwarding
• Si recibe BPDU → pierde edge status
```

---

## **DIAPOSITIVA 16: VLAN Theory - Segmentación Lógica**
```
🏷️ VIRTUAL LANs - EVOLUCIÓN CONCEPTUAL

PROBLEMA REDES PLANAS:
┌─────────────────────────────────────────┐
│          SINGLE BROADCAST DOMAIN        │
│ ┌──────┐ ┌──────┐ ┌──────┐ ┌──────┐    │
│ │ Depto│ │ Depto│ │ Depto│ │ Depto│    │
│ │  A   │ │  B   │ │  C   │ │  D   │    │
│ └──────┘ └──────┘ └──────┘ └──────┘    │
└─────────────────────────────────────────┘
Broadcast storm affects EVERYONE

SOLUCIÓN VLANs:
┌─────────────┬─────────────┬─────────────┐
│ VLAN 10     │ VLAN 20     │ VLAN 30     │
│ Depto A     │ Depto B     │ Depto C     │
│ Isolated    │ Isolated    │ Isolated    │
└─────────────┴─────────────┴─────────────┘

BENEFICIOS CUANTIFICABLES:
• 80% reducción broadcast traffic
• Improved security posture
• Flexible network reconfiguration
• Optimal resource utilization
```

---

## **DIAPOSITIVA 17: IEEE 802.1Q Frame Structure**
```
📦 VLAN TAGGING DEEP DIVE

ORIGINAL ETHERNET FRAME:
┌─────────┬─────────┬─────────┬──────────────┬─────┐
│ Dest MAC│ Src MAC │Type/Len │     Data     │ FCS │
│ 6 bytes │ 6 bytes │ 2 bytes │   46-1500    │ 4 B │
└─────────┴─────────┴─────────┴──────────────┴─────┘

802.1Q TAGGED FRAME:
┌─────────┬─────────┬─────┬─────────┬─────────┬──────────────┬─────┐
│ Dest MAC│ Src MAC │TPID │   TCI   │Type/Len │     Data     │ FCS │
│ 6 bytes │ 6 bytes │0x8100│ 2 bytes │ 2 bytes │   46-1500    │ 4 B │
└─────────┴─────────┴─────┴─────────┴─────────┴──────────────┴─────┘
                           │
                           ▼
                    ┌───┬───┬────────────┐
                    │PRI│CFI│  VLAN ID   │
                    │3bit│1bit│  12 bit   │
                    └───┴───┴────────────┘
                     QoS  │   1-4094
                         Reserved
```

---

## **DIAPOSITIVA 18: VLAN Frame Processing**
```
⚙️ INGRESS/EGRESS PROCESSING

INGRESS PROCESSING (Frame → Switch):
┌─────────────┐    ┌─────────────────┐    ┌─────────────────┐
│ Untagged    │───►│ Add PVID tag    │───►│ Internal        │
│ Frame       │    │ (Port VLAN ID)  │    │ Processing      │
└─────────────┘    └─────────────────┘    └─────────────────┘

┌─────────────┐    ┌─────────────────┐    ┌─────────────────┐
│ Tagged      │───►│ Validate VLAN   │───►│ Internal        │
│ Frame       │    │ membership      │    │ Processing      │
└─────────────┘    └─────────────────┘    └─────────────────┘

EGRESS PROCESSING (Switch → Frame):
┌─────────────────┐    ┌─────────────────┐    ┌─────────────┐
│ Internal        │───►│ Check port      │───►│ Tagged      │
│ Tagged Frame    │    │ VLAN config     │    │ Frame OUT   │
└─────────────────┘    └─────────────────┘    └─────────────┘
                              │
                              ▼
                    ┌─────────────────┐    ┌─────────────┐
                    │ Remove VLAN tag │───►│ Untagged    │
                    │ (Access port)   │    │ Frame OUT   │
                    └─────────────────┘    └─────────────┘
```

---

## **DIAPOSITIVA 19: VLAN Port Types Deep Dive**
```
🔌 ACCESS, TRUNK, HYBRID PORTS

ACCESS PORT OPERATION:
HOST ◄────────► [Access Port] ◄────────► SWITCH
     Untagged                   PVID=10

Ingress: Untagged → Add PVID tag (10)
Egress:  VLAN 10 → Remove tag → Untagged
Host: No VLAN knowledge required

TRUNK PORT OPERATION:
SWITCH-A ◄────────► [Trunk Port] ◄────────► SWITCH-B
         Tagged                    VLANs 10,20,30

Ingress: Validate tag against allowed VLANs
Egress:  Maintain tags (except native VLAN)
Switches: Full VLAN awareness required

NATIVE VLAN SECURITY:
• Default: VLAN 1 native (INSECURE!)
• Best practice: Unused VLAN as native
• Prevents VLAN hopping attacks
• Example: Native VLAN 999 (dummy)
```

---

## **DIAPOSITIVA 20: Inter-VLAN Routing Theory**
```
🔄 LAYER 3 ROUTING BETWEEN VLANS

EL PROBLEMA:
VLANs = Dominios Layer 2 separados
→ No comunicación natural entre VLANs
→ Necesidad de routing Layer 3

PROCESO INTER-VLAN ROUTING:
Host A (VLAN 10) → Host B (VLAN 20)

1. Host A determina: "Destino en diferente subnet"
2. Host A → Default Gateway (192.168.10.1)
3. Router recibe en VLAN 10 interface
4. Router consulta routing table
5. Router determina: "Salida via VLAN 20 interface"  
6. Router modifica headers:
   ├─ Source MAC → Router VLAN 20 MAC
   ├─ Dest MAC → Host B MAC (via ARP)
   └─ TTL decremented
7. Router transmite en VLAN 20
8. Host B recibe frame

REQUISITO: Router con interface en cada VLAN
```

---

## **DIAPOSITIVA 21: Arquitecturas Inter-VLAN**
```
🏗️ DESIGN PATTERNS INTER-VLAN ROUTING

ROUTER-ON-A-STICK:
┌─────────┐     Trunk     ┌─────────┐
│ Switch  │◄─────────────►│ Router  │
│ VLANs:  │ 802.1Q tagged │ Sub-int │
│ 10,20,30│               │ per VLAN│
└─────────┘               └─────────┘

PROS: Cost-effective, single connection
CONS: Bandwidth bottleneck, single point failure

LAYER 3 SWITCH (MIKROTIK METHOD):
┌─────────────────────────────┐
│     Layer 3 Switch          │
│ ┌─────┬─────┬─────┬─────┐   │
│ │VLAN │VLAN │VLAN │VLAN │   │
│ │ 10  │ 20  │ 30  │ 40  │   │
│ │SVI  │SVI  │SVI  │SVI  │   │
│ └─────┴─────┴─────┴─────┘   │
└─────────────────────────────┘

PROS: Wire-speed routing, integrated switching
CONS: Higher cost, complexity

MIKROTIK = VLAN interfaces act as SVIs
```

---

## **DIAPOSITIVA 22: LABORATORIO 1 - Interfaces y Monitoreo**
```
🧪 LABORATORIO PRÁCTICO 1

CONFIGURACIÓN INTERFACES Y MONITOREO

OBJETIVOS TEÓRICOS:
• Aplicar conceptos auto-negotiation
• Verificar performance metrics reales  
• Implementar monitoring sistemático
• Correlacionar teoría con behavior real

OBJETIVOS PRÁCTICOS:
• Configurar parámetros físicos ethernet
• Implementar monitoreo traffic patterns
• Optimizar wireless configuration
• Measure performance baselines

TOPOLOGÍA:
[Laptop-1] ───[ether2]
[Laptop-2] ───[ether3] RB951-2HnD [ether1]───[Internet]
[Laptop-3] ───[ether4]           [wlan1]
[Laptop-4] ───[ether5]

MÉTRICAS A MEDIR:
• Link negotiation results
• Throughput real vs theoretical  
• Error rates y retransmissions
• Wireless performance characteristics
```

---

## **DIAPOSITIVA 23: LABORATORIO 2 - Bridge Implementation**
```
🧪 LABORATORIO PRÁCTICO 2

BRIDGE Y TRANSPARENT SWITCHING

OBJETIVOS TEÓRICOS:
• Implementar transparent bridging algorithm
• Verificar MAC address learning process
• Aplicar STP/RSTP configuration
• Observar convergence behavior

BRIDGE CONFIGURATION:
• Bridge: bridge-lan (RSTP protocol)
• Ports: ether2-5 como edge ports
• MAC table: Automatic learning habilitado
• STP: Root bridge priority configuration

TESTING SCENARIOS:
1. MAC learning verification
2. Broadcast flood behavior
3. STP loop creation/resolution
4. Performance impact analysis

OBSERVACIONES ESPERADAS:
• Bridge table population automática
• STP convergence < 2 seconds (RSTP)
• Hardware offload operation
• Zero packet loss normal operation
```

---

## **DIAPOSITIVA 24: LABORATORIO 3 - VLANs Advanced**
```
🧪 LABORATORIO PRÁCTICO 3

VLAN IMPLEMENTATION Y SEGMENTATION

OBJETIVOS TEÓRICOS:
• Implementar IEEE 802.1Q standard
• Verificar frame tagging process
• Aplicar VLAN design principles  
• Validate security isolation

VLAN DESIGN:
┌─────────────────────────────────────────┐
│ VLAN 10: Admin (192.168.10.0/24)       │
│ ├─ ether2 (access port)               │
│ ├─ PVID: 10                           │
│ └─ Management functions               │
├─────────────────────────────────────────┤
│ VLAN 20: Users (192.168.20.0/24)       │
│ ├─ ether3, ether4 (access ports)      │
│ ├─ PVID: 20                           │
│ └─ General user traffic               │
├─────────────────────────────────────────┤
│ VLAN 30: Guest (192.168.30.0/24)       │
│ ├─ ether5, wlan1 (access ports)       │
│ ├─ PVID: 30                           │
│ └─ Restricted guest access            │
└─────────────────────────────────────────┘

TRUNK PORT: ether1 (tagged VLANs 10,20,30)
```

---

## **DIAPOSITIVA 25: VLAN Configuration Methods**
```
⚙️ MIKROTIK VLAN IMPLEMENTATION

MÉTODO 1: INTERFACE VLAN (Legacy)
```bash
/interface vlan add name=vlan10 vlan-id=10 interface=bridge1
/interface vlan add name=vlan20 vlan-id=20 interface=bridge1
/ip address add address=192.168.10.1/24 interface=vlan10
```

MÉTODO 2: BRIDGE VLAN TABLE (Recomendado RouterOS v7)
```bash
/interface bridge set bridge1 vlan-filtering=yes

/interface bridge vlan add bridge=bridge1 tagged=bridge1,ether1 untagged=ether2 vlan-ids=10

/interface bridge port set [find interface=ether2] pvid=10 frame-types=admit-only-untagged-and-priority-tagged
```

VENTAJAS BRIDGE VLAN TABLE:
• Configuración centralizada
• Mejor hardware offload  
• Troubleshooting simplificado
• Performance optimizado

💡 RouterOS v7: Bridge VLAN table = método preferido
```

---

## **DIAPOSITIVA 26: Frame Types Deep Dive**
```
📦 VLAN FRAME TYPES CONFIGURATION

ADMIT-ONLY-UNTAGGED-AND-PRIORITY-TAGGED:
• Acepta: Frames sin VLAN tag + Priority frames (VLAN 0)
• Rechaza: Frames con VLAN tag ≠ 0
• Uso típico: ACCESS PORTS
• Dispositivos: PCs, impresoras, servers

ADMIT-ONLY-VLAN-TAGGED:  
• Acepta: Solo frames con VLAN tag válido
• Rechaza: Frames untagged
• Uso típico: TRUNK PORTS
• Dispositivos: Switches, routers VLAN-aware

ADMIT-ALL:
• Acepta: Tagged + untagged frames
• Uso: HYBRID PORTS (advanced scenarios)
• Debugging y configurations especiales

SECURITY IMPLICATION:
Frame-types = primera línea defense contra VLAN hopping
Configuración incorrecta = security vulnerability
```

---

## **DIAPOSITIVA 27: Inter-VLAN Security Policies**
```
🔒 FIREWALL INTER-VLAN

BUSINESS LOGIC → SECURITY POLICIES:

ADMIN VLAN (10):
✅ Full access to all VLANs
✅ Internet access unrestricted  
✅ Management protocols allowed
✅ Server access privileged

USER VLAN (20):
✅ Internet access allowed
✅ Specific servers access  
❌ Admin VLAN blocked
❌ Management protocols blocked

GUEST VLAN (30):
✅ Internet access only
❌ All internal VLANs blocked
❌ Inter-guest isolation
❌ Management access blocked

FIREWALL IMPLEMENTATION:
```bash
# Accept admin VLAN full access
/ip firewall filter add chain=forward action=accept src-address=192.168.10.0/24

# Block guest to internal
/ip firewall filter add chain=forward action=drop src-address=192.168.30.0/24 dst-address=192.168.0.0/16

# Allow established/related
/ip firewall filter add chain=forward action=accept connection-state=established,related
```

---

## **DIAPOSITIVA 28: Troubleshooting Methodology**
```
🔧 SYSTEMATIC NETWORK TROUBLESHOOTING

OSI-BASED APPROACH:

LAYER 1 DIAGNOSTICS:
├─ Physical connectivity verification
├─ Cable continuity testing
├─ Link status monitoring
└─ Auto-negotiation results

LAYER 2 DIAGNOSTICS:
├─ Bridge MAC table analysis
├─ VLAN membership verification
├─ STP topology validation  
└─ Frame type configuration

LAYER 3 DIAGNOSTICS:
├─ IP address configuration
├─ Routing table analysis
├─ ARP table verification
└─ Gateway connectivity

MIKROTIK DIAGNOSTIC TOOLS:
/interface monitor-traffic    → Real-time traffic analysis
/tool sniffer                → Packet capture y analysis  
/tool bandwidth-test         → Performance measurement
/interface bridge host print → MAC learning verification
```

---

## **DIAPOSITIVA 29: Common Problem Patterns**
```
🚨 TROUBLESHOOTING PATTERNS

SÍNTOMA: "No conectividad"
ROOT CAUSES ANALYSIS:
┌─ Layer 1: Cable, interface down, speed/duplex mismatch
├─ Layer 2: VLAN configuration, STP blocking, MAC issues
├─ Layer 3: IP misconfig, routing problem, firewall  
└─ Layer 4+: Service down, authentication, DNS

DIAGNOSTIC WORKFLOW:
1. Verify interface UP status
2. Check VLAN membership
3. Validate IP configuration
4. Test Layer 3 connectivity
5. Verify application services

SÍNTOMA: "Intermittent connectivity"
TYPICAL CAUSES:
├─ Physical: Cable degradation, duplex mismatch
├─ Layer 2: STP topology changes, network loops
├─ Layer 3: Route flapping, load balancing
└─ Traffic: Congestion, buffer overflows

SÍNTOMA: "Performance degraded"  
ANALYSIS POINTS:
├─ Interface utilization rates
├─ Hardware vs software switching
├─ Buffer utilization patterns
└─ QoS policy effectiveness
```

---

## **DIAPOSITIVA 30: Performance Optimization**
```
⚡ NETWORK PERFORMANCE TUNING

HARDWARE OPTIMIZATION:
┌─ Switch chip offload verification
├─ Buffer management tuning  
├─ Flow control configuration
└─ Interrupt processing optimization

SOFTWARE OPTIMIZATION:  
┌─ Bridge configuration efficiency
├─ VLAN filtering optimization
├─ STP convergence tuning
└─ CPU utilization monitoring

MIKROTIK SPECIFIC:
```bash
# Verify hardware offload status
/interface bridge port print where hw-offload=yes

# Monitor performance metrics  
/interface monitor-traffic interface=bridge1

# CPU utilization analysis
/system resource cpu print

# Bridge statistics monitoring
/interface bridge print stats
```

PERFORMANCE TARGETS:
• CPU utilization < 70% sustainable
• Interface utilization < 80% peak
• Packet loss = 0% normal operation
• Latency < 10ms intra-VLAN
```

---

## **DIAPOSITIVA 31: Design Best Practices**
```
🏗️ NETWORK DESIGN PRINCIPLES

HIERARCHICAL DESIGN:
```
         CORE LAYER
    (High-speed switching)
              │
       DISTRIBUTION LAYER  
    (Policy enforcement)
              │
        ACCESS LAYER
    (End-device connectivity)
```

VLAN DESIGN STRATEGY:
┌─────────────┬─────────────────┬─────────────┐
│ VLAN Range  │ Purpose         │ Sizing      │
├─────────────┼─────────────────┼─────────────┤
│ 1-99        │ Management      │ /29, /28    │
│ 100-199     │ Users           │ /24, /23    │
│ 200-299     │ Servers         │ /25, /26    │
│ 300-399     │ DMZ             │ /26, /27    │
│ 400-499     │ Voice           │ /24         │
│ 500-599     │ Wireless        │ /23, /24    │
│ 600-699     │ Guest           │ /24         │
│ 700-799     │ IoT             │ /25, /26    │
└─────────────┴─────────────────┴─────────────┘

SECURITY BY DESIGN:
• Default deny policies
• Micro-segmentation approach
• Defense in depth layers
• Zero trust principles
```

---

## **DIAPOSITIVA 32: Advanced Monitoring**
```
📊 NETWORK OBSERVABILITY

MONITORING PILLARS:

1. METRICS (Quantitative):
├─ Interface utilization (%)
├─ Packet rates (PPS)
├─ Error rates y drops
├─ Latency measurements
└─ Resource utilization

2. LOGS (Event data):  
├─ Interface state changes
├─ STP topology changes  
├─ Authentication events
└─ Configuration changes

3. TRACES (Packet flows):
├─ End-to-end packet flow
├─ Protocol analysis
├─ Performance bottlenecks
└─ Security incident analysis

MIKROTIK MONITORING TOOLS:
/tool graphing     → Historical performance data
/system logging    → Event correlation
/tool sniffer      → Deep packet inspection
/tool netwatch     → Proactive monitoring
```

---

## **DIAPOSITIVA 33: Homework Project**
```
🏠 PROYECTO INDIVIDUAL AVANZADO

GNS3 ENTERPRISE NETWORK SIMULATION

REQUERIMIENTOS TÉCNICOS:
• 1 CHR como core Layer 3 switch
• 5 VLANs con different security policies
• 2 access switches (emulación con CHR)
• PC virtual en cada VLAN
• Inter-VLAN routing con restrictions
• Redundant links con STP

VLAN DESIGN DIFERENTE A LABORATORIO:
• VLAN 100: Executive (high security)
• VLAN 200: Engineering (medium security)  
• VLAN 300: Sales (standard security)
• VLAN 400: Guest (restricted access)
• VLAN 999: Management (admin only)

ADVANCED FEATURES:
• DHCP snooping implementation
• Port security configuration
• Traffic shaping por VLAN
• Monitoring y alerting setup

ANÁLISIS REQUERIDO:
• Packet capture y 802.1Q analysis
• Performance testing inter-VLAN
• Security policy validation
• Troubleshooting scenario documentation
```

---

## **DIAPOSITIVA 34: Deliverables y Timeline**
```
📋 ENTREGABLES PROJECT

DOCUMENTACIÓN TÉCNICA:
1. Network design document
   ├─ Topology diagrams
   ├─ VLAN assignment matrix
   ├─ IP addressing scheme
   └─ Security policy definition

2. Implementation guide
   ├─ Step-by-step configuration
   ├─ Verification procedures
   ├─ Rollback procedures  
   └─ Troubleshooting guide

3. Analysis report
   ├─ Performance test results
   ├─ Security validation tests
   ├─ Packet capture analysis
   └─ Lessons learned

ARCHIVOS TÉCNICOS:
• GNS3 project file (.gns3project)
• Configuration exports (.rsc files)
• Packet capture files (.pcap)
• Performance test logs

TIMELINE:
📅 Semana 1: Design y basic implementation
📅 Semana 2: Advanced features y testing
📅 Entrega: Final de módulo
```

---

## **DIAPOSITIVA 35: Evaluación Integral**
```
📊 ASSESSMENT CRITERIA

DISTRIBUCIÓN CALIFICACIONES:
• Laboratorio práctico switching (50%)
  ├─ Bridge configuration correcta
  ├─ VLAN implementation funcional
  ├─ Inter-VLAN routing operativo
  └─ Troubleshooting effectiveness

• Proyecto GNS3 documentado (35%)
  ├─ Design complexity y innovation
  ├─ Technical implementation quality
  ├─ Documentation completeness
  └─ Analysis depth

• Evaluación teórica conceptos (15%)
  ├─ STP/RSTP algorithm understanding
  ├─ VLAN theory comprehension
  ├─ Troubleshooting methodology
  └─ Best practices knowledge

COMPETENCY LEVELS:
🟢 Expert (90-100): Diseña redes complejas independientemente
🔵 Advanced (80-89): Implementa solutions con minimal guidance
🟡 Competent (70-79): Configura con supervisión occasional
🔴 Developing (<70): Requiere support substancial
```

---

## **DIAPOSITIVA 36: Preparación Módulo 3**
```
🔮 TRANSICIÓN A ROUTING AVANZADO

MÓDULO 3 PREVIEW: ROUTING ESTÁTICO Y DINÁMICO

FOUNDATION ESTABLISHED:
✅ Layer 2 switching mastery
✅ VLAN segmentation expertise
✅ Inter-VLAN routing basics
✅ Troubleshooting methodology

NEXT LEVEL TOPICS:
• Static routing optimization
• OSPF multi-área implementation  
• BGP fundamentals y policy
• Route redistribution strategies
• Policy-based routing
• Advanced route filtering

CONNECTION POINTS:
• Inter-VLAN routing → Static routing
• VLAN segmentation → Route summarization
• Performance optimization → Routing efficiency
• Security policies → Route filtering

PREPARATION ACTIVITIES:
📖 Review OSI Layer 3 concepts
🌐 Study IP subnetting advanced  
⚙️ Practice GNS3 multi-router topologies
🔧 Familiarize con routing table analysis

🎯 FROM SWITCHING EXPERT TO ROUTING ENGINEER
```

---

## **DIAPOSITIVA 37: Resources y Support**
```
📚 RECURSOS DE APRENDIZAJE CONTINUO

DOCUMENTACIÓN OFICIAL:
🌐 help.mikrotik.com/docs/spaces/ROS/pages/bridging
📖 wiki.mikrotik.com/wiki/Manual:Interface/Bridge  
📋 wiki.mikrotik.com/wiki/Manual:Interface/VLAN

LIBROS RECOMENDADOS:
📚 "Ethernet: The Definitive Guide" - Charles E. Spurgeon
📚 "Interconnections" - Radia Perlman (STP inventor!)
📚 "Network Warrior" - Gary A. Donahue

HERRAMIENTAS ADICIONALES:
🔍 Wireshark → Advanced packet analysis
📊 PRTG/LibreNMS → Enterprise monitoring
🖥️ EVE-NG → Alternative virtualization platform

CERTIFICACIONES PATH:
🎓 MTCNA → Network Associate (foundation)
🎓 MTCRE → Routing Engineer (next step)
🎓 MTCWE → Wireless Engineer (specialization)

COMMUNITY SUPPORT:
💬 MikroTik Discord communities
🌐 Reddit r/mikrotik subreddit
📧 [email profesor] para consultas específicas
⏰ Horario oficina: [horario disponible]
```

---

## **DIAPOSITIVA 38: Final Q&A**
```
❓ PREGUNTAS Y DISCUSIÓN TÉCNICA

TEMAS ABIERTOS PARA DISCUSIÓN:

🔧 CONFIGURACIÓN:
• ¿Problemas específicos durante laboratorios?
• ¿Dudas sobre Bridge vs VLAN methods?
• ¿Clarificaciones hardware offload?

📚 CONCEPTOS TEÓRICOS:
• ¿Aspectos STP/RSTP unclear?
• ¿Frame processing questions?
• ¿Inter-VLAN routing scenarios?

🏠 HOMEWORK PROJECT:
• ¿GNS3 topology complexity questions?
• ¿Analysis requirements clarification?
• ¿Timeline y deliverables questions?

🎯 CARRERA DEVELOPMENT:
• ¿Certification pathway guidance?
• ¿Industry applications discussion?
• ¿Advanced topics preview?

📧 CONTACTO POST-CLASE:
• Email: [direccion email]
• Horario oficina: [dias y horarios]
• Forum curso: [enlace si disponible]

¡Excelente trabajo en este módulo fundamental! 🎉
PRÓXIMA CLASE: Módulo 3 - Routing Adventures Begin!
```

---

## **Notas para el Docente - Versión Actualizada**

### **Timing Sugerido por Sección:**
- **Introducción y objetivos (1-3):** 8 minutos
- **Fundamentos teóricos profundos (4-11):** 50 minutos
- **STP/RSTP theory deep dive (12-15):** 25 minutos
- **VLAN theory comprehensive (16-21):** 35 minutos
- **Laboratorios integration (22-24):** 20 minutos
- **Configuration methods (25-27):** 20 minutos
- **Troubleshooting y optimization (28-32):** 30 minutos
- **Project y evaluation (33-38):** 15 minutos

**Total: ~3.5 horas presentation + 4.5 horas lab time = 8 hours module**

### **Puntos Críticos para Enfatizar:**

1. **Teoría como Foundation:** Entender WHY antes de HOW
2. **Evolution Path:** Hub → Switch → VLAN switching progression
3. **Performance Impact:** Hardware vs software switching implications
4. **Security by Design:** VLAN isolation como security boundary
5. **Systematic Troubleshooting:** OSI-based diagnostic approach
6. **Best Practices:** Design principles for scalable networks

### **Demostraciones en Vivo Críticas:**
- **Bridge MAC learning** real-time observation (8 min)
- **STP convergence** con loop creation/removal (5 min)
- **VLAN frame tagging** packet capture analysis (10 min)
- **Inter-VLAN routing** traffic flow demonstration (7 min)
- **Performance comparison** hardware vs software switching (5 min)

### **Material Visual Esencial:**
- **Algorithm flowcharts** para STP y bridge learning
- **Frame structure diagrams** para 802.1Q
- **Network topology diagrams** para cada laboratorio
- **Performance graphs** comparison charts
- **Troubleshooting decision trees**

### **Assessment Integration:**
- **Formative assessment:** Durante demostraciones (Q&A)
- **Peer learning:** Group discussions on complex concepts
- **Practical validation:** Immediate lab application
- **Comprehensive evaluation:** Theory + practice integration

Esta presentación actualizada proporciona una educación integral que combina fundamentos teóricos sólidos con implementación práctica efectiva, preparando estudiantes para ser verdaderos ingenieros de red con understanding profundo de switching y VLANs.

