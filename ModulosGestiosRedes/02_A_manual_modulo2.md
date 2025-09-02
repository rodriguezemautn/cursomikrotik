# Manual Completo - Módulo 2: Configuración de Interfaces y Switching

## Información del Módulo

**Duración:** 2 semanas (8 horas académicas)  
**Modalidad:** Híbrida (Presencial + Homework)  
**Prerequisitos:** Módulo 1 completado - Fundamentos RouterOS  

## Competencias a Desarrollar

Al finalizar este módulo, el estudiante será capaz de:

1. **Configurar y gestionar interfaces físicas y virtuales optimizando el rendimiento de red**
2. **Implementar configuraciones de switching con VLANs y segmentación de red**

---

## 8. Teoría de Diseño de Redes y Mejores Prácticas

### 8.1 Principios de Diseño de Red Moderna

**Diseño Jerárquico de Redes:**

**Modelo de Tres Capas (Traditional):**
```
CORE LAYER:
├─ High-speed switching/routing
├─ Redundancy y fault tolerance  
├─ Minimal processing (forwarding optimizado)
└─ No workstation access directo

DISTRIBUTION LAYER:  
├─ Policy enforcement point
├─ VLAN routing y inter-VLAN communication
├─ Security filtering y access control
└─ WAN access y remote connectivity

ACCESS LAYER:
├─ Workstation y server connectivity
├─ VLAN assignment y port security  
├─ Power over Ethernet (PoE)
└─ High port density, cost-effective
```

**Principios de Diseño VLAN:**

**VLAN Numbering Strategy:**
```
RANGE-BASED ALLOCATION:
1-99:     Reserved/Management
100-199:  User VLANs (por edificio/departamento)  
200-299:  Server VLANs (por función)
300-399:  DMZ/External facing
400-499:  Voice VLANs (VoIP)
500-599:  Wireless VLANs
600-699:  Guest networks
700-799:  IoT/Building automation
800-899:  Security cameras/physical security
900-999:  Testing/temporary
```

**VLAN Sizing Considerations:**
- Subnet size basado en growth projection
- /24 networks (254 hosts) común para user VLANs
- /25 or /26 para server VLANs (control más granular)
- /29 or /30 para point-to-point links

### 8.2 Teoría de Convergencia y Disponibilidad

**Conceptos de High Availability:**

**Failure Domain Isolation:**
- Single points of failure identification
- Redundancy en cada layer crítico  
- Fault tolerance vs fault recovery
- Graceful degradation bajo falla

**Recovery Time Objectives:**
```
RTO (Recovery Time Objective):
- Tiempo máximo acceptable para restoration
- STP convergence: 30-50 segundos (legacy)
- RSTP convergence: <2 segundos  
- Hardware failure detection: <1 segundo

RPO (Recovery Point Objective):  
- Cantidad data/state loss acceptable
- Network state: Generalmente cero loss objetivo
- MAC table: Acceptable learning delay
- Routing convergence: Minimal packet loss
```

### 8.3 Teoría de Performance y Escalabilidad

**Conceptos de Network Performance:**

**Bandwidth vs Throughput vs Goodput:**
```
BANDWIDTH: Capacidad teórica del enlace (1 Gbps)
    │
    ▼
THROUGHPUT: Datos actually transmitted (950 Mbps)
    │ (accounting para protocol overhead)
    ▼  
GOODPUT: Application data útil (900 Mbps)
    (accounting para retransmissions, headers)
```

**Factors Affecting Performance:**
- **Latency:** Round-trip time, processing delay
- **Jitter:** Variability en latency (crítico para VoIP/video)
- **Packet Loss:** Congestion, errors, policy drops
- **Buffer Management:** Queue depth, scheduling algorithms

**Scaling Considerations:**

**Horizontal vs Vertical Scaling:**
```
VERTICAL SCALING (Scale-Up):
├─ Upgrade to faster switches/routers
├─ Higher port density equipment  
├─ More powerful CPUs/ASICs
└─ Limitation: Single device capacity

HORIZONTAL SCALING (Scale-Out):
├─ Add more switches/routers
├─ Distribute load across devices
├─ Link aggregation (LACP)
└─ More complex pero better fault tolerance
```

### 8.4 Security-by-Design Principles

**Zero Trust Network Concepts:**

**Micro-segmentation Strategy:**
```
TRADITIONAL PERIMETER SECURITY:
[Internet] ──── [Firewall] ──── [Flat Internal Network]
                    │
                    └─ Single trust boundary

ZERO TRUST MICRO-SEGMENTATION:
[Internet] ──── [Edge FW] ──── [Core Network]
                                    │
                   ┌────────────────┼────────────────┐
                   ▼                ▼                ▼
              [User VLAN]    [Server VLAN]    [Management VLAN]
                   │                │                │
                   └── Policy ──── Policy ──── Policy
                   
Every VLAN boundary = Security boundary
```

**Defense in Depth para VLANs:**

**Layer 1 Security:**
- Physical port security
- Cable plant protection
- Port shutdown automatico

**Layer 2 Security:**  
- VLAN isolation enforcement
- MAC address filtering
- STP security (BPDU guard, root guard)
- 802.1X port authentication

**Layer 3 Security:**
- Inter-VLAN firewall rules
- ACLs granulares
- Route filtering
- Anti-spoofing measures

### 8.5 Teoría de Monitoreo y Observabilidad

**Network Observability Pillars:**

**1. Metrics (Quantitative data):**
```
INFRASTRUCTURE METRICS:
- Interface utilization (%)
- Packet rates (PPS)  
- Error rates (CRC, drops)
- CPU/Memory utilization

PERFORMANCE METRICS:
- Latency (round-trip time)
- Jitter (latency variation)
- Throughput (actual data rate)
- Availability (uptime %)
```

**2. Logs (Event data):**
```
OPERATIONAL LOGS:
- Interface state changes
- STP topology changes
- Authentication events
- Configuration changes

SECURITY LOGS:
- Access violations
- Unusual traffic patterns
- Failed authentication attempts
- Policy violations
```

**3. Traces (Request flow):**
```
PACKET TRACES:
- End-to-end packet flow
- Protocol analysis
- Performance bottleneck identification
- Security incident investigation
```

**Proactive vs Reactive Monitoring:**

**Proactive Monitoring:**
- Baseline establishment
- Threshold alerting
- Trend analysis
- Predictive failure detection

**Reactive Monitoring:**
- Incident response
- Fault isolation
- Performance troubleshooting  
- Post-incident analysis

---

## 7. Teoría de Troubleshooting Sistemático

### 7.1 Metodología de Diagnóstico de Redes

**Enfoque Estructurado para Troubleshooting:**

El troubleshooting efectivo requiere metodología sistemática basada en el modelo OSI y principios de ingeniería de redes.

**Modelo OSI para Troubleshooting:**
```
Layer 7 - Application  → ¿Aplicación funciona correctamente?
Layer 6 - Presentation → ¿Encryption/compression OK?  
Layer 5 - Session      → ¿Session establishment OK?
Layer 4 - Transport    → ¿TCP/UDP connectivity OK?
Layer 3 - Network      → ¿IP routing funciona?
Layer 2 - Data Link    → ¿Switching/VLAN OK?
Layer 1 - Physical     → ¿Cable/interface UP?
```

**Estrategias de Troubleshooting:**

**1. Bottom-Up Approach:**
- Comenzar en Physical Layer
- Avanzar hacia Application Layer
- Útil para problemas de conectividad básica
- Sistemático pero puede ser lento

**2. Top-Down Approach:**  
- Comenzar en Application Layer
- Descender hacia Physical Layer
- Útil para problemas específicos de aplicación
- Más rápido para issues complejas

**3. Divide-and-Conquer:**
- Dividir red en segmentos lógicos
- Aislar problema a segmento específico
- Approach más eficiente para redes grandes

**4. Spot-the-Differences:**
- Comparar configuración working vs non-working
- Identificar cambios recientes
- Effective para problems post-change

### 7.2 Herramientas de Diagnóstico Específicas

**Jerarquía de Herramientas de Diagnóstico:**

**Layer 1 Diagnostics:**
```
PHYSICAL CONNECTIVITY:
- Cable tester → Continuidad, wiring correcta
- Link lights → Estado físico de enlace
- Interface statistics → RX/TX counters, errores

MIKROTIK TOOLS:
/interface monitor ether1     # Estado físico detallado
/interface print stats        # Estadísticas todas interfaces
```

**Layer 2 Diagnostics:**
```  
SWITCHING/VLAN ISSUES:
- Bridge host table → MAC learning verification
- VLAN table → VLAN membership verification  
- STP status → Loop prevention verification

MIKROTIK TOOLS:
/interface bridge host print                    # Tabla MAC
/interface bridge vlan print                    # VLAN configuration
/interface bridge monitor bridge1               # STP status
/tool sniffer start interface=bridge1           # Packet capture
```

**Layer 3 Diagnostics:**
```
ROUTING/IP ISSUES:
- Routing table → Path selection verification
- ARP table → Layer 2/3 address resolution
- Ping/traceroute → End-to-end connectivity

MIKROTIK TOOLS:  
/ip route print                                 # Routing table
/ip arp print                                   # ARP table
/ping 8.8.8.8 interface=vlan10                 # Source-specific ping
/tool traceroute 8.8.8.8                       # Path tracing
```

### 7.3 Patrón de Síntomas vs Root Cause

**Matriz de Troubleshooting Común:**

**SÍNTOMA: "No hay conectividad"**
```
POSSIBLE ROOT CAUSES:
Layer 1: Cable disconnected, interface down, duplex mismatch
Layer 2: VLAN mismatch, STP blocking, MAC filtering  
Layer 3: IP misconfiguration, routing issue, firewall block
Layer 4+: Service down, authentication failure, DNS issue

DIAGNOSTIC APPROACH:
1. Verificar link status (/interface print)
2. Verificar VLAN membership
3. Verificar IP configuration  
4. Test Layer 3 connectivity (ping)
5. Test specific service (telnet port)
```

**SÍNTOMA: "Conectividad intermitente"**
```
POSSIBLE ROOT CAUSES:
Layer 1: Cable problem, duplex negotiation issue
Layer 2: STP convergence, loop in network
Layer 3: Route flapping, load balancing issue
Higher: DNS resolution timeout, application retry

DIAGNOSTIC APPROACH:
1. Monitor interface statistics (errores increasing?)
2. Monitor STP topology changes
3. Monitor routing table stability
4. Long-term traffic monitoring
```

**SÍNTOMA: "Performance degradado"**
```
POSSIBLE ROOT CAUSES:  
Layer 1: Speed negotiation suboptimal
Layer 2: Software switching vs hardware
Layer 3: Suboptimal routing path
Traffic: Congestion, QoS misconfiguration

DIAGNOSTIC APPROACH:
1. Verificar speed/duplex negotiation
2. Verificar hardware offload status
3. Bandwidth testing
4. Traffic pattern analysis
```

---

## Fundamentos Teóricos de Networking

### Conceptos Fundamentales de Switching

#### Evolución del Networking
El networking moderno ha evolucionado desde redes basadas en hubs (dominios de colisión compartidos) hacia switching inteligente que proporciona:

**Ventajas del Switching sobre Hub-based Networks:**
- **Dominios de colisión separados:** Cada puerto del switch es su propio dominio de colisión
- **Full-duplex communication:** Transmisión y recepción simultáneas
- **Bandwidth dedicado:** Cada puerto obtiene todo el ancho de banda disponible
- **Collision detection automática:** CSMA/CD mejorado
- **Escalabilidad:** Agregar dispositivos no reduce performance para otros

#### Principios del Modelo OSI en Switching

**Layer 1 (Físico):**
- Señales eléctricas y ópticas
- Velocidades de línea (10/100/1000 Mbps)
- Detección de enlace automática
- Auto-negotiation de parámetros

**Layer 2 (Data Link):**
- Frames Ethernet como unidad básica
- Direcciones MAC para identificación única
- Error detection con Frame Check Sequence (FCS)
- Flow control para prevenir overflow

#### Teoría de Congestión y Control de Flujo

**Buffer Management:**
Los switches modernos incluyen buffers para manejar ráfagas de tráfico:
- **Input buffering:** Buffers en puertos de entrada
- **Output buffering:** Buffers en puertos de salida
- **Shared buffering:** Pool común de memoria

**Flow Control Mechanisms:**
- **IEEE 802.3x Pause frames:** Pausar transmisión cuando buffer lleno
- **Priority-based flow control:** Pausa selectiva por prioridad
- **Adaptive buffer management:** Ajuste dinámico según carga

---

## 1. Gestión de Interfaces

### 1.1 Tipos de Interfaces en MikroTik

RouterOS implementa una arquitectura de interfaces unificada que abstrae diferentes tecnologías de red bajo una API común. Esta aproximación permite configuración consistente independientemente del tipo de interface física o lógica.

#### Teoría de Interfaces de Red

**Concepto de Interface:**
Una interface de red representa un punto de entrada/salida de datos del sistema. En RouterOS, cada interface tiene propiedades comunes:
- **Identificador único:** Nombre asignado por sistema o usuario
- **Estadísticas de tráfico:** Contadores RX/TX automáticos
- **Estado operacional:** UP/DOWN basado en condiciones físicas y lógicas
- **Configuración de Layer 2:** MTU, MAC address, encapsulation

**Modelo de Capas en RouterOS:**
```
┌─────────────────┐
│   Layer 3       │ ← IP, Routing, Firewall
├─────────────────┤
│   Layer 2.5     │ ← MPLS, VLANs, Bridging  
├─────────────────┤
│   Layer 2       │ ← Switching, MAC learning
├─────────────────┤
│   Layer 1       │ ← Physical interfaces
└─────────────────┘
```

RouterOS maneja múltiples tipos de interfaces, cada una con características específicas:

#### 1.1.1 Interfaces Ethernet

**Fundamentos Teóricos Ethernet:**

Ethernet es el estándar dominante para redes locales cableadas, definido originalmente por el estándar IEEE 802.3. Los principios fundamentales incluyen:

**CSMA/CD (Carrier Sense Multiple Access with Collision Detection):**
- **Carrier Sense:** Escuchar el medio antes de transmitir
- **Multiple Access:** Múltiples dispositivos pueden acceder al medio
- **Collision Detection:** Detectar y manejar colisiones (relevante en half-duplex)

**Auto-negotiation Process (IEEE 802.3u):**
El proceso de auto-negotiation permite que dos dispositivos Ethernet negotien automáticamente:

1. **Link Pulse Detection:** Detección de pulsos de enlace (NLP - Normal Link Pulses)
2. **Fast Link Pulse (FLP) Exchange:** Intercambio de capacidades
3. **Common Denominator Selection:** Selección de máxima capacidad común
4. **Parameter Configuration:** Configuración de velocidad, duplex, flow control

**Jerarquía de Preferencia Auto-negotiation:**
```
1000BASE-T Full Duplex  (Más preferido)
1000BASE-T Half Duplex
100BASE-TX Full Duplex
100BASE-T4
100BASE-TX Half Duplex  
10BASE-T Full Duplex
10BASE-T Half Duplex    (Menos preferido)
```

**Conceptos de Performance Ethernet:**

**Utilización Teórica vs Real:**
- **100 Mbps Ethernet:** ~94.7 Mbps máximo útil (overhead de headers, IFG)
- **Interframe Gap (IFG):** 96 bits de separación obligatoria entre frames
- **Preamble:** 8 bytes de sincronización por frame
- **Frame overhead:** Headers Ethernet (14 bytes) + FCS (4 bytes)

**Características:**
- Interfaces físicas cableadas
- Soporte 10/100/1000 Mbps según hardware
- Auto-negotiation y configuración manual
- Duplex y velocidad configurables

**Configuración básica:**
```bash
# Ver todas las interfaces
/interface ethernet print

# Configurar velocidad y duplex específicos
/interface ethernet set ether1 speed=100Mbps duplex=full

# Deshabilitar auto-negotiation
/interface ethernet set ether1 auto-negotiation=no

# Habilitar/deshabilitar interface
/interface ethernet set ether1 disabled=no
```

**Parámetros importantes:**
- **Speed:** auto, 10Mbps, 100Mbps, 1Gbps
- **Duplex:** auto, half, full
- **Auto-negotiation:** yes/no
- **MTU:** Maximum Transmission Unit (default 1500)

#### 1.1.2 Interfaces Bridge

**Fundamentos Teóricos de Bridging:**

Un bridge (puente) es un dispositivo que opera en Layer 2 del modelo OSI, conectando segmentos de red y filtrando tráfico basado en direcciones MAC. Los bridges implementan el concepto de "transparent bridging" definido en IEEE 802.1D.

**Principios del Transparent Bridging:**

1. **Transparencia:** Los hosts no necesitan conocer la existencia del bridge
2. **Learning:** El bridge aprende direcciones MAC automáticamente
3. **Forwarding:** Las decisiones se basan en la tabla de forwarding
4. **Flooding:** Broadcast cuando el destino es desconocido
5. **Filtering:** No reenviar tráfico innecesario entre segmentos

**Algoritmo de Learning Bridge:**

```
CUANDO frame llega al puerto P con MAC origen S y destino D:
1. LEARNING PHASE:
   - Agregar/actualizar entrada: MAC S asociada con puerto P
   - Timestamp de última actividad
   
2. FORWARDING PHASE:
   - SI D está en tabla de forwarding:
     - SI puerto destino ≠ puerto origen:
       → Reenviar por puerto específico
     - SI puerto destino = puerto origen:
       → Filtrar (no reenviar)
   - SI D NO está en tabla:
     → Flood a todos los puertos excepto origen
     
3. AGING PHASE:
   - Eliminar entradas no utilizadas después de aging time
```

**Conceptos de Performance en Bridging:**

**Store-and-Forward vs Cut-Through:**
- **Store-and-Forward:** Recibe frame completo antes de reenviar (MikroTik method)
  - Ventaja: Error checking completo, QoS analysis
  - Desventaja: Latencia mayor
- **Cut-Through:** Comienza reenvío tras leer dirección destino
  - Ventaja: Latencia mínima
  - Desventaja: Propaga frames corruptos

**Métricas de Performance Bridge:**
- **Forwarding Rate:** Frames por segundo procesados
- **Latency:** Tiempo entre ingreso y egreso de frame
- **Throughput:** Ancho de banda total manejado
- **Learning Rate:** MACs aprendidas por segundo

**Conceptos fundamentales:**
Las interfaces bridge permiten crear un switch virtual que conecta múltiples interfaces en un mismo dominio de broadcast.

**Características:**
- Opera en Layer 2 (Data Link)
- Aprende direcciones MAC automáticamente
- Maneja tabla de forwarding
- Soporte STP/RSTP

**Creación de bridge:**
```bash
# Crear bridge principal
/interface bridge add name=bridge1 protocol=rstp

# Agregar puertos al bridge
/interface bridge port add bridge=bridge1 interface=ether2
/interface bridge port add bridge=bridge1 interface=ether3
/interface bridge port add bridge=bridge1 interface=ether4

# Ver tabla MAC del bridge
/interface bridge host print
```

#### 1.1.3 Interfaces VLAN

**Fundamentos Teóricos de VLANs:**

Las Virtual Local Area Networks (VLANs) representan una innovación fundamental en el diseño de redes modernas, permitiendo la segmentación lógica de redes físicas sin requerir hardware adicional.

**Conceptos Fundamentales:**

**Dominio de Broadcast:**
- Conjunto de dispositivos que reciben frames de broadcast enviados por cualquier dispositivo del grupo
- Sin VLANs: Todo el switch = un dominio de broadcast
- Con VLANs: Cada VLAN = dominio de broadcast independiente

**Segmentación Lógica vs Física:**
```
SEGMENTACIÓN FÍSICA (Tradicional):
[Switch A] ── [Depto. A]
[Switch B] ── [Depto. B]
[Switch C] ── [Depto. C]
↑ Requiere hardware dedicado

SEGMENTACIÓN LÓGICA (VLANs):
[Switch Universal] ─┬─ VLAN 10 [Depto. A]
                    ├─ VLAN 20 [Depto. B]  
                    └─ VLAN 30 [Depto. C]
↑ Un solo switch físico
```

**Ventajas de la Segmentación VLAN:**

1. **Reducción de Dominios de Broadcast:**
   - Menos tráfico broadcast propagado
   - Mejor performance de red
   - Reducción de tormentas de broadcast

2. **Seguridad Mejorada:**
   - Aislamiento natural entre VLANs
   - Control granular de acceso
   - Segmentación de tráfico sensible

3. **Flexibilidad Administrativa:**
   - Cambios lógicos vs físicos
   - Administración centralizada
   - Reasignación simple de usuarios

4. **Optimización de Recursos:**
   - Un switch maneja múltiples segmentos
   - Mejor utilización de hardware
   - Reducción de costos infraestructura

**IEEE 802.1Q Standard Deep Dive:**

**Estructura del Header 802.1Q:**
```
┌─────────────┬─────────────┬─────┬─────────────┬──────────┐
│ Dest MAC    │ Src MAC     │TPID │    TCI      │   Data   │
│ (6 bytes)   │ (6 bytes)   │0x8100│ (2 bytes)   │          │
└─────────────┴─────────────┴─────┴─────────────┴──────────┘
                                   │
                                   ▼
                            ┌───┬───┬────────────┐
                            │PRI│CFI│  VLAN ID   │
                            │3bit│1bit│  12 bit   │
                            └───┴───┴────────────┘
```

**Campos del TCI (Tag Control Information):**

1. **Priority (PRI) - 3 bits:** IEEE 802.1p Quality of Service
   ```
   000 (0) = Background (BK)      100 (4) = Controlled Load (CL)
   001 (1) = Best Effort (BE)     101 (5) = Video (VI)
   010 (2) = Excellent Effort     110 (6) = Voice (VO)  
   011 (3) = Critical Applications 111 (7) = Network Control
   ```

2. **CFI (Canonical Format Indicator) - 1 bit:**
   - 0 = Canonical format (Ethernet)
   - 1 = Non-canonical format (Token Ring legacy)

3. **VLAN ID - 12 bits:**
   - Rango: 0-4095
   - VLAN 0: Priority tagging sin VLAN
   - VLAN 1: Default VLAN (nativa)
   - VLAN 2-4094: VLANs utilizables
   - VLAN 4095: Reservada

**Conceptos IEEE 802.1Q:**
- Permite segmentar una red física en múltiples redes lógicas
- Tagging de frames con VLAN ID (1-4094)
- VLAN 1 = VLAN nativa (untagged por defecto)
- Aislamiento de tráfico por VLAN

**Configuración VLAN:**
```bash
# Crear interface VLAN
/interface vlan add name=vlan10 vlan-id=10 interface=bridge1

# Asignar IP a VLAN
/ip address add address=192.168.10.1/24 interface=vlan10

# VLAN con tagging específico
/interface vlan add name=vlan20 vlan-id=20 interface=ether1
```

#### 1.1.4 Interfaces Wireless

**Características específicas:**
- Access Point o Station mode
- Configuración RF (frecuencia, potencia, canal)
- Seguridad wireless (WPA2/WPA3)
- Múltiples SSIDs virtuales

**Configuración básica wireless:**
```bash
# Configurar como Access Point
/interface wireless set wlan1 mode=ap-bridge ssid="MiRed-WiFi" frequency=2442

# Configurar seguridad
/interface wireless security-profiles add name=wpa2-profile mode=dynamic-keys authentication-types=wpa2-psk wpa2-pre-shared-key="ClaveSegura123!"

# Aplicar perfil de seguridad
/interface wireless set wlan1 security-profile=wpa2-profile
```

### 1.2 Configuración de Parámetros Físicos

#### 1.2.1 MTU (Maximum Transmission Unit)

**Conceptos:**
- Tamaño máximo de frame por interface
- Default: 1500 bytes para Ethernet
- Jumbo frames: >1500 bytes
- Fragmentación si excede MTU

**Configuración MTU:**
```bash
# Ver MTU actual
/interface ethernet print where name=ether1

# Configurar MTU personalizado
/interface ethernet set ether1 mtu=9000

# MTU para bridge
/interface bridge set bridge1 mtu=1500
```

#### 1.2.2 Configuración de Velocidad y Duplex

**Auto-negotiation vs Manual:**
```bash
# Auto-negotiation (recomendado)
/interface ethernet set ether1 auto-negotiation=yes

# Configuración manual (troubleshooting)
/interface ethernet set ether1 auto-negotiation=no speed=1Gbps duplex=full

# Verificar estado actual
/interface ethernet monitor ether1
```

#### 1.2.3 Configuración L2 Header

**VLAN Header y Tagging:**
```bash
# Configurar VLAN tagging en puerto
/interface bridge port set [find interface=ether2] frame-types=admit-only-vlan-tagged

# Puerto access (untagged)
/interface bridge port set [find interface=ether3] frame-types=admit-only-untagged-and-priority-tagged

# Puerto trunk (tagged + untagged)
/interface bridge port set [find interface=ether4] frame-types=admit-all
```

### 1.3 Monitoreo y Estadísticas

#### 1.3.1 Comandos de Monitoreo

**Estado de interfaces:**
```bash
# Estado general todas las interfaces
/interface print stats

# Monitoreo específico interface
/interface monitor-traffic interface=ether1

# Estadísticas detalladas
/interface ethernet monitor ether1

# Ver contadores de errores
/interface print stats-detail
```

#### 1.3.2 Herramientas de Diagnóstico

**Tools integradas:**
```bash
# Ping desde interface específica
/ping 8.8.8.8 interface=ether1

# Bandwidth test entre interfaces
/tool bandwidth-test direction=both protocol=tcp address=192.168.1.10

# Interface traffic monitor
/tool traffic-monitor interface=ether1
```

#### 1.3.3 Logging y Troubleshooting

**Configuración de logs:**
```bash
# Habilitar logging interfaces
/system logging add topics=interface action=memory

# Ver logs específicos
/log print where topics~"interface"

# Debug de interface específica
/interface ethernet set ether1 debug=yes
```

---

## 2. Bridge y Switching

### 2.1 Conceptos de Switching en MikroTik

#### 2.1.1 Funcionamiento del Bridge

**Principios de operación:**
1. **Learning:** Aprende direcciones MAC origen
2. **Forwarding:** Envía frames al puerto correcto
3. **Flooding:** Broadcast cuando destino desconocido
4. **Aging:** Elimina entradas MAC no utilizadas

**Tabla MAC Address:**
```bash
# Ver tabla de direcciones MAC
/interface bridge host print

# Agregar entrada estática
/interface bridge host add bridge=bridge1 mac-address=aa:bb:cc:dd:ee:ff interface=ether2

# Limpiar tabla MAC
/interface bridge host flush bridge=bridge1
```

#### 2.1.2 Bridge Configuration

**Parámetros importantes:**
```bash
# Crear bridge con configuración completa
/interface bridge add name=sw-principal \
    protocol=rstp \
    stp-enabled=yes \
    ageing-time=5m \
    max-message-age=20s \
    hello-time=2s \
    forward-delay=15s

# Configurar prioridad STP
/interface bridge set sw-principal priority=0x1000
```

#### 2.1.3 Bridge Ports Configuration

**Configuración de puertos bridge:**
```bash
# Agregar puerto con configuración específica
/interface bridge port add \
    bridge=sw-principal \
    interface=ether2 \
    path-cost=10 \
    priority=0x80 \
    edge=yes \
    point-to-point=yes

# Puerto con restricciones
/interface bridge port add \
    bridge=sw-principal \
    interface=ether3 \
    learn=no \
    horizon=1
```

### 2.2 Hardware vs Software Switching

**Fundamentos Teóricos de Packet Processing:**

El switching de paquetes puede implementarse mediante software (CPU) o hardware dedicado (ASIC). Comprender estas diferencias es fundamental para diseñar redes de alta performance.

**Arquitectura de Switching Tradicional vs Moderna:**

**Software-based Switching (CPU):**
```
Packet → NIC → Driver → OS Kernel → Switching Logic → OS Kernel → Driver → NIC
         │                                                              │
         └──────────────── CPU Processing Path ─────────────────────────┘
```

**Hardware-based Switching (ASIC):**
```
Packet → PHY → MAC → Switch ASIC → MAC → PHY
                    (Wire-speed)
```

**Teoría de ASICs (Application-Specific Integrated Circuits):**

**Características de Switch ASICs:**
1. **Parallel Processing:** Procesamiento simultáneo múltiples puertos
2. **Pipelined Architecture:** Etapas de procesamiento especializadas
3. **Content Addressable Memory (CAM):** Búsqueda MAC en un ciclo de clock
4. **Ternary CAM (TCAM):** Matching de reglas complejas (wildcards)

**Pipeline de Procesamiento ASIC:**
```
Ingress → Parsing → Lookup → Modification → Queuing → Scheduling → Egress
   │         │        │         │           │           │           │
   ▼         ▼        ▼         ▼           ▼           ▼           ▼
Header    L2/L3    MAC/Route  Header     Buffer     Traffic     Frame
Extract   Fields   Tables     Rewrite    Mgmt      Shaping     Transmit
```

**Performance Comparison:**

**Latency Analysis:**
- **Software switching:** 50-500 microsegundos
  - Context switches, interrupts, memory copies
  - Variable según CPU load
- **Hardware switching:** 1-10 microsegundos
  - Dedicated silicon, no OS overhead
  - Consistent, predictable latency

**Throughput Analysis:**
- **Software:** Depende de CPU speed y architecture
  - Typical: 1-10 Gbps aggregate
  - Degradación con features (firewall, QoS)
- **Hardware:** Line-rate forwarding
  - Typical: 100+ Gbps aggregate
  - Consistent performance con features habilitadas

**Packet Rate Performance:**
```
Ethernet Frame Size vs PPS (Packets Per Second):

64 bytes   → 1,488,095 PPS (1 Gbps line rate)
128 bytes  → 844,594 PPS
256 bytes  → 452,898 PPS  
512 bytes  → 234,962 PPS
1024 bytes → 119,332 PPS
1518 bytes → 81,274 PPS (Maximum Ethernet frame)
```

**MikroTik Switch Chip Implementation:**

**RouterOS v7 Hardware Offload:**
- Automatic fallback: Hardware cuando posible, software cuando necesario
- Transparent operation: Misma configuración, different execution path
- Feature compatibility: Algunas funciones requieren software path

#### 2.2.1 Hardware Switching (Switch Chip)

**Ventajas del switch chip:**
- Forwarding en hardware (wire-speed)
- No consume CPU para switching
- Latencia mínima
- Soporte para características avanzadas

**Identificar switch chip:**
```bash
# Ver switch chips disponibles
/interface ethernet switch print

# Configuración switch chip
/interface ethernet switch port print

# Verificar si está habilitado
/interface bridge port print where hw-offload=yes
```

**Habilitar hardware switching:**
```bash
# RouterOS v6.x
/interface ethernet switch set switch1 cpu-flow-control=yes

# RouterOS v7.x (automático con bridge)
/interface bridge port set [find interface=ether2] hw-offload=yes
```

#### 2.2.2 Software Switching

**Cuándo se usa software switching:**
- Hardware no soporta la función
- Configuraciones complejas (firewall en bridge)
- Debugging y packet capture
- Funciones Layer 3 en bridge

**Verificar modo de switching:**
```bash
# Ver qué puertos usan hardware/software
/interface bridge port monitor [find bridge=bridge1]

# Forzar software switching
/interface bridge port set [find interface=ether2] hw-offload=no
```

### 2.3 STP y RSTP Configuration

**Fundamentos Teóricos de Spanning Tree Protocol:**

El Spanning Tree Protocol (STP) resuelve uno de los problemas fundamentales en redes switched: la prevención de loops mientras se mantiene conectividad redundante para alta disponibilidad.

**Teoría de Grafos aplicada a Redes:**

**Conceptos Matemáticos:**
- **Grafo:** Conjunto de nodos (switches) conectados por aristas (enlaces)
- **Árbol:** Grafo conectado sin ciclos
- **Spanning Tree:** Árbol que conecta todos los nodos con mínimo número de aristas
- **Minimum Spanning Tree:** Spanning tree con menor costo total

**El Problema del Loop en Redes Ethernet:**

**Broadcast Storm Mechanism:**
```
1. Host A envía broadcast frame
2. Switch 1 flood a todos los puertos (incluyendo Switch 2)
3. Switch 2 recibe y flood de vuelta (incluyendo Switch 1)  
4. Switch 1 recibe el mismo frame y flood nuevamente
5. Loop infinito → Saturación de red
```

**MAC Address Table Instability:**
- Frame del mismo MAC llega por múltiples puertos
- Bridge actualiza tabla MAC constantemente
- "Flapping" de entradas MAC
- Forwarding decisions inconsistentes

**Algoritmo STP (IEEE 802.1D):**

**Fase 1: Root Bridge Election**
```
BRIDGE ID = [Priority (2 bytes)] + [MAC Address (6 bytes)]

Proceso de elección:
1. Cada bridge asume ser root inicialmente
2. Envía BPDUs con su Bridge ID como root
3. Al recibir BPDU superior:
   - Si recibido Bridge ID < propio Bridge ID
   → Acepta nuevo root, reenvía BPDU
   - Si recibido Bridge ID > propio Bridge ID  
   → Ignora BPDU, mantiene creencia
4. Convergencia cuando todos acuerdan mismo root
```

**Fase 2: Root Port Selection (cada non-root bridge)**
```
CÁLCULO DE PATH COST hacia root:

Cost acumulado = Σ(costs de todos los enlaces al root)

IEEE 802.1D Costs:
10 Mbps   → 100
100 Mbps  → 19  
1 Gbps    → 4
10 Gbps   → 2

Criterios de desempate (orden):
1. Lowest cumulative path cost
2. Lowest Bridge ID del sender
3. Lowest Port Priority del sender  
4. Lowest Port ID del sender
```

**Fase 3: Designated Port Selection (cada segmento)**
```
Para cada enlace físico:
- Un puerto debe ser "designated" (forwarding)
- Selección basada en:
  1. Bridge con menor path cost al root
  2. Bridge con menor Bridge ID (desempate)
  3. Puerto con menor Port ID (desempate final)
```

**Estados de Puerto STP:**

**Blocking (20 segundos):**
- No forwarding de data frames
- Escucha BPDUs solamente
- Previene loops durante convergencia

**Listening (15 segundos - Forward Delay):**
- No forwarding de data frames
- Procesa y envía BPDUs
- Determina rol final del puerto

**Learning (15 segundos - Forward Delay):**
- No forwarding de data frames
- Aprende direcciones MAC
- Poblando bridge table

**Forwarding:**
- Forwarding normal de data frames
- Procesamiento normal de BPDUs
- Operación estable

**Timers STP:**
- **Hello Time:** 2 segundos (frecuencia BPDUs)
- **Max Age:** 20 segundos (timeout BPDU)
- **Forward Delay:** 15 segundos (Listening + Learning)

**Total Convergence Time: 30-50 segundos**

**Rapid Spanning Tree Protocol (RSTP) - IEEE 802.1w:**

**Mejoras Fundamentales sobre STP:**

**1. Convergencia Rápida:**
- **Edge ports:** Transición inmediata a Forwarding
- **Point-to-point links:** Handshake rápido entre switches
- **Alternate/Backup ports:** Pre-computados para failover

**2. Estados Simplificados:**
- **Discarding:** Combina Blocking, Listening, Disabled
- **Learning:** Aprende MACs, no forwarding
- **Forwarding:** Operación normal

**3. Port Roles Expandidos:**
- **Root Port:** Mejor camino al root bridge
- **Designated Port:** Forwarding port en segmento
- **Alternate Port:** Backup para root port
- **Backup Port:** Backup para designated port  
- **Disabled Port:** Administrativamente down

**4. Proposal/Agreement Mechanism:**
```
Switch A propone nuevo topology:
1. Switch A envía Proposal BPDU
2. Switch B pone todos non-edge ports en Discarding
3. Switch B responde con Agreement BPDU  
4. Switch A transiciona puerto a Forwarding inmediatamente
5. Convergencia < 2 segundos
```

**Conceptos Avanzados RSTP:**

**Edge Port Concept:**
- Puertos conectados a hosts finales (no switches)
- Transición inmediata a Forwarding state
- Si recibe BPDU → pierde edge status automáticamente
- Critical para convergencia rápida

**Point-to-Point Detection:**
- Enlace full-duplex → Assumed point-to-point
- Permite proposal/agreement mechanism
- Half-duplex → Shared medium, STP compatibility mode

#### 2.3.1 Spanning Tree Protocol (STP)

**Propósito y funcionamiento:**
- Previene loops en topologías redundantes  
- Calcula shortest path tree
- Estados de puerto: Blocking, Listening, Learning, Forwarding
- Convergencia lenta (~30-50 segundos)

#### 2.3.2 Rapid Spanning Tree Protocol (RSTP)

**Ventajas de RSTP:**
- Convergencia rápida (<2 segundos)
- Compatibility con STP estándar
- Estados simplificados: Discarding, Learning, Forwarding
- Edge ports para hosts finales

**Configuración RSTP:**
```bash
# Bridge con RSTP
/interface bridge add name=rstp-bridge protocol=rstp

# Configurar bridge como root
/interface bridge set rstp-bridge priority=0x0000

# Puerto edge (conecta host, no switch)
/interface bridge port set [find interface=ether2] edge=yes

# Puerto point-to-point (conexión directa switch)
/interface bridge port set [find interface=ether3] point-to-point=yes
```

#### 2.3.3 Troubleshooting STP

**Comandos de diagnóstico:**
```bash
# Ver estado STP del bridge
/interface bridge print detail where name=rstp-bridge

# Estado STP por puerto
/interface bridge port print where bridge=rstp-bridge

# Monitor STP en tiempo real
/interface bridge monitor rstp-bridge

# Logs de STP
/system logging add topics=bridge action=memory
```

---

## 3. VLANs y Segmentación

### 3.1 Conceptos IEEE 802.1Q

#### 3.1.1 VLAN Fundamentals

**Teoría de Segmentación de Redes:**

**Evolución de la Segmentación:**
```
REDES PLANAS (Single Broadcast Domain):
[Hub] ── Todos los dispositivos
└─ PROBLEMA: Broadcasts saturan toda la red

SEGMENTACIÓN FÍSICA (Multiple Hubs/Switches):  
[Hub-A] ── Depto A    [Hub-B] ── Depto B
└─ PROBLEMA: Inflexibilidad, costo hardware

SEGMENTACIÓN LÓGICA (VLANs):
[Switch VLAN-aware] ─┬─ VLAN 10 ── Depto A
                     ├─ VLAN 20 ── Depto B  
                     └─ VLAN 30 ── Depto C
└─ SOLUCIÓN: Flexibilidad + eficiencia
```

**Beneficios Cuantificables de VLANs:**

**Reducción de Tráfico Broadcast:**
- Sin VLANs: Broadcast domain = entire switch (100 dispositivos)
- Con VLANs: 5 VLANs × 20 dispositivos = 5× reducción broadcast traffic
- Impacto: ~80% reducción en broadcast overhead

**Mejora de Seguridad:**
- Aislamiento natural Layer 2
- Attack surface reducida
- Lateral movement prevention
- Compliance boundary enforcement

**¿Qué es una VLAN?**
- Virtual Local Area Network
- Segmentación lógica de red física
- Aislamiento de dominios de broadcast
- Flexibilidad en diseño de red

**Componentes del VLAN tagging:**
- **VLAN ID:** 1-4094 (12 bits)
- **Priority:** 0-7 (3 bits, QoS)
- **Canonical Format:** 1 bit
- **EtherType:** 0x8100 (identifica 802.1Q)

**Conceptos de Frame Processing con VLANs:**

**Ingress Processing (Frame entrante):**
```
1. Frame llega sin VLAN tag (untagged)
   → Asignar PVID (Port VLAN ID) del puerto
   
2. Frame llega con VLAN tag (tagged)
   → Validar VLAN ID contra allowed VLANs del puerto
   → Procesar si válido, descartar si inválido
   
3. Frame classification
   → Determinar VLAN membership
   → Aplicar políticas por VLAN
```

**Egress Processing (Frame saliente):**
```
1. Consultar configuración puerto destino
   
2. Si puerto UNTAGGED para esta VLAN:
   → Remover VLAN tag antes de transmitir
   
3. Si puerto TAGGED para esta VLAN:  
   → Mantener/agregar VLAN tag apropiado
   
4. Si puerto no member de VLAN:
   → Descartar frame (no transmitir)
```

#### 3.1.2 Tipos de Puertos VLAN

**Teoría de VLAN Port Types:**

**Access Port Deep Dive:**

**Conceptos Fundamentales:**
- Puerto pertenece a exactamente UNA VLAN
- Host conectado no conoce concepto de VLAN
- Switch maneja todo el VLAN processing transparentemente
- Simplicidad para dispositivos finales

**Funcionamiento Access Port:**
```
HOST → [Access Port] → SWITCH PROCESSING

Ingress (Host → Switch):
1. Frame sin tag llega al puerto
2. Switch agrega VLAN tag interno (PVID del puerto)  
3. Frame procesado internamente con VLAN context

Egress (Switch → Host):  
1. Frame interno con VLAN tag
2. Switch remueve VLAN tag antes de transmitir
3. Host recibe frame estándar Ethernet
```

**Access Port Configuration:**
- **PVID (Port VLAN ID):** VLAN asignada al puerto
- **Frame-types:** admit-only-untagged-and-priority-tagged
- **VLAN Membership:** Untagged member de una sola VLAN

**Trunk Port Deep Dive:**

**Conceptos Fundamentales:**
- Puerto transporta múltiples VLANs simultáneamente
- Comunicación entre switches VLAN-aware
- Frames tagged con VLAN ID específico
- Eficiencia de enlaces inter-switch

**Funcionamiento Trunk Port:**
```
SWITCH-A → [Trunk Port] → SWITCH-B

Para cada VLAN transportada:
1. Frame interno con VLAN context
2. Si VLAN ≠ Native VLAN:
   → Agregar 802.1Q header con VLAN ID
3. Si VLAN = Native VLAN:
   → Transmitir untagged (compatibility)  
4. Switch remoto procesa según tag received
```

**Native VLAN Concept:**
```
PROBLEMA: Backward compatibility con switches no-VLAN-aware

SOLUCIÓN: Native VLAN
- Una VLAN designada como "native" en trunk
- Tráfico native VLAN enviado untagged
- Switch receptor asigna a native VLAN configurada
- Default: VLAN 1 como native

SECURITY IMPLICATION:
- Native VLAN debe ser unused/dummy VLAN
- Previene VLAN hopping attacks
- Best practice: Native VLAN ≠ user VLANs
```

**Trunk Port Configuration:**
- **Tagged VLANs:** Lista de VLANs transported con tags
- **Native VLAN:** VLAN enviada untagged (default VLAN 1)
- **Frame-types:** admit-only-vlan-tagged o admit-all
- **Allowed VLANs:** Control de qué VLANs se permiten

**Hybrid Port Concept:**

**Casos de Uso Hybrid:**
- Puerto que combina características access + trunk
- Algunas VLANs tagged, otras untagged
- Configuraciones especializadas
- Menos común, mayor complejidad

**Ejemplo Hybrid Configuration:**
```
Puerto Hybrid ejemplo:
- VLAN 10: Untagged (como access)
- VLAN 20, 30: Tagged (como trunk)  
- Host obtiene VLAN 10 automáticamente
- Trunk traffic para VLANs 20,30 pasa tagged
```

**Puerto Access (untagged):**
- Pertenece a una sola VLAN
- Tráfico untagged
- Host no conoce VLAN ID

**Puerto Trunk (tagged):**
- Transporta múltiples VLANs
- Tráfico tagged
- Comunicación entre switches

**Puerto Hybrid:**
- Combina access y trunk
- Algunas VLANs tagged, otras untagged

### 3.2 Implementación VLAN en MikroTik

#### 3.2.1 VLAN Configuration Method 1: Interface VLAN

**Método tradicional:**
```bash
# 1. Crear bridge
/interface bridge add name=sw-vlans

# 2. Agregar puertos físicos
/interface bridge port add bridge=sw-vlans interface=ether2
/interface bridge port add bridge=sw-vlans interface=ether3
/interface bridge port add bridge=sw-vlans interface=ether4

# 3. Crear interfaces VLAN
/interface vlan add name=vlan10 vlan-id=10 interface=sw-vlans
/interface vlan add name=vlan20 vlan-id=20 interface=sw-vlans

# 4. Asignar IPs a VLANs
/ip address add address=192.168.10.1/24 interface=vlan10
/ip address add address=192.168.20.1/24 interface=vlan20
```

#### 3.2.2 VLAN Configuration Method 2: Bridge VLAN Table

**Método recomendado (RouterOS v6.41+):**
```bash
# 1. Crear bridge con VLAN filtering
/interface bridge add name=bridge-vlans vlan-filtering=yes

# 2. Agregar puertos
/interface bridge port add bridge=bridge-vlans interface=ether2
/interface bridge port add bridge=bridge-vlans interface=ether3

# 3. Configurar VLAN table
/interface bridge vlan add bridge=bridge-vlans tagged=ether2,bridge-vlans untagged=ether3 vlan-ids=10
/interface bridge vlan add bridge=bridge-vlans tagged=ether2,bridge-vlans vlan-ids=20

# 4. Configurar port VLANs
/interface bridge port set [find interface=ether3] pvid=10

# 5. Crear VLAN interfaces para routing
/interface vlan add name=vlan10 vlan-id=10 interface=bridge-vlans
/interface vlan add name=vlan20 vlan-id=20 interface=bridge-vlans
```

#### 3.2.3 VLAN Port Types Configuration

**Puerto Access (untagged):**
```bash
# Puerto access VLAN 10
/interface bridge port set [find interface=ether3] pvid=10 frame-types=admit-only-untagged-and-priority-tagged

# Agregar VLAN untagged a tabla
/interface bridge vlan set [find vlan-ids=10] untagged=ether3,bridge-vlans
```

**Puerto Trunk (tagged):**
```bash
# Puerto trunk para VLANs 10,20,30
/interface bridge port set [find interface=ether2] frame-types=admit-only-vlan-tagged

# Agregar VLANs tagged a tabla
/interface bridge vlan add bridge=bridge-vlans tagged=ether2,bridge-vlans vlan-ids=10,20,30
```

### 3.3 Inter-VLAN Routing

**Fundamentos Teóricos del Inter-VLAN Routing:**

**El Problema del Aislamiento VLAN:**
VLANs crean dominios de broadcast separados, lo que significa:
- Dispositivos en diferentes VLANs no pueden comunicarse naturalmente
- Cada VLAN funciona como una red física separada
- Necesidad de routing Layer 3 para conectividad inter-VLAN

**Conceptos de Layer 3 Routing:**

**Routing vs Switching:**
```
SWITCHING (Layer 2):
- Decisiones basadas en MAC addresses
- Scope limitado al dominio de broadcast (VLAN)
- No modifica headers Layer 3
- Forwarding basado en bridge table

ROUTING (Layer 3):  
- Decisiones basadas en IP addresses
- Scope global (cross-subnet/VLAN)
- Modifica headers Layer 2 y Layer 3
- Forwarding basado en routing table
```

**Proceso de Inter-VLAN Routing:**
```
HOST-A (VLAN 10) quiere comunicar con HOST-B (VLAN 20):

1. HOST-A determina que destino no está en su subnet
2. HOST-A envía frame a su default gateway (router)
3. Router recibe frame en VLAN 10 interface
4. Router examina destino IP, consulta routing table
5. Router determina salida via VLAN 20 interface  
6. Router modifica:
   - Source MAC → Router VLAN 20 interface MAC
   - Destination MAC → HOST-B MAC (via ARP)
   - TTL decremented by 1
7. Router transmite frame en VLAN 20
8. HOST-B recibe frame
```

**Arquitecturas de Inter-VLAN Routing:**

**1. Router-on-a-Stick:**
```
[Switch] ──── Trunk Link ──── [Router]
   │                           │
   ├─ VLAN 10                  ├─ VLAN 10 subinterface
   ├─ VLAN 20                  ├─ VLAN 20 subinterface  
   └─ VLAN 30                  └─ VLAN 30 subinterface

VENTAJAS:
+ Single physical connection
+ Cost-effective para small networks
+ Centralized routing policy

DESVENTAJAS:  
- Single point of failure
- Bandwidth bottleneck en trunk link
- Higher latency (traffic hair-pinning)
```

**2. Layer 3 Switch (Recommended):**
```
[Layer 3 Switch]
├─ VLAN 10 ── SVI 192.168.10.1/24
├─ VLAN 20 ── SVI 192.168.20.1/24
└─ VLAN 30 ── SVI 192.168.30.1/24

VENTAJAS:
+ Wire-speed routing (hardware)  
+ No external router required
+ Low latency switching/routing
+ Scalable performance

MIKROTIK IMPLEMENTATION:
- VLAN interfaces act como SVIs
- Hardware routing cuando disponible
- Integrated switching + routing
```

**3. Distributed Routing:**
```
[Access Switch-1] ── [Core Router] ── [Access Switch-2]
    VLAN 10                              VLAN 20
    
VENTAJAS:
+ Maximum redundancy
+ Distributed forwarding
+ Scalability para large networks

DESVENTAJAS:
- Higher complexity
- More expensive
- Multiple routing domains
```

#### 3.3.1 Router-on-a-Stick

**Configuración básica:**
```bash
# Bridge con VLANs configuradas
/interface bridge add name=bridge-main vlan-filtering=yes

# VLAN interfaces para routing
/interface vlan add name=vlan10-gw vlan-id=10 interface=bridge-main
/interface vlan add name=vlan20-gw vlan-id=20 interface=bridge-main

# IPs gateway para cada VLAN
/ip address add address=192.168.10.1/24 interface=vlan10-gw
/ip address add address=192.168.20.1/24 interface=vlan20-gw

# Habilitar IP forwarding (default habilitado)
/ip settings set ip-forward=yes
```

#### 3.3.2 DHCP por VLAN

**Configuración DHCP separada:**
```bash
# DHCP pool para VLAN 10
/ip pool add name=pool-vlan10 ranges=192.168.10.100-192.168.10.200
/ip dhcp-server add name=dhcp-vlan10 interface=vlan10-gw lease-time=1d address-pool=pool-vlan10 disabled=no
/ip dhcp-server network add address=192.168.10.0/24 gateway=192.168.10.1 dns-server=8.8.8.8

# DHCP pool para VLAN 20
/ip pool add name=pool-vlan20 ranges=192.168.20.100-192.168.20.200
/ip dhcp-server add name=dhcp-vlan20 interface=vlan20-gw lease-time=1d address-pool=pool-vlan20 disabled=no
/ip dhcp-server network add address=192.168.20.0/24 gateway=192.168.20.1 dns-server=8.8.8.8
```

#### 3.3.3 Firewall entre VLANs

**Control de tráfico inter-VLAN:**
```bash
# Permitir tráfico establecido
/ip firewall filter add chain=forward action=accept connection-state=established,related

# Permitir VLAN 10 a Internet, denegar a VLAN 20
/ip firewall filter add chain=forward action=accept src-address=192.168.10.0/24 out-interface=ether1
/ip firewall filter add chain=forward action=drop src-address=192.168.10.0/24 dst-address=192.168.20.0/24

# VLAN 20 solo tráfico local
/ip firewall filter add chain=forward action=drop src-address=192.168.20.0/24 out-interface=ether1

# Drop todo lo demás
/ip firewall filter add chain=forward action=drop
```

---

## 4. Configuraciones Avanzadas

### 4.1 Bridge Filtering

#### 4.1.1 MAC Address Filtering

```bash
# Denegar MAC específica
/interface bridge filter add chain=forward action=drop src-mac-address=aa:bb:cc:dd:ee:ff

# Permitir solo MACs específicas en puerto
/interface bridge filter add chain=input action=accept in-interface=ether3 src-mac-address=11:22:33:44:55:66
/interface bridge filter add chain=input action=drop in-interface=ether3
```

#### 4.1.2 Protocol Filtering

```bash
# Bloquear NetBIOS en bridge
/interface bridge filter add chain=forward action=drop protocol=udp dst-port=137-139

# Permitir solo HTTP/HTTPS
/interface bridge filter add chain=forward action=accept protocol=tcp dst-port=80,443
/interface bridge filter add chain=forward action=drop protocol=tcp
```

### 4.2 VLAN Security

#### 4.2.1 VLAN Hopping Prevention

```bash
# Configurar frame-types correctamente
/interface bridge port set [find interface=ether3] frame-types=admit-only-untagged-and-priority-tagged

# No permitir VLAN 1 nativa
/interface bridge port set [find interface=ether2] frame-types=admit-only-vlan-tagged

# PVID diferente a 1
/interface bridge port set [find interface=ether3] pvid=100
```

#### 4.2.2 Port Security

```bash
# Limitar MACs aprendidas por puerto
/interface bridge port set [find interface=ether3] learn=yes multicast-router=disabled

# Deshabilitar learning en puertos específicos
/interface bridge port set [find interface=ether4] learn=no

# Static MAC entries
/interface bridge host add bridge=bridge1 mac-address=aa:bb:cc:dd:ee:ff interface=ether3
```

---

## 5. Monitoreo y Troubleshooting

### 5.1 Herramientas de Diagnóstico

#### 5.1.1 Bridge Monitoring

```bash
# Estado del bridge
/interface bridge print stats

# Tabla MAC en tiempo real
/interface bridge host print

# Monitor STP
/interface bridge monitor bridge1

# Estadísticas por puerto
/interface bridge port print stats
```

#### 5.1.2 VLAN Diagnostics

```bash
# Ver configuración VLAN table
/interface bridge vlan print

# Monitor tráfico por VLAN
/tool traffic-monitor interface=vlan10

# Packet sniffer en VLAN
/tool sniffer start interface=vlan10 filter-stream=yes
```

#### 5.1.3 Performance Analysis

```bash
# CPU usage del bridge
/system resource cpu print

# Interface load
/interface monitor-traffic interface=bridge1

# Bandwidth test entre VLANs
/tool bandwidth-test direction=both address=192.168.20.10 local-address=192.168.10.1
```

### 5.2 Troubleshooting Común

#### 5.2.1 Problemas de Conectividad

**No hay comunicación entre VLANs:**
1. Verificar VLAN table configuration
2. Confirmar interface VLAN creadas
3. Verificar IP routing habilitado
4. Revisar firewall rules

**VLAN tagging no funciona:**
1. Verificar frame-types en puertos
2. Confirmar VLAN IDs coinciden
3. Revisar bridge VLAN filtering
4. Verificar pvid en access ports

#### 5.2.2 Performance Issues

**Switching lento:**
1. Verificar hardware offload habilitado
2. Confirmar CPU no saturado
3. Revisar MTU mismatches
4. Verificar duplex/speed settings

### 5.3 Best Practices

#### 5.3.1 Diseño de Red

1. **Planificación VLAN:**
   - Documentar VLAN plan
   - Reservar rangos para crecimiento
   - Usar VLAN names descriptivas

2. **Seguridad:**
   - No usar VLAN 1 para tráfico
   - Implementar frame-types correctos
   - Configurar port security

3. **Performance:**
   - Habilitar hardware switching
   - Configurar STP edge ports
   - Monitor bridge statistics

#### 5.3.2 Documentación

```bash
# Export configuración
/export file=vlan-config

# Backup con descripción
/system backup save name=vlans-$(date)

# Comentarios en configuración
/interface vlan add name=vlan10 vlan-id=10 interface=bridge1 comment="Administracion"
```

---

## 6. Ejemplos Prácticos

### 6.1 Escenario Empresarial Básico

**Requerimientos:**
- VLAN 10: Administración (192.168.10.0/24)
- VLAN 20: Usuarios (192.168.20.0/24)  
- VLAN 30: Guest (192.168.30.0/24)
- Inter-VLAN routing con restricciones

**Configuración completa:**
```bash
# 1. Bridge principal
/interface bridge add name=sw-empresa vlan-filtering=yes

# 2. Puertos físicos
/interface bridge port add bridge=sw-empresa interface=ether2 comment="Trunk-Switch2"
/interface bridge port add bridge=sw-empresa interface=ether3 comment="Admin-PC"
/interface bridge port add bridge=sw-empresa interface=ether4 comment="User-PC"

# 3. VLAN Table
/interface bridge vlan add bridge=sw-empresa tagged=ether2,sw-empresa untagged=ether3 vlan-ids=10
/interface bridge vlan add bridge=sw-empresa tagged=ether2,sw-empresa untagged=ether4 vlan-ids=20
/interface bridge vlan add bridge=sw-empresa tagged=ether2,sw-empresa vlan-ids=30

# 4. Port VLANs
/interface bridge port set [find interface=ether3] pvid=10 frame-types=admit-only-untagged-and-priority-tagged
/interface bridge port set [find interface=ether4] pvid=20 frame-types=admit-only-untagged-and-priority-tagged
/interface bridge port set [find interface=ether2] frame-types=admit-only-vlan-tagged

# 5. VLAN Interfaces
/interface vlan add name=admin-gw vlan-id=10 interface=sw-empresa
/interface vlan add name=users-gw vlan-id=20 interface=sw-empresa
/interface vlan add name=guest-gw vlan-id=30 interface=sw-empresa

# 6. IP Addressing
/ip address add address=192.168.10.1/24 interface=admin-gw
/ip address add address=192.168.20.1/24 interface=users-gw
/ip address add address=192.168.30.1/24 interface=guest-gw

# 7. DHCP Services
/ip pool add name=admin-pool ranges=192.168.10.100-192.168.10.200
/ip pool add name=users-pool ranges=192.168.20.100-192.168.20.200
/ip pool add name=guest-pool ranges=192.168.30.100-192.168.30.200

/ip dhcp-server add name=admin-dhcp interface=admin-gw address-pool=admin-pool lease-time=1d disabled=no
/ip dhcp-server add name=users-dhcp interface=users-gw address-pool=users-pool lease-time=8h disabled=no
/ip dhcp-server add name=guest-dhcp interface=guest-gw address-pool=guest-pool lease-time=1h disabled=no

/ip dhcp-server network add address=192.168.10.0/24 gateway=192.168.10.1 dns-server=192.168.10.1
/ip dhcp-server network add address=192.168.20.0/24 gateway=192.168.20.1 dns-server=192.168.20.1
/ip dhcp-server network add address=192.168.30.0/24 gateway=192.168.30.1 dns-server=192.168.30.1

# 8. Firewall Inter-VLAN
/ip firewall filter add chain=forward action=accept connection-state=established,related
/ip firewall filter add chain=forward action=accept src-address=192.168.10.0/24
/ip firewall filter add chain=forward action=accept src-address=192.168.20.0/24 dst-address=!192.168.10.0/24
/ip firewall filter add chain=forward action=drop src-address=192.168.30.0/24 dst-address=192.168.0.0/16
/ip firewall filter add chain=forward action=drop
```

## Comandos de Referencia Rápida

### Interfaces
```bash
/interface print                    # Ver todas las interfaces
/interface ethernet print          # Solo interfaces Ethernet
/interface ethernet monitor ether1 # Monitor interface específica
/interface disable ether2          # Deshabilitar interface
/interface enable ether2           # Habilitar interface
```

### Bridge
```bash
/interface bridge print                    # Ver bridges
/interface bridge port print              # Ver puertos bridge
/interface bridge host print              # Tabla MAC
/interface bridge monitor bridge1         # Estado STP
/interface bridge vlan print              # VLAN table
```

### VLAN
```bash
/interface vlan print               # Ver interfaces VLAN
/interface vlan add name=vlan10 vlan-id=10 interface=bridge1
/interface bridge port set [find interface=ether3] pvid=10
/interface bridge vlan add bridge=bridge1 tagged=ether2 untagged=ether3 vlan-ids=10
```

### Diagnóstico
```bash
/tool traffic-monitor interface=bridge1    # Monitor tráfico
/tool sniffer start interface=vlan10       # Captura de paquetes
/ping 192.168.10.1 interface=vlan20       # Ping desde VLAN específica
/tool bandwidth-test address=192.168.20.10 # Test ancho de banda
```