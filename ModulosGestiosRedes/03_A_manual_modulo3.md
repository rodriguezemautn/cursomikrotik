# Manual Completo - Módulo 3: Routing Estático y Dinámico

## Información del Módulo

**Duración:** 2 semanas (8 horas académicas)  
**Modalidad:** Híbrida (Presencial + Homework)  
**Prerequisitos:** Módulo 2 completado - Interfaces y Switching  

## Competencias a Desarrollar

Al finalizar este módulo, el estudiante será capaz de:

1. **Diseñar e implementar topologías de routing estático y dinámico según requerimientos de red**
2. **Optimizar el rendimiento de routing aplicando técnicas avanzadas de configuración**

---

## Fundamentos Teóricos de Routing

### Conceptos Fundamentales de Routing

#### El Problema del Routing en Redes

**Definición de Routing:**
El routing es el proceso de determinar el mejor camino para enviar paquetes de datos desde un origen hasta un destino a través de una red de routers interconectados.

**Diferencia entre Switching y Routing:**
```
SWITCHING (Layer 2):
├─ Decisiones basadas en MAC addresses
├─ Scope limitado al dominio de broadcast (VLAN)
├─ Forwarding table poblada por learning
└─ No modificación de headers Layer 3

ROUTING (Layer 3):
├─ Decisiones basadas en IP addresses
├─ Scope global (cross-network)
├─ Routing table poblada por protocolos
└─ Modificación de headers Layer 2 y Layer 3
```

**El Proceso de Routing Paso a Paso:**
```
CUANDO LLEGA UN PAQUETE IP:

1. PACKET RECEPTION:
   ├─ Router recibe frame en interface
   ├─ Verifica destination MAC = router MAC
   └─ Extrae paquete IP del frame

2. PACKET ANALYSIS:
   ├─ Examina destination IP address
   ├─ Decrementa TTL (Time To Live)
   ├─ Verifica TTL > 0 (descarta si TTL=0)
   └─ Calcula nuevo checksum IP

3. ROUTING TABLE LOOKUP:
   ├─ Busca longest prefix match
   ├─ Determina next-hop IP address
   ├─ Identifica outgoing interface
   └─ Si no hay ruta → ICMP unreachable

4. ARP RESOLUTION:
   ├─ Consulta ARP table para next-hop MAC
   ├─ Si no existe → envía ARP request
   └─ Espera ARP reply

5. FRAME CONSTRUCTION:
   ├─ Crea nuevo frame Layer 2
   ├─ Source MAC → outgoing interface MAC
   ├─ Dest MAC → next-hop MAC
   └─ Payload → paquete IP modificado

6. PACKET FORWARDING:
   └─ Transmite frame por outgoing interface
```

#### Teoría de Grafos Aplicada a Routing

**Representación de Red como Grafo:**
Una red puede modelarse como un grafo dirigido donde:
- **Nodos (V):** Routers o redes
- **Aristas (E):** Enlaces de comunicación
- **Pesos (W):** Métricas de costo (bandwidth, delay, reliability)

**Problema del Camino Más Corto:**
El routing se reduce al problema matemático de encontrar el camino de menor costo entre dos nodos en un grafo ponderado.

**Algoritmos Fundamentales:**

**1. Algoritmo de Dijkstra (Link State):**
```
USADO POR: OSPF, IS-IS

FUNCIONAMIENTO:
1. Inicialización:
   ├─ Distancia a origen = 0
   ├─ Distancia a todos los demás = ∞
   └─ Conjunto de nodos no visitados

2. Iteración:
   ├─ Seleccionar nodo con menor distancia
   ├─ Marcar como visitado
   ├─ Actualizar distancias a vecinos
   └─ Repetir hasta visitar todos

3. Resultado:
   └─ Shortest Path Tree desde origen
```

**2. Algoritmo Bellman-Ford (Distance Vector):**
```
USADO POR: RIP, EIGRP (variación)

FUNCIONAMIENTO:
1. Inicialización:
   ├─ Distancia a sí mismo = 0
   ├─ Distancia a todos los demás = ∞
   └─ Vector de distancias inicial

2. Iteración (n-1 veces):
   ├─ Para cada arista (u,v):
   ├─ Si dist[u] + peso(u,v) < dist[v]:
   ├─   dist[v] = dist[u] + peso(u,v)
   └─   predecesor[v] = u

3. Detección de loops negativos:
   └─ Una iteración adicional detecta cycles
```

### Arquitectura de Routing Tables

#### Estructura de la Tabla de Rutas

**Componentes de una Entrada de Ruta:**
```
ROUTING TABLE ENTRY:
┌─────────────────┬─────────────────┬─────────────────┬─────────────────┐
│ Destination     │ Prefix Length   │ Next Hop        │ Outgoing        │
│ Network         │ (Subnet Mask)   │ IP Address      │ Interface       │
├─────────────────┼─────────────────┼─────────────────┼─────────────────┤
│ Administrative  │ Metric          │ Route Type      │ Age/Timeout     │
│ Distance        │ (Cost)          │ (Static/Dynamic)│                 │
└─────────────────┴─────────────────┴─────────────────┴─────────────────┘
```

**Administrative Distance (AD):**
Medida de confiabilidad de la fuente de routing information:
```
ADMINISTRATIVE DISTANCES (RouterOS):
├─ Directly Connected: 0
├─ Static Routes: 1
├─ OSPF: 110
├─ RIP: 120
├─ External OSPF: 110
├─ BGP (eBGP): 20
├─ BGP (iBGP): 200
└─ Unknown: 255
```

**Longest Prefix Match Algorithm:**
```
EJEMPLO DE LOOKUP:
Destination IP: 192.168.10.50

TABLA DE RUTAS:
├─ 0.0.0.0/0 → Gateway A (default route)
├─ 192.168.0.0/16 → Gateway B (/16 prefix)
├─ 192.168.10.0/24 → Gateway C (/24 prefix)
└─ 192.168.10.48/28 → Gateway D (/28 prefix)

LONGEST MATCH: 192.168.10.48/28 (/28 = más específico)
SELECTED ROUTE: Gateway D
```

#### Route Selection Process

**Multi-step Route Selection:**
```
ROUTE SELECTION CRITERIA (orden de precedencia):

1. LONGEST PREFIX MATCH:
   └─ Ruta más específica (mayor prefix length)

2. ADMINISTRATIVE DISTANCE:
   └─ Menor AD = más confiable

3. METRIC COMPARISON:
   └─ Menor metric = mejor ruta

4. EQUAL COST MULTI-PATH (ECMP):
   ├─ Si múltiples rutas tienen mismo cost
   ├─ Load balancing entre rutas
   └─ Round-robin o hash-based distribution
```

---

## 1. Routing Estático

### 1.1 Fundamentos del Routing Estático

#### Conceptos Teóricos

**Definición:**
El routing estático consiste en configurar manualmente las entradas de la tabla de rutas. Cada ruta se define explícitamente por el administrador de red.

**Características del Routing Estático:**
```
VENTAJAS:
✓ Control total sobre path selection
✓ No overhead de protocolos dinámicos
✓ Behavior predecible y determinístico  
✓ Seguridad (no intercambio de información)
✓ Recursos mínimos (CPU, memoria, bandwidth)

DESVENTAJAS:
✗ Escalabilidad limitada (administración manual)
✗ No adaptación automática a cambios de topology
✗ Single point of failure (ruta fija)
✗ Maintenance overhead alto en redes grandes
✗ Configuración propensa a errores humanos
```

#### Tipos de Rutas Estáticas

**1. Rutas Específicas:**
```
DEFINICIÓN: Ruta hacia red específica
EJEMPLO: 192.168.10.0/24 → 10.0.0.2
USO: Control granular de routing
```

**2. Ruta por Defecto (Default Route):**
```
DEFINICIÓN: 0.0.0.0/0 → Gateway
PROPÓSITO: Catch-all para destinos no específicos
USO TÍPICO: Conexión a Internet
```

**3. Rutas de Host:**
```
DEFINICIÓN: /32 prefix (host específico)
EJEMPLO: 192.168.10.50/32 → 10.0.0.3
USO: Routing específico a host individual
```

**4. Rutas Flotantes (Floating Routes):**
```
DEFINICIÓN: Rutas backup con higher AD
EJEMPLO: 
├─ Primary: 0.0.0.0/0 → ISP1 (AD=1)
└─ Backup: 0.0.0.0/0 → ISP2 (AD=10)
COMPORTAMIENTO: Backup activa solo si primary falla
```

### 1.2 Configuración de Rutas Estáticas en RouterOS

#### Sintaxis Básica

**Comando Base:**
```bash
/ip route add dst-address=NETWORK/PREFIX gateway=NEXT_HOP
```

**Parámetros Importantes:**
```bash
PARÁMETROS PRINCIPALES:
├─ dst-address: Red destino (CIDR notation)
├─ gateway: Next-hop IP o outgoing interface
├─ distance: Administrative distance (default=1)
├─ scope: Scope de la ruta (default=30)
├─ target-scope: Target scope (default=10)
└─ comment: Descripción de la ruta
```

#### Ejemplos Prácticos

**Ruta Estática Básica:**
```bash
# Ruta hacia red específica:
/ip route add dst-address=192.168.20.0/24 gateway=10.0.0.2 comment="Red sucursal A"
```

**Ruta por Defecto:**
```bash
# Default route hacia Internet:
/ip route add dst-address=0.0.0.0/0 gateway=203.0.113.1 comment="Internet via ISP"
```

**Múltiples Rutas para Load Balancing:**
```bash
# ECMP (Equal Cost Multi-Path):
/ip route add dst-address=0.0.0.0/0 gateway=203.0.113.1 distance=1 comment="ISP1"
/ip route add dst-address=0.0.0.0/0 gateway=203.0.113.5 distance=1 comment="ISP2"
```

**Floating Route para Redundancia:**
```bash
# Primary route:
/ip route add dst-address=0.0.0.0/0 gateway=203.0.113.1 distance=1 comment="Primary ISP"
# Backup route:
/ip route add dst-address=0.0.0.0/0 gateway=203.0.113.5 distance=10 comment="Backup ISP"
```

### 1.3 Balanceamento de Carga Estático

#### Teoría del Load Balancing

**Equal Cost Multi-Path (ECMP):**
Cuando múltiples rutas hacia el mismo destino tienen el mismo costo, el router puede distribuir tráfico entre ellas.

**Algoritmos de Load Balancing:**

**1. Per-Packet Load Balancing:**
```
DESCRIPCIÓN: Cada paquete toma ruta diferente
VENTAJAS: Perfect load distribution
DESVENTAJAS: Packet reordering, different latencies
USO: Rarely used in practice
```

**2. Per-Flow Load Balancing:**
```
DESCRIPCIÓN: Mismo flow siempre usa misma ruta
BASE: Hash de src IP, dst IP, src port, dst port
VENTAJAS: Mantiene order dentro del flow
USO: Most common approach
```

#### Implementación en RouterOS

**PCC (Per Connection Classifier):**
RouterOS usa PCC para intelligent load balancing:

```bash
# Marcar conexiones para balanceamento:
/ip firewall mangle add chain=input in-interface=ether1 action=mark-connection new-connection-mark=ISP1_conn per-connection-classifier=both-addresses:2/0
/ip firewall mangle add chain=input in-interface=ether1 action=mark-connection new-connection-mark=ISP2_conn per-connection-classifier=both-addresses:2/1

# Rutas específicas por conexión:
/ip route add dst-address=0.0.0.0/0 gateway=203.0.113.1 routing-mark=ISP1_conn
/ip route add dst-address=0.0.0.0/0 gateway=203.0.113.5 routing-mark=ISP2_conn
```

---

## 2. Protocolos de Routing Dinámico

### 2.1 Clasificación de Protocolos de Routing

#### Taxonomía de Protocolos

**Por Algoritmo:**
```
DISTANCE VECTOR:
├─ Algoritmo: Bellman-Ford
├─ Información: Distance + Direction
├─ Convergencia: Lenta
├─ Ejemplos: RIP, EIGRP
└─ Problema: Count-to-infinity

LINK STATE:
├─ Algoritmo: Dijkstra (SPF)
├─ Información: Complete topology
├─ Convergencia: Rápida
├─ Ejemplos: OSPF, IS-IS
└─ Overhead: Mayor memoria y CPU
```

**Por Scope:**
```
INTERIOR GATEWAY PROTOCOLS (IGP):
├─ Scope: Dentro de Autonomous System
├─ Optimization: Performance, convergencia
├─ Ejemplos: OSPF, EIGRP, IS-IS, RIP
└─ Métricas: Bandwidth, delay, hop count

EXTERIOR GATEWAY PROTOCOLS (EGP):
├─ Scope: Entre Autonomous Systems
├─ Optimization: Policy, control
├─ Ejemplos: BGP
└─ Métricas: AS-path, policies
```

#### Métricas de Routing

**Tipos de Métricas:**

**1. Hop Count (RIP):**
```
DESCRIPCIÓN: Número de saltos hasta destino
RANGE: 1-15 (16 = infinito en RIP)
VENTAJAS: Simple, fácil entendimiento
DESVENTAJAS: No considera bandwidth, delay
```

**2. Cost (OSPF):**
```
DESCRIPCIÓN: Basado en bandwidth del enlace
CÁLCULO: Cost = Reference Bandwidth / Interface Bandwidth
REFERENCE: 100 Mbps (default), configurable
EJEMPLO: 
├─ 10 Mbps link = 100/10 = 10
├─ 100 Mbps link = 100/100 = 1
└─ 1 Gbps link = 100/1000 = 1 (minimum)
```

**3. Composite Metric (EIGRP):**
```
DESCRIPCIÓN: Múltiples factores combinados
FACTORES: Bandwidth, delay, reliability, load
FÓRMULA: K1*BW + K2*BW/(256-Load) + K3*Delay
DEFAULT K-values: K1=1, K2=0, K3=1, K4=0, K5=0
```

### 2.2 OSPF (Open Shortest Path First)

#### Fundamentos Teóricos de OSPF

**Características Principales:**
```
PROTOCOLO: Link State
ALGORITMO: Dijkstra Shortest Path First
STANDARD: RFC 2328 (OSPFv2), RFC 5340 (OSPFv3)
MÉTRICA: Cost (basada en bandwidth)
CONVERGENCIA: Rápida (sub-segundo con optimizaciones)
ESCALABILIDAD: Jerárquica con áreas
```

**Arquitectura Jerárquica:**
```
BACKBONE AREA (Area 0):
├─ Core de la topology OSPF
├─ Todas las demás áreas deben conectar a backbone
├─ ABRs (Area Border Routers) conectan áreas
└─ Distribución de LSAs entre áreas

ÁREAS NO-BACKBONE:
├─ Reduce flooding scope de LSAs
├─ Hierarchical summarization
├─ Faster convergence (local changes)
└─ Resource optimization
```

#### OSPF Packet Types

**1. Hello Packets (Type 1):**
```
PROPÓSITO: Neighbor discovery y maintenance
FRECUENCIA: Cada 10 segundos (LAN), 30 segundos (WAN)
CONTENIDO:
├─ Router ID
├─ Area ID
├─ Network mask
├─ Hello/Dead intervals
├─ DR/BDR election info
└─ Neighbor list
```

**2. Database Description (Type 2):**
```
PROPÓSITO: Database synchronization
USO: Durante neighbor adjacency establishment
CONTENIDO:
├─ LSA headers (resumen de topology database)
├─ Sequence numbers
└─ Checksum information
```

**3. Link State Request (Type 3):**
```
PROPÓSITO: Solicitar LSAs específicas
USO: Cuando router needs specific topology info
```

**4. Link State Update (Type 4):**
```
PROPÓSITO: Transportar LSAs
CONTENIDO: Una o múltiples LSAs
FLOODING: Reliable distribution por toda el área
```

**5. Link State Acknowledgment (Type 5):**
```
PROPÓSITO: Confirmar recepción de LSUs
RELIABILITY: Asegura reliable flooding
```

#### OSPF Neighbor States

**Proceso de Adjacency Establishment:**
```
1. DOWN:
   └─ No communication con neighbor

2. INIT:
   ├─ Hello packet recibido de neighbor
   └─ Router no ve su propio Router ID en neighbor list

3. 2-WAY:
   ├─ Bidirectional communication established
   ├─ Router ve su Router ID en neighbor's hello
   └─ DR/BDR election occurs en este estado

4. ExSTART:
   ├─ Master/slave relationship established
   ├─ Database exchange about to begin
   └─ Initial sequence numbers determined

5. EXCHANGE:
   ├─ Database Description packets exchanged
   ├─ Each router describes its link state database
   └─ LSA headers compared

6. LOADING:
   ├─ Missing LSAs requested with Link State Requests
   ├─ Link State Updates sent with missing LSAs
   └─ Database synchronization in progress

7. FULL:
   ├─ Databases fully synchronized
   ├─ Adjacency complete
   └─ SPF calculation can include this neighbor
```

#### OSPF LSA Types

**Type 1 - Router LSA:**
```
GENERADO POR: Cada router en área
SCOPE: Dentro del área únicamente
CONTENIDO:
├─ Router ID
├─ Links directamente conectados
├─ Link types (point-to-point, transit, stub)
└─ Cost de cada link
```

**Type 2 - Network LSA:**
```
GENERADO POR: Designated Router en multi-access networks
SCOPE: Dentro del área únicamente
CONTENIDO:
├─ Subnet mask de la network
├─ List de routers attached a la network
└─ DR que generó el LSA
```

**Type 3 - Summary LSA:**
```
GENERADO POR: Area Border Routers (ABRs)
SCOPE: Entre áreas (no intra-área)
CONTENIDO:
├─ Network advertisements desde otras áreas
├─ Cost to reach external networks
└─ Route summarization information
```

**Type 4 - ASBR Summary LSA:**
```
GENERADO POR: ABRs
PROPÓSITO: Advertise location of ASBR
CONTENIDO: Cost to reach ASBR en different area
```

**Type 5 - External LSA:**
```
GENERADO POR: Autonomous System Boundary Routers (ASBRs)
SCOPE: Flooded throughout OSPF domain
CONTENIDO:
├─ External routes (non-OSPF routes)
├─ External metric type (E1 or E2)
└─ Forwarding address
```

#### OSPF Area Types

**Standard Area:**
```
CARACTERÍSTICAS:
├─ Acepta todos los LSA types
├─ Complete topology information
├─ Full routing table
└─ Highest memory y CPU requirements
```

**Stub Area:**
```
CARACTERÍSTICAS:
├─ No Type 5 LSAs (external routes)
├─ ABR injects default route
├─ Reduced memory requirements
└─ Suitable para areas sin external connectivity
```

**Totally Stubby Area:**
```
CARACTERÍSTICAS:
├─ No Type 3, 4, o 5 LSAs
├─ Only intra-area routes + default
├─ Minimum memory requirements
└─ Cisco proprietary extension
```

**Not-So-Stubby Area (NSSA):**
```
CARACTERÍSTICAS:
├─ Type 7 LSAs para external routes
├─ Type 7 translated to Type 5 at ABR
├─ Allows limited external connectivity
└─ Useful para areas con few external routes
```

### 2.3 BGP (Border Gateway Protocol)

#### Fundamentos BGP

**Características Principales:**
```
PROTOCOLO: Path Vector
STANDARD: RFC 4271 (BGP-4)
PROPÓSITO: Inter-domain routing
MÉTRICA: AS-Path + attributes
CONVERGENCIA: Policy-based, no automática
ESCALABILIDAD: Global Internet (800k+ routes)
```

**Diferencias BGP vs IGPs:**
```
BGP (EGP):                    IGP (OSPF, EIGRP):
├─ Policy-based routing       ├─ Performance-based routing
├─ AS-Path loop prevention    ├─ Metric-based optimization  
├─ Slow convergence (minutes) ├─ Fast convergence (seconds)
├─ Complex attribute system   ├─ Simple metrics
├─ Manual summarization       ├─ Automatic summarization
└─ Administrative control     └─ Technical optimization
```

#### BGP Session Types

**External BGP (eBGP):**
```
DEFINICIÓN: BGP session entre different AS
CARACTERÍSTICAS:
├─ Direct connection típicamente required
├─ AD = 20 (higher preference)
├─ Next-hop modifications
├─ AS-Path prepending
└─ Policy enforcement at AS boundaries
```

**Internal BGP (iBGP):**
```
DEFINICIÓN: BGP session dentro del mismo AS
CARACTERÍSTICAS:
├─ Loopback-to-loopback sessions
├─ AD = 200 (lower preference than IGP)
├─ No AS-Path modification
├─ Split-horizon rule (no iBGP-to-iBGP advertising)
└─ Route Reflectors o Confederations para scaling
```

#### BGP Attributes

**Well-Known Mandatory:**
```
1. ORIGIN:
   ├─ IGP (i): Route originated from IGP
   ├─ EGP (e): Route learned via EGP
   └─ Incomplete (?): Route redistributed

2. AS_PATH:
   ├─ List de AS numbers en path to destination
   ├─ Loop prevention mechanism
   └─ Shorter path preferred

3. NEXT_HOP:
   ├─ IP address de next router en path
   ├─ Modified en eBGP boundaries
   └─ Unchanged en iBGP (requires IGP reachability)
```

**Well-Known Discretionary:**
```
1. LOCAL_PREFERENCE:
   ├─ Local significance within AS
   ├─ Higher values preferred
   └─ Used para outbound traffic engineering

2. ATOMIC_AGGREGATE:
   └─ Indicates route summarization occurred
```

**Optional Transitive:**
```
1. AGGREGATOR:
   └─ AS y Router ID que performed aggregation

2. COMMUNITY:
   ├─ 32-bit values para route tagging
   ├─ Policy application
   └─ Well-known communities (NO_EXPORT, NO_ADVERTISE)
```

**Optional Non-Transitive:**
```
1. MULTI_EXIT_DISC (MED):
   ├─ Hint to external AS sobre preferred entry point
   ├─ Lower values preferred
   └─ Compared only entre routes from same AS

2. CLUSTER_LIST:
   └─ Route Reflector path tracking
```

#### BGP Path Selection Algorithm

**BGP Decision Process (orden de precedencia):**
```
1. HIGHEST WEIGHT (Cisco proprietary)
2. HIGHEST LOCAL_PREFERENCE  
3. LOCALLY ORIGINATED routes
4. SHORTEST AS_PATH
5. LOWEST ORIGIN code (IGP < EGP < Incomplete)
6. LOWEST MED (cuando comparing routes from same AS)
7. eBGP over iBGP
8. LOWEST IGP metric to NEXT_HOP
9. OLDEST route (for stability)
10. LOWEST Router ID
11. SHORTEST cluster list (Route Reflectors)
12. LOWEST neighbor address
```

### 2.4 RIP (Routing Information Protocol)

#### RIP Fundamentals

**Características:**
```
PROTOCOLO: Distance Vector
ALGORITMO: Bellman-Ford
MÉTRICA: Hop count (1-15, 16=infinite)
UPDATES: Periodic (30 seconds)
CONVERGENCIA: Lenta (minutes)
USO: Legacy networks, simple topologies
```

**RIP Versions:**

**RIPv1:**
```
CARACTERÍSTICAS:
├─ Classful routing (no subnet mask)
├─ Broadcast updates (255.255.255.255)
├─ No authentication
└─ Limited scalability
```

**RIPv2:**
```
MEJORAS:
├─ Classless routing (CIDR support)
├─ Multicast updates (224.0.0.9)  
├─ Authentication support
├─ Route tags
└─ Next-hop specification
```

#### RIP Problems y Solutions

**Count-to-Infinity Problem:**
```
PROBLEMA:
├─ Network failure causes gradual increase de hop count
├─ Routing loops durante convergence
└─ Slow convergence to infinity (16 hops)

SOLUTIONS:
├─ Split Horizon: Don't advertise route back to source
├─ Poison Reverse: Advertise unreachable routes as 16
├─ Triggered Updates: Immediate updates on changes
└─ Hold-down timers: Prevent rapid flapping
```

---

## 3. Optimización de Routing

### 3.1 Route Filtering

#### Conceptos de Route Filtering

**Propósitos del Filtering:**
```
SECURITY:
├─ Prevent routing table pollution
├─ Deny access to sensitive networks
└─ Implement routing policies

PERFORMANCE:
├─ Reduce routing table size
├─ Decrease convergence time
├─ Optimize memory usage
└─ Minimize update overhead

POLICY:
├─ Traffic engineering
├─ Provider/customer relationships
└─ Administrative boundaries
```

#### Filtering Mechanisms en RouterOS

**Access Lists (Prefix Lists):**
```bash
# Crear prefix list:
/routing filter add chain=ospf-out prefix=192.168.0.0/16 prefix-length=16-24 action=accept
/routing filter add chain=ospf-out action=discard

# Aplicar a OSPF:
/routing ospf instance set default out-filter=ospf-out
```

**Route Maps:**
```bash
# Route map para BGP:
/routing filter add chain=bgp-out prefix=10.0.0.0/8 action=discard comment="Block private networks"
/routing bgp peer set peer1 out-filter=bgp-out
```

### 3.2 Route Redistribution

#### Teoría de Redistribution

**Definición:**
Process de importing routes desde un routing protocol a otro, permitiendo communication entre dominios de routing diferentes.

**Challenges de Redistribution:**
```
METRIC INCOMPATIBILITY:
├─ Different protocols use different metrics
├─ Need metric conversion/assignment
└─ Default metrics may be suboptimal

ROUTING LOOPS:
├─ Mutual redistribution can cause loops
├─ Administrative distance differences
└─ Route feedback prevention needed

CONVERGENCE ISSUES:
├─ Different convergence characteristics  
├─ Route flapping
└─ Sub-optimal path selection
```

#### Redistribution Best Practices

**Metric Assignment:**
```bash
# OSPF to BGP redistribution:
/routing ospf instance set default redistribute-connected=as-type-1 redistribute-static=as-type-2

# BGP to OSPF redistribution:
/routing bgp instance set default redistribute-connected=yes redistribute-ospf=yes
```

**Route Tagging:**
```bash
# Tag routes durante redistribution:
/routing filter add chain=ospf-to-bgp action=accept set-bgp-communities=65001:100

# Filter based on tags:
/routing filter add chain=bgp-in bgp-communities=65001:100 action=discard
```

### 3.3 Policy-Based Routing (PBR)

#### Conceptos de PBR

**Definición:**
Policy-Based Routing permite routing decisions basadas en políticas definidas por el administrador, no solo en destination address.

**Criterios de Policy:**
```
SOURCE-BASED:
├─ Source IP address
├─ Source network
└─ User/group identification

APPLICATION-BASED:
├─ Protocol type
├─ Port numbers  
├─ DSCP markings
└─ Packet size

TIME-BASED:
├─ Time of day
├─ Day of week
└─ Maintenance windows
```

#### Implementación PBR en RouterOS

**Routing Marks:**
```bash
# Marcar tráfico por source:
/ip firewall mangle add chain=prerouting src-address=192.168.10.0/24 action=mark-routing new-routing-mark=admin-traffic

# Routing table específica:
/ip route add dst-address=0.0.0.0/0 gateway=ISP1-gateway routing-mark=admin-traffic

# Marcar por aplicación:
/ip firewall mangle add chain=prerouting protocol=tcp dst-port=443 action=mark-routing new-routing-mark=https-traffic
```

**Advanced PBR Scenarios:**
```bash
# Load balancing por usuario:
/ip firewall mangle add chain=prerouting src-address=192.168.1.0/24 per-connection-classifier=both-addresses:2/0 action=mark-routing new-routing-mark=ISP1
/ip firewall mangle add chain=prerouting src-address=192.168.1.0/24 per-connection-classifier=both-addresses:2/1 action=mark-routing new-routing-mark=ISP2

# Time-based routing:
/system scheduler add name=business-hours on-event="/ip route enable [find comment=\"business-route\"]" start-time=08:00:00 interval=1d
/system scheduler add name=after-hours on-event="/ip route disable [find comment=\"business-route\"]" start-time=18:00:00 interval=1d
```

---

## 4. Configuraciones Avanzadas y Optimización

### 4.1 OSPF Advanced Configuration

#### Multi-Area OSPF Design

**Area Design Principles:**
```
BACKBONE AREA (Area 0):
├─ Central hub de todas las communications
├─ Should be contiguous
├─ Contains core infrastructure
└─ ABRs connect all other areas

REGULAR AREAS:
├─ Connected to backbone via ABRs
├─ Local traffic stays within area
├─ Summary LSAs provide reachability
└─ Optimal size: 50-100 routers
```

**RouterOS Multi-Area Configuration:**
```bash
# Configure OSPF instance:
/routing ospf instance set default router-id=1.1.1.1

# Configure backbone area:
/routing ospf area add name=backbone area-id=0.0.0.0 instance=default

# Configure regular area:
/routing ospf area add name=branch area-id=0.0.0.1 instance=default type=default

# Assign interfaces to areas:
/routing ospf interface add interface=ether1 area=backbone cost=10
/routing ospf interface add interface=ether2 area=branch cost=100
```

#### OSPF Optimization Techniques

**LSA Throttling:**
```bash
# Control LSA generation frequency:
/routing ospf instance set default spf-delay=1s spf-max-delay=10s
```

**Summarization:**
```bash
# Area summarization at ABR:
/routing ospf area range add area=branch prefix=192.168.0.0/16 advertise=yes cost=100
```

**Stub Area Configuration:**
```bash
# Configure stub area:
/routing ospf area set branch type=stub default-cost=10

# Totally stubby area (RouterOS extension):
/routing ospf area set branch type=stub no-summaries=yes
```

### 4.2 BGP Advanced Configuration

#### iBGP Scaling Solutions

**Route Reflector Configuration:**
```bash
# Configure Route Reflector:
/routing bgp instance set default as=65001 router-id=1.1.1.1

# RR client configuration:
/routing bgp peer add remote-address=10.0.0.2 remote-as=65001 route-reflect=yes

# RR non-client configuration:  
/routing bgp peer add remote-address=10.0.0.3 remote-as=65001
```

**BGP Communities:**
```bash
# Set communities on outbound routes:
/routing filter add chain=bgp-out action=accept set-bgp-communities=65001:100,65001:200

# Filter based on communities:
/routing filter add chain=bgp-in bgp-communities=65001:100 action=accept
/routing filter add chain=bgp-in action=discard
```

#### BGP Traffic Engineering

**Local Preference Manipulation:**
```bash
# Prefer specific path outbound:
/routing filter add chain=bgp-in prefix=0.0.0.0/0 action=accept set-bgp-local-pref=150

# AS-Path Prepending för inbound traffic engineering:
/routing filter add chain=bgp-out action=accept set-bgp-prepend=3
```

**MED (Multi-Exit Discriminator):**
```bash
# Set MED to influence inbound traffic:
/routing filter add chain=bgp-out prefix=192.168.0.0/16 action=accept set-bgp-med=100
```

### 4.3 Performance Monitoring y Troubleshooting

#### Routing Performance Metrics

**Convergence Time Measurement:**
```bash
# Monitor OSPF convergence:
/log print where topics~"ospf"

# BGP session monitoring:
/routing bgp monitor peer1

# Route table changes:
/ip route monitor
```

**Memory y CPU Optimization:**
```bash
# Monitor routing process resource usage:
/system resource print

# OSPF database size:
/routing ospf lsa print count-only

# BGP table size:
/routing bgp advertisements print count-only where bgp-instance=default
```

#### Troubleshooting Common Issues

**OSPF Adjacency Problems:**
```bash
# Check neighbor states:
/routing ospf neighbor print

# Verify area configuration:
/routing ospf area print

# Check interface configuration:
/routing ospf interface print

# Debug OSPF packets:
/system logging add topics=ospf action=memory
```

**BGP Session Troubleshooting:**
```bash
# Check BGP session state:
/routing bgp peer print status

# Verify BGP table:
/routing bgp advertisements print

# Check route filtering:
/routing filter print where chain~"bgp"
```

**Route Selection Issues:**
```bash
# Detailed route information:
/ip route print detail where dst-address=X.X.X.X/Y

# Check administrative distances:
/routing table print

# Verify metric calculations:
/routing ospf interface print detail
```

---

## 5. Best Practices y Design Guidelines

### 5.1 Network Design Principles

#### Hierarchical Design

**Three-Tier Model aplicado a Routing:**
```
CORE LAYER:
├─ High-speed routing entre sites
├─ Minimal policy enforcement  
├─ Redundant paths
└─ OSPF backbone area típicamente

DISTRIBUTION LAYER:
├─ Route summarization
├─ Policy enforcement points
├─ OSPF regular areas
└─ Redistribution boundaries

ACCESS LAYER:
├─ Default routes típicamente
├─ Minimal routing complexity
├─ Static routing often sufficient
└─ Route filtering para security
```

#### Routing Protocol Selection

**Decision Matrix:**
```
OSPF RECOMENDADO PARA:
├─ Enterprise networks (< 500 routers)
├─ Fast convergence requirements
├─ Hierarchical design needed
└─ Link state benefits desired

BGP RECOMENDADO PARA:
├─ Internet connectivity
├─ Multi-homing scenarios
├─ Policy-based routing requirements
└─ Provider networks

STATIC ROUTING PARA:
├─ Small networks (< 10 routers)
├─ Hub-and-spoke topologies
├─ High security requirements
└─ Minimal administrative overhead
```

### 5.2 Security Considerations

#### Routing Security Threats

**Common Attacks:**
```
ROUTE INJECTION:
├─ Malicious route advertisements
├─ Traffic hijacking
└─ Blackhole attacks

ROUTING LOOPS:
├─ Intentional loop creation
├─ DoS via routing instability
└─ Resource exhaustion

INFORMATION DISCLOSURE:
├─ Routing table reconnaissance
├─ Network topology mapping
└─ Traffic pattern analysis
```

#### Security Mitigation Strategies

**Authentication:**
```bash
# OSPF area authentication:
/routing ospf area set backbone auth-type=md5 auth-key="SecureKey123"

# BGP authentication:
/routing bgp peer set peer1 tcp-md5-key="BGPSecure456"
```

**Route Filtering:**
```bash
# Filter private networks from BGP:
/routing filter add chain=bgp-out prefix=10.0.0.0/8 action=discard
/routing filter add chain=bgp-out prefix=172.16.0.0/12 action=discard  
/routing filter add chain=bgp-out prefix=192.168.0.0/16 action=discard
```

**Administrative Controls:**
```bash
# Limit routing updates:
/routing ospf interface set ether1 passive=yes

# Control redistribution:
/routing ospf instance set default redistribute-connected=no redistribute-static=no
```

### 5.3 Capacity Planning

#### Scaling Considerations

**OSPF Scalability Limits:**
```
SINGLE AREA:
├─ Maximum: ~200 routers
├─ LSA flooding overhead increases quadratically
└─ SPF calculation complexity: O(n log n)

MULTI-AREA:
├─ Backbone area: ~100 routers
├─ Regular areas: ~50-100 routers each
├─ Total: ~1000 routers theoretical
└─ Practical limit: ~500 routers
```

**BGP Scalability:**
```
GLOBAL INTERNET:
├─ Current: ~800,000+ prefixes
├─ Memory: ~2-4 GB para full table
├─ CPU: Significant for updates processing
└─ Typical enterprise: 1,000-10,000 prefixes
```

#### Resource Planning

**Memory Requirements:**
```bash
# Estimate OSPF memory usage:
# ~1 KB per LSA average
# ~10-20 LSAs per router average
# Total ≈ (Number of routers × 15 KB)

# BGP memory estimation:
# ~500 bytes per prefix average  
# Full table ≈ 800,000 × 500 bytes ≈ 400 MB
# Plus processing overhead ≈ 2-4 GB total
```

**CPU Considerations:**
```
SPF CALCULATION FREQUENCY:
├─ Triggered by LSA changes
├─ Throttling prevents excessive calculation
├─ Modern routers: sub-second SPF times
└─ Network size affects calculation time

BGP PROCESSING:
├─ Incremental updates preferred
├─ Policy processing overhead
├─ Route reflector CPU requirements
└─ Full table recalculation rare
```

---

## Comandos de Referencia Rápida

### Static Routing
```bash
# Basic static route:
/ip route add dst-address=192.168.10.0/24 gateway=10.0.0.1

# Default route:
/ip route add dst-address=0.0.0.0/0 gateway=203.0.113.1

# Multiple routes for load balancing:
/ip route add dst-address=0.0.0.0/0 gateway=203.0.113.1 distance=1
/ip route add dst-address=0.0.0.0/0 gateway=203.0.113.5 distance=1

# Floating route:
/ip route add dst-address=0.0.0.0/0 gateway=203.0.113.5 distance=10
```

### OSPF Configuration
```bash
# Basic OSPF setup:
/routing ospf instance set default router-id=1.1.1.1
/routing ospf area add name=backbone area-id=0.0.0.0
/routing ospf interface add interface=ether1 area=backbone

# Multi-area configuration:
/routing ospf area add name=branch area-id=0.0.0.1
/routing ospf interface add interface=ether2 area=branch

# Authentication:
/routing ospf area set backbone auth-type=md5 auth-key="password"
```

### BGP Configuration
```bash
# Basic BGP setup:
/routing bgp instance set default as=65001 router-id=1.1.1.1
/routing bgp peer add remote-address=10.0.0.1 remote-as=65002

# iBGP configuration:
/routing bgp peer add remote-address=10.0.0.2 remote-as=65001

# Route filtering:
/routing filter add chain=bgp-out prefix=10.0.0.0/8 action=discard
/routing bgp peer set peer1 out-filter=bgp-out
```

### Monitoring Commands
```bash
# Route table:
/ip route print

# OSPF neighbors:
/routing ospf neighbor print

# BGP sessions:
/routing bgp peer print status

# Route details:
/ip route print detail where dst-address=192.168.10.0/24
```