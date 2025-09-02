# Laboratorios Presenciales - Módulo 3: Routing Estático y Dinámico

## Información General

**Equipamiento:** RouterBOARD RB951-2HnD por grupo (máximo 4 estudiantes)  
**Duración:** 4 sesiones de 2 horas cada una  
**Modalidad:** Grupos de trabajo colaborativo  
**Prerequisito:** Módulo 2 completado (VLANs y switching funcionando)

---

## **INFORMACIÓN IMPORTANTE**

**⚠️ ANTES DE COMENZAR:**
- Módulo 2 completado y VLANs funcionando
- Configuración bridge con VLANs activa del módulo anterior
- Access a otros grupos para crear topologías multi-router
- Calculadora de subnetting disponible

**📋 EQUIPAMIENTO POR GRUPO:**
- 1 RouterBOARD RB951-2HnD (configurado con VLANs del Módulo 2)
- Cables Ethernet para interconexión entre grupos
- Laptops con Winbox y GNS3
- Acceso a documentación de referencia

**🎯 OBJETIVOS DEL MÓDULO:**
Al completar estos laboratorios podrás:
- Configurar routing estático con redundancia y load balancing
- Implementar OSPF en topologías multi-área
- Configurar BGP básico para conectividad inter-AS
- Aplicar optimizaciones de routing y troubleshoot problemas complejos

---

## **LABORATORIO 1: Routing Estático y Redundancia**
**Duración estimada: 45 minutos**

### **Objetivo:**
Configurar routing estático con múltiples paths, implementar load balancing y floating routes para redundancia.

### **Topología Inter-Grupos:**
```
Grupo A (Router A)          Grupo B (Router B)          Grupo C (Router C)
192.168.10.0/24    ether1 ←→ ether1    192.168.20.0/24    ether1 ←→ ether1    192.168.30.0/24
     ↕ ether2                               ↕ ether2                               ↕ ether2  
10.0.1.0/30                             10.0.2.0/30                           10.0.3.0/30
     ↕                                       ↕                                       ↕
     ↕ ether3 ←─────────────── 10.0.4.0/30 ether3 ←──────────────── 10.0.5.0/30 ether3
                  (Backup Path)                          (Backup Path)
```

### **Addressing Scheme:**
```
GRUPO A (Router A):
├─ ether1 (to Router B): 10.0.1.1/30
├─ ether2 (LAN): 192.168.10.1/24  
├─ ether3 (to Router C): 10.0.4.1/30
└─ Loopback: 1.1.1.1/32

GRUPO B (Router B):  
├─ ether1 (to Router A): 10.0.1.2/30
├─ ether2 (to Router C): 10.0.2.1/30
├─ ether3 (LAN): 192.168.20.1/24
└─ Loopback: 2.2.2.2/32

GRUPO C (Router C):
├─ ether1 (to Router B): 10.0.2.2/30  
├─ ether2 (LAN): 192.168.30.1/24
├─ ether3 (to Router A): 10.0.4.2/30
└─ Loopback: 3.3.3.3/32
```

---

### **PASO 1: Preparación y Cleanup de Configuración VLANs**

#### **1.1 Backup Configuración Actual**

**MÉTODO CLI:**
```bash
# Backup completo de configuración VLANs:
/export file=backup-vlans-modulo2
/system backup save name=modulo2-completo
```

#### **1.2 Simplificar Configuración para Routing**

**⚠️ IMPORTANTE:** Mantendremos VLANs en algunos puertos, pero simplificaremos para routing.

**MÉTODO CLI:**
```bash
# Remover VLANs de ether1 (será WAN routing):
/interface bridge port remove [find interface=ether1]

# Crear IP directa en ether1 para routing:
/ip address add address=10.0.1.1/30 interface=ether1 comment="Link to Router B"
```

**MÉTODO WINBOX:**
1. **Interfaces → Bridge → Ports**
2. **Seleccionar puerto ether1**
3. **Eliminar del bridge** (botón X)
4. **IP → Addresses → "+"**
5. **Address:** según tu grupo (ver addressing scheme)
6. **Interface:** ether1

#### **1.3 Configurar Router ID (Loopback)**

**MÉTODO CLI:**
```bash
# Crear loopback interface:
/interface bridge add name=loopback
/ip address add address=1.1.1.1/32 interface=loopback comment="Router ID"
```

**MÉTODO WINBOX:**
1. **Interfaces → Bridge → "+"**
2. **Name:** `loopback`
3. **IP → Addresses → "+"**
4. **Address:** según tu grupo (1.1.1.1/32, 2.2.2.2/32, o 3.3.3.3/32)

**✅ CHECKPOINT 1:**
```bash
# Verificar configuración:
/ip address print
# Debe mostrar:
# - IP en ether1 para routing
# - IP loopback como router ID
# - VLANs existentes mantenidas
```

---

### **PASO 2: Configurar Enlaces Point-to-Point**

#### **2.1 Coordinación Entre Grupos**

**COORDINACIÓN REQUERIDA:**
- Grupo A conecta con Grupo B (ether1 ↔ ether1)
- Grupo B conecta con Grupo C (ether2 ↔ ether1)  
- Grupo A conecta con Grupo C (ether3 ↔ ether3) - Backup path

#### **2.2 Configurar Enlaces Adicionales**

**Para Grupo A:**
```bash
# Link backup a Router C:
/ip address add address=10.0.4.1/30 interface=ether3 comment="Backup link to Router C"
```

**Para Grupo B:**
```bash
# Link principal a Router C:
/ip address add address=10.0.2.1/30 interface=ether2 comment="Link to Router C"
```

**Para Grupo C:**
```bash
# Links desde otros routers:
/ip address add address=10.0.2.2/30 interface=ether1 comment="Link to Router B"
/ip address add address=10.0.4.2/30 interface=ether3 comment="Backup link to Router A"
```

#### **2.3 Verificar Conectividad Point-to-Point**

**Testing de Enlaces:**
```bash
# Desde Router A:
ping 10.0.1.2 count=3    # Test link to Router B
ping 10.0.4.2 count=3    # Test backup link to Router C

# Desde Router B:
ping 10.0.1.1 count=3    # Test link to Router A  
ping 10.0.2.2 count=3    # Test link to Router C

# Desde Router C:
ping 10.0.2.1 count=3    # Test link to Router B
ping 10.0.4.1 count=3    # Test backup link to Router A
```

**✅ CHECKPOINT 2:**
- Todos los enlaces point-to-point funcionando
- Ping exitoso entre routers directamente conectados
- No routing end-to-end todavía (esperado)

---

### **PASO 3: Configurar Routing Estático Básico**

#### **3.1 Rutas hacia Redes Remotas**

**Router A - Configuración:**
```bash
# Ruta hacia red de Router B (vía link directo):
/ip route add dst-address=192.168.20.0/24 gateway=10.0.1.2 comment="To Router B LAN"

# Ruta hacia red de Router C (vía Router B - preferred):
/ip route add dst-address=192.168.30.0/24 gateway=10.0.1.2 distance=1 comment="To Router C via B (primary)"

# Ruta hacia red de Router C (vía backup link):
/ip route add dst-address=192.168.30.0/24 gateway=10.0.4.2 distance=10 comment="To Router C direct (backup)"

# Rutas hacia loopbacks:
/ip route add dst-address=2.2.2.2/32 gateway=10.0.1.2 comment="Router B loopback"
/ip route add dst-address=3.3.3.3/32 gateway=10.0.1.2 distance=1 comment="Router C loopback via B"
/ip route add dst-address=3.3.3.3/32 gateway=10.0.4.2 distance=10 comment="Router C loopback direct"
```

**Router B - Configuración:**
```bash
# Ruta hacia red de Router A:
/ip route add dst-address=192.168.10.0/24 gateway=10.0.1.1 comment="To Router A LAN"

# Ruta hacia red de Router C:
/ip route add dst-address=192.168.30.0/24 gateway=10.0.2.2 comment="To Router C LAN"

# Rutas hacia loopbacks:
/ip route add dst-address=1.1.1.1/32 gateway=10.0.1.1 comment="Router A loopback"
/ip route add dst-address=3.3.3.3/32 gateway=10.0.2.2 comment="Router C loopback"
```

**Router C - Configuración:**
```bash
# Ruta hacia red de Router A (vía Router B - preferred):
/ip route add dst-address=192.168.10.0/24 gateway=10.0.2.1 distance=1 comment="To Router A via B (primary)"

# Ruta hacia red de Router A (vía backup link):  
/ip route add dst-address=192.168.10.0/24 gateway=10.0.4.1 distance=10 comment="To Router A direct (backup)"

# Ruta hacia red de Router B:
/ip route add dst-address=192.168.20.0/24 gateway=10.0.2.1 comment="To Router B LAN"

# Rutas hacia loopbacks:
/ip route add dst-address=1.1.1.1/32 gateway=10.0.2.1 distance=1 comment="Router A loopback via B"
/ip route add dst-address=1.1.1.1/32 gateway=10.0.4.1 distance=10 comment="Router A loopback direct"
/ip route add dst-address=2.2.2.2/32 gateway=10.0.2.1 comment="Router B loopback"
```

#### **3.2 Verificar Routing Table**

**MÉTODO CLI:**
```bash
# Ver tabla de rutas completa:
/ip route print

# Ver solo rutas estáticas:
/ip route print where static=yes

# Verificar rutas específicas:
/ip route print detail where dst-address=192.168.30.0/24
```

**✅ CHECKPOINT 3:**
```bash
# Test end-to-end connectivity:
# Desde Router A:
ping 192.168.20.1    # Router B LAN
ping 192.168.30.1    # Router C LAN  
ping 2.2.2.2         # Router B loopback
ping 3.3.3.3         # Router C loopback

# Similar tests desde otros routers
```

---

### **PASO 4: Implementar Load Balancing**

#### **4.1 ECMP (Equal Cost Multi-Path)**

**Escenario:** Router A wants load balancing toward Router C

**Modificar rutas en Router A:**
```bash
# Remover rutas existentes hacia Router C:
/ip route remove [find dst-address=192.168.30.0/24]
/ip route remove [find dst-address=3.3.3.3/32]

# Agregar rutas ECMP (same distance):
/ip route add dst-address=192.168.30.0/24 gateway=10.0.1.2 distance=1 comment="To Router C via B (ECMP)"
/ip route add dst-address=192.168.30.0/24 gateway=10.0.4.2 distance=1 comment="To Router C direct (ECMP)"

/ip route add dst-address=3.3.3.3/32 gateway=10.0.1.2 distance=1 comment="Router C loopback via B (ECMP)"
/ip route add dst-address=3.3.3.3/32 gateway=10.0.4.2 distance=1 comment="Router C loopback direct (ECMP)"
```

#### **4.2 Testing Load Balancing**

**MÉTODO CLI:**
```bash
# Multiple tests para observar load balancing:
for i from=1 to=10 do={
    /tool traceroute 192.168.30.1 count=1
    :delay 1s
}
```

**Observaciones esperadas:**
- Algunos traces van vía 10.0.1.2 (Router B)
- Algunos traces van vía 10.0.4.2 (direct)
- Distribution puede no ser perfecta (depends on hashing algorithm)

#### **4.3 Advanced Load Balancing con PCC**

**Si quieren más control sobre load balancing:**
```bash
# Marcar conexiones para balancing:
/ip firewall mangle add chain=prerouting dst-address=192.168.30.0/24 \
    per-connection-classifier=both-addresses:2/0 action=mark-routing new-routing-mark=path1

/ip firewall mangle add chain=prerouting dst-address=192.168.30.0/24 \
    per-connection-classifier=both-addresses:2/1 action=mark-routing new-routing-mark=path2

# Rutas específicas por marking:
/ip route add dst-address=192.168.30.0/24 gateway=10.0.1.2 routing-mark=path1
/ip route add dst-address=192.168.30.0/24 gateway=10.0.4.2 routing-mark=path2
```

**✅ CHECKPOINT 4:**
- Load balancing funcionando (traceroute shows different paths)
- Performance similar por ambos paths
- Failover automático si un path falla

---

### **PASO 5: Testing de Failover y Floating Routes**

#### **5.1 Configurar Floating Routes**

**Revertir a configuración con floating routes:**
```bash
# Router A - Remove ECMP routes:
/ip route remove [find comment~"ECMP"]

# Add primary and floating routes:
/ip route add dst-address=192.168.30.0/24 gateway=10.0.1.2 distance=1 comment="Primary via B"  
/ip route add dst-address=192.168.30.0/24 gateway=10.0.4.2 distance=10 comment="Floating direct"
```

#### **5.2 Simulate Link Failure**

**Testing procedure:**
1. **Verify primary path working:**
   ```bash
   /tool traceroute 192.168.30.1
   # Should show path via 10.0.1.2
   ```

2. **Simulate link failure:**
   - Desconectar cable entre Router A y Router B físicamente
   - O deshabilitar interface: `/interface disable ether1`

3. **Verify failover:**
   ```bash
   # Wait 30 seconds for route timeout
   /ip route print where dst-address=192.168.30.0/24
   # Primary route should show unreachable
   
   /tool traceroute 192.168.30.1  
   # Should now use direct path via 10.0.4.2
   ```

4. **Restore link:**
   - Reconectar cable o habilitar interface
   - Verify return to primary path

#### **5.3 Route Monitoring**

**MÉTODO CLI:**
```bash
# Monitor route changes in real-time:
/log print where topics~"route"

# Check interface status:
/interface print stats where running=no

# Monitor routing table changes:
/ip route print where active=no
```

**✅ CHECKPOINT 5:**
- Floating routes functioning correctly
- Automatic failover upon link failure
- Automatic recovery when link restored
- End-to-end connectivity maintained during failures

---

### **PASO 6: Optimization y Documentation**

#### **6.1 Route Summarization (Manual)**

**Si hay múltiples subnets, implementar summarization:**
```bash
# Example: Si Router A tuviera múltiples LANs:
# 192.168.10.0/24, 192.168.11.0/24, 192.168.12.0/24
# En otros routers, usar:
/ip route add dst-address=192.168.0.0/16 gateway=10.0.1.1 comment="Router A networks (summary)"
```

#### **6.2 Route Filtering Básico**

**Prevenir routing loops y mejorar security:**
```bash
# En Router B, prevent routing back to source:
/ip firewall filter add chain=forward src-address=192.168.10.0/24 dst-address=192.168.10.0/24 action=drop comment="Prevent hairpin routing"
```

#### **6.3 Performance Monitoring**

**MÉTODO CLI:**
```bash
# Monitor route lookup performance:
/tool profile duration=10 on-event="/ip route print count-only"

# Interface statistics:
/interface print stats where name~"ether"

# Traffic analysis:
/tool traffic-monitor interface=ether1 duration=10s
```

#### **6.4 Documentation**

**Crear documentación del diseño:**
```
ROUTING DESIGN - GRUPOS A-B-C

TOPOLOGY:
├─ Triangle topology con backup paths
├─ Primary path: A ↔ B ↔ C
├─ Backup path: A ↔ C (direct)
└─ Load balancing/Floating routes implemented

ADDRESSING:
├─ Point-to-point links: 10.0.x.0/30 networks
├─ LAN networks: 192.168.x0.0/24 per router  
├─ Loopbacks: x.x.x.x/32 as router IDs
└─ No address space waste (efficient /30 links)

ROUTING POLICY:
├─ Static routing throughout
├─ Floating routes para redundancy  
├─ Load balancing donde múltiples paths available
└─ Administrative distance based failover

TESTED SCENARIOS:
├─ Normal operation: ✅
├─ Primary link failure: ✅
├─ Link restoration: ✅  
├─ Load balancing: ✅
└─ End-to-end connectivity: ✅
```

**✅ FINAL CHECKPOINT LAB 1:**
- Static routing configurado y funcional
- Redundancy y failover tested
- Load balancing implemented y verified
- Performance acceptable
- Documentation completed

---

## **LABORATORIO 2: OSPF Multi-Área**
**Duración estimada: 75 minutos**

### **Objetivo:**
Implementar OSPF en topología multi-área, configurar ABRs, y optimizar convergencia.

### **Topología OSPF:**
```
AREA 0 (BACKBONE):                    AREA 1:
                                      
Grupo A (ABR)  ←→  Grupo B (ABR)  ←→  Grupo C (Internal)
192.168.10.0/24    192.168.20.0/24    192.168.30.0/24
Area 0 & Area 1   Area 0 only        Area 1 only
```

### **Diseño de Áreas:**
```
AREA 0 (BACKBONE):
├─ Interconecta todas las demás áreas
├─ Grupos A y B participan (ABRs)  
├─ Enlaces: A-B en backbone area
└─ Core routing functionality

AREA 1:
├─ Área regular conectada al backbone
├─ Grupos A y C participan
├─ Router A = ABR (conecta areas 0 y 1)
└─ Router C = Internal router (solo area 1)
```

---

### **PASO 1: Preparar Configuración OSPF**

#### **1.1 Remover Static Routes**

**⚠️ IMPORTANTE:** Backup static routes antes de remover.

**MÉTODO CLI:**
```bash
# Backup static routes configuration:
/ip route export file=static-routes-backup

# Remove todas las static routes (except connected):
/ip route remove [find static=yes]

# Verify only connected routes remain:
/ip route print
# Should show only directly connected networks
```

#### **1.2 Configurar Router IDs**

**MÉTODO CLI:**
```bash
# Grupo A (ABR):
/routing ospf instance set default router-id=1.1.1.1

# Grupo B (Backbone only):  
/routing ospf instance set default router-id=2.2.2.2

# Grupo C (Area 1 only):
/routing ospf instance set default router-id=3.3.3.3
```

**MÉTODO WINBOX:**
1. **Routing → OSPF → Instances**
2. **Doble click "default"**
3. **Router ID:** (según tu grupo)
4. **Clic "OK"**

#### **1.3 Create OSPF Areas**

**Todos los routers:**
```bash
# Create backbone area (area 0):
/routing ospf area add name=backbone area-id=0.0.0.0 instance=default

# Create regular area (area 1) - solo Grupos A y C:
/routing ospf area add name=area1 area-id=0.0.0.1 instance=default
```

**✅ CHECKPOINT 1:**
```bash
# Verify OSPF configuration:
/routing ospf instance print
/routing ospf area print

# Should show:
# - Router ID configured
# - Areas 0 y 1 created
# - Instance "default" active
```

---

### **PASO 2: Configurar OSPF Interfaces**

#### **2.1 Router A (ABR) - Dual Area Configuration**

**MÉTODO CLI:**
```bash
# Interface hacia Router B (Area 0 - Backbone):
/routing ospf interface add interface=ether1 area=backbone cost=10 comment="To Router B (Area 0)"

# Interface hacia Router C (Area 1):  
/routing ospf interface add interface=ether3 area=area1 cost=10 comment="To Router C (Area 1)"

# LAN interface (Area 1):
/routing ospf interface add interface=vlan10-admin area=area1 cost=1 comment="LAN Area 1"
```

**MÉTODO WINBOX:**
1. **Routing → OSPF → Interfaces**
2. **Clic "+" para cada interface**
3. **Interface:** seleccionar interface física
4. **Area:** seleccionar área apropiada
5. **Cost:** configurar según design

#### **2.2 Router B (Backbone Router)**

**MÉTODO CLI:**
```bash
# Interface hacia Router A (Area 0):
/routing ospf interface add interface=ether1 area=backbone cost=10 comment="To Router A (Area 0)"

# LAN interface (Area 0):
/routing ospf interface add interface=vlan20-users area=backbone cost=1 comment="LAN Area 0"
```

#### **2.3 Router C (Internal Router - Area 1)**

**MÉTODO CLI:**
```bash
# Interface hacia Router A (Area 1):
/routing ospf interface add interface=ether3 area=area1 cost=10 comment="To Router A (Area 1)"

# LAN interface (Area 1):  
/routing ospf interface add interface=vlan30-guest area=area1 cost=1 comment="LAN Area 1"
```

**✅ CHECKPOINT 2:**
```bash
# Verify OSPF interface configuration:
/routing ospf interface print

# Check interface assignment to areas:
# Router A: ether1→Area 0, ether3→Area 1
# Router B: ether1→Area 0  
# Router C: ether3→Area 1
```

---

### **PASO 3: Verificar OSPF Adjacencies**

#### **3.1 Monitor Neighbor Discovery**

**MÉTODO CLI:**
```bash
# Check OSPF neighbors (takes 10-40 seconds to establish):
/routing ospf neighbor print

# Expected neighbors:
# Router A: Should see Router B (Area 0) y Router C (Area 1)
# Router B: Should see Router A (Area 0)
# Router C: Should see Router A (Area 1)
```

**MÉTODO WINBOX:**
1. **Routing → OSPF → Neighbors**
2. **Refresh periodically to see state changes**

#### **3.2 Troubleshoot Adjacency Issues**

**If neighbors not appearing:**
```bash
# Check OSPF interface status:
/routing ospf interface print detail

# Verify area configuration:
/routing ospf area print

# Check physical connectivity:
/ping 10.0.1.2 source=10.0.1.1

# Enable OSPF logging:
/system logging add topics=ospf action=memory
/log print where topics~"ospf"
```

#### **3.3 Verify Neighbor States**

**Expected neighbor states:**
```bash
# All neighbors should show state=Full
/routing ospf neighbor print

# If stuck in other states:
# ExStart/Exchange = database synchronization
# Init/2-Way = bidirectional communication issues  
# Down = no communication
```

**✅ CHECKPOINT 3:**
- All OSPF neighbors en estado "Full"
- Router A shows neighbors en both areas  
- Adjacency establishment < 2 minutes
- No error messages en OSPF logs

---

### **PASO 4: Verificar OSPF Routing**

#### **4.1 Check Routing Table**

**MÉTODO CLI:**
```bash
# Ver routing table completa:
/ip route print

# Ver solo rutas OSPF:
/ip route print where protocol=ospf

# Verificar rutas inter-area:
# Router C should see Router B networks via Router A (ABR)
```

**Expected routing behavior:**
- **Intra-area routes:** Direct OSPF communication
- **Inter-area routes:** Via ABR (Router A)
- **Route types:** O (intra-area), O IA (inter-area)

#### **4.2 Test End-to-End Connectivity**

**Comprehensive connectivity testing:**
```bash
# Desde Router A:
ping 2.2.2.2    # Router B loopback (intra-area)
ping 3.3.3.3    # Router C loopback (intra-area en Area 1)
ping 192.168.20.1  # Router B LAN
ping 192.168.30.1  # Router C LAN

# Desde Router C:
ping 1.1.1.1    # Router A loopback (intra-area)
ping 2.2.2.2    # Router B loopback (inter-area via ABR)
ping 192.168.10.1  # Router A LAN
ping 192.168.20.1  # Router B LAN (inter-area)
```

#### **4.3 Analyze Route Types**

**MÉTODO CLI:**
```bash
# Detailed route analysis:
/ip route print detail where protocol=ospf

# Look for:
# - Route types (intra-area vs inter-area)
# - Administrative distances
# - Next-hop routers (should be ABR for inter-area)
```

**✅ CHECKPOINT 4:**
- OSPF routing table populated correctly
- Inter-area routes visible via ABR
- End-to-end connectivity functional
- Route types appropriate (O vs O IA)

---

### **PASO 5: OSPF Database Analysis**

#### **5.1 Examine OSPF LSA Database**

**MÉTODO CLI:**
```bash
# Ver OSPF LSA database:
/routing ospf lsa print

# Analyze LSA types:
# Type 1 (Router LSAs): One per router per area
# Type 2 (Network LSAs): For multi-access networks with DR
# Type 3 (Summary LSAs): Inter-area routes at ABRs
```

**Expected LSAs:**
```
AREA 0:
├─ Type 1 LSAs: Router A y Router B
├─ Type 2 LSAs: Networks en Area 0 (if multi-access)
└─ Type 3 LSAs: Summaries from Area 1 (generated by Router A)

AREA 1:  
├─ Type 1 LSAs: Router A y Router C
├─ Type 2 LSAs: Networks en Area 1
└─ Type 3 LSAs: Summaries from Area 0 (generated by Router A)
```

#### **5.2 ABR Functionality Verification**

**En Router A (ABR):**
```bash
# Verify ABR status:
/routing ospf area print detail

# Check LSA generation:
/routing ospf lsa print where originator=1.1.1.1

# Should generate Type 3 LSAs para inter-area routes
```

**✅ CHECKPOINT 5:**
- LSA database populated appropriately
- Type 3 LSAs present para inter-area communication  
- ABR generating summary LSAs correctly
- No LSA age issues (Age < 3600 seconds)

---

### **PASO 6: OSPF Optimization**

#### **6.1 Adjust OSPF Timers**

**Optimize convergence speed:**
```bash
# Faster hello intervals para quicker neighbor detection:
/routing ospf interface set [find area=backbone] hello-interval=5s dead-interval=20s

# Adjust SPF timers:
/routing ospf instance set default spf-delay=1s spf-max-delay=5s
```

#### **6.2 Configure Authentication**

**Add OSPF area authentication:**
```bash
# Configure MD5 authentication para Area 0:
/routing ospf area set backbone auth-type=md5 auth-key="OSPFSecure123"

# Apply to all routers en backbone area:
# (Coordinate with other groups)
```

#### **6.3 Cost Optimization**

**Adjust interface costs for path preference:**
```bash
# Prefer certain paths by adjusting costs:
/routing ospf interface set [find interface=ether1] cost=5  # Preferred path
/routing ospf interface set [find interface=ether3] cost=20 # Backup path
```

#### **6.4 Area Summarization**

**Configure route summarization at ABR:**
```bash
# En Router A (ABR), summarize Area 1 networks:
/routing ospf area range add area=area1 prefix=192.168.0.0/16 advertise=yes cost=10
```

**✅ CHECKPOINT 6:**
- OSPF timers optimized for faster convergence
- Authentication configured y tested
- Cost optimization applied
- Summarization working (if implemented)

---

### **PASO 7: Failover Testing**

#### **7.1 Test Link Failure Scenarios**

**Scenario 1: Backbone link failure (Router A ↔ Router B)**
```bash
# Simulate failure:
/interface disable ether1

# Monitor convergence:
/routing ospf neighbor print
/ip route print where protocol=ospf

# Expected behavior:
# - Router A y B lose backbone adjacency
# - Inter-area routing still works via Area 1 if alternate path exists
```

**Scenario 2: Area 1 link failure (Router A ↔ Router C)**
```bash
# Simulate failure:
/interface disable ether3

# Monitor impact:
# - Area 1 partitioned
# - Router C isolated from backbone
# - No alternate path (single ABR scenario)
```

#### **7.2 Convergence Time Measurement**

**MÉTODO CLI:**
```bash
# Before test:
/log print where topics~"ospf" | head

# Create failure and measure time to:
# 1. Neighbor adjacency down
# 2. LSA propagation  
# 3. SPF recalculation
# 4. Routing table update

# Expected convergence: < 5 seconds for local changes
```

#### **7.3 Recovery Testing**

**Restore links and verify:**
```bash
# Re-enable interfaces:
/interface enable ether1
/interface enable ether3

# Verify recovery:
/routing ospf neighbor print
/ip route print where protocol=ospf

# Test end-to-end connectivity restoration
```

**✅ CHECKPOINT 7:**
- Link failure detection < 20 seconds (dead-interval)
- Route convergence < 5 seconds after detection
- Recovery successful when links restored
- No permanent routing issues after failures

---

### **PASO 8: Documentation y Analysis**

#### **8.1 Performance Documentation**

```
OSPF PERFORMANCE ANALYSIS - MULTI-AREA

TOPOLOGY SUMMARY:
├─ Area 0 (Backbone): Routers A, B
├─ Area 1 (Regular): Routers A, C  
├─ ABR: Router A (connects areas)
└─ Total LSAs: ~6-10 (depending on network types)

CONVERGENCE METRICS:
├─ Neighbor adjacency establishment: ~30 seconds
├─ Initial database sync: ~10 seconds
├─ Link failure detection: ~20 seconds (hello/dead intervals)
├─ Route convergence after failure: ~2-5 seconds
└─ Recovery time: ~30 seconds (neighbor re-establishment)

RESOURCE UTILIZATION:
├─ Memory usage: Minimal (small network)
├─ CPU impact: Low (few routers, simple topology)  
├─ Bandwidth overhead: ~200 bytes/hello interval per neighbor
└─ Routing table size: 6-10 routes total

OPTIMIZATION APPLIED:
├─ Authentication: MD5 area authentication
├─ Timer tuning: Faster convergence timers
├─ Cost optimization: Preferred path configuration
└─ Summarization: Inter-area route summarization (if applicable)
```

#### **8.2 Lessons Learned**

**Key takeaways:**
- Multi-area design provides scalability benefits even en small networks
- ABR placement critical para network design
- Authentication adds security but requires coordination
- OSPF converges much faster than static routing en failure scenarios
- Proper area design prevents routing loops naturally

**✅ FINAL CHECKPOINT LAB 2:**
- OSPF multi-area funcionando correctamente
- ABR functionality verified
- Convergence y failover tested
- Optimization techniques applied
- Comprehensive documentation completed

---

## **LABORATORIO 3: BGP Básico**
**Duración estimada: 60 minutos**

### **Objetivo:**
Configurar BGP entre different autonomous systems, implementar basic policies, y entender BGP path selection.

### **Topología BGP:**
```
AS 65001 (Grupo A)    AS 65002 (Grupo B)    AS 65003 (Grupo C)
     ISP A        ←→        ISP B        ←→        ISP C
192.168.10.0/24          192.168.20.0/24      192.168.30.0/24
     ↕ eBGP                   ↕ eBGP               ↕ eBGP
Customer Networks        Transit Provider      Customer Networks
```

### **AS Assignment y Roles:**
```
AS 65001 (Grupo A):
├─ Role: Customer ISP
├─ Networks: 192.168.10.0/24, 172.16.1.0/24
├─ BGP connections: AS 65002
└─ Policy: Receive default route, advertise customer networks

AS 65002 (Grupo B):
├─ Role: Transit Provider  
├─ Networks: 192.168.20.0/24
├─ BGP connections: AS 65001, AS 65003
└─ Policy: Provide transit between customers

AS 65003 (Grupo C):
├─ Role: Customer ISP
├─ Networks: 192.168.30.0/24, 172.16.3.0/24  
├─ BGP connections: AS 65002
└─ Policy: Receive default route, advertise customer networks
```

---

### **PASO 1: Preparar Infrastructure para BGP**

#### **1.1 Disable OSPF**

**⚠️ ADVERTENCIA:** Backup OSPF config antes de deshabilitar.

**MÉTODO CLI:**
```bash
# Export OSPF configuration:
/routing ospf export file=ospf-backup

# Disable OSPF interfaces (keep routes until BGP working):
/routing ospf interface set [find] disabled=yes

# Note: Routes will remain until replaced by BGP
```

#### **1.2 Configure Loopbacks para BGP**

**Configure stable BGP router IDs:**
```bash
# Grupo A (AS 65001):
/interface bridge add name=bgp-loopback
/ip address add address=10.1.1.1/32 interface=bgp-loopback comment="BGP Router ID"

# Grupo B (AS 65002):  
/interface bridge add name=bgp-loopback
/ip address add address=10.2.2.2/32 interface=bgp-loopback comment="BGP Router ID"

# Grupo C (AS 65003):
/interface bridge add name=bgp-loopback  
/ip address add address=10.3.3.3/32 interface=bgp-loopback comment="BGP Router ID"
```

#### **1.3 Configure Static Routes to Loopbacks**

**Para BGP session establishment:**
```bash
# En cada router, add static routes to neighbor loopbacks:
# Router A:
/ip route add dst-address=10.2.2.2/32 gateway=10.0.1.2

# Router B:  
/ip route add dst-address=10.1.1.1/32 gateway=10.0.1.1
/ip route add dst-address=10.3.3.3/32 gateway=10.0.2.2

# Router C:
/ip route add dst-address=10.2.2.2/32 gateway=10.0.2.1
```

**✅ CHECKPOINT 1:**
```bash
# Verify loopback connectivity:
ping 10.2.2.2 source=10.1.1.1  # From A to B
ping 10.3.3.3 source=10.2.2.2  # From B to C

# Should have connectivity for BGP session establishment
```

---

### **PASO 2: Configure Basic BGP**

#### **2.1 Configure BGP Instances**

**Router A (AS 65001):**
```bash
# Configure BGP instance:
/routing bgp instance set default as=65001 router-id=10.1.1.1

# Configure BGP peer to AS 65002:
/routing bgp peer add remote-address=10.2.2.2 remote-as=65002 update-source=bgp-loopback comment="To AS 65002"
```

**Router B (AS 65002):**
```bash
# Configure BGP instance:
/routing bgp instance set default as=65002 router-id=10.2.2.2

# Configure BGP peers:
/routing bgp peer add remote-address=10.1.1.1 remote-as=65001 update-source=bgp-loopback comment="To AS 65001"
/routing bgp peer add remote-address=10.3.3.3 remote-as=65003 update-source=bgp-loopback comment="To AS 65003"
```

**Router C (AS 65003):**
```bash
# Configure BGP instance:
/routing bgp instance set default as=65003 router-id=10.3.3.3

# Configure BGP peer to AS 65002:
/routing bgp peer add remote-address=10.2.2.2 remote-as=65002 update-source=bgp-loopback comment="To AS 65002"
```

**MÉTODO WINBOX:**
1. **Routing → BGP → Instances**
2. **Double-click "default"**
3. **AS:** your AS number
4. **Router ID:** your loopback IP

**Para peers:**
1. **Routing → BGP → Peers**
2. **Clic "+"**  
3. **Configure peer parameters**

#### **2.2 Verify BGP Session Establishment**

**MÉTODO CLI:**
```bash
# Check BGP session status:
/routing bgp peer print status

# Expected status: "established" 
# If not established, check:
# - Connectivity to remote loopback
# - AS numbers correct
# - Router IDs configured
```

**Troubleshooting BGP sessions:**
```bash
# If sessions not establishing:
/log print where topics~"bgp"

# Check TCP connectivity:
/tool telnet 10.2.2.2 179

# Common issues:
# - No route to remote router-id
# - Incorrect AS numbers
# - Firewall blocking TCP 179
```

**✅ CHECKPOINT 2:**
- BGP sessions "established" between all neighbors
- No BGP error messages en logs  
- TCP connection active on port 179

---

### **PASO 3: BGP Network Advertisement**

#### **3.1 Advertise Local Networks**

**Router A (AS 65001):**
```bash
# Advertise local networks:
/routing bgp network add network=192.168.10.0/24 comment="LAN network"
/routing bgp network add network=172.16.1.0/24 comment="Customer network A1"

# Create customer network for testing:
/ip address add address=172.16.1.1/24 interface=loopback comment="Customer network A1"
```

**Router B (AS 65002 - Transit Provider):**
```bash
# Advertise local network:
/routing bgp network add network=192.168.20.0/24 comment="LAN network"

# Note: Transit provider typically doesn't originate many routes
# Mainly provides transit between customers
```

**Router C (AS 65003):**
```bash
# Advertise local networks:
/routing bgp network add network=192.168.30.0/24 comment="LAN network"  
/routing bgp network add network=172.16.3.0/24 comment="Customer network C1"

# Create customer network:
/ip address add address=172.16.3.1/24 interface=loopback comment="Customer network C1"
```

#### **3.2 Verify Route Advertisement**

**MÉTODO CLI:**
```bash
# Check advertised routes:
/routing bgp advertisements print

# Check received routes:
/routing bgp advertisements print where received=yes

# Verify routes installed en routing table:
/ip route print where protocol=bgp
```

**Expected behavior:**
- Router A: Should see routes from AS 65003 via AS 65002
- Router B: Should see routes from both AS 65001 y AS 65003
- Router C: Should see routes from AS 65001 via AS 65002

**✅ CHECKPOINT 3:**
- Networks advertised correctly por each AS
- Routes received from remote ASes
- BGP routes installed en IP routing table
- Transit functionality working (A ↔ B ↔ C)

---

### **PASO 4: BGP Path Manipulation**

#### **4.1 Local Preference Configuration**

**Router A - prefer certain routes:**
```bash
# Create route-map to set local preference:
/routing filter add chain=bgp-in prefix=172.16.3.0/24 action=accept set-bgp-local-pref=200 comment="Prefer route to AS 65003"

# Apply filter to peer:
/routing bgp peer set [find remote-as=65002] in-filter=bgp-in
```

#### **4.2 AS-Path Prepending**

**Router C - make routes less attractive inbound:**
```bash
# Prepend AS path to make routes less preferred:
/routing filter add chain=bgp-out prefix=172.16.3.0/24 action=accept set-bgp-prepend=3 comment="Make route less attractive"

# Apply filter:
/routing bgp peer set [find remote-as=65002] out-filter=bgp-out
```

#### **4.3 MED (Multi-Exit Discriminator)**

**Router B - influence inbound traffic:**
```bash
# Set MED on advertisements:
/routing filter add chain=bgp-out prefix=192.168.20.0/24 action=accept set-bgp-med=100

/routing bgp peer set [find remote-as=65001] out-filter=bgp-out
```

#### **4.4 Verify Path Attributes**

**MÉTODO CLI:**
```bash
# Check BGP path attributes:
/routing bgp advertisements print detail where network=172.16.3.0/24

# Look for:
# - AS-Path length (should see prepending)
# - Local Preference values  
# - MED values
# - Origin codes
```

**✅ CHECKPOINT 4:**
- Path manipulation policies applied
- BGP attributes modified correctly
- Route preferences changed as intended
- Traffic flows follow preferred paths

---

### **PASO 5: BGP Communities**

#### **5.1 Configure Community Tagging**

**Router B (Transit Provider) - tag customer routes:**
```bash
# Tag routes from AS 65001:
/routing filter add chain=bgp-in src-address=10.1.1.1 action=accept set-bgp-communities=65002:100 comment="Tag AS 65001 routes"

# Tag routes from AS 65003:  
/routing filter add chain=bgp-in src-address=10.3.3.3 action=accept set-bgp-communities=65002:200 comment="Tag AS 65003 routes"

# Apply filters:
/routing bgp peer set [find remote-as=65001] in-filter=bgp-in
/routing bgp peer set [find remote-as=65003] in-filter=bgp-in
```

#### **5.2 Community-Based Filtering**

**Router A - filter based on communities:**
```bash
# Accept only specific communities:
/routing filter add chain=bgp-in bgp-communities=65002:200 action=accept comment="Accept AS 65003 via AS 65002"  
/routing filter add chain=bgp-in action=discard comment="Deny all others"

# Apply filter (be careful - this is restrictive):
# /routing bgp peer set [find remote-as=65002] in-filter=bgp-in
```

#### **5.3 Well-Known Communities**

**Configure NO_EXPORT community:**
```bash
# Routes tagged with NO_EXPORT won't be advertised to eBGP peers:
/routing filter add chain=bgp-out prefix=192.168.10.0/24 action=accept set-bgp-communities=no-export comment="Local use only"
```

**✅ CHECKPOINT 5:**
- BGP communities configured y propagated
- Community-based policies working
- Route tagging functioning correctly
- Well-known communities respected

---

### **PASO 6: BGP Monitoring y Troubleshooting**

#### **6.1 BGP Session Monitoring**

**MÉTODO CLI:**
```bash
# Monitor BGP session status:
/routing bgp peer print status

# Check session uptime y statistics:
/routing bgp peer print stats

# Monitor BGP keepalives:
/log add topics=bgp action=memory
/log print where topics~"bgp"
```

#### **6.2 BGP Table Analysis**

**Comprehensive BGP table analysis:**
```bash
# Full BGP table:
/routing bgp advertisements print

# Routes per AS:
/routing bgp advertisements print where bgp-as-path~"65001"
/routing bgp advertisements print where bgp-as-path~"65003"

# Best path selection:
/routing bgp advertisements print where network=172.16.3.0/24
```

#### **6.3 Performance Metrics**

**Monitor BGP performance:**
```bash
# BGP memory usage:
/system resource print

# Route table size:
/ip route print count-only where protocol=bgp

# BGP update frequency:
/routing bgp peer print stats
# Look at "updates sent/received"
```

#### **6.4 Troubleshooting Common Issues**

**Issue: Routes not propagating**
```bash
# Check route advertisement:
/routing bgp network print

# Verify network exists en routing table:
/ip route print where dst-address=192.168.10.0/24

# Check route filters:
/routing filter print
```

**Issue: Suboptimal path selection**
```bash
# Analyze path selection:
/routing bgp advertisements print detail where network=X.X.X.X/Y

# Compare path attributes:
# - AS-Path length
# - Local Preference  
# - Origin code
# - MED values
```

**✅ CHECKPOINT 6:**
- BGP monitoring tools functioning
- Performance metrics within acceptable ranges
- Troubleshooting procedures tested
- Common issues identified y resolved

---

### **PASO 7: Advanced BGP Features**

#### **7.1 Route Reflector (Optional)**

**Si hay tiempo, configure route reflector en AS 65002:**
```bash
# Router B as route reflector:
/routing bgp peer set [find remote-as=65001] route-reflect=yes
/routing bgp peer set [find remote-as=65003] route-reflect=yes

# Note: This changes dynamics - monitor carefully
```

#### **7.2 BGP Graceful Restart**

**Enable graceful restart para better convergence:**
```bash
# Configure graceful restart:
/routing bgp instance set default graceful-restart=yes
```

#### **7.3 BGP Confederation (Conceptual)**

**Discuss confederation concepts:**
- Used para scaling iBGP en large networks
- Creates sub-ASes within main AS
- Reduces iBGP mesh requirements
- RouterOS supports confederation configuration

**✅ CHECKPOINT 7:**
- Advanced features understood conceptually
- Route reflector tested (if implemented)
- Graceful restart configured
- Confederation concepts discussed

---

### **PASO 8: Documentation y Analysis**

#### **8.1 BGP Design Documentation**

```
BGP IMPLEMENTATION ANALYSIS

AUTONOMOUS SYSTEMS:
├─ AS 65001: Customer ISP (Router A)
├─ AS 65002: Transit Provider (Router B)  
├─ AS 65003: Customer ISP (Router C)
└─ Total prefixes advertised: 6 networks

SESSION ESTABLISHMENT:
├─ eBGP sessions: A-B, B-C
├─ Session establishment time: ~30 seconds
├─ TCP connections: 179/BGP
└─ Authentication: None (can be added)

ROUTE POLICIES IMPLEMENTED:
├─ Local Preference manipulation (Router A)
├─ AS-Path prepending (Router C)
├─ MED settings (Router B)
├─ Community tagging (all routers)
└─ Route filtering (selective)

CONVERGENCE CHARACTERISTICS:
├─ Initial convergence: ~60 seconds
├─ Route withdrawal propagation: ~30 seconds  
├─ Best path recalculation: Immediate
└─ Session re-establishment: ~30 seconds

TROUBLESHOOTING PERFORMED:
├─ Session establishment issues: ✅
├─ Route propagation verification: ✅
├─ Path selection analysis: ✅
├─ Policy effectiveness testing: ✅
└─ Performance monitoring: ✅
```

#### **8.2 Lessons Learned**

**Key BGP takeaways:**
- BGP is policy-driven, not performance-driven
- Session establishment requires careful planning
- Path attributes control traffic flow effectively
- Communities provide flexible policy implementation
- Troubleshooting requires understanding of BGP mechanics
- BGP scales well but requires proper design

**✅ FINAL CHECKPOINT LAB 3:**
- BGP sessions established y stable
- Route propagation functioning correctly
- Policy implementation successful
- Advanced features tested
- Comprehensive documentation completed
- Troubleshooting methodology mastered

---

## **LABORATORIO 4: Optimization y Troubleshooting Avanzado**
**Duración estimada: 60 minutos**

### **Objetivo:**
Aplicar técnicas de optimización de routing, implementar route filtering avanzado, y develop systematic troubleshooting methodology.

---

### **PASO 1: Route Filtering Avanzado**

#### **1.1 Comprehensive Prefix Lists**

**Create detailed filtering policies:**
```bash
# Router B (Transit Provider) - Implement comprehensive filtering:

# Block private networks from BGP:
/routing filter add chain=bgp-out prefix=10.0.0.0/8 action=discard comment="Block RFC1918"
/routing filter add chain=bgp-out prefix=172.16.0.0/12 action=discard comment="Block RFC1918"  
/routing filter add chain=bgp-out prefix=192.168.0.0/16 action=discard comment="Block RFC1918"

# Block default route from customers:
/routing filter add chain=bgp-in prefix=0.0.0.0/0 action=discard comment="Block default from customers"

# Allow only customer networks:
/routing filter add chain=bgp-in prefix=192.168.0.0/16 prefix-length=24-32 action=accept comment="Allow customer LANs"
/routing filter add chain=bgp-in prefix=172.16.0.0/16 prefix-length=24-32 action=accept comment="Allow customer networks"

# Default deny:
/routing filter add chain=bgp-in action=discard comment="Deny all others"
```

#### **1.2 Route Aggregation**

**Implement route summarization:**
```bash
# Router A - Aggregate customer networks:
# Instead of advertising 172.16.1.0/24, 172.16.2.0/24, etc.
# Advertise summary 172.16.0.0/16

# Remove specific advertisements:
/routing bgp network remove [find network=172.16.1.0/24]

# Add summary route:
/ip route add dst-address=172.16.0.0/16 type=blackhole comment="Summary route"
/routing bgp network add network=172.16.0.0/16 comment="Customer summary"
```

#### **1.3 AS-Path Filtering**

**Filter based on AS-Path attributes:**
```bash
# Router C - Accept routes only from trusted AS paths:
/routing filter add chain=bgp-in bgp-as-path="^65002$" action=accept comment="Direct from AS 65002"
/routing filter add chain=bgp-in bgp-as-path="^65002 65001$" action=accept comment="Via AS 65002 from AS 65001"  
/routing filter add chain=bgp-in action=discard comment="Block unexpected AS paths"

# Apply filter:
/routing bgp peer set [find remote-as=65002] in-filter=bgp-in
```

**✅ CHECKPOINT 1:**
- Comprehensive filtering policies implemented
- Route aggregation functioning
- AS-Path filtering working correctly
- Security enhanced through filtering

---

### **PASO 2: Performance Optimization**

#### **2.1 BGP Optimization**

**Optimize BGP timers y behavior:**
```bash
# Faster BGP convergence:
/routing bgp instance set default keepalive-time=30s hold-time=90s

# Enable BGP fast external fallover:
/routing bgp peer set [find] tcp-md5-key="" connect-retry=30s

# Optimize BGP scanner intervals:
/routing bgp instance set default scanner=30s
```

#### **2.2 Routing Table Optimization**

**Optimize route lookup performance:**
```bash
# Create routing marks for policy routing:
/ip firewall mangle add chain=prerouting src-address=192.168.10.0/24 action=mark-routing new-routing-mark=priority-traffic

# Separate routing tables:
/routing table add name=priority-table fib

# Install specific routes en priority table:
/ip route add dst-address=0.0.0.0/0 gateway=10.0.1.2 routing-table=priority-table distance=1
```

#### **2.3 Memory Optimization**

**Optimize routing protocol memory usage:**
```bash
# Limit OSPF LSA database size (if OSPF still running):
/routing ospf area set backbone default-cost=100

# Optimize BGP memory usage:
/routing bgp instance set default hold-time=180s keepalive-time=60s

# Monitor memory usage:
/system resource print
```

#### **2.4 CPU Optimization**

**Reduce CPU impact of routing protocols:**
```bash
# Optimize SPF calculation intervals:
/routing ospf instance set default spf-delay=5s spf-max-delay=10s

# Reduce BGP update processing:
/routing bgp peer set [find] soft-reconfiguration=no

# Monitor CPU usage:
/system resource cpu print
```

**✅ CHECKPOINT 2:**
- Routing protocol timers optimized
- Memory usage minimized  
- CPU impact reduced
- Performance metrics improved

---

### **PASO 3: Advanced Route Redistribution**

#### **3.1 Selective Redistribution**

**Carefully control route redistribution:**
```bash
# OSPF to BGP redistribution with filtering:
/routing filter add chain=ospf-to-bgp prefix=192.168.0.0/16 prefix-length=24-32 action=accept set-bgp-med=50
/routing filter add chain=ospf-to-bgp action=discard

# BGP to OSPF redistribution:
/routing filter add chain=bgp-to-ospf bgp-as-path="^65002$" action=accept set-ospf-tag=100
/routing filter add chain=bgp-to-ospf action=discard

# Apply redistribution filters:
/routing ospf instance set default redistribute-bgp=yes redistribute-filter=bgp-to-ospf
/routing bgp instance set default redistribute-ospf=yes redistribute-filter=ospf-to-bgp
```

#### **3.2 Route Tagging**

**Implement comprehensive route tagging:**
```bash
# Tag routes by source protocol:
/routing filter add chain=static-tag action=accept set-ospf-tag=1 comment="Static routes"
/routing filter add chain=ospf-tag action=accept set-bgp-communities=65002:1000 comment="OSPF routes"
/routing filter add chain=connected-tag action=accept set-bgp-communities=65002:2000 comment="Connected routes"
```

#### **3.3 Administrative Distance Manipulation**

**Control route preference through administrative distance:**
```bash
# Prefer OSPF over static routes (unusual):
/ip route set [find static=yes] distance=130

# Prefer specific BGP routes:
/routing filter add chain=bgp-priority bgp-as-path="^65001$" action=accept set-distance=50
```

**✅ CHECKPOINT 3:**
- Selective redistribution implemented
- Route tagging functioning
- Administrative distances optimized
- Route preference control effective

---

### **PASO 4: Systematic Troubleshooting**

#### **4.1 Develop Troubleshooting Methodology**

**Layer-by-layer troubleshooting approach:**
```bash
# LAYER 1 (Physical):
/interface print where running=no
/interface monitor ether1

# LAYER 2 (Data Link):  
/interface bridge host print
/tool ping 10.0.1.2

# LAYER 3 (Network):
/ip route print where active=no  
/tool traceroute 192.168.30.1

# LAYER 4 (Transport):
/tool telnet 10.2.2.2 179  # BGP port

# APPLICATION (Routing Protocols):
/routing bgp peer print status
/routing ospf neighbor print
```

#### **4.2 Routing Protocol Specific Diagnostics**

**OSPF troubleshooting:**
```bash
# OSPF neighbor issues:
/routing ospf neighbor print detail
/routing ospf area print  
/routing ospf interface print detail

# LSA database problems:
/routing ospf lsa print where age>3500
/routing ospf lsa print count-only

# Common OSPF issues checklist:
# □ Area ID mismatch
# □ Authentication failure  
# □ Hello/Dead interval mismatch
# □ Network type mismatch
# □ MTU mismatch
```

**BGP troubleshooting:**
```bash
# BGP session problems:
/routing bgp peer print status
/log print where topics~"bgp"

# Route advertisement issues:
/routing bgp advertisements print where network=X.X.X.X/Y
/routing bgp network print

# Common BGP issues checklist:
# □ AS number mismatch
# □ Router ID conflicts
# □ Update-source misconfiguration  
# □ Route filtering blocking advertisements
# □ Next-hop reachability issues
```

#### **4.3 Performance Troubleshooting**

**Identify performance bottlenecks:**
```bash
# Route lookup performance:
/tool profile duration=10 on-event="/ip route print dst-address=192.168.30.1"

# Protocol convergence timing:
/system logging add topics=ospf,bgp action=memory
# Generate failure y measure convergence time

# Memory leak detection:
# Take baseline: /system resource print
# Run protocols for extended period
# Compare memory usage over time
```

#### **4.4 Traffic Flow Analysis**

**Analyze actual traffic paths:**
```bash
# Verify traffic follows expected routing:
/tool traceroute 192.168.30.1 source=192.168.10.1

# Check for asymmetric routing:
# Trace forward path from A to C
# Trace return path from C to A  
# Should be symmetric unless policy routing applied

# Monitor traffic distribution:
/tool traffic-monitor interface=ether1 duration=30s
```

**✅ CHECKPOINT 4:**
- Systematic troubleshooting methodology developed
- Protocol-specific diagnostics mastered
- Performance bottlenecks identified
- Traffic flow analysis completed

---

### **PASO 5: Comprehensive Network Testing**

#### **5.1 Failure Scenario Testing**

**Test comprehensive failure scenarios:**

**Scenario 1: Primary router failure**
```bash
# Simulate Router B (transit provider) failure:
# Physically disconnect Router B

# Expected behavior:
# - Direct communication A-C should work (if direct link exists)
# - Transit traffic should fail
# - BGP sessions should timeout y reconvergence

# Monitor:
/routing bgp peer print status
/ip route print where protocol=bgp
```

**Scenario 2: Partial network partition**
```bash
# Simulate area partition en OSPF:
# Disconnect Router A from Area 1

# Expected behavior:
# - Area 1 isolated from backbone
# - Inter-area routing fails
# - Intra-area routing continues

# Recovery testing:
# - Restore connectivity
# - Measure reconvergence time
# - Verify full functionality restoration
```

#### **5.2 Load Testing**

**Test routing under load:**
```bash
# Generate traffic load:
/tool traffic-generator interface=ether1 packet-size=1024 rate=10000

# Monitor during load:
/system resource print
/routing bgp peer print stats
/interface print stats

# Verify routing stability under load
```

#### **5.3 Configuration Validation**

**Validate entire configuration:**
```bash
# Export complete configuration:
/export file=final-routing-config

# Verify all routing protocols:
/routing bgp peer print status
/routing ospf neighbor print  
/ip route print count-only

# Test all network reachability:
# Create comprehensive ping test script
:for i from=1 to=3 do={
    /ping (192.168.10.1, 192.168.20.1, 192.168.30.1) count=1 size=64
}
```

**✅ CHECKPOINT 5:**
- Comprehensive failure testing completed
- Load testing performed successfully
- Configuration validated
- All network reachability confirmed

---

### **PASO 6: Final Documentation y Best Practices**

#### **6.1 Complete Network Documentation**

```markdown
# ROUTING IMPLEMENTATION FINAL REPORT

## NETWORK TOPOLOGY
```
AS 65001 (A) ←→ AS 65002 (B) ←→ AS 65003 (C)
     │               │               │
OSPF Area 1    OSPF Area 0    OSPF Area 1
```

## PROTOCOLS IMPLEMENTED
├─ Static Routing: Basic connectivity y backup paths
├─ OSPF: Multi-area with ABR functionality  
├─ BGP: Inter-AS routing with policy control
└─ Route Redistribution: Controlled protocol interaction

## OPTIMIZATIONS APPLIED
├─ Route Filtering: Security y performance
├─ Route Aggregation: Reduced table size
├─ Timer Optimization: Faster convergence
├─ Administrative Distance: Route preference control
└─ Policy Routing: Traffic engineering

## PERFORMANCE METRICS
├─ OSPF Convergence: <5 seconds
├─ BGP Convergence: <30 seconds
├─ Route Table Size: ~25 routes total
├─ Memory Usage: <64MB routing processes
└─ CPU Impact: <10% during normal operation

## TROUBLESHOOTING PROCEDURES
├─ Layer-by-layer diagnosis methodology
├─ Protocol-specific diagnostic commands
├─ Performance analysis tools
├─ Configuration validation procedures
└─ Failure scenario recovery processes

## LESSONS LEARNED
├─ Route filtering essential for security y performance
├─ Multiple protocols can coexist with proper design
├─ Systematic troubleshooting saves time
├─ Documentation critical for maintenance
└─ Testing validates design assumptions
```

#### **6.2 Best Practices Summary**

**Routing Design Best Practices:**
1. **Hierarchical Design:** Use areas y AS boundaries appropriately
2. **Route Filtering:** Always filter advertisements para security
3. **Redundancy:** Implement backup paths with appropriate metrics  
4. **Summarization:** Aggregate routes where possible
5. **Documentation:** Maintain current network documentation
6. **Monitoring:** Implement continuous monitoring
7. **Testing:** Regularly test failure scenarios

#### **6.3 Maintenance Procedures**

**Regular maintenance tasks:**
```bash
# Weekly tasks:
/routing bgp peer print stats          # Check BGP session health
/routing ospf neighbor print           # Verify OSPF adjacencies
/system resource print                # Monitor resource usage

# Monthly tasks:
/system backup save name=monthly-backup
/log print where topics~"error"       # Review error logs
/ip route print count-only            # Monitor table growth

# Quarterly tasks:
# - Review y update route filtering policies
# - Test backup path functionality  
# - Validate documentation accuracy
# - Performance optimization review
```

**✅ FINAL CHECKPOINT LAB 4:**
- Advanced optimization techniques implemented
- Comprehensive troubleshooting methodology mastered
- Failure scenarios tested y documented
- Best practices documented y applied
- Maintenance procedures established
- Complete network documentation finalized

---

## **EVALUACIÓN Y ENTREGABLES**

### **Checklist de Completion Total**

**Laboratorio 1: Static Routing (✅/❌)**
- [ ] Static routes configured con redundancy
- [ ] Load balancing implemented y tested
- [ ] Floating routes functioning correctly
- [ ] Performance optimization applied
- [ ] Failover scenarios tested

**Laboratorio 2: OSPF Multi-Área (✅/❌)**
- [ ] OSPF multi-area topology implemented  
- [ ] ABR functionality verified
- [ ] LSA database analyzed
- [ ] Convergence testing completed
- [ ] Authentication y optimization configured

**Laboratorio 3: BGP Básico (✅/❌)**
- [ ] BGP sessions established between ASes
- [ ] Route advertisements functioning
- [ ] Path manipulation policies applied
- [ ] Communities configured y tested
- [ ] Advanced features implemented

**Laboratorio 4: Advanced Optimization (✅/❌)**
- [ ] Route filtering comprehensively implemented
- [ ] Performance optimization applied
- [ ] Troubleshooting methodology mastered
- [ ] Failure scenarios tested
- [ ] Documentation completed

### **Entregables Requeridos**

1. **Configuration Files:**
   - Complete routing configuration export (.rsc)
   - Backup de cada major configuration milestone (.backup)
   - Protocol-specific configurations exported

2. **Documentation:**
   - Network topology diagrams
   - IP addressing y AS assignment tables
   - Routing policy documentation
   - Troubleshooting procedures developed
   - Performance testing results

3. **Testing Evidence:**
   - Connectivity test results
   - Failover testing logs
   - Performance measurement data  
   - Route table snapshots
   - Protocol status screenshots

4. **Analysis Reports:**
   - Protocol comparison analysis
   - Optimization effectiveness assessment
   - Lessons learned summary
   - Best practices recommendations

### **Criterios de Evaluación**

**Excelente (90-100%):**
- All routing protocols implemented flawlessly
- Advanced optimization techniques applied successfully
- Comprehensive troubleshooting demonstrated
- Outstanding documentation y analysis
- Innovation en implementation approach

**Competente (75-89%):**
- Basic routing protocols working correctly
- Standard optimization applied
- Good troubleshooting capability
- Adequate documentation
- Understanding of concepts demonstrated

**En Desarrollo (60-74%):**
- Basic functionality achieved with guidance
- Limited optimization applied
- Basic troubleshooting with assistance
- Basic documentation provided
- Fundamental concepts understood

**Insuficiente (<60%):**
- Major configuration errors preventing functionality
- No optimization applied
- Unable to troubleshoot effectively
- Poor or missing documentation
- Fundamental misunderstanding of concepts

---

¡Excelentes! Han completado exitosamente el Módulo 3 de Routing Estático y Dinámico. Ahora tienen las habilidades fundamentales para diseñar, implementar y mantener redes empresariales complejas con múltiples protocolos de routing.