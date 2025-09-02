# Laboratorios Presenciales - Módulo 2: Configuración de Interfaces y Switching

## Información General

**Equipamiento:** RouterBOARD RB951-2HnD por grupo (máximo 4 estudiantes)  
**Duración:** 4 sesiones de 2 horas cada una  
**Modalidad:** Grupos de trabajo colaborativo  
**Prerequisito:** Configuración inicial del Módulo 1 aplicada

---

## LABORATORIO 1: Configuración de Interfaces y Monitoreo
**Duración:** 45 minutos  
**Objetivos:** Configurar y monitorear interfaces físicas, entender diferentes tipos de interfaces

### Preparación del Entorno

**Materiales Necesarios:**
- 1 RouterBOARD RB951-2HnD por grupo (configurado Módulo 1)
- 4 Cables Ethernet CAT5e/CAT6 por grupo
- Laptops con Winbox y conectividad
- Switch de aula para interconexión

**Topología del Laboratorio:**
```
[Laptop-1] ---- [ether2] 
[Laptop-2] ---- [ether3] RB951-2HnD [ether1] ---- [Switch Aula] ---- [Internet]
[Laptop-3] ---- [ether4]                [wlan1] ---- [WiFi Devices]
[Laptop-4] ---- [ether5]
```

### Paso a Paso - Configuración de Interfaces

#### Paso 1: Verificar Estado Inicial de Interfaces

1. **Conectar a RouterBOARD con Winbox:**
   - Usuario: netadmin (configurado en Módulo 1)
   - Conectar por IP: 192.168.88.1:8192

2. **Navegar a Interfaces → Interface:**
   - Ver todas las interfaces disponibles
   - Identificar: ether1, ether2-5, wlan1

3. **Revisar configuración por defecto:**
   ```
   ether1: Habilitado, conectado al switch aula
   ether2-5: Habilitado, sin configuración específica
   wlan1: Habilitado (Access Point por defecto)
   ```

4. **Verificar estadísticas básicas:**
   - Seleccionar ether1 → Statistics tab
   - Observar: RX/TX packets, bytes, errors

#### Paso 2: Configurar Parámetros Físicos

1. **Configurar interface ether2:**
   - Doble clic en ether2
   - **Name:** `lan-port1`
   - **Comment:** `Laptop del Estudiante 1`
   - **MTU:** 1500 (mantener default)
   - Clic "OK"

2. **Configurar velocidad específica ether3:**
   - Doble clic en ether3  
   - **Name:** `lan-port2`
   - **Auto Negotiation:** ✓ (mantener habilitado)
   - **Comment:** `Laptop del Estudiante 2`
   - Clic "OK"

3. **Configurar interface wireless:**
   - Doble clic en wlan1
   - **Comment:** `WiFi Lab Grupo XX`
   - **SSID:** `Lab-GrupoXX-WiFi` (reemplazar XX con número grupo)
   - Clic "OK"

#### Paso 3: Monitoreo de Interfaces

1. **Monitor tráfico en tiempo real:**
   - Seleccionar ether1 → Monitor Traffic tab
   - Conectar laptop de un estudiante a ether2
   - Generar tráfico: ping continuo desde laptop
   ```bash
   ping -t 8.8.8.8
   ```
   - Observar estadísticas RX/TX en Winbox

2. **Usar herramienta bandwidth test:**
   - **Tools → Bandwidth Test**
   - **Direction:** both
   - **Duration:** 10 seconds
   - **Protocol:** TCP
   - **Address:** 8.8.8.8
   - Click "Start" y observar resultados

3. **Interface monitoring avanzado:**
   - Seleccionar ether2 → Monitor tab
   - Conectar/desconectar cable del laptop
   - Observar cambios en status (running/not-running)

#### Paso 4: Configuración Wireless Básica

1. **Configurar seguridad wireless:**
   - **Wireless → Security Profiles**
   - Clic "+" para agregar nuevo perfil
   - **Name:** `lab-security`
   - **Mode:** dynamic keys
   - **Authentication Types:** ✓ WPA2 PSK
   - **WPA2 Pre-Shared Key:** `LabGrupoXX2024!`
   - Clic "OK"

2. **Aplicar perfil a interface wireless:**
   - **Interfaces → wlan1** (doble clic)
   - **Security Profile:** lab-security
   - **Frequency:** 2442 MHz (canal 7)
   - Clic "OK"

3. **Verificar conectividad wireless:**
   - Conectar dispositivo móvil a red WiFi Lab-GrupoXX-WiFi
   - Verificar obtención IP automática
   - Hacer ping a gateway desde dispositivo móvil

---

## LABORATORIO 2: Configuración Bridge entre Puertos
**Duración:** 60 minutos  
**Objetivos:** Crear bridge para conectar puertos LAN, entender switching básico

### Paso a Paso - Bridge Configuration

#### Paso 1: Crear Bridge Principal

1. **Eliminar configuración IP existente:**
   - **IP → Addresses**
   - Seleccionar dirección en ether1 (192.168.88.1/24)
   - Clic "X" (eliminar) - ⚠️ **Nota:** Se perderá conectividad temporalmente

2. **Crear bridge:**
   - **Interface → Bridge**
   - Clic "+" para agregar
   - **Name:** `bridge-lan`
   - **Protocol:** RSTP
   - **Comment:** `Bridge principal LAN Grupo XX`
   - Clic "OK"

3. **Configurar bridge avanzado:**
   - Doble clic en bridge-lan
   - **STP:** ✓ Enabled
   - **Hello Time:** 2s
   - **Max Message Age:** 20s
   - **Forward Delay:** 15s
   - Clic "OK"

#### Paso 2: Agregar Puertos al Bridge

1. **Agregar ether1 (WAN) al bridge:**
   - **Interface → Bridge → Ports tab**
   - Clic "+"
   - **Interface:** ether1
   - **Bridge:** bridge-lan
   - **Comment:** `Puerto WAN/Internet`
   - Clic "OK"

2. **Agregar puertos LAN (ether2-5):**
   ```
   # Repetir para cada puerto ether2, ether3, ether4, ether5
   Interface: ether2    Bridge: bridge-lan    Comment: "LAN Port 1"
   Interface: ether3    Bridge: bridge-lan    Comment: "LAN Port 2"  
   Interface: ether4    Bridge: bridge-lan    Comment: "LAN Port 3"
   Interface: ether5    Bridge: bridge-lan    Comment: "LAN Port 4"
   ```

3. **Configurar puertos como edge ports:**
   - Seleccionar puerto ether2 en Bridge Ports
   - Doble clic para editar
   - **Edge:** yes (puerto conecta host, no switch)
   - **Point to Point:** yes
   - Repetir para ether3, ether4, ether5

#### Paso 3: Configurar IP en Bridge

1. **Asignar IP al bridge:**
   - **IP → Addresses**
   - Clic "+"
   - **Address:** 192.168.88.1/24
   - **Interface:** bridge-lan
   - **Comment:** `Gateway principal`
   - Clic "OK"

2. **Verificar conectividad:**
   - Conectar con Winbox nuevamente: 192.168.88.1:8192
   - Conectar laptops a puertos ether2, ether3, ether4
   - Verificar obtención IP automática (DHCP del módulo 1)

#### Paso 4: Verificar Funcionamiento Bridge

1. **Ver tabla MAC del bridge:**
   - **Interface → Bridge → Hosts tab**
   - Conectar/desconectar dispositivos
   - Observar como aparecen/desaparecen MACs
   - Identificar qué MAC corresponde a cada puerto

2. **Test de conectividad entre puertos:**
   - Laptop en ether2: IP 192.168.88.100
   - Laptop en ether3: IP 192.168.88.101  
   - Desde laptop 1: `ping 192.168.88.101`
   - Verificar respuesta correcta (comunicación Layer 2)

3. **Monitor STP del bridge:**
   - **Interface → Bridge** 
   - Seleccionar bridge-lan → Monitor tab
   - Verificar: Root Bridge, Bridge Priority, Topology Changes

#### Paso 5: Testing de Redundancia STP

1. **Crear loop temporal:**
   - Conectar cable entre ether2 y ether3 directamente
   - ⚠️ **CUIDADO:** Hacer conexión rápida para test

2. **Observar comportamiento STP:**
   - **Interface → Bridge → Ports**
   - Ver estado de cada puerto: Forwarding/Discarding
   - STP debe bloquear un puerto para prevenir loop

3. **Remover loop:**
   - Desconectar cable directo entre ether2-ether3
   - Observar convergencia STP (puertos vuelven a Forwarding)

---

## LABORATORIO 3: Implementación VLANs Básicas
**Duración:** 75 minutos  
**Objetivos:** Crear VLANs funcionales, configurar puertos access y trunk

### Paso a Paso - VLAN Configuration

#### Paso 1: Preparar Bridge para VLANs

1. **Habilitar VLAN filtering:**
   - **Interface → Bridge**
   - Doble clic en bridge-lan
   - **VLAN Filtering:** ✓ Yes
   - ⚠️ **ADVERTENCIA:** Esto puede cortar conectividad temporalmente
   - Clic "OK"

2. **Reconfigurar acceso management:**
   - Si se pierde conectividad, usar MAC address para reconectar
   - O usar console/reset si es necesario

#### Paso 2: Configurar VLAN Table

1. **Crear VLAN 10 (Administración):**
   - **Interface → Bridge → VLANs tab**
   - Clic "+"
   - **Bridge:** bridge-lan
   - **VLAN IDs:** 10  
   - **Tagged:** bridge-lan, ether1 (trunk al switch externo)
   - **Untagged:** ether2 (puerto access)
   - **Comment:** `VLAN Admin`
   - Clic "OK"

2. **Crear VLAN 20 (Usuarios):**
   - Clic "+"
   - **Bridge:** bridge-lan
   - **VLAN IDs:** 20
   - **Tagged:** bridge-lan, ether1
   - **Untagged:** ether3, ether4 (puertos access)
   - **Comment:** `VLAN Users`
   - Clic "OK"

3. **Crear VLAN 30 (Guest):**
   - Clic "+"
   - **Bridge:** bridge-lan
   - **VLAN IDs:** 30
   - **Tagged:** bridge-lan, ether1  
   - **Untagged:** ether5
   - **Comment:** `VLAN Guest`
   - Clic "OK"

#### Paso 3: Configurar Port VLANs (PVID)

1. **Configurar puerto ether2 (VLAN 10):**
   - **Interface → Bridge → Ports tab**
   - Doble clic en puerto ether2
   - **PVID:** 10
   - **Frame Types:** admit-only-untagged-and-priority-tagged
   - Clic "OK"

2. **Configurar puertos ether3 y ether4 (VLAN 20):**
   ```
   Puerto ether3: PVID=20, Frame Types=admit-only-untagged-and-priority-tagged
   Puerto ether4: PVID=20, Frame Types=admit-only-untagged-and-priority-tagged
   ```

3. **Configurar puerto ether5 (VLAN 30):**
   - PVID: 30
   - Frame Types: admit-only-untagged-and-priority-tagged

4. **Configurar puerto ether1 (Trunk):**
   - PVID: 1 (dejar default)
   - **Frame Types:** admit-only-vlan-tagged

#### Paso 4: Crear VLAN Interfaces para Routing

1. **Interface VLAN 10:**
   - **Interface → VLAN**
   - Clic "+"
   - **Name:** vlan10-admin
   - **VLAN ID:** 10
   - **Interface:** bridge-lan
   - **Comment:** `Gateway VLAN Administracion`
   - Clic "OK"

2. **Interface VLAN 20:**
   - **Name:** vlan20-users
   - **VLAN ID:** 20
   - **Interface:** bridge-lan
   - **Comment:** `Gateway VLAN Usuarios`

3. **Interface VLAN 30:**
   - **Name:** vlan30-guest
   - **VLAN ID:** 30
   - **Interface:** bridge-lan
   - **Comment:** `Gateway VLAN Guest`

#### Paso 5: Configurar IP Addressing por VLAN

1. **Eliminar IP del bridge principal:**
   - **IP → Addresses**
   - Eliminar 192.168.88.1/24 del bridge-lan

2. **Asignar IPs a VLANs:**
   ```
   VLAN 10: 192.168.10.1/24 → vlan10-admin
   VLAN 20: 192.168.20.1/24 → vlan20-users  
   VLAN 30: 192.168.30.1/24 → vlan30-guest
   ```

3. **Configurar IP VLAN 10 (management):**
   - **IP → Addresses → "+"**
   - **Address:** 192.168.10.1/24
   - **Interface:** vlan10-admin
   - Clic "OK"

4. **Repetir para VLAN 20 y 30:**
   - VLAN 20: 192.168.20.1/24 en vlan20-users
   - VLAN 30: 192.168.30.1/24 en vlan30-guest

---

## LABORATORIO 4: Testing y Pruebas de Conectividad VLANs
**Duración:** 60 minutos  
**Objetivos:** Verificar aislamiento VLAN y configurar inter-VLAN routing

### Paso a Paso - Testing VLANs

#### Paso 1: Configurar DHCP por VLAN

1. **DHCP Pool para VLAN 10:**
   - **IP → Pool**
   - Clic "+" 
   - **Name:** pool-admin
   - **Addresses:** 192.168.10.100-192.168.10.200
   - Clic "OK"

2. **DHCP Server para VLAN 10:**
   - **IP → DHCP Server**
   - Clic "+"
   - **Name:** dhcp-admin
   - **Interface:** vlan10-admin
   - **Address Pool:** pool-admin
   - **Lease Time:** 1d
   - Clic "OK"

3. **DHCP Network para VLAN 10:**
   - **DHCP Server → Networks**
   - Clic "+"
   - **Address:** 192.168.10.0/24
   - **Gateway:** 192.168.10.1
   - **DNS Servers:** 8.8.8.8
   - Clic "OK"

4. **Repetir configuración para VLAN 20 y 30:**
   ```
   VLAN 20: Pool 192.168.20.100-200, Gateway 192.168.20.1
   VLAN 30: Pool 192.168.30.100-200, Gateway 192.168.30.1
   ```

#### Paso 2: Testing Aislamiento de VLANs

1. **Conectar laptops a diferentes VLANs:**
   ```
   Laptop-1 → ether2 (VLAN 10) → Debe obtener IP 192.168.10.x
   Laptop-2 → ether3 (VLAN 20) → Debe obtener IP 192.168.20.x
   Laptop-3 → ether4 (VLAN 20) → Debe obtener IP 192.168.20.x
   Laptop-4 → ether5 (VLAN 30) → Debe obtener IP 192.168.30.x
   ```

2. **Verificar obtención de IPs:**
   - Renovar IP en cada laptop: `ipconfig /renew` (Windows) o `dhclient` (Linux)
   - Confirmar IPs obtenidas en rangos correctos

3. **Test aislamiento between VLANs:**
   ```bash
   # Desde Laptop-1 (VLAN 10):
   ping 192.168.20.100  # Debe FALLAR (diferentes VLANs)
   ping 192.168.30.100  # Debe FALLAR (diferentes VLANs)
   
   # Desde Laptop-2 (VLAN 20):  
   ping 192.168.20.101  # Debe FUNCIONAR (misma VLAN)
   ping 192.168.10.100  # Debe FALLAR (diferente VLAN)
   ```

4. **Verificar conectividad a gateway:**
   ```bash
   # Cada laptop debe poder hacer ping a su gateway VLAN:
   Laptop-1: ping 192.168.10.1  # OK
   Laptop-2: ping 192.168.20.1  # OK
   Laptop-4: ping 192.168.30.1  # OK
   ```

#### Paso 3: Configurar Inter-VLAN Routing

1. **Verificar IP forwarding habilitado:**
   - **IP → Settings**
   - **IP Forward:** debe estar ✓ Yes (default)

2. **Test routing entre VLANs:**
   ```bash
   # Ahora desde Laptop-1 (VLAN 10):
   ping 192.168.20.100  # Debe FUNCIONAR (routing habilitado)
   ping 192.168.30.100  # Debe FUNCIONAR
   ```

3. **Verificar tabla de rutas:**
   - **IP → Routes**
   - Debe mostrar rutas conectadas para cada VLAN:
   ```
   192.168.10.0/24 → vlan10-admin
   192.168.20.0/24 → vlan20-users
   192.168.30.0/24 → vlan30-guest
   ```

#### Paso 4: Configurar Políticas Inter-VLAN

1. **Permitir VLAN Admin acceso total:**
   - **IP → Firewall → Filter Rules**
   - Clic "+"
   - **Chain:** forward
   - **Src. Address:** 192.168.10.0/24
   - **Action:** accept
   - **Comment:** `Admin VLAN access`
   - Clic "OK"

2. **Bloquear VLAN Guest acceso a Admin:**
   - Clic "+"
   - **Chain:** forward
   - **Src. Address:** 192.168.30.0/24
   - **Dst. Address:** 192.168.10.0/24
   - **Action:** drop
   - **Comment:** `Block Guest to Admin`
   - Clic "OK"

3. **Testing políticas de firewall:**
   ```bash
   # Desde Laptop-4 (VLAN 30 Guest):
   ping 192.168.10.100  # Debe FALLAR (bloqueado por firewall)
   ping 192.168.20.100  # Debe FUNCIONAR (no bloqueado)
   
   # Desde Laptop-1 (VLAN 10 Admin):
   ping 192.168.30.100  # Debe FUNCIONAR (admin acceso total)
   ```

#### Paso 5: Monitoreo y Diagnostics

1. **Monitor tráfico VLAN:**
   - **Tools → Traffic Monitor**
   - **Interface:** vlan20-users
   - **Duration:** 10 seconds
   - Generar tráfico ping y observar estadísticas

2. **Ver tabla MAC por VLAN:**
   - **Interface → Bridge → Hosts**
   - Identificar MACs en cada VLAN
   - Verificar puerto correcto para cada dispositivo

3. **Packet sniffer en VLAN específica:**
   - **Tools → Packet Sniffer**
   - **Interface:** vlan10-admin
   - **Filter Stream:** ✓ Yes
   - Start capture y generar tráfico
   - Analizar captures para verificar VLAN tags

---

## LABORATORIO 5: Configuración Avanzada y Troubleshooting
**Duración:** 40 minutos  
**Objetivos:** Configuraciones avanzadas de switching y resolución de problemas

### Paso a Paso - Advanced Configuration

#### Paso 1: Configurar Wireless con VLAN

1. **Agregar wireless al bridge:**
   - **Interface → Bridge → Ports**
   - Clic "+"
   - **Interface:** wlan1  
   - **Bridge:** bridge-lan
   - **PVID:** 30 (VLAN Guest)
   - **Frame Types:** admit-only-untagged-and-priority-tagged
   - Clic "OK"

2. **Actualizar VLAN table para wireless:**
   - **Interface → Bridge → VLANs**
   - Doble clic en VLAN 30
   - **Untagged:** ether5, wlan1 (agregar wlan1)
   - Clic "OK"

3. **Testing wireless VLAN:**
   - Conectar dispositivo móvil a WiFi
   - Verificar obtención IP en rango 192.168.30.x
   - Test conectividad y restricciones

#### Paso 2: Bridge Monitoring Avanzado

1. **Monitor estadísticas bridge:**
   - **Interface → Bridge**
   - Seleccionar bridge-lan → Stats tab
   - Observar: RX/TX per interface, Drops, Errors

2. **STP Advanced monitoring:**
   - **Interface → Bridge** → Monitor tab
   - Verificar:
     - **Root Bridge:** Debe ser nuestro bridge
     - **Root Path Cost:** 0 (somos root)
     - **Topology Changes:** Contador de cambios

3. **Performance testing:**
   - **Tools → Bandwidth Test**
   - Test entre diferentes VLANs:
   ```bash
   # Desde router to device en VLAN 20:
   Local Address: 192.168.20.1
   Remote Address: 192.168.20.100
   Protocol: TCP, Direction: Both
   ```

#### Paso 3: Troubleshooting Scenarios

**Escenario 1: VLAN no funciona**

1. **Síntomas simulados:**
   - Cambiar PVID de ether3 a 99 (VLAN inexistente)
   - Laptop en ether3 no obtiene IP

2. **Proceso de diagnóstico:**
   ```bash
   # Verificar configuración puerto:
   Interface → Bridge → Ports → ether3 (revisar PVID)
   
   # Verificar VLAN table:
   Interface → Bridge → VLANs (VLAN 99 no existe)
   
   # Verificar DHCP:
   IP → DHCP Server → Leases (no hay lease para dispositivo)
   ```

3. **Resolución:**
   - Corregir PVID de ether3 a 20
   - Renovar IP en laptop
   - Verificar funcionamiento

**Escenario 2: Connectividad intermitente**

1. **Simular problema:**
   - Crear loop conectando ether2 y ether3 directamente

2. **Diagnóstico STP:**
   - **Interface → Bridge → Monitor**
   - Observar Topology Changes incrementando
   - Ver puertos en estado Discarding

3. **Resolución:**
   - Identificar y remover loop físico
   - Verificar convergencia STP

#### Paso 4: Backup y Documentation

1. **Export configuración VLANs:**
   - **Terminal:**
   ```bash
   /interface bridge vlan export file=vlan-config
   /interface bridge port export file=bridge-ports
   /ip address export file=ip-addresses
   ```

2. **Crear backup completo:**
   - **Files → Backup**
   - **Name:** `Grupo-XX-VLANs-Lab`
   - **Password:** `VlanBackup2024!`

3. **Documentar configuración aplicada:**
   ```markdown
   # CONFIGURACIÓN VLANS - Grupo XX
   
   ## Bridge Configuration
   - Bridge: bridge-lan (RSTP enabled)
   - VLAN Filtering: Enabled
   
   ## VLANs Configuradas
   - VLAN 10: Administración (192.168.10.0/24) → ether2
   - VLAN 20: Usuarios (192.168.20.0/24) → ether3, ether4  
   - VLAN 30: Guest (192.168.30.0/24) → ether5, wlan1
   
   ## Servicios
   - DHCP habilitado en cada VLAN
   - Inter-VLAN routing habilitado
   - Firewall: Guest no accede a Admin
   
   ## Testing Realizado
   - Aislamiento VLANs: ✓
   - Inter-VLAN routing: ✓  
   - Políticas firewall: ✓
   - Wireless VLAN: ✓
   ```

---

## Verificación y Testing Final

### Lista de Verificación Completa

**□ Interfaces:**
- [ ] Interfaces físicas configuradas con nombres descriptivos
- [ ] Parámetros físicos configurados correctamente
- [ ] Monitoreo de interfaces funcionando

**□ Bridge:**
- [ ] Bridge creado con STP habilitado
- [ ] Todos los puertos agregados al bridge
- [ ] Edge ports configurados correctamente
- [ ] Tabla MAC poblándose automáticamente

**□ VLANs:**
- [ ] VLAN filtering habilitado en bridge
- [ ] VLAN table configurada para VLANs 10, 20, 30
- [ ] PVIDs configurados en puertos access
- [ ] Frame types configurados correctamente
- [ ] VLAN interfaces creadas para routing

**□ IP y Servicios:**
- [ ] IPs asignadas a cada VLAN interface
- [ ] DHCP funcionando en cada VLAN
- [ ] DNS resolver configurado

**□ Testing:**
- [ ] Aislamiento between VLANs verificado
- [ ] Inter-VLAN routing funcionando
- [ ] Políticas firewall aplicadas
- [ ] Wireless integrado en VLAN
- [ ] Performance testing completado

**□ Documentación:**
- [ ] Configuración exportada
- [ ] Backup creado
- [ ] Documentación técnica completada

### Comandos de Verificación

```bash
# Verificar bridge y puertos
/interface bridge print
/interface bridge port print
/interface bridge host print

# Verificar VLANs
/interface bridge vlan print
/interface vlan print

# Verificar IPs y routing
/ip address print
/ip route print

# Verificar DHCP
/ip dhcp-server print
/ip dhcp-server lease print

# Performance y estadísticas
/interface monitor-traffic interface=bridge-lan duration=10
/interface print stats
```

### Troubleshooting Checklist

**Problema: No obtiene IP por DHCP**
1. Verificar DHCP server habilitado
2. Verificar network DHCP configurada
3. Verificar pool DHCP no exhausto
4. Verificar VLAN correcta en puerto

**Problema: No conectividad entre VLANs**
1. Verificar IP forwarding habilitado
2. Verificar rutas en tabla routing
3. Verificar firewall no bloquea tráfico
4. Verificar VLAN interfaces UP

**Problema: VLAN tagging no funciona**
1. Verificar VLAN filtering habilitado
2. Verificar VLAN table configurada
3. Verificar PVID correcto en puertos
4. Verificar frame-types apropiados

---

## Preparación para Homework Individual

### Entregables del Laboratorio

**Archivos para subir:**
- Backup configuración VLANs (.backup)
- Export bridge configuration (.rsc)
- Export VLAN configuration (.rsc)
- Documentación técnica aplicada (.pdf/.docx)
- Screenshots verificación funcionamiento

### Assignment CHR Individual

**GNS3 Project: Red Empresarial 3 VLANs**
- Topology con 1 CHR como Layer 3 switch
- 3 VLANs diferentes a las del laboratorio
- PC virtual en cada VLAN
- Inter-VLAN routing y restricciones
- Packet capture traffic analysis

**Análisis de Tráfico:**
- Configurar packet sniffer
- Capturar tráfico tagged/untagged
- Analizar frames 802.1Q
- Documentar findings

**Próxima Sesión:**
- Review homework CHR configurations
- Comparar implementaciones grupo vs individual
- Preparar para Módulo 3: Routing