# Notas Completas del Instructor - Módulo 2
## Configuración de Interfaces y Switching

---

## **INFORMACIÓN GENERAL DEL MÓDULO**

**Duración Total:** 8 horas académicas (2 semanas)  
**Modalidad:** 3.5 horas presentación teórica + 4.5 horas laboratorios prácticos  
**Nivel:** Intermedio (post-básico de RouterOS)  
**Equipamiento:** RB951-2HnD por grupo + laptops + GNS3 para homework

---

## **OBJETIVOS PEDAGÓGICOS ESPECÍFICOS**

### **Objetivos de Aprendizaje por Taxonomía de Bloom:**

**RECORDAR (Knowledge):**
- Definir conceptos fundamentales: bridge, VLAN, STP, 802.1Q
- Listar tipos de interfaces en RouterOS
- Enumerar estados de puerto STP

**COMPRENDER (Comprehension):**
- Explicar algoritmo transparent bridging
- Describir proceso auto-negotiation Ethernet
- Comparar hardware vs software switching

**APLICAR (Application):**
- Configurar bridge con multiple puertos
- Implementar VLANs usando bridge VLAN table
- Configurar inter-VLAN routing

**ANALIZAR (Analysis):**
- Diagnosticar problemas connectivity usando metodología OSI
- Evaluar performance implications de configuraciones
- Identificar security vulnerabilities en VLAN designs

**SINTETIZAR (Synthesis):**
- Diseñar red empresarial con múltiples VLANs
- Crear políticas de seguridad inter-VLAN
- Desarrollar troubleshooting methodology

**EVALUAR (Evaluation):**
- Justificar decisiones de design architecture
- Kritizar configuraciones existentes
- Recomendar optimizations de performance

---

## **ESTRUCTURA DETALLADA DE LA CLASE**

### **SESIÓN 1: Fundamentos Teóricos (2 horas)**

#### **Apertura (10 minutos) - Diapositivas 1-3**

**Objetivos de la Sesión:**
- Contextualizar switching en arquitectura de red moderna
- Establecer expectations de aprendizaje
- Revisar prerequisites del Módulo 1

**Script del Instructor:**
> "Hoy vamos a transformar su understanding de networking. Van a dejar de ser usuarios de routers para convertirse en diseñadores de redes. El switching no es solo 'conectar cables' - es el foundation de toda la infraestructura de red moderna."

**Preguntas de Warm-up:**
1. "¿Qué diferencia hay entre un hub y un switch?"
2. "¿Por qué necesitamos VLANs si ya tenemos subnets?"
3. "¿Cuál fue el último problema de red que troubleshootearon?"

**Notas Pedagógicas:**
- Usar analogías: "Switch es como central telefónica inteligente vs hub como megáfono"
- Establecer connection con experiencias previas de estudiantes
- Crear expectation de "engineering thinking" vs "configuration memorization"

---

#### **Evolución del Networking (15 minutos) - Diapositivas 4-6**

**Puntos Críticos para Enfatizar:**

**1. Progresión Histórica (5 min):**
```
HUB ERA → Shared collision domain, half-duplex
    ↓
SWITCH ERA → Separate collision domains, full-duplex  
    ↓
VLAN ERA → Separate broadcast domains, logical segmentation
```

**Demostración Visual:** 
- Mostrar colisiones en hub usando simulación simple
- Contrastar con switching inteligente
- Enfatizar evolution = solving real problems

**2. Modelo OSI Aplicado (5 min):**
- Layer 1: Physical connectivity y link detection
- Layer 2: MAC address learning y forwarding
- Layer 2.5: VLAN processing y classification

**Nota Importante:** 
> "No memorizem el modelo OSI - úsenlo como troubleshooting framework. Cada layer resuelve problemas específicos."

**3. Buffer Management (5 min):**
- Explicar WHY switches necesitan buffers
- Analogía: "Buffer como estacionamiento temporal"
- Flow control como "semáforo inteligente"

**Preguntas para Verificar Comprensión:**
- "¿Qué problema resuelve el buffer management?"
- "¿Por qué full-duplex elimina colisiones?"
- "¿Cuándo necesitaríamos pause frames?"

**Errores Comunes de Estudiantes:**
❌ "Switch = router simple"  
✅ "Switch = intelligent Layer 2 forwarding device"

❌ "VLANs = physical separation"  
✅ "VLANs = logical broadcast domain separation"

---

#### **Ethernet Theory Profundo (20 minutos) - Diapositivas 7-8**

**1. CSMA/CD Algorithm (8 min):**

**Demostración Step-by-Step:**
```
1. CARRIER SENSE:
   - "Antes de hablar, escucho si alguien más está hablando"
   - Mostrar como device detecta carrier signal
   
2. MULTIPLE ACCESS:
   - "Todos pueden acceder al medio, pero coordinadamente"
   - Explicar shared medium concept
   
3. COLLISION DETECTION:
   - "Si dos hablan simultáneamente, ambos se callan"
   - Demostrar collision detection mechanism
   
4. EXPONENTIAL BACKOFF:
   - "Esperan tiempo random antes de retry"
   - Explicar como previene repeated collisions
```

**Analogía Efectiva:** 
> "CSMA/CD es como conversación educada en reunión - escuchas antes de hablar, si interrumpes a alguien, ambos se callan y esperan."

**2. Auto-negotiation Process (7 min):**

**Secuencia Técnica:**
```
PASO 1: Link Pulse Detection
- Normal Link Pulses (NLP) cada 16ms
- Detecta presencia del partner

PASO 2: Fast Link Pulse (FLP) Exchange  
- 33ms burst con capability information
- Transmite supported speeds/duplex modes

PASO 3: Common Denominator Selection
- Ambos devices comparan capabilities
- Seleccionan highest common capability

PASO 4: Configuration Application
- Ambos configuran speed/duplex acordado
- Link established con parameters óptimos
```

**Demo Práctica:** Si hay switches managed disponibles, mostrar negotiation results en real-time.

**3. Performance Real vs Teórico (5 min):**

**Cálculos Importantes:**
```
ETHERNET OVERHEAD:
Preamble: 8 bytes
Headers: 14 bytes (DA+SA+Type)
FCS: 4 bytes
IFG: 12 bytes (96 bits)
Total per frame: 38 bytes overhead

REAL THROUGHPUT:
1 Gbps Ethernet con 64-byte frames:
- Theoretical: 1,000,000,000 bps
- Real: ~947 Mbps (94.7%)
- PPS: 1,488,095 frames/second
```

**Punto Clave:** 
> "Wire-speed switching significa mantener esta performance teórica máxima sin degradación."

---

#### **Bridge Theory Comprehensive (25 minutos) - Diapositivas 9-11**

**1. Transparent Bridging Fundamentals (10 min):**

**Los 5 Principios (explicar c/u con ejemplo):**

```
1. TRANSPARENCY:
   - Hosts no saben que bridge existe
   - Bridge invisible a end devices
   - No configuration required en hosts

2. LEARNING:
   - Bridge observa source MAC de cada frame
   - Builds tabla MAC automáticamente
   - Associates MAC with ingress port

3. FORWARDING:
   - Lookup destination MAC en tabla
   - Forward solo al puerto correcto
   - Eliminates unnecessary traffic

4. FLOODING:
   - Si destino unknown → flood all ports
   - Excluye puerto de origen
   - Eventually learns via return traffic

5. AGING:
   - Remove unused MAC entries
   - Default: 300 seconds (5 minutos)
   - Prevents stale entries
```

**Demostración Algoritmo (usar whiteboard):**
```
ESCENARIO: 3 ports bridge, Host A (MAC: aaaa) en port 1

Frame 1: A→B (bbbb)
├─ Learning: aaaa → port 1
├─ Forwarding: bbbb unknown → flood ports 2,3
└─ Result: B learns about A

Frame 2: B→A (desde port 2)  
├─ Learning: bbbb → port 2
├─ Forwarding: aaaa known → send only port 1
└─ Result: Efficient forwarding established
```

**2. Performance Considerations (8 min):**

**Store-and-Forward Processing:**
```
ADVANTAGES:
✓ Complete error checking (FCS validation)
✓ QoS analysis possible
✓ Full frame available for processing
✓ No propagation of corrupt frames

DISADVANTAGES:
✗ Higher latency (complete frame receive)
✗ Buffer memory requirements
✗ Processing overhead
```

**Comparación con Cut-Through:**
> "MikroTik usa store-and-forward para reliability. Cut-through sacrifica error checking por latency mínima."

**MAC Table Sizing:**
- Typical: 8192-16384 entries per bridge
- Hash-based lookup algorithms
- Overflow handling graceful

**3. Learning Rate y Convergence (7 min):**

**Factores que Afectan Learning:**
- Network topology complexity
- Traffic patterns (unicast vs broadcast)
- Aging time configuration
- Table size limits

**Convergence Time Analysis:**
```
TYPICAL SCENARIOS:
Small office (50 devices): 30-60 seconds full convergence
Medium enterprise (500 devices): 2-5 minutes
Large network (5000+ devices): 10-15 minutes

OPTIMIZATION:
- Static MAC entries for servers
- Appropriate aging time tuning
- Bridge priority configuration
```

**Preguntas de Comprensión:**
1. "¿Por qué flooding es necesario inicialmente?"
2. "¿Qué pasa si tabla MAC se llena?"
3. "¿Cuándo usarían static MAC entries?"

---

#### **Hardware vs Software Switching (20 minutos) - Diapositivas 10-11**

**1. Arquitectural Differences (10 min):**

**Software Switching Path:**
```
PACKET INGRESS:
NIC → Device Driver → Kernel Network Stack → Bridge Module → Routing Decision → Egress Queue → Device Driver → NIC

CHARACTERISTICS:
- CPU-intensive processing
- Context switches overhead  
- Memory copy operations
- Interrupt processing
- Variable latency (depends on CPU load)
- Typical: 50-500 microseconds latency
```

**Hardware Switching Path:**
```
PACKET INGRESS:
PHY → MAC → Switch ASIC Pipeline → MAC → PHY

ASIC PIPELINE STAGES:
├─ Parse: Extract headers, classify packet
├─ Lookup: MAC table search (CAM/TCAM)
├─ Modify: Header modifications if needed
├─ Queue: Buffer management, QoS
└─ Schedule: Egress port scheduling

CHARACTERISTICS:
- Dedicated silicon processing
- Parallel port processing
- Wire-speed performance
- Predictable latency: 1-10 microseconds
- No CPU overhead
```

**Analogía Útil:**
> "Software switching = persona inteligente haciendo múltiples tareas. Hardware switching = fábrica automatizada con workers especializados."

**2. Performance Metrics Deep Dive (10 min):**

**Latency Analysis:**
```
SOFTWARE SWITCHING COMPONENTS:
├─ Interrupt handling: 10-50 μs
├─ Context switching: 5-20 μs  
├─ Memory operations: 10-100 μs
├─ Processing logic: 20-200 μs
└─ Total: 45-370 μs (variable)

HARDWARE SWITCHING:
├─ Packet parsing: 0.1-0.5 μs
├─ Table lookup: 0.5-2 μs
├─ Buffer management: 0.5-2 μs
└─ Total: 1.1-4.5 μs (consistent)
```

**Throughput Calculations:**
```
WIRE-SPEED REQUIREMENTS:
1 Gbps port = 1,488,095 PPS (64-byte frames)
24-port Gigabit switch = 35,714,280 PPS aggregate
Software: CPU limiting factor (~1-10 Mpps typical)
Hardware: ASIC designed for wire-speed
```

**MikroTik Specific Implementation:**
- RouterOS v7: Automatic hardware offload
- Fallback to software cuando hardware no supports feature
- Transparent operation (same configuration API)
- Bridge hw-offload status visible in interface monitoring

---

### **BREAK (15 minutos)**

---

#### **STP/RSTP Theory Profundo (30 minutos) - Diapositivas 12-15**

**1. El Problema Fundamental (8 min):**

**Demostración del Loop Problem:**
```
SETUP: Draw simple triangle topology on whiteboard
Switch A ←→ Switch B  
    ↘    ↙
   Switch C

SIN STP - Broadcast Storm Scenario:
1. Host sends broadcast frame  
2. Switch A floods to B and C
3. Switch B floods to A and C  
4. Switch C floods to A and B
5. INFINITE LOOP: Exponential frame multiplication
```

**Síntomas del Broadcast Storm:**
- Link utilization → 100% instantly
- MAC table instability (flapping)
- Network becomes completely unusable
- Only solution: physical disconnection

**Analogía Efectiva:**
> "Broadcast storm es como rumor en oficina - cada persona lo cuenta a todos los demás, y el rumor crece exponencialmente hasta que nadie puede trabajar."

**2. STP Algorithm Step-by-Step (15 min):**

**FASE 1: Root Bridge Election (5 min)**

```
BRIDGE ID STRUCTURE:
┌─────────────────┬─────────────────────────┐
│ Priority        │ MAC Address             │  
│ 2 bytes         │ 6 bytes                 │
│ Default: 0x8000 │ Unique per device       │
└─────────────────┴─────────────────────────┘
         ↓
   8-byte unique identifier

ELECTION PROCESS:
1. Each bridge assumes it's root initially
2. Sends BPDUs claiming to be root
3. Compares received Bridge IDs:
   - If received < own → accept new root
   - If received > own → ignore BPDU
4. Convergence when all agree on lowest Bridge ID
```

**Demostración Práctica:** 
- Usar 3 estudiantes como bridges con different MAC addresses
- Simulate BPDU exchange process
- Show convergence to lowest Bridge ID

**FASE 2: Root Port Selection (5 min)**

```
PATH COST CALCULATION:
┌─────────────┬─────────────┐
│ Link Speed  │ Path Cost   │
├─────────────┼─────────────┤
│ 10 Mbps     │ 100         │
│ 100 Mbps    │ 19          │  
│ 1 Gbps      │ 4           │
│ 10 Gbps     │ 2           │
└─────────────┴─────────────┘

SELECTION CRITERIA (priority order):
1. Lowest cumulative path cost to root
2. Lowest sender Bridge ID (tie-breaker)
3. Lowest sender Port Priority (tie-breaker)  
4. Lowest sender Port ID (final tie-breaker)
```

**FASE 3: Designated Port Selection (5 min)**

```
PER-SEGMENT ANALYSIS:
For each physical link:
├─ Exactly one designated port (forwarding)
├─ Selection based on:
│  ├─ Bridge with lower path cost to root
│  ├─ Lower Bridge ID (if equal cost)
│  └─ Lower Port ID (final tie-breaker)
└─ Other end becomes non-designated (blocking)
```

**Whiteboard Exercise:** 
- Draw complex 4-switch topology
- Walk through algorithm step-by-step
- Students participate in calculations

**3. STP Port States y Timers (7 min):**

**State Transition Diagram:**
```
BLOCKING (20s Max Age)
    ↓
LISTENING (15s Forward Delay)  
    ↓
LEARNING (15s Forward Delay)
    ↓
FORWARDING
```

**Detailed State Functions:**
```
BLOCKING STATE:
├─ No data frame forwarding
├─ Listens to BPDUs only
├─ Prevents loops during convergence
└─ Duration: Until superior BPDU stops arriving

LISTENING STATE:
├─ No data frame forwarding  
├─ Processes and sends BPDUs
├─ Determines final port role
└─ Duration: Forward Delay (15s default)

LEARNING STATE:
├─ No data frame forwarding
├─ Learns MAC addresses (populates bridge table)
├─ Processes BPDUs normally
└─ Duration: Forward Delay (15s default)

FORWARDING STATE:
├─ Normal data forwarding
├─ MAC learning continues
├─ BPDU processing active
└─ Stable operational state
```

**Timer Configuration:**
- Hello Time: 2s (BPDU transmission frequency)
- Max Age: 20s (BPDU timeout)
- Forward Delay: 15s (Listening + Learning duration)
- **Total Convergence: 30-50 seconds** (Major problem!)

---

#### **RSTP Improvements (15 min) - Diapositiva 15**

**1. Fundamental Enhancements (8 min):**

**Convergence Comparison:**
```
STP (802.1D):
├─ Timer-based convergence
├─ 30-50 second recovery
├─ Passive waiting approach
└─ 5 port states

RSTP (802.1w):  
├─ Handshake-based convergence
├─ <2 second recovery
├─ Active negotiation approach
└─ 3 port states (simplified)
```

**RSTP Port States:**
```
DISCARDING: Combines Blocking + Listening + Disabled
LEARNING: Same as STP Learning
FORWARDING: Same as STP Forwarding
```

**2. Proposal/Agreement Mechanism (7 min):**

**Handshake Process:**
```
SCENARIO: New link between Switch A and Switch B

STEP 1: Switch A sends Proposal BPDU
├─ "I want to be designated on this port"
├─ Contains current bridge information
└─ Requests immediate response

STEP 2: Switch B receives Proposal
├─ Puts all non-edge ports in Discarding
├─ Ensures no loops can form
└─ Prepares Agreement response

STEP 3: Switch B sends Agreement BPDU
├─ "I accept your proposal"  
├─ Confirms loop-free topology
└─ Authorizes immediate forwarding

STEP 4: Immediate Convergence
├─ Switch A transitions port to Forwarding
├─ No timer-based waiting
└─ <2 second total process
```

**3. Edge Port Concept (Critical Understanding):**

```
EDGE PORT DEFINITION:
├─ Port connected to end host (not switch)
├─ Cannot create bridging loop
├─ Safe for immediate forwarding
└─ Critical for fast convergence

EDGE PORT BEHAVIOR:
├─ Immediate transition to Forwarding
├─ No proposal/agreement needed
├─ If receives BPDU → loses edge status
└─ Automatically reverts to normal STP

CONFIGURATION IMPORTANCE:
✓ Configure edge on host-facing ports
✗ Never configure edge on switch-to-switch links
⚠️ Misconfiguration can cause temporary loops
```

**Preguntas de Assessment:**
1. "¿Por qué RSTP es más rápido que STP?"
2. "¿Cuándo configurarían un puerto como edge?"
3. "¿Qué pasa si configuran edge port incorrectamente?"

---

### **SESIÓN 2: VLAN Theory y Implementation (2 horas)**

#### **VLAN Fundamentals Deep Dive (25 minutos) - Diapositivas 16-19**

**1. Segmentación Conceptual (10 min):**

**Evolution of Network Segmentation:**
```
PHYSICAL SEGMENTATION (Traditional):
├─ Separate switches per department
├─ Physical cable runs  
├─ Hardware-based isolation
└─ High cost, inflexibility

LOGICAL SEGMENTATION (VLANs):
├─ Single switch, multiple VLANs
├─ Software-based isolation
├─ Flexible reassignment
└─ Cost-effective scaling
```

**Broadcast Domain Impact Analysis:**
```
WITHOUT VLANs:
Single switch with 100 ports = 1 broadcast domain
├─ Every broadcast affects all 100 devices
├─ ARP requests flood entire network
├─ DHCP discover floods everywhere
└─ Network chatter affects everyone

WITH VLANs (5 VLANs × 20 ports):
├─ 5 separate broadcast domains
├─ 80% reduction in broadcast traffic  
├─ Contained ARP/DHCP scope
└─ Improved performance and security
```

**Security Benefits Quantified:**
- Attack surface reduced by VLAN isolation
- Lateral movement prevention
- Compliance boundary enforcement
- Audit trail separation

**2. IEEE 802.1Q Standard Detailed (15 min):**

**Frame Structure Analysis:**
```
ORIGINAL ETHERNET FRAME (No VLAN):
┌─────────┬─────────┬─────────┬──────────┬─────┐
│ Dest MAC│ Src MAC │Type/Len │   Data   │ FCS │
│ 6 bytes │ 6 bytes │ 2 bytes │ Variable │ 4B  │
└─────────┴─────────┴─────────┴──────────┴─────┘
Total: 14 bytes header + data + 4 bytes FCS

802.1Q TAGGED FRAME:
┌─────────┬─────────┬─────┬─────┬─────────┬──────────┬─────┐
│ Dest MAC│ Src MAC │ TPID│ TCI │Type/Len │   Data   │ FCS │
│ 6 bytes │ 6 bytes │0x8100│2 B │ 2 bytes │ Variable │ 4B  │
└─────────┴─────────┴─────┴─────┴─────────┴──────────┴─────┘
Total: 18 bytes header + data + 4 bytes FCS (+4 bytes overhead)
```

**TCI Field Breakdown:**
```
┌─────────────┬─────┬─────────────────────┐
│ Priority    │ CFI │      VLAN ID        │
│ 3 bits      │1 bit│     12 bits         │
│ (QoS 802.1p)│ DEI │    (1-4094)         │
└─────────────┴─────┴─────────────────────┘

PRIORITY FIELD (802.1p QoS):
000 (0) = Background (BK)
001 (1) = Best Effort (BE) - Default  
010 (2) = Excellent Effort (EE)
011 (3) = Critical Applications (CA)
100 (4) = Video (VI) < 100ms latency
101 (5) = Voice (VO) < 10ms latency  
110 (6) = Internetwork Control
111 (7) = Network Control - Highest

CFI/DEI (Drop Eligible Indicator):
├─ 0 = Frame may not be dropped (normal)
└─ 1 = Frame eligible for dropping (QoS)

VLAN ID RANGES:
├─ 0: Priority tagging without VLAN
├─ 1: Default VLAN (native, often unused)
├─ 2-4094: Normal VLAN range
└─ 4095: Reserved (implementation specific)
```

**Frame Processing Deep Dive:**
```
INGRESS PROCESSING:
Untagged Frame Arrives:
├─ Check port PVID (Port VLAN ID)
├─ Add internal VLAN tag = PVID
├─ Process frame with VLAN context
└─ Forward based on VLAN membership

Tagged Frame Arrives:
├─ Extract VLAN ID from 802.1Q header
├─ Validate against port's allowed VLANs
├─ If valid: process with VLAN context
└─ If invalid: discard frame

EGRESS PROCESSING:
Frame Ready for Transmission:
├─ Check destination port VLAN config
├─ If port is UNTAGGED for this VLAN:
│  ├─ Remove VLAN tag
│  └─ Send as normal Ethernet frame
├─ If port is TAGGED for this VLAN:
│  ├─ Keep/add 802.1Q header
│  └─ Send as tagged frame
└─ If port not member: discard
```

---

#### **VLAN Port Types Implementation (20 minutos) - Diapositivas 19-21**

**1. Access Port Deep Analysis (8 min):**

**Access Port Characteristics:**
```
DEFINITION:
├─ Port belongs to exactly ONE VLAN
├─ Connected device unaware of VLANs
├─ Switch handles all VLAN processing
└─ Simplicity for end devices

TYPICAL USE CASES:
├─ Desktop computers
├─ Printers and IoT devices  
├─ IP phones (data VLAN)
├─ Servers (specific VLAN membership)
└─ Any device not VLAN-aware
```

**Frame Processing Example:**
```
SCENARIO: PC connected to access port PVID=20

PC sends frame to Server:
├─ PC: Standard Ethernet frame (no VLAN tag)
├─ Switch ingress: Add VLAN 20 tag internally
├─ Switch processing: Frame belongs to VLAN 20
├─ Switch egress (to PC): Remove tag, send standard frame
└─ PC: Receives standard Ethernet frame

CONFIGURATION REQUIREMENTS:
├─ PVID: 20 (assigns VLAN to untagged traffic)
├─ Frame-types: admit-only-untagged-and-priority-tagged
├─ VLAN membership: Untagged member of VLAN 20
└─ Security: No access to other VLANs
```

**2. Trunk Port Deep Analysis (8 min):**

**Trunk Port Characteristics:**
```
DEFINITION:
├─ Port carries multiple VLANs simultaneously  
├─ Uses 802.1Q tagging for identification
├─ Communication between VLAN-aware devices
└─ Efficiency for inter-switch connections

TYPICAL USE CASES:
├─ Switch-to-switch connections
├─ Switch-to-router connections
├─ Server connections (VLAN-aware servers)
├─ Hypervisor connections (VM VLANs)
└─ Wireless controller connections
```

**Native VLAN Concept (Critical):**
```
NATIVE VLAN PURPOSE:
├─ Backward compatibility with non-VLAN switches
├─ One VLAN sent untagged on trunk
├─ Default: VLAN 1 (INSECURE!)
└─ Receiving switch maps untagged to native VLAN

SECURITY IMPLICATIONS:
❌ Native VLAN = production VLAN (vulnerable)
✅ Native VLAN = unused dummy VLAN (secure)

VLAN HOPPING ATTACK:
1. Attacker sends double-tagged frame
2. First tag = native VLAN (stripped)
3. Second tag = target VLAN (now untagged → native)
4. Attack succeeds if native = production VLAN

PREVENTION:
├─ Use unused VLAN as native (e.g., VLAN 999)
├─ Disable native VLAN entirely if possible
├─ Never use VLAN 1 as native
└─ Monitor native VLAN traffic
```

**3. Hybrid Port Applications (4 min):**

**Hybrid Port Use Cases:**
```
DEFINITION:
├─ Port with both tagged and untagged VLANs
├─ Complex configuration requirements
├─ Specific business use cases
└─ Advanced networking scenarios

PRACTICAL EXAMPLE:
IP Phone + PC Connection:
├─ VLAN 10: Untagged (PC data traffic)
├─ VLAN 100: Tagged (VoIP traffic)
├─ Phone acts as mini-switch
└─ Separates voice and data automatically

CONFIGURATION COMPLEXITY:
├─ Higher configuration overhead
├─ More troubleshooting complexity
├─ Security considerations increase
└─ Use only when necessary
```

---

#### **Inter-VLAN Routing Theory (25 minutos) - Diapositivas 20-21**

**1. Layer 2 vs Layer 3 Boundary (10 min):**

**The Fundamental Problem:**
```
VLAN ISOLATION:
├─ VLANs create separate broadcast domains
├─ Layer 2 switching operates within VLAN
├─ No natural communication between VLANs
└─ Need Layer 3 routing for inter-VLAN communication

LAYER 2 SCOPE (Switching):
├─ MAC address-based forwarding
├─ Same subnet/VLAN communication
├─ Broadcast domain limited
└─ No IP address modification

LAYER 3 SCOPE (Routing):  
├─ IP address-based forwarding
├─ Cross-subnet/VLAN communication
├─ Route table consultation
└─ IP header modification required
```

**Detailed Inter-VLAN Routing Process:**
```
SCENARIO: Host A (VLAN 10) → Host B (VLAN 20)

STEP 1: SOURCE HOST ANALYSIS
├─ Host A IP: 192.168.10.5/24
├─ Host B IP: 192.168.20.10/24  
├─ Host A determines: "Different subnet"
└─ Host A decision: "Send to default gateway"

STEP 2: LAYER 2 TO GATEWAY
├─ Host A sends to gateway MAC (192.168.10.1)
├─ Frame: [Host A MAC][Gateway MAC][IP: A→B]
├─ Switch forwards within VLAN 10
└─ Router receives frame on VLAN 10 interface

STEP 3: LAYER 3 ROUTING DECISION  
├─ Router extracts IP packet
├─ Destination: 192.168.20.10
├─ Route lookup: 192.168.20.0/24 → VLAN 20 interface
└─ Forward decision: Exit via VLAN 20 interface

STEP 4: ARP RESOLUTION (if needed)
├─ Router needs Host B MAC address
├─ ARP request in VLAN 20: "Who has 192.168.20.10?"
├─ Host B responds with MAC address
└─ Router builds new Layer 2 frame

STEP 5: LAYER 2 TO DESTINATION
├─ New frame: [Router VLAN20 MAC][Host B MAC][IP: A→B]
├─ TTL decremented in IP header
├─ Switch forwards within VLAN 20
└─ Host B receives frame
```

**2. Router-on-a-Stick Implementation (8 min):**

**Architecture Analysis:**
```
TOPOLOGY:
[Switch] ──── 802.1Q Trunk ──── [Router]
   │                              │
   ├─ VLAN 10 Access Ports       ├─ Subinterface 10
   ├─ VLAN 20 Access Ports       ├─ Subinterface 20
   └─ VLAN 30 Access Ports       └─ Subinterface 30

ADVANTAGES:
✓ Single physical connection to router
✓ Cost-effective for small networks
✓ Centralized routing policy
✓ Easy management

DISADVANTAGES:
✗ Single point of failure (trunk link)
✗ Bandwidth bottleneck (all inter-VLAN traffic)
✗ Hair-pinning latency (traffic goes to router and back)
✗ Scalability limitations
```

**Traffic Flow Analysis:**
```
INTRA-VLAN COMMUNICATION:
├─ PC1 (VLAN 10) → PC2 (VLAN 10)
├─ Traffic stays on switch (Layer 2)
├─ No router involvement
└─ Wire-speed performance

INTER-VLAN COMMUNICATION:
├─ PC1 (VLAN 10) → PC3 (VLAN 20)  
├─ Traffic: Switch → Router → Switch
├─ Router processes every inter-VLAN packet
└─ Performance limited by router and trunk
```

**3. Layer 3 Switch Implementation (7 min):**

**MikroTik Layer 3 Switch Approach:**
```
ARCHITECTURE:
┌─────────────────────────────┐
│    MikroTik Router/Switch   │
│                             │
│ ┌─────┬─────┬─────┬─────┐   │
│ │VLAN │VLAN │VLAN │VLAN │   │ ← VLAN Interfaces
│ │ 10  │ 20  │ 30  │ 40  │   │   (Act as SVIs)
│ │ SVI │ SVI │ SVI │ SVI │   │
│ └─────┴─────┴─────┴─────┘   │
│          │                  │
│ ┌───────────────────────┐   │
│ │    Bridge Layer       │   │ ← Layer 2 Switching
│ │  (Hardware Offload)   │   │
│ └───────────────────────┘   │
│          │                  │  
│ ┌───────────────────────┐   │
│ │   Physical Ports      │   │ ← Hardware Interfaces
│ │ ether1-5, wlan1       │   │
│ └───────────────────────┘   │
└─────────────────────────────┘

ADVANTAGES:
✓ Wire-speed switching AND routing
✓ No external router required
✓ Low latency (hardware acceleration)
✓ Integrated management
✓ Scalable performance

CONFIGURATION APPROACH:
├─ Bridge with VLAN filtering
├─ VLAN interfaces for each VLAN
├─ IP addresses on VLAN interfaces  
└─ Hardware offload automatic
```

**Performance Comparison:**
```
ROUTER-ON-A-STICK:
├─ Switching: Wire-speed (hardware)
├─ Inter-VLAN routing: Router CPU limited
├─ Typical: 100-500 Mbps inter-VLAN
└─ Latency: 1-10ms (depends on router)

LAYER 3 SWITCH:
├─ Switching: Wire-speed (hardware)  
├─ Inter-VLAN routing: Hardware accelerated
├─ Typical: Wire-speed inter-VLAN
└─ Latency: <1ms (hardware forwarding)
```

---

### **BREAK (15 minutos)**

---

### **SESIÓN 3: Laboratorios Prácticos (4.5 horas)**

#### **Laboratorio 1: Interfaces y Monitoreo (45 minutos)**

**Objetivos de Aprendizaje Específicos:**
- Aplicar conceptos de auto-negotiation en práctica
- Correlacionar theory performance con measurements reales
- Implementar systematic monitoring approach
- Troubleshoot interface issues metodológicamente

**Pre-lab Setup (5 min):**

**Instructor Checklist:**
- [ ] RB951-2HnD por grupo funcionando
- [ ] Laptops con Winbox updated version
- [ ] Cables Ethernet tested
- [ ] Switch aula operational
- [ ] Internet connectivity available

**Briefing para Estudiantes:**
> "Este laboratorio va a validar todo lo que acabamos de aprender sobre Ethernet theory. Van a ver auto-negotiation en acción, medir performance real, y implementar monitoring que van a usar en el mundo real."

**Sección 1: Interface Configuration (15 min)**

**Step-by-Step con Explicaciones:**

```
PASO 1: Interface Inventory
Objetivo: Entender hardware disponible

Instructor dice: "Primero vamos a hacer inventory de nuestro hardware"
├─ /interface print
├─ Identificar: ether1-5, wlan1, bridge (si existe del módulo anterior)
└─ Verificar status: enabled/disabled, running/not-running

PUNTOS DE ENSEÑANZA:
- "Running" = physical link detected
- "Enabled" = administratively up
- Interface types diferentes tienen capabilities diferentes
```

```
PASO 2: Physical Parameters Configuration  
Objetivo: Aplicar teoría auto-negotiation

Instructor demonstra:
/interface ethernet print detail
├─ Mostrar auto-negotiation results
├─ Explicar speed/duplex detected
└─ Correlacionar con teoría presentada

STUDENT ACTIVITY:
├─ Configurar ether2 name="lab-port1"
├─ Configurar comment descriptivo
├─ Verificar auto-negotiation results
└─ Document findings
```

**Troubleshooting Común:**
- **Problema:** "Interface no shows running"
  **Solución:** Verify cable, check laptop NIC status
- **Problema:** "Auto-negotiation fails"  
  **Solución:** Manual configuration, cable replacement

**Sección 2: Performance Monitoring (20 min)**

**Real-time Traffic Monitoring:**

```
DEMOSTRACIÓN INSTRUCTOR:
/interface monitor-traffic interface=ether1 duration=10

Mientras corre monitoring:
├─ Generar tráfico ping continuo
├─ Observar RX/TX counters en real-time  
├─ Explicar what numbers mean
└─ Correlacionar con teoría overhead
```

**Student Activity - Bandwidth Testing:**
```
OBJETIVO: Measure real performance vs theoretical

STEPS:
1. Setup: Laptop conectada a ether2
2. Baseline: /tool bandwidth-test address=8.8.8.8
3. Analysis: Compare results to theoretical 100Mbps
4. Variables: Test different packet sizes
5. Documentation: Record all measurements

EXPECTED RESULTS:
├─ ~94 Mbps maximum (Ethernet overhead)
├─ Lower performance con small packets
├─ Higher performance con large packets
└─ Variable results depending on network conditions
```

**Assessment Questions Durante Lab:**
1. "¿Por qué no obtienen exactly 100 Mbps?"
2. "¿Qué factors afectan performance measurements?"
3. "¿Cómo correlaciona esto con teoría Ethernet overhead?"

**Sección 3: Wireless Configuration (5 min)**

**Quick Wireless Setup:**
- Configurar SSID descriptivo
- Aplicar WPA2 security
- Test connectivity con mobile device
- Monitor wireless statistics

**Lab Debrief (5 min):**
- ¿Qué aprendieron que no esperaban?
- ¿Problems encountered y cómo resolved?
- ¿Performance results vs expectations?

---

#### **Laboratorio 2: Bridge Implementation (60 minutos)**

**Learning Objectives:**
- Implement transparent bridging algorithm
- Observe MAC address learning process
- Configure STP/RSTP with understanding
- Validate theory con practical observation

**Pre-lab Theory Refresh (5 min):**
> "Vamos a crear un switch virtual que va a implementar todo el algoritmo de transparent bridging que explicamos. Van a ver MAC learning en tiempo real."

**Sección 1: Bridge Creation y Basic Config (20 min)**

**Step 1: Remove Previous Configuration**
```
INSTRUCTOR GUIDANCE:
"Vamos a empezar desde clean slate"

COMMANDS:
/ip address remove [find interface=bridge-lan]  
/interface bridge port remove [find bridge=bridge-lan]
/interface bridge remove bridge-lan

TEACHING POINT:
├─ Order matters: IP first, then ports, then bridge
├─ Clean configuration = easier troubleshooting
└─ Document what you remove
```

**Step 2: Create Bridge con STP**
```
INSTRUCTOR DEMONSTRATES:
/interface bridge add name=bridge-lab protocol=rstp

STUDENT ACTIVITY:
├─ Verify bridge created: /interface bridge print
├─ Check default parameters
├─ Modify parameters: hello-time, forward-delay
└─ Understand each parameter's purpose

THEORY CONNECTION:
├─ Protocol=rstp → Fast convergence
├─ STP enabled by default
└─ Parameters affect convergence time
```

**Step 3: Add Ports to Bridge**
```
SYSTEMATIC APPROACH:
/interface bridge port add bridge=bridge-lab interface=ether1 comment="WAN"
/interface bridge port add bridge=bridge-lab interface=ether2 comment="PC1" 
/interface bridge port add bridge=bridge-lab interface=ether3 comment="PC2"

CONFIGURATION BEST PRACTICES:
├─ Always use descriptive comments
├─ Configure edge ports: edge=yes
├─ Point-to-point for direct connections
└─ Document port assignments
```

**Sección 2: MAC Learning Observation (25 min)**

**Real-time MAC Table Monitoring:**
```
INSTRUCTOR DEMO:
/interface bridge host print

Initial state: Empty table

STUDENT ACTIVITY SEQUENCE:
1. Connect laptop to ether2
2. Observe: /interface bridge host print
3. Generate traffic (ping)
4. Observe MAC appears in table
5. Connect second laptop to ether3  
6. Generate inter-PC traffic
7. Observe both MACs learned
8. Document aging behavior
```

**Advanced Monitoring:**
```
CONTINUOUS MONITORING:
/interface bridge host print interval=1

WHILE MONITORING:
├─ Connect/disconnect devices
├─ Generate broadcast traffic
├─ Observe learning/aging process
└─ Correlate with theory

TEACHING MOMENTS:
├─ "¿Por qué MAC appears immediately?"
├─ "¿What happens during broadcast?"  
├─ "¿How does aging work?"
└─ "¿Performance impact of table size?"
```

**Sección 3: STP Testing (10 min)**

**Loop Creation Exercise:**
```
CONTROLLED LOOP TEST:
⚠️ INSTRUCTOR SUPERVISION REQUIRED

SETUP:
1. Two laptops connected to ether2, ether3
2. Both laptops can communicate
3. Prepare extra cable for loop

LOOP CREATION:
├─ Quick connection: ether2 to ether3 directly
├─ Observe STP response: /interface bridge monitor bridge-lab
├─ Watch port states change
└─ Remove loop immediately

OBSERVATIONS:
├─ One port goes to discarding
├─ Topology change counter increases
├─ Convergence time measurement
└─ Recovery behavior
```

**Common Issues y Solutions:**
- **Issue:** "MAC table not populating"
  **Solution:** Generate traffic, check cables
- **Issue:** "STP not converging"
  **Solution:** Verify STP enabled, check loop scenario

---

#### **Laboratorio 3: VLAN Implementation Advanced (75 minutos)**

**Learning Objectives:**
- Implement IEEE 802.1Q standard practically
- Validate frame processing theory
- Configure complex VLAN scenarios
- Troubleshoot VLAN issues systematically

**Pre-lab Architecture Review (10 min):**

**Target VLAN Design:**
```
┌─────────────────────────────────────────────┐
│ VLAN PLAN - GRUPO [XX]                      │  
├─────────────────────────────────────────────┤
│ VLAN 10: Admin (192.168.10.0/24)           │
│ ├─ Purpose: Network management              │
│ ├─ Access port: ether2                     │
│ ├─ Security: Full access to other VLANs    │
│ └─ DHCP: 192.168.10.100-200               │
├─────────────────────────────────────────────┤
│ VLAN 20: Users (192.168.20.0/24)           │
│ ├─ Purpose: General user traffic           │  
│ ├─ Access ports: ether3, ether4            │
│ ├─ Security: Restricted inter-VLAN         │
│ └─ DHCP: 192.168.20.100-200               │
├─────────────────────────────────────────────┤
│ VLAN 30: Guest (192.168.30.0/24)           │
│ ├─ Purpose: Guest network isolation        │
│ ├─ Access ports: ether5, wlan1             │
│ ├─ Security: Internet only, no LAN         │
│ └─ DHCP: 192.168.30.100-200               │
├─────────────────────────────────────────────┤
│ TRUNK: ether1 (tagged 10,20,30)            │
│ └─ Connection to external switch/router     │
└─────────────────────────────────────────────┘
```

**Sección 1: Bridge VLAN Table Configuration (30 min)**

**Step 1: Enable VLAN Filtering**
```
CRITICAL INSTRUCTOR WARNING:
"This command will temporarily break connectivity. Use MAC address to reconnect if needed."

COMMAND:
/interface bridge set bridge-lab vlan-filtering=yes

EXPECTED BEHAVIOR:
├─ Temporary connectivity loss possible
├─ Bridge starts filtering based on VLAN table  
├─ Untagged traffic may be blocked
└─ Need to configure VLAN table immediately
```

**Step 2: Configure VLAN Table Systematically**
```
VLAN 10 CONFIGURATION:
/interface bridge vlan add bridge=bridge-lab tagged=bridge-lab,ether1 untagged=ether2 vlan-ids=10

EXPLANATION TO STUDENTS:
├─ tagged=bridge-lab: Router can process this VLAN
├─ tagged=ether1: Trunk port carries this VLAN tagged
├─ untagged=ether2: Access port provides untagged access
└─ vlan-ids=10: This configuration applies to VLAN 10

REPEAT FOR ALL VLANs:
├─ VLAN 20: untagged=ether3,ether4
├─ VLAN 30: untagged=ether5,wlan1
└─ All VLANs: tagged=bridge-lab,ether1
```

**Step 3: Configure Port VLANs (PVID)**
```
ACCESS PORT CONFIGURATION:
/interface bridge port set [find interface=ether2] pvid=10 frame-types=admit-only-untagged-and-priority-tagged

EXPLANATION:
├─ pvid=10: Assigns VLAN 10 to untagged traffic
├─ frame-types: Only accept untagged frames
├─ Security: Prevents VLAN hopping attacks
└─ Repeat for each access port

TRUNK PORT CONFIGURATION:  
/interface bridge port set [find interface=ether1] frame-types=admit-only-vlan-tagged

EXPLANATION:
├─ Only tagged frames accepted
├─ Security measure against attacks
├─ Forces proper VLAN tagging
└─ Trunk port best practices
```

**Troubleshooting During Configuration:**
- **Issue:** "Lost connectivity after vlan-filtering=yes"
  **Solution:** Connect by MAC, configure VLAN table immediately
- **Issue:** "Devices not getting DHCP"  
  **Solution:** Check PVID configuration, verify VLAN membership

**Sección 2: VLAN Interface Creation y IP Addressing (25 min)**

**Create VLAN Interfaces:**
```
SYSTEMATIC APPROACH:
/interface vlan add name=vlan10-admin vlan-id=10 interface=bridge-lab
/interface vlan add name=vlan20-users vlan-id=20 interface=bridge-lab  
/interface vlan add name=vlan30-guest vlan-id=30 interface=bridge-lab

TEACHING POINTS:
├─ VLAN interfaces act as gateways
├─ Each VLAN needs separate IP interface
├─ Naming convention important for management
└─ Interface acts as router port for VLAN
```

**Configure IP Addressing:**
```
IP ASSIGNMENT:
/ip address add address=192.168.10.1/24 interface=vlan10-admin
/ip address add address=192.168.20.1/24 interface=vlan20-users
/ip address add address=192.168.30.1/24 interface=vlan30-guest

VERIFICATION:
/ip address print
├─ Verify all addresses assigned
├─ Check interface associations  
├─ Verify subnet calculations
└─ Test gateway connectivity
```

**Sección 3: DHCP Configuration per VLAN (20 min)**

**DHCP Pools Creation:**
```
POOL CONFIGURATION:
/ip pool add name=admin-pool ranges=192.168.10.100-192.168.10.200
/ip pool add name=users-pool ranges=192.168.20.100-192.168.20.200
/ip pool add name=guest-pool ranges=192.168.30.100-192.168.30.200

DHCP SERVERS:
/ip dhcp-server add name=admin-dhcp interface=vlan10-admin address-pool=admin-pool lease-time=1d disabled=no

DHCP NETWORKS:
/ip dhcp-server network add address=192.168.10.0/24 gateway=192.168.10.1 dns-server=8.8.8.8

REPEAT FOR ALL VLANs with appropriate parameters
```

**Testing DHCP Functionality:**
- Connect devices to access ports
- Verify IP assignment per VLAN
- Test gateway connectivity
- Validate DNS resolution

---

#### **Laboratorio 4: Testing y Validation (30 min)**

**Comprehensive VLAN Testing:**

**Test 1: VLAN Isolation Verification**
```
TESTING MATRIX:
┌─────────────┬─────────┬─────────┬─────────┐
│ From \ To   │ VLAN 10 │ VLAN 20 │ VLAN 30 │
├─────────────┼─────────┼─────────┼─────────┤
│ VLAN 10     │    ✓    │    ✓    │    ✓    │
│ VLAN 20     │    ✓    │    ✓    │    ?    │  
│ VLAN 30     │    ?    │    ?    │    ✓    │
└─────────────┴─────────┴─────────┴─────────┘

EXPECTED RESULTS (with IP forwarding enabled):
├─ Intra-VLAN: Always works (Layer 2)
├─ Inter-VLAN: Works by default (Layer 3)
└─ Need firewall for restrictions
```

**Test 2: Performance Validation**
```
BANDWIDTH TESTING:
├─ Intra-VLAN performance (Layer 2 switching)
├─ Inter-VLAN performance (Layer 3 routing)
├─ Compare hardware vs software switching
└─ Document performance differences

MONITORING DURING TESTS:
├─ /interface monitor-traffic interface=bridge-lab
├─ /system resource cpu print
├─ /interface bridge print stats
└─ Document CPU impact
```

**Test 3: Security Verification**
- Packet capture con VLAN tags
- Frame-type validation
- VLAN hopping attempt (educational)
- Native VLAN security verification

---

### **SESIÓN 4: Troubleshooting y Advanced Topics (1.5 horas)**

#### **Troubleshooting Methodology Application (45 min)**

**Systematic Troubleshooting Workshop:**

**Scenario 1: "No DHCP in VLAN 20"**
```
STUDENT DIAGNOSTIC PROCESS:
├─ Layer 1: Check physical connectivity
├─ Layer 2: Verify VLAN membership
├─ Layer 3: Check DHCP configuration  
├─ Layer 4: Test DHCP service status
└─ Resolution: Systematic approach

COMMON CAUSES:
├─ PVID misconfiguration
├─ VLAN table missing entries
├─ DHCP pool exhausted
├─ Interface down
└─ Frame-types blocking traffic
```

**Scenario 2: "Intermittent Connectivity"**
```
DIAGNOSTIC TOOLS:
├─ /interface bridge host print (MAC table stability)
├─ /interface bridge monitor (STP changes)
├─ /log print (system events)
├─ /tool sniffer (packet analysis)
└─ Performance monitoring

ROOT CAUSE ANALYSIS:
├─ Network loops (STP reconvergence)
├─ Duplex mismatches
├─ Cable issues
├─ Buffer overruns
└─ Software vs hardware switching
```

#### **Performance Optimization (30 min)**

**Hardware Offload Verification:**
```
OPTIMIZATION CHECKLIST:
├─ Verify hardware offload: /interface bridge port print where hw-offload=yes
├─ Monitor CPU usage: /system resource cpu print
├─ Check interface statistics: /interface print stats
├─ Optimize buffer settings
└─ STP parameter tuning
```

**Best Practices Implementation:**
- Edge ports on host connections
- Appropriate aging times
- Buffer management
- Monitoring setup

#### **Lab Documentation y Assessment (15 min)**

**Documentation Requirements:**
- Configuration exports
- Performance measurements  
- Troubleshooting logs
- Lessons learned

**Assessment Questions:**
1. Explain MAC learning process observed
2. Compare STP vs RSTP convergence
3. Justify VLAN design decisions
4. Describe troubleshooting methodology used

---

## **EVALUACIÓN Y ASSESSMENT**

### **Rubric de Evaluación Detallada**

#### **Laboratorio Práctico (50% de la calificación final)**

**Criterios de Evaluación:**

**1. Bridge Configuration (20 puntos)**
```
EXCELENTE (18-20):
├─ Bridge configurado correctamente con RSTP
├─ Puertos agregados con edge configuration apropiada
├─ STP convergence observado y documentado
├─ MAC learning process explicado correctamente
└─ Troubleshooting efectivo de issues

COMPETENTE (14-17):
├─ Bridge básico funcional
├─ Mayoría de puertos configurados correctamente
├─ Understanding de STP concepts
├─ Alguna documentación presente
└─ Troubleshooting con guidance

EN DESARROLLO (10-13):
├─ Bridge funciona pero configuration suboptimal
├─ Concepts básicos entendidos
├─ Troubleshooting con assistance considerable
└─ Documentation incompleta

INSUFICIENTE (0-9):
├─ Bridge no funcional o mal configurado
├─ Misunderstanding de concepts fundamentales
├─ Unable to troubleshoot effectively
└─ No documentation or incorrect
```

**2. VLAN Implementation (20 puntos)**
```
EXCELENTE (18-20):
├─ Bridge VLAN table configurado perfectamente
├─ Access y trunk ports funcionando correctamente
├─ Frame-types configurado para security
├─ VLAN isolation verificado
├─ Inter-VLAN routing operativo
└─ Performance optimization considerado

COMPETENTE (14-17):
├─ VLANs básicos funcionando
├─ Mayoría de configuraciones correctas
├─ Testing básico completado
└─ Understanding de concepts

EN DESARROLLO (10-13):
├─ VLANs parcialmente funcionando
├─ Configuration errors present
├─ Limited testing performed
└─ Basic understanding only

INSUFICIENTE (0-9):
├─ VLANs no funcionando
├─ Major configuration errors
├─ No testing or verification
└─ Fundamental misunderstanding
```

**3. Troubleshooting Effectiveness (10 puntos)**
```
EVALUATION CRITERIA:
├─ Systematic approach using OSI model
├─ Proper use of diagnostic tools
├─ Documentation of process y findings
├─ Ability to resolve problems independently
└─ Learning from troubleshooting experience
```

#### **Proyecto GNS3 (35% de la calificación final)**

**Evaluation Rubric:**

**1. Network Design (15 puntos)**
- Topology complexity y realism
- VLAN design rationale
- Security considerations
- Scalability considerations
- Documentation quality

**2. Technical Implementation (15 puntos)**  
- Configuration accuracy
- Functionality verification
- Performance optimization
- Advanced features implementation
- Error handling

**3. Analysis y Documentation (5 puntos)**
- Packet capture analysis
- Performance testing results
- Troubleshooting scenarios
- Lessons learned documentation
- Professional presentation

#### **Evaluación Teórica (15% de la calificación final)**

**Sample Questions:**

**Conceptual Understanding:**
1. Explain the transparent bridging algorithm step-by-step
2. Compare STP vs RSTP convergence mechanisms  
3. Describe IEEE 802.1Q frame processing
4. Analyze hardware vs software switching implications

**Applied Knowledge:**
1. Design VLAN strategy for 500-user enterprise
2. Troubleshoot VLAN connectivity issue given symptoms
3. Optimize bridge performance for specific scenario
4. Implement security policies for multi-VLAN network

---

## **RECURSOS PARA EL INSTRUCTOR**

### **Preparación Pre-Clase**

**Equipment Checklist:**
- [ ] RB951-2HnD devices tested y functional
- [ ] Latest RouterOS version installed
- [ ] Winbox latest version available
- [ ] Cables tested for continuity
- [ ] Switch aula operational
- [ ] Internet connectivity verified
- [ ] Backup equipment available
- [ ] GNS3 server operational (if available)

**Knowledge Preparation:**
- [ ] Review latest RouterOS v7 changes
- [ ] Test all lab procedures personally
- [ ] Prepare troubleshooting scenarios
- [ ] Update performance benchmarks
- [ ] Review student background levels

### **Common Student Questions y Responses**

**Q: "¿Por qué usar bridge VLAN table instead of interface VLAN method?"**
A: "Bridge VLAN table es el método recomendado en RouterOS v7 porque:
- Mejor hardware offload support
- Configuración centralizada más clara
- Better performance con switch chip
- Easier troubleshooting y management
- Future-proof approach"

**Q: "¿Cuándo necesito trunk ports vs access ports?"**  
A: "Use access ports para end devices (PCs, printers) que no need VLAN awareness. Use trunk ports para carry múltiples VLANs between switches o to VLAN-aware devices como routers o servers."

**Q: "¿Por qué mi performance es slower con VLANs?"**
A: "Varias posibilidades:
- Software switching si hardware offload no available
- Inter-VLAN traffic requires Layer 3 processing
- Check CPU usage during tests
- Verify hardware offload status
- Consider bridge configuration optimization"

**Q: "¿How do I prevent VLAN hopping attacks?"**
A: "Key security measures:
- Use unused VLAN como native (not VLAN 1)
- Configure frame-types appropriately
- Disable unused ports
- Use access ports donde sea posible
- Monitor native VLAN traffic
- Implement 802.1X si applicable"

### **Troubleshooting Guide para Instructor**

**Common Lab Issues:**

**Issue: Students can't connect after VLAN filtering enabled**
```
SOLUTION STEPS:
1. Connect via MAC address usando Winbox
2. Verify VLAN table configuration
3. Check PVID settings on ports
4. Ensure management VLAN properly configured
5. Verify bridge tagged interfaces include bridge itself

PREVENTION:
├─ Warn students about temporary disconnection
├─ Have students document current config before changes
├─ Ensure backup connection method available
└─ Practice recovery procedures
```

**Issue: DHCP not working en specific VLAN**
```
DIAGNOSIS WORKFLOW:
1. Check DHCP server status: /ip dhcp-server print
2. Verify pool availability: /ip pool print
3. Check DHCP network config: /ip dhcp-server network print  
4. Verify VLAN interface UP: /interface vlan print
5. Check client VLAN membership
6. Test manual IP assignment

COMMON CAUSES:
├─ DHCP server disabled
├─ Pool exhausted  
├─ VLAN interface down
├─ PVID misconfiguration
└─ Frame-types blocking DHCP packets
```

**Issue: Performance significantly degraded**
```
DIAGNOSTIC STEPS:
1. Check hardware offload: /interface bridge port print where hw-offload=yes
2. Monitor CPU usage: /system resource cpu print
3. Check interface statistics: /interface print stats  
4. Verify STP stability: /interface bridge monitor
5. Analyze traffic patterns: /tool traffic-monitor

OPTIMIZATION ACTIONS:
├─ Enable hardware offload if available
├─ Optimize STP parameters
├─ Check for network loops
├─ Verify appropriate interface speeds
└─ Consider buffer management tuning
```

### **Advanced Topics para Estudiantes Avanzados**

**Si hay tiempo adicional o estudiantes advanced:**

**1. Bridge Filtering Rules**
```
ADVANCED SECURITY:
/interface bridge filter add chain=forward action=drop src-mac-address=aa:bb:cc:dd:ee:ff
/interface bridge filter add chain=input action=accept src-mac-address=11:22:33:44:55:66 in-interface=ether3
```

**2. VLAN Header Manipulation**
```
ADVANCED VLAN PROCESSING:
/interface bridge port set [find interface=ether2] ingress-filtering=yes
/interface bridge port set [find interface=ether3] frame-types=admit-all
```

**3. Performance Monitoring Setup**
```
CONTINUOUS MONITORING:
/tool graphing interface add interface=bridge-lab
/system logging add topics=bridge action=memory
/tool netwatch add host=192.168.10.1 interval=30s
```

### **Assessment Adaptations**

**Para Diferentes Niveles de Estudiantes:**

**Principiantes:**
- Más tiempo en concepts fundamentales
- Step-by-step guidance con verification
- Focus en configuration correctness
- Basic troubleshooting scenarios

**Intermedios:**
- Independent configuration implementation
- Performance optimization challenges  
- Advanced troubleshooting scenarios
- Design justification requirements

**Avanzados:**  
- Complex multi-vendor scenarios
- Advanced security implementations
- Performance tuning y optimization
- Teaching others (peer mentoring)

---

## **POST-CLASE FOLLOW-UP**

### **Immediate Actions (Within 24 hours)**

**1. Student Assessment:**
- Review lab completion rates
- Identify students needing additional support
- Document common issues encountered
- Plan remediation para próxima clase

**2. Equipment Maintenance:**
- Reset devices to clean state
- Update any firmware if needed
- Test equipment for next class
- Document any hardware issues

**3. Content Refinement:**
- Note timing adjustments needed
- Update explanations based on student feedback
- Refine troubleshooting scenarios
- Update performance benchmarks

### **Weekly Follow-up**

**1. Homework Review:**
- Evaluate GNS3 projects submitted
- Provide detailed feedback
- Identify advanced students for additional challenges
- Plan remediation for struggling students

**2. Preparation for Module 3:**
- Review routing prerequisite concepts
- Prepare advanced scenarios
- Update equipment configuration
- Plan integration points with current module

### **Continuous Improvement**

**Feedback Collection:**
- Student feedback on difficulty level
- Industry relevance assessment  
- Equipment adequacy evaluation
- Timing and pacing feedback

**Professional Development:**
- Stay updated con RouterOS changes
- Review industry networking trends
- Update examples con current technology
- Enhance troubleshooting methodologies

Esta guía comprehensiva debe permitir a cualquier instructor competente deliver el Módulo 2 successfully, proporcionando both theoretical understanding y practical skills que preparen estudiantes para networking careers reales.