# Instructivo Paso a Paso - Laboratorios Módulo 2
## Configuración de Interfaces y Switching

---

## **INFORMACIÓN IMPORTANTE**

**⚠️ ANTES DE COMENZAR:**
- Completar Módulo 1 (configuración inicial segura)
- Tener acceso a RouterBOARD RB951-2HnD
- Winbox instalado y funcionando
- Cables Ethernet disponibles
- Laptops/PCs para testing

**📋 EQUIPAMIENTO POR GRUPO:**
- 1 RouterBOARD RB951-2HnD
- 4 cables Ethernet CAT5e/6
- Laptops con Winbox
- Acceso a switch del aula

**🎯 OBJETIVOS:**
Al completar estos laboratorios podrás:
- Configurar interfaces físicas y virtuales
- Implementar bridge switching
- Crear y gestionar VLANs
- Configurar inter-VLAN routing
- Troubleshoot problemas de conectividad

---

## **LABORATORIO 1: Configuración de Interfaces y Monitoreo**
**Duración estimada: 45 minutos**

### **Objetivo:**
Configurar interfaces físicas, aplicar monitoreo de tráfico y optimizar parámetros de red.

### **Topología:**
```
[Laptop-1] ── ether2 (lan-port1)
[Laptop-2] ── ether3 (lan-port2)     RB951-2HnD
[Laptop-3] ── ether4 (lan-port3)     [ether1] ── Switch Aula ── Internet
[Laptop-4] ── ether5 (lan-port4)     [wlan1]  ── WiFi
```

---

### **PASO 1: Conectar y Verificar Estado Inicial**

#### **1.1 Conexión Initial**
1. **Conectar RB951-2HnD:**
   - Alimentación conectada (LED PWR encendido)
   - Cable desde laptop a ether1
   - Laptop configurado para DHCP automático

2. **Conectar con Winbox:**
   - Abrir Winbox
   - Clic en "..." (botón Neighbors)
   - Doble clic en dispositivo mostrado (usar MAC address)
   - Usuario: `netadmin` (del Módulo 1)
   - Password: [password configurado en Módulo 1]
   - Clic "Connect"

#### **1.2 Inventario de Interfaces**

**MÉTODO WINBOX:**
1. Ir a **Interfaces → Interface**
2. Observar lista de interfaces disponibles:
   - `ether1` - `ether5` (puertos físicos)
   - `wlan1` (wireless interface)
   - Posible `bridge` (del módulo anterior)

**MÉTODO CLI:**
```bash
# Abrir terminal en Winbox: New Terminal
/interface print
```

**✅ CHECKPOINT 1:**
```
Debes ver algo similar a:
0  ether1    enabled   running
1  ether2    enabled   not-running  
2  ether3    enabled   not-running
3  ether4    enabled   not-running
4  ether5    enabled   not-running
5  wlan1     enabled   running
```

**🔧 TROUBLESHOOTING:**
- Si interfaces muestran `disabled`: Habilitar con comando o Winbox
- Si `not-running`: Verificar cables conectados

---

### **PASO 2: Configurar Parámetros de Interfaces**

#### **2.1 Configurar Interface ether2**

**MÉTODO WINBOX:**
1. **Interfaces → Interface**
2. **Doble clic en `ether2`**
3. **General Tab:**
   - **Name:** `lan-port1`
   - **Comment:** `Laptop Estudiante 1`
   - **MTU:** `1500` (mantener default)
   - **Disabled:** ❌ (NO marcado)
4. **Clic "OK"**

**MÉTODO CLI:**
```bash
/interface ethernet set ether2 name=lan-port1 comment="Laptop Estudiante 1"
```

#### **2.2 Repetir para Otras Interfaces**

**CONFIGURACIONES SIMILARES:**
```bash
# CLI method - ejecutar uno por uno:
/interface ethernet set ether3 name=lan-port2 comment="Laptop Estudiante 2"
/interface ethernet set ether4 name=lan-port3 comment="Laptop Estudiante 3" 
/interface ethernet set ether5 name=lan-port4 comment="Laptop Estudiante 4"
```

**WINBOX method:** Repetir proceso anterior para ether3, ether4, ether5

#### **2.3 Configurar Wireless Básico**

**MÉTODO WINBOX:**
1. **Interfaces → Interface → Doble clic `wlan1`**
2. **Wireless Tab:**
   - **SSID:** `Lab-Grupo[XX]-WiFi` (reemplazar XX con número de grupo)
   - **Frequency:** `2442` (canal 7)
   - **Comment:** `WiFi Lab Grupo XX`
3. **Clic "OK"**

**MÉTODO CLI:**
```bash
/interface wireless set wlan1 ssid="Lab-Grupo01-WiFi" frequency=2442 comment="WiFi Lab Grupo 01"
```

**✅ CHECKPOINT 2:**
Verificar cambios aplicados:
```bash
/interface print
# Debes ver los nuevos nombres y comments
```

---

### **PASO 3: Monitoreo de Interfaces**

#### **3.1 Conectar Dispositivo de Prueba**
1. **Conectar laptop/PC a `lan-port1` (ether2)**
2. **Verificar que obtiene IP automática**
3. **Verificar conectividad:** `ping 192.168.88.1`

#### **3.2 Monitoreo en Tiempo Real**

**MÉTODO WINBOX:**
1. **Interfaces → Interface**
2. **Seleccionar `lan-port1`**
3. **Clic en pestaña "Traffic"**
4. **Observar estadísticas RX/TX en tiempo real**

**MÉTODO CLI:**
```bash
# Monitoreo de tráfico en tiempo real (10 segundos):
/interface monitor-traffic interface=lan-port1 duration=10
```

#### **3.3 Generar Tráfico para Observar**

**Desde laptop conectado:**
```bash
# Windows:
ping -t 8.8.8.8

# Linux/Mac:
ping 8.8.8.8
```

**Observar en RouterOS:**
- Contadores RX/TX incrementando
- Paquetes por segundo
- Bytes transferidos

#### **3.4 Monitor Interface Específica**

**MÉTODO CLI:**
```bash
# Monitor detallado de interface:
/interface ethernet monitor lan-port1

# Ver estadísticas acumuladas:
/interface print stats
```

**WINBOX Method:**
1. **Seleccionar `lan-port1`**
2. **Clic pestaña "Status"**
3. **Observar:** Link speed, duplex, auto-negotiation results

**✅ CHECKPOINT 3:**
Debes observar:
- Tráfico RX/TX activo durante ping
- Link speed negociado (10/100/1000)
- Duplex mode (full-duplex típicamente)
- Counters incrementando

---

### **PASO 4: Testing de Performance**

#### **4.1 Bandwidth Test Interno**

**MÉTODO CLI:**
```bash
# Test de bandwidth a servidor externo:
/tool bandwidth-test direction=both address=8.8.8.8 protocol=tcp duration=10s
```

**MÉTODO WINBOX:**
1. **Tools → Bandwidth Test**
2. **Configuration:**
   - **Direction:** both
   - **Address:** `8.8.8.8`
   - **Protocol:** TCP
   - **Duration:** 10 seconds
3. **Clic "Start"**

#### **4.2 Interpretar Resultados**

**Resultados Esperados:**
```
Bandwidth Test Results:
Upload: ~80-95 Mbps
Download: ~80-95 Mbps  
(Valores dependen de conexión Internet)
```

**Factores que Afectan Performance:**
- Conexión a Internet del aula
- Carga de red actual
- Overhead de protocolos
- Limitaciones del hardware

#### **4.3 Interface Statistics Detalladas**

**MÉTODO CLI:**
```bash
# Estadísticas detalladas:
/interface ethernet print stats-detail where name=lan-port1

# Reset counters (opcional):
/interface ethernet reset-counters lan-port1
```

**✅ CHECKPOINT 4:**
Documentar en cuaderno:
- Velocidades obtenidas en bandwidth test
- Auto-negotiation results
- Cualquier error observado en statistics

---

### **PASO 5: Configurar Seguridad Wireless**

#### **5.1 Crear Security Profile**

**MÉTODO WINBOX:**
1. **Wireless → Security Profiles**
2. **Clic "+" (Add)**
3. **Configuration:**
   - **Name:** `lab-security`
   - **Mode:** `dynamic keys`
   - **Authentication Types:** ✅ `WPA2 PSK`
   - **WPA2 Pre-Shared Key:** `Lab2024Seguro!`
4. **Clic "OK"**

**MÉTODO CLI:**
```bash
/interface wireless security-profiles add name=lab-security mode=dynamic-keys authentication-types=wpa2-psk wpa2-pre-shared-key="Lab2024Seguro!"
```

#### **5.2 Aplicar Security Profile**

**MÉTODO WINBOX:**
1. **Interfaces → Interface → Doble clic `wlan1`**
2. **Wireless Tab:**
   - **Security Profile:** `lab-security`
3. **Clic "OK"**

**MÉTODO CLI:**
```bash
/interface wireless set wlan1 security-profile=lab-security
```

#### **5.3 Verificar Wireless Functionality**

1. **Desde dispositivo móvil:**
   - Buscar red `Lab-Grupo[XX]-WiFi`
   - Conectar con password: `Lab2024Seguro!`
   - Verificar obtención de IP

2. **Test conectividad:**
   - Ping al gateway: `192.168.88.1`
   - Ping a Internet: `8.8.8.8`

**✅ CHECKPOINT 5:**
- Wireless network visible y secure
- Dispositivos conectan correctamente
- Internet access funcional

---

## **LABORATORIO 2: Implementación de Bridge**
**Duración estimada: 60 minutos**

### **Objetivo:**
Crear bridge para conectar múltiples puertos, implementar STP/RSTP, y observar MAC address learning.

---

### **PASO 1: Preparar Configuración Clean**

#### **1.1 Backup Configuración Actual**

**MÉTODO CLI:**
```bash
# Crear backup antes de cambios importantes:
/export file=backup-antes-bridge
```

**MÉTODO WINBOX:**
1. **Files → Backup**
2. **Name:** `backup-antes-bridge`
3. **Password:** (opcional)
4. **Clic "Backup"**

#### **1.2 Remover Configuración IP Existente**

**⚠️ ADVERTENCIA:** Esto causará pérdida temporal de conectividad

**MÉTODO CLI:**
```bash
# Verificar configuración IP actual:
/ip address print

# Remover IP del bridge existente (si existe):
/ip address remove [find interface=bridge-lan]
```

**MÉTODO WINBOX:**
1. **IP → Addresses**
2. **Seleccionar dirección en bridge (si existe)**
3. **Clic "X" (Delete)**

---

### **PASO 2: Crear Bridge Principal**

#### **2.1 Create Bridge Interface**

**MÉTODO WINBOX:**
1. **Interfaces → Bridge**
2. **Clic "+" (Add)**
3. **Configuration:**
   - **Name:** `bridge-switching`
   - **Protocol:** `rstp`
   - **Comment:** `Bridge principal switching Grupo XX`
4. **STP Tab:**
   - **STP:** ✅ Enabled
   - **Hello Time:** `2s`
   - **Max Message Age:** `20s`
   - **Forward Delay:** `15s`
5. **Clic "OK"**

**MÉTODO CLI:**
```bash
/interface bridge add name=bridge-switching protocol=rstp comment="Bridge principal switching Grupo XX"
```

#### **2.2 Verificar Bridge Creado**

**MÉTODO CLI:**
```bash
/interface bridge print
# Debe mostrar bridge-switching created
```

**✅ CHECKPOINT 1:**
Bridge debe aparecer en lista de interfaces como:
- Name: bridge-switching
- Protocol: rstp
- Status: enabled

---

### **PASO 3: Agregar Puertos al Bridge**

#### **3.1 Agregar Puertos Sistemáticamente**

**MÉTODO WINBOX:**
1. **Interfaces → Bridge → Ports Tab**
2. **Clic "+" (Add)**
3. **Por cada puerto configurar:**

**Puerto ether1 (WAN):**
- **Interface:** `ether1`
- **Bridge:** `bridge-switching`
- **Comment:** `WAN/Internet connection`
- **Edge:** ❌ NO (puede conectar otro switch)
- **Point to Point:** ✅ YES

**Puertos LAN (ether2-5):**
- **Interface:** `lan-port1` (ether2)
- **Bridge:** `bridge-switching`  
- **Comment:** `LAN Port 1`
- **Edge:** ✅ YES (conecta host final)
- **Point to Point:** ✅ YES

**MÉTODO CLI:**
```bash
# Agregar puertos uno por uno:
/interface bridge port add bridge=bridge-switching interface=ether1 comment="WAN/Internet" edge=no point-to-point=yes

/interface bridge port add bridge=bridge-switching interface=lan-port1 comment="LAN Port 1" edge=yes point-to-point=yes

/interface bridge port add bridge=bridge-switching interface=lan-port2 comment="LAN Port 2" edge=yes point-to-point=yes

/interface bridge port add bridge=bridge-switching interface=lan-port3 comment="LAN Port 3" edge=yes point-to-point=yes

/interface bridge port add bridge=bridge-switching interface=lan-port4 comment="LAN Port 4" edge=yes point-to-point=yes
```

#### **3.2 Agregar Wireless al Bridge**

**MÉTODO CLI:**
```bash
/interface bridge port add bridge=bridge-switching interface=wlan1 comment="Wireless LAN" edge=yes point-to-point=no
```

**MÉTODO WINBOX:**
Similar al proceso anterior, seleccionar `wlan1` como interface.

**✅ CHECKPOINT 2:**
Verificar puertos agregados:
```bash
/interface bridge port print
# Debe mostrar todos los puertos agregados al bridge
```

---

### **PASO 4: Configurar IP en Bridge**

#### **4.1 Asignar IP al Bridge**

**MÉTODO CLI:**
```bash
/ip address add address=192.168.88.1/24 interface=bridge-switching comment="Gateway principal"
```

**MÉTODO WINBOX:**
1. **IP → Addresses**
2. **Clic "+" (Add)**
3. **Configuration:**
   - **Address:** `192.168.88.1/24`
   - **Interface:** `bridge-switching`
   - **Comment:** `Gateway principal`
4. **Clic "OK"**

#### **4.2 Verificar Conectividad**

1. **Reconectar Winbox:** IP `192.168.88.1`
2. **Conectar laptops a puertos LAN**
3. **Verificar DHCP functionality** (del Módulo 1)
4. **Test ping entre devices**

**✅ CHECKPOINT 3:**
- Winbox conecta por IP 192.168.88.1
- Laptops obtienen IPs automáticamente
- Ping funciona entre devices

---

### **PASO 5: Observar MAC Address Learning**

#### **5.1 Monitor MAC Table en Tiempo Real**

**MÉTODO WINBOX:**
1. **Interfaces → Bridge → Hosts Tab**
2. **Observar tabla inicialmente vacía**
3. **Conectar dispositivos y generar tráfico**
4. **Observar MACs apareciendo automáticamente**

**MÉTODO CLI:**
```bash
# Ver tabla MAC actual:
/interface bridge host print

# Monitor continuo (actualización cada 1 segundo):
/interface bridge host print interval=1
```

#### **5.2 Generar Tráfico para Learning**

**Actividad por pasos:**
1. **Conectar Laptop 1 a lan-port1**
2. **Generar tráfico:** `ping 192.168.88.1`
3. **Observar:** MAC address aparece en tabla
4. **Conectar Laptop 2 a lan-port2**
5. **Ping entre laptops:** Laptop1 → Laptop2
6. **Observar:** Ambos MACs en tabla con puertos correctos

#### **5.3 Testing MAC Aging**

**MÉTODO CLI:**
```bash
# Verificar aging time:
/interface bridge print detail where name=bridge-switching

# Cambiar aging time para testing (opcional):
/interface bridge set bridge-switching ageing-time=1m
```

**Observar proceso:**
1. Desconectar dispositivo
2. Esperar aging time
3. Verificar MAC desaparece de tabla

**✅ CHECKPOINT 4:**
- MACs aparecen automáticamente cuando devices conectan
- Puerto correcto asociado con cada MAC
- Aging process funciona correctamente

---

### **PASO 6: Testing STP Convergence**

#### **6.1 Verificar Estado STP**

**MÉTODO CLI:**
```bash
# Status del bridge STP:
/interface bridge monitor bridge-switching

# Status de cada puerto:
/interface bridge port print where bridge=bridge-switching
```

**MÉTODO WINBOX:**
1. **Interfaces → Bridge**
2. **Seleccionar `bridge-switching`**
3. **Clic "Monitor" tab**
4. **Observar:** Root Bridge status, Port states

#### **6.2 Crear Loop Controlado (CUIDADO)**

**⚠️ SOLO HACER BAJO SUPERVISIÓN:**

**Procedimiento de Prueba:**
1. **Conectar 2 laptops a puertos diferentes**
2. **Verificar comunicación normal**
3. **Preparar cable extra**
4. **RÁPIDAMENTE conectar:** lan-port1 ↔ lan-port2
5. **Observar STP response**
6. **INMEDIATAMENTE desconectar loop**

**Observaciones Durante Test:**
```bash
# Monitor STP changes:
/interface bridge monitor bridge-switching

# Ver topology changes:
/interface bridge port monitor [find bridge=bridge-switching]
```

**Comportamiento Esperado:**
- Un puerto va a "discarding" state
- Topology change counter incrementa
- Network mantiene conectividad
- Recovery automático al remover loop

**✅ CHECKPOINT 5:**
- STP detecta loop automáticamente
- Un puerto bloquea correctamente
- Network recovery rápida (<2 segundos con RSTP)

---

### **PASO 7: Performance Analysis**

#### **7.1 Bridge Performance Statistics**

**MÉTODO CLI:**
```bash
# Bridge statistics:
/interface bridge print stats

# Port statistics:
/interface bridge port print stats

# General interface stats:
/interface print stats
```

#### **7.2 CPU Impact Analysis**

**MÉTODO CLI:**
```bash
# CPU usage monitoring:
/system resource cpu print

# Memory usage:
/system resource print
```

**Durante Diferentes Cargas:**
1. **Idle:** Sin tráfico
2. **Normal:** Ping básico
3. **High load:** Multiple bandwidth tests simultáneos

**✅ CHECKPOINT 6:**
Documentar:
- CPU usage en diferentes scenarios
- Bridge forwarding performance
- Memory utilization
- Hardware offload status (si disponible)

---

## **LABORATORIO 3: Implementación VLAN Avanzada**
**Duración estimada: 75 minutos**

### **Objetivo:**
Implementar VLANs usando Bridge VLAN table, configurar access y trunk ports, y establecer inter-VLAN routing.

### **Diseño VLAN Target:**
```
VLAN 10: Administración (192.168.10.0/24) → lan-port1 (ether2)
VLAN 20: Usuarios (192.168.20.0/24) → lan-port2, lan-port3 (ether3, ether4)  
VLAN 30: Guest (192.168.30.0/24) → lan-port4, wlan1 (ether5, wireless)
TRUNK: ether1 (tagged todas las VLANs)
```

---

### **PASO 1: Backup y Preparación**

#### **1.1 Backup Configuración Actual**

**MÉTODO CLI:**
```bash
/export file=backup-antes-vlans
```

#### **1.2 Documentar Estado Actual**

**MÉTODO CLI:**
```bash
# Verificar bridge actual:
/interface bridge print
/interface bridge port print

# Verificar IPs:
/ip address print
```

---

### **PASO 2: Habilitar VLAN Filtering**

#### **2.1 Enable VLAN Filtering en Bridge**

**⚠️ ADVERTENCIA CRÍTICA:**
Este comando causará pérdida temporal de conectividad. Reconectar usando MAC address si es necesario.

**MÉTODO CLI:**
```bash
/interface bridge set bridge-switching vlan-filtering=yes
```

**MÉTODO WINBOX:**
1. **Interfaces → Bridge**
2. **Doble clic `bridge-switching`**
3. **VLAN Tab:**
   - **VLAN Filtering:** ✅ YES
4. **Clic "OK"**

**Si Pierdes Conectividad:**
1. **Reconectar Winbox using MAC address**
2. **Continuar configuración inmediatamente**

---

### **PASO 3: Configurar VLAN Table**

#### **3.1 Configurar VLAN 10 (Administración)**

**MÉTODO WINBOX:**
1. **Interfaces → Bridge → VLANs Tab**
2. **Clic "+" (Add)**
3. **Configuration:**
   - **Bridge:** `bridge-switching`
   - **VLAN IDs:** `10`
   - **Tagged:** `bridge-switching,ether1`
   - **Untagged:** `lan-port1`
   - **Comment:** `VLAN Admin`
4. **Clic "OK"**

**MÉTODO CLI:**
```bash
/interface bridge vlan add bridge=bridge-switching tagged=bridge-switching,ether1 untagged=lan-port1 vlan-ids=10 comment="VLAN Admin"
```

#### **3.2 Configurar VLAN 20 (Usuarios)**

**MÉTODO CLI:**
```bash
/interface bridge vlan add bridge=bridge-switching tagged=bridge-switching,ether1 untagged=lan-port2,lan-port3 vlan-ids=20 comment="VLAN Users"
```

**MÉTODO WINBOX:**
Similar al proceso anterior:
- **VLAN IDs:** `20`
- **Tagged:** `bridge-switching,ether1`
- **Untagged:** `lan-port2,lan-port3`

#### **3.3 Configurar VLAN 30 (Guest)**

**MÉTODO CLI:**
```bash
/interface bridge vlan add bridge=bridge-switching tagged=bridge-switching,ether1 untagged=lan-port4,wlan1 vlan-ids=30 comment="VLAN Guest"
```

#### **3.4 Verificar VLAN Table**

**MÉTODO CLI:**
```bash
/interface bridge vlan print
```

**Debe mostrar:**
```
0  bridge-switching  10  bridge-switching,ether1  lan-port1
1  bridge-switching  20  bridge-switching,ether1  lan-port2,lan-port3  
2  bridge-switching  30  bridge-switching,ether1  lan-port4,wlan1
```

**✅ CHECKPOINT 1:**
- VLAN table configurada con 3 VLANs
- Tagged ports incluyen bridge y ether1
- Untagged ports asignados correctamente

---

### **PASO 4: Configurar Port VLANs (PVID)**

#### **4.1 Configurar Access Ports**

**MÉTODO CLI:**
```bash
# Puerto admin (VLAN 10):
/interface bridge port set [find interface=lan-port1] pvid=10 frame-types=admit-only-untagged-and-priority-tagged

# Puertos users (VLAN 20):  
/interface bridge port set [find interface=lan-port2] pvid=20 frame-types=admit-only-untagged-and-priority-tagged
/interface bridge port set [find interface=lan-port3] pvid=20 frame-types=admit-only-untagged-and-priority-tagged

# Puerto guest (VLAN 30):
/interface bridge port set [find interface=lan-port4] pvid=30 frame-types=admit-only-untagged-and-priority-tagged
/interface bridge port set [find interface=wlan1] pvid=30 frame-types=admit-only-untagged-and-priority-tagged
```

**MÉTODO WINBOX:**
1. **Interfaces → Bridge → Ports Tab**
2. **Doble clic en `lan-port1`**
3. **Configuration:**
   - **PVID:** `10`
   - **Frame Types:** `admit-only-untagged-and-priority-tagged`
4. **Repetir para cada puerto**

#### **4.2 Configurar Trunk Port**

**MÉTODO CLI:**
```bash
# Puerto trunk (ether1):
/interface bridge port set [find interface=ether1] frame-types=admit-only-vlan-tagged
```

#### **4.3 Verificar Port Configuration**

**MÉTODO CLI:**
```bash
/interface bridge port print where bridge=bridge-switching
```

**✅ CHECKPOINT 2:**
- Access ports con PVID correcto
- Frame-types configurado apropiadamente  
- Trunk port acepta solo tagged frames

---

### **PASO 5: Crear VLAN Interfaces**

#### **5.1 Create VLAN Interfaces para Routing**

**MÉTODO WINBOX:**
1. **Interfaces → VLAN**
2. **Clic "+" (Add)**
3. **Para VLAN 10:**
   - **Name:** `vlan10-admin`
   - **VLAN ID:** `10`
   - **Interface:** `bridge-switching`
   - **Comment:** `Gateway VLAN Admin`
4. **Repetir para VLAN 20 y 30**

**MÉTODO CLI:**
```bash
/interface vlan add name=vlan10-admin vlan-id=10 interface=bridge-switching comment="Gateway VLAN Admin"
/interface vlan add name=vlan20-users vlan-id=20 interface=bridge-switching comment="Gateway VLAN Users"  
/interface vlan add name=vlan30-guest vlan-id=30 interface=bridge-switching comment="Gateway VLAN Guest"
```

#### **5.2 Verificar VLAN Interfaces**

**MÉTODO CLI:**
```bash
/interface vlan print
```

**✅ CHECKPOINT 3:**
- 3 VLAN interfaces creadas
- Nombres descriptivos asignados
- VLAN IDs correctos

---

### **PASO 6: Configurar IP Addressing**

#### **6.1 Remover IP Anterior del Bridge**

**MÉTODO CLI:**
```bash
# Remover IP anterior:
/ip address remove [find interface=bridge-switching]
```

#### **6.2 Asignar IPs a VLAN Interfaces**

**MÉTODO CLI:**
```bash
/ip address add address=192.168.10.1/24 interface=vlan10-admin comment="Gateway Admin"
/ip address add address=192.168.20.1/24 interface=vlan20-users comment="Gateway Users"
/ip address add address=192.168.30.1/24 interface=vlan30-guest comment="Gateway Guest"
```

**MÉTODO WINBOX:**
1. **IP → Addresses**
2. **Clic "+" (Add)**
3. **Para cada VLAN:**
   - **Address:** `192.168.10.1/24` (cambiar según VLAN)
   - **Interface:** `vlan10-admin` (cambiar según VLAN)
   - **Comment:** descriptivo

#### **6.3 Verificar IP Configuration**

**MÉTODO CLI:**
```bash
/ip address print
```

**Debe mostrar:**
```
0  192.168.10.1/24  vlan10-admin
1  192.168.20.1/24  vlan20-users  
2  192.168.30.1/24  vlan30-guest
```

**✅ CHECKPOINT 4:**
- IPs asignadas a cada VLAN interface
- Subnets diferentes para cada VLAN
- Gateway addresses configurados

---

### **PASO 7: Configurar DHCP por VLAN**

#### **7.1 Crear DHCP Pools**

**MÉTODO CLI:**
```bash
/ip pool add name=pool-admin ranges=192.168.10.100-192.168.10.200 comment="Pool VLAN Admin"
/ip pool add name=pool-users ranges=192.168.20.100-192.168.20.200 comment="Pool VLAN Users"  
/ip pool add name=pool-guest ranges=192.168.30.100-192.168.30.200 comment="Pool VLAN Guest"
```

**MÉTODO WINBOX:**
1. **IP → Pool**
2. **Clic "+" (Add)**
3. **Para cada pool:**
   - **Name:** `pool-admin`
   - **Addresses:** `192.168.10.100-192.168.10.200`
   - **Comment:** descriptivo

#### **7.2 Crear DHCP Servers**

**MÉTODO CLI:**
```bash
/ip dhcp-server add name=dhcp-admin interface=vlan10-admin address-pool=pool-admin lease-time=1d disabled=no comment="DHCP Admin"
/ip dhcp-server add name=dhcp-users interface=vlan20-users address-pool=pool-users lease-time=8h disabled=no comment="DHCP Users"
/ip dhcp-server add name=dhcp-guest interface=vlan30-guest address-pool=pool-guest lease-time=1h disabled=no comment="DHCP Guest"
```

#### **7.3 Crear DHCP Networks**

**MÉTODO CLI:**
```bash
/ip dhcp-server network add address=192.168.10.0/24 gateway=192.168.10.1 dns-server=8.8.8.8 comment="Network Admin"
/ip dhcp-server network add address=192.168.20.0/24 gateway=192.168.20.1 dns-server=8.8.8.8 comment="Network Users"
/ip dhcp-server network add address=192.168.30.0/24 gateway=192.168.30.1 dns-server=8.8.8.8 comment="Network Guest"
```

**MÉTODO WINBOX:**
1. **IP → DHCP Server → Networks Tab**
2. **Clic "+" (Add)**
3. **Para cada network:**
   - **Address:** `192.168.10.0/24`
   - **Gateway:** `192.168.10.1`
   - **DNS Servers:** `8.8.8.8`

**✅ CHECKPOINT 5:**
- DHCP servers creados para cada VLAN
- Pools con rangos apropiados
- Networks configuradas con gateways correctos

---

### **PASO 8: Testing VLAN Functionality**

#### **8.1 Test DHCP Assignment**

**Procedimiento de Testing:**
1. **Conectar Laptop 1 a lan-port1 (VLAN 10)**
   - Verificar IP obtenida: `192.168.10.x`
   - Test ping gateway: `ping 192.168.10.1`

2. **Conectar Laptop 2 a lan-port2 (VLAN 20)**
   - Verificar IP obtenida: `192.168.20.x`  
   - Test ping gateway: `ping 192.168.20.1`

3. **Conectar Laptop 3 a lan-port4 (VLAN 30)**
   - Verificar IP obtenida: `192.168.30.x`
   - Test ping gateway: `ping 192.168.30.1`

#### **8.2 Test VLAN Isolation**

**Testing Matrix:**
```bash
# Desde Laptop VLAN 10:
ping 192.168.20.x  # Debe FUNCIONAR (inter-VLAN routing)
ping 192.168.30.x  # Debe FUNCIONAR (inter-VLAN routing)

# Desde Laptop VLAN 20:  
ping 192.168.10.x  # Debe FUNCIONAR
ping 192.168.30.x  # Debe FUNCIONAR

# Desde Laptop VLAN 30:
ping 192.168.10.x  # Debe FUNCIONAR  
ping 192.168.20.x  # Debe FUNCIONAR
```

**Nota:** Por defecto, inter-VLAN routing está habilitado. Para restricciones, necesitamos firewall (próximo paso).

#### **8.3 Verificar DHCP Leases**

**MÉTODO CLI:**
```bash
/ip dhcp-server lease print
```

**Debe mostrar leases activos en cada VLAN.**

**✅ CHECKPOINT 6:**
- DHCP funcionando en cada VLAN
- IPs asignadas en rangos correctos
- Gateways accesibles desde cada VLAN
- Inter-VLAN communication funcional

---

### **PASO 9: Configurar Políticas Inter-VLAN**

#### **9.1 Configurar Firewall Rules**

**Objetivo:** Implementar políticas de seguridad:
- Admin VLAN: Acceso total
- Users VLAN: Sin acceso a Admin VLAN  
- Guest VLAN: Solo Internet, no acceso LAN

**MÉTODO CLI:**
```bash
# Permitir established/related connections:
/ip firewall filter add chain=forward action=accept connection-state=established,related comment="Allow established"

# Permitir Admin VLAN acceso total:
/ip firewall filter add chain=forward action=accept src-address=192.168.10.0/24 comment="Admin full access"

# Permitir Users VLAN a Internet, bloquear Admin VLAN:
/ip firewall filter add chain=forward action=drop src-address=192.168.20.0/24 dst-address=192.168.10.0/24 comment="Block Users to Admin"
/ip firewall filter add chain=forward action=accept src-address=192.168.20.0/24 comment="Users allow other"

# Guest VLAN: Solo Internet (bloquear todas las LANs):
/ip firewall filter add chain=forward action=drop src-address=192.168.30.0/24 dst-address=192.168.0.0/16 comment="Block Guest to LAN"  
/ip firewall filter add chain=forward action=accept src-address=192.168.30.0/24 comment="Guest Internet only"

# Drop everything else:
/ip firewall filter add chain=forward action=drop comment="Drop all other"
```

**MÉTODO WINBOX:**
1. **IP → Firewall → Filter Rules**
2. **Clic "+" (Add)**
3. **Para cada rule, configurar:**
   - **Chain:** forward
   - **Src. Address / Dst. Address:** según política
   - **Action:** accept/drop
   - **Comment:** descriptivo

#### **9.2 Test Security Policies**

**Testing Scenarios:**
```bash
# Test 1: Desde Admin VLAN (debe funcionar todo):
ping 192.168.20.x  ✅ Should work
ping 192.168.30.x  ✅ Should work  
ping 8.8.8.8       ✅ Should work

# Test 2: Desde Users VLAN:
ping 192.168.10.x  ❌ Should FAIL (blocked)
ping 192.168.30.x  ✅ Should work
ping 8.8.8.8       ✅ Should work

# Test 3: Desde Guest VLAN:
ping 192.168.10.x  ❌ Should FAIL (blocked)
ping 192.168.20.x  ❌ Should FAIL (blocked)  
ping 8.8.8.8       ✅ Should work
```

**✅ CHECKPOINT 7:**
- Políticas de seguridad funcionando correctamente
- Admin tiene acceso completo
- Users bloqueados de Admin VLAN
- Guest aislado de todas las VLANs internas

---

### **PASO 10: Wireless Integration**

#### **10.1 Test Wireless VLAN Assignment**

1. **Conectar dispositivo móvil a WiFi**
2. **Verificar IP asignada:** Debe ser `192.168.30.x` (Guest VLAN)
3. **Test connectivity:**
   - Gateway: `ping 192.168.30.1` ✅
   - Internet: `ping 8.8.8.8` ✅  
   - LAN: `ping 192.168.10.1` ❌ (blocked)

#### **10.2 Monitor Wireless VLAN**

**MÉTODO CLI:**
```bash
# Verificar wireless clients:
/interface wireless registration-table print

# Monitor traffic en VLAN 30:
/interface monitor-traffic interface=vlan30-guest duration=10
```

**✅ CHECKPOINT 8:**
- Wireless devices asignados a Guest VLAN
- Políticas de seguridad aplicadas a wireless
- Internet access funcional, LAN blocked

---

## **LABORATORIO 4: Testing y Troubleshooting Avanzado**
**Duración estimada: 30 minutos**

### **Objetivo:**
Validar configuración completa, realizar troubleshooting sistemático, y optimizar performance.

---

### **PASO 1: Validation Testing Completa**

#### **1.1 Connectivity Matrix Testing**

**Crear Connectivity Matrix:**
```
TESTING MATRIX:
┌─────────────┬─────────┬─────────┬─────────┬─────────┐
│ From \ To   │ VLAN 10 │ VLAN 20 │ VLAN 30 │ Internet│
├─────────────┼─────────┼─────────┼─────────┼─────────┤
│ VLAN 10     │    ✅    │    ✅    │    ✅    │    ✅    │
│ VLAN 20     │    ❌    │    ✅    │    ✅    │    ✅    │  
│ VLAN 30     │    ❌    │    ❌    │    ✅    │    ✅    │
└─────────────┴─────────┴─────────┴─────────┴─────────┘
```

**Systematic Testing:**
1. **Documentar IP de cada device conectado**
2. **Test desde cada VLAN a cada destino**  
3. **Documentar resultados vs expected**
4. **Investigar any discrepancies**

#### **1.2 Performance Testing**

**Intra-VLAN Performance (Layer 2):**
```bash
# Test entre devices en misma VLAN:
/tool bandwidth-test address=192.168.20.100 local-address=192.168.20.1 direction=both duration=10s
```

**Inter-VLAN Performance (Layer 3):**
```bash
# Test entre different VLANs:
/tool bandwidth-test address=192.168.30.100 local-address=192.168.10.1 direction=both duration=10s
```

**Documentar resultados y comparar.**

---

### **PASO 2: Packet Capture Analysis**

#### **2.1 Capture VLAN Tagged Traffic**

**MÉTODO CLI:**
```bash
# Capture en trunk port para ver VLAN tags:
/tool sniffer start interface=ether1 filter-stream=yes

# Generar tráfico inter-VLAN
# Después de unos segundos:
/tool sniffer stop
/tool sniffer print
```

#### **2.2 Analyze VLAN Headers**

**Look for en packet captures:**
- 802.1Q headers con VLAN IDs correctos
- Priority markings
- Frame size increases (4 bytes por VLAN tag)

**✅ CHECKPOINT 1:**
- VLAN tags visible en trunk traffic
- Correct VLAN IDs in headers
- Untagged traffic en access ports

---

### **PASO 3: Systematic Troubleshooting**

#### **3.1 Common Problem Scenarios**

**Scenario 1: "Device no obtiene DHCP en VLAN 20"**

**Diagnostic Process:**
```bash
# Step 1: Verify physical (Layer 1):
/interface print where name=lan-port2
# Check: running status

# Step 2: Verify VLAN membership (Layer 2):  
/interface bridge port print where interface=lan-port2
# Check: PVID=20, frame-types correct

/interface bridge vlan print where vlan-ids=20
# Check: untagged includes lan-port2

# Step 3: Verify DHCP service (Layer 3):
/ip dhcp-server print where interface=vlan20-users  
# Check: enabled, correct pool

/ip pool print where name=pool-users
# Check: available addresses

/ip dhcp-server lease print
# Check: lease assignments

# Step 4: Test manual assignment:
# En laptop: configure static IP 192.168.20.150/24
# Test: ping 192.168.20.1
```

**Scenario 2: "Inter-VLAN connectivity no funciona"**

**Diagnostic Process:**
```bash
# Verify IP forwarding:
/ip settings print
# Check: ip-forward=yes (default)

# Verify VLAN interfaces UP:
/interface vlan print  
# Check: all interfaces running

# Verify routing table:
/ip route print
# Check: connected routes para each VLAN

# Check firewall rules:
/ip firewall filter print
# Verify: rules no blocking intended traffic
```

#### **3.2 Performance Troubleshooting**

**Slow Inter-VLAN Performance:**

**Diagnostic Steps:**
```bash
# Check hardware offload status:
/interface bridge port print where hw-offload=yes

# Monitor CPU usage during testing:
/system resource cpu print

# Check interface statistics for errors:
/interface print stats where name~"vlan"

# Monitor bridge performance:
/interface bridge print stats
```

**✅ CHECKPOINT 2:**
- Systematic troubleshooting approach applied
- Root causes identified correctly
- Performance issues diagnosed

---

### **PASO 4: Optimization y Best Practices**

#### **4.1 Performance Optimization**

**Hardware Offload Verification:**
```bash
# Verify bridge ports using hardware acceleration:
/interface bridge port print where hw-offload=yes

# If available but not enabled:
# Note: Some features may require software switching
```

**STP Optimization:**
```bash
# Verify edge ports configured:
/interface bridge port print where edge=yes

# Should include all access ports (lan-port1-4, wlan1)
```

#### **4.2 Security Hardening**

**Additional Security Measures:**
```bash
# Verify frame-types security:
/interface bridge port print detail

# Should show appropriate frame-types:
# Access ports: admit-only-untagged-and-priority-tagged  
# Trunk ports: admit-only-vlan-tagged
```

**Monitor for Security Issues:**
```bash
# Check for unexpected VLAN traffic:
/tool traffic-monitor interface=ether1

# Monitor firewall logs:
/log print where topics~"firewall"
```

#### **4.3 Management y Monitoring Setup**

**Setup Continuous Monitoring:**
```bash
# Enable interface graphing:
/tool graphing interface add interface=bridge-switching

# Setup logging para important events:
/system logging add topics=interface,bridge action=memory

# Configure SNMP (if required):
/snmp set enabled=yes
```

**✅ CHECKPOINT 3:**
- Performance optimized donde sea posible
- Security hardening applied
- Monitoring systems configured

---

### **PASO 5: Documentation y Backup Final**

#### **5.1 Complete Configuration Export**

**MÉTODO CLI:**
```bash
# Full configuration export:
/export file=config-vlans-completo

# Backup binary:
/system backup save name=vlans-funcional password="BackupLab2024!"
```

#### **5.2 Create Documentation**

**Configuration Summary Document:**
```
CONFIGURACIÓN VLAN IMPLEMENTADA - GRUPO [XX]

BRIDGE CONFIGURATION:
- Bridge: bridge-switching (RSTP enabled)
- VLAN Filtering: Enabled
- Hardware Offload: [Status]

VLANS CONFIGURADAS:
┌─────────────────────────────────────────────────────┐
│ VLAN 10: Admin (192.168.10.0/24)                   │
│ ├─ Ports: lan-port1 (access)                       │
│ ├─ DHCP: 192.168.10.100-200                        │
│ └─ Access: Full (to all VLANs + Internet)          │
├─────────────────────────────────────────────────────┤
│ VLAN 20: Users (192.168.20.0/24)                   │  
│ ├─ Ports: lan-port2, lan-port3 (access)            │
│ ├─ DHCP: 192.168.20.100-200                        │
│ └─ Access: Restricted (no Admin VLAN)              │
├─────────────────────────────────────────────────────┤
│ VLAN 30: Guest (192.168.30.0/24)                   │
│ ├─ Ports: lan-port4, wlan1 (access)                │
│ ├─ DHCP: 192.168.30.100-200                        │
│ └─ Access: Internet only (no LAN access)           │
└─────────────────────────────────────────────────────┘

TRUNK CONFIGURATION:
- Port: ether1
- VLANs: 10, 20, 30 (all tagged)
- Frame-types: admit-only-vlan-tagged

TESTING RESULTS:
- VLAN Isolation: ✅ Functional  
- DHCP per VLAN: ✅ Working
- Inter-VLAN Routing: ✅ Working
- Security Policies: ✅ Applied
- Performance: [Document measured speeds]

TROUBLESHOOTING PERFORMED:
- [Document any issues encountered and solutions]

OPTIMIZATION APPLIED:
- Hardware offload: [Status]
- STP edge ports: ✅ Configured
- Firewall optimization: ✅ Applied
```

#### **5.3 Final Validation Checklist**

**Complete Validation:**
```bash
# Final system check:
/interface print stats
/interface bridge print stats  
/interface bridge vlan print
/ip address print
/ip dhcp-server lease print
/ip firewall filter print
/log print where topics~"error"
```

**✅ FINAL CHECKPOINT:**
- [ ] All VLANs functional y isolated appropriately
- [ ] DHCP working en cada VLAN
- [ ] Security policies implemented y tested
- [ ] Performance within expected parameters
- [ ] Configuration backed up y documented
- [ ] Troubleshooting methodology practiced
- [ ] System stable y ready para production use

---

## **TROUBLESHOOTING GUIDE**

### **Problemas Comunes y Soluciones**

#### **Problema 1: "Lost connectivity after enabling VLAN filtering"**

**Síntomas:**
- No se puede conectar con Winbox por IP
- Devices no obtienen DHCP
- Network completamente inaccesible

**Solución:**
```bash
# Método 1: Reconectar por MAC address
1. Abrir Winbox
2. Clic "..." (Neighbors)
3. Conectar usando MAC address (no IP)
4. Continuar configuración VLAN table

# Método 2: Deshabilitar VLAN filtering temporalmente
/interface bridge set bridge-switching vlan-filtering=no
# Reconfigurar y volver a habilitar
```

#### **Problema 2: "DHCP not working en specific VLAN"**

**Diagnostic Checklist:**
```bash
# Check DHCP server status:
/ip dhcp-server print
# Should show enabled=yes

# Check DHCP pool:
/ip pool print  
# Verify range not exhausted

# Check VLAN interface:
/interface vlan print
# Should show running=yes

# Check bridge port PVID:
/interface bridge port print where bridge=bridge-switching
# Verify correct PVID assigned

# Check VLAN table membership:
/interface bridge vlan print
# Verify port listed in untagged for correct VLAN
```

#### **Problema 3: "Performance significantly slower con VLANs"**

**Diagnostic Steps:**
```bash
# Check hardware offload:
/interface bridge port print where hw-offload=yes

# Monitor CPU usage:
/system resource cpu print

# Check for software switching:
/interface bridge port monitor [find bridge=bridge-switching]

# Test with different frame sizes:
/tool bandwidth-test address=X.X.X.X packet-size=1024
```

#### **Problema 4: "Inter-VLAN routing no funciona"**

**Verification Steps:**
```bash
# Check IP forwarding (should be enabled by default):
/ip settings print

# Verify VLAN interfaces UP:
/interface vlan print

# Check routing table:
/ip route print
# Should show connected routes for each VLAN

# Verify gateway addresses:
/ip address print

# Check firewall rules:
/ip firewall filter print
```

#### **Problema 5: "VLAN hopping security issue"**

**Prevention/Check:**
```bash
# Verify frame-types settings:
/interface bridge port print detail

# Access ports should have:
# frame-types=admit-only-untagged-and-priority-tagged

# Trunk ports should have:
# frame-types=admit-only-vlan-tagged

# Check PVID assignments:
/interface bridge port print where bridge=bridge-switching
```

---

## **COMANDOS DE REFERENCIA RÁPIDA**

### **Interfaces y Bridge**
```bash
# Interface basic commands:
/interface print
/interface ethernet set ether1 name=new-name
/interface monitor ether1

# Bridge commands:
/interface bridge add name=bridge1
/interface bridge port add bridge=bridge1 interface=ether1
/interface bridge host print
/interface bridge print stats
```

### **VLAN Configuration**
```bash
# VLAN table method:
/interface bridge set bridge1 vlan-filtering=yes
/interface bridge vlan add bridge=bridge1 tagged=bridge1 untagged=ether2 vlan-ids=10
/interface bridge port set [find interface=ether2] pvid=10

# VLAN interfaces:
/interface vlan add name=vlan10 vlan-id=10 interface=bridge1
/ip address add address=192.168.10.1/24 interface=vlan10
```

### **DHCP Configuration**
```bash
# DHCP setup:
/ip pool add name=pool1 ranges=192.168.10.100-200
/ip dhcp-server add name=dhcp1 interface=vlan10 address-pool=pool1
/ip dhcp-server network add address=192.168.10.0/24 gateway=192.168.10.1
```

### **Monitoring y Diagnostics**
```bash
# Traffic monitoring:
/interface monitor-traffic interface=bridge1 duration=10
/tool bandwidth-test address=192.168.1.100

# Packet capture:
/tool sniffer start interface=ether1
/tool sniffer print

# System monitoring:
/system resource print
/log print
```

### **Firewall Basic**
```bash
# Basic firewall rules:
/ip firewall filter add chain=forward action=accept connection-state=established,related
/ip firewall filter add chain=forward action=accept src-address=192.168.10.0/24
/ip firewall filter add chain=forward action=drop src-address=192.168.30.0/24 dst-address=192.168.10.0/24
```

---

## **EVALUACIÓN Y ENTREGABLES**

### **Checklist de Completion**

**Laboratorio 1: Interfaces (✅/❌)**
- [ ] Interfaces configuradas con nombres descriptivos
- [ ] Auto-negotiation results documentados
- [ ] Performance testing completado
- [ ] Wireless security configurado
- [ ] Monitoring tools utilizados

**Laboratorio 2: Bridge (✅/❌)**  
- [ ] Bridge creado con STP/RSTP
- [ ] Puertos agregados correctamente
- [ ] MAC learning observado y documentado
- [ ] STP testing realizado
- [ ] Performance analysis completado

**Laboratorio 3: VLANs (✅/❌)**
- [ ] VLAN filtering habilitado
- [ ] VLAN table configurada (3 VLANs)
- [ ] Access y trunk ports configurados
- [ ] VLAN interfaces creadas
- [ ] DHCP por VLAN funcionando
- [ ] Security policies implementadas

**Laboratorio 4: Testing (✅/❌)**
- [ ] Connectivity matrix testing completado
- [ ] Packet capture analysis realizado
- [ ] Troubleshooting scenarios practicados
- [ ] Performance optimization aplicado
- [ ] Configuration backed up

### **Entregables Requeridos**

1. **Configuration Files:**
   - Backup final (.backup file)
   - Configuration export (.rsc file)

2. **Documentation:**
   - Network diagram con VLAN assignments
   - IP addressing scheme
   - Testing results matrix
   - Troubleshooting log

3. **Performance Data:**
   - Bandwidth test results
   - CPU utilization measurements
   - Interface statistics

4. **Screenshots/Evidence:**
   - Key configuration screens (Winbox)
   - Successful ping tests
   - DHCP lease assignments
   - Packet capture examples

### **Criterios de Evaluación**

**Excelente (90-100%):**
- All configurations functional perfectly
- Advanced troubleshooting demonstrated  
- Performance optimizations applied
- Complete documentation provided

**Competente (75-89%):**
- Basic configurations working
- Standard troubleshooting completed
- Good documentation
- Minor issues resolved

**En Desarrollo (60-74%):**
- Basic functionality achieved
- Limited troubleshooting
- Basic documentation
- Assistance required

**Insuficiente (<60%):**
- Major configuration errors
- Unable to troubleshoot effectively
- Poor or missing documentation
- Fundamental misunderstanding

---

¡Felicitaciones por completar los laboratorios del Módulo 2! Has implementado exitosamente switching avanzado con VLANs, una habilidad fundamental para cualquier ingeniero de redes.