# Comandos y Herramientas de Verificación
## Laboratorio MikroTik - Troubleshooting y Monitoreo

---

## Metodología Sistemática de Verificación

### Enfoque Layer por Layer (Modelo OSI)

```
LAYER 1 - FÍSICO:
├─ Link status verification
├─ Cable connectivity testing  
├─ Interface statistics analysis
└─ Hardware health check

LAYER 2 - DATA LINK:
├─ Bridge operation verification
├─ VLAN configuration validation
├─ MAC address learning check
├─ STP convergence verification
└─ Switch port status

LAYER 3 - NETWORK:
├─ IP address configuration
├─ Routing table verification
├─ OSPF adjacencies check
├─ Route path testing
└─ Gateway connectivity

LAYER 4+ - TRANSPORT/APPLICATION:
├─ Service connectivity testing
├─ DHCP functionality verification
├─ DNS resolution testing  
└─ End-to-end application testing
```

---

## Comandos de Verificación por Laboratorio

### Laboratorio 1: Configuración Inicial

#### Verificación de Sistema Base
```bash
# IDENTIDAD Y VERSIÓN
/system identity print
/system resource print
/system package print
/system routerboard print

# USUARIOS Y ACCESO
/user print
/user active print
/ip service print

# CONECTIVIDAD BÁSICA
/ping 8.8.8.8 count=5
/tool netwatch add host=8.8.8.8 interval=30s timeout=5s
```

#### Verificación de Seguridad Inicial
```bash
# SERVICIOS HABILITADOS
/ip service print where disabled=no

# CONFIGURACIÓN SSH
/ip service print where name=ssh

# CONFIGURACIÓN WINBOX  
/ip service print where name=winbox

# LOG DE ACCESOS
/log print where topics~"system"
```

#### Comandos de Troubleshooting L1
```bash
# ESTADO DE INTERFACES
/interface print stats
/interface ethernet print
/interface ethernet monitor ether1 once
/interface ethernet monitor ether1 duration=10

# ESTADÍSTICAS DE ERRORES
/interface print stats-detail
```

### Laboratorio 2: Switching y VLANs

#### Verificación de Bridge
```bash
# CONFIGURACIÓN BRIDGE
/interface bridge print detail
/interface bridge port print detail
/interface bridge settings print

# ESTADO STP/RSTP
/interface bridge monitor sw-rojo
/interface bridge port monitor [find bridge=sw-rojo]

# TABLA MAC
/interface bridge host print
/interface bridge host print where bridge=sw-rojo
```

#### Verificación de VLANs
```bash
# CONFIGURACIÓN VLAN
/interface vlan print detail
/interface bridge vlan print

# VLAN FILTERING
/interface bridge get sw-rojo vlan-filtering
/interface bridge port print where bridge=sw-rojo

# TESTING VLAN CONNECTIVITY
/ping 172.16.1.1 interface=mgmt-vlan10
/tool flood-ping 172.16.1.1 interface=mgmt-vlan10 count=100
```

#### Comandos de Troubleshooting L2
```bash
# ANÁLISIS DE TRÁFICO BRIDGE
/tool sniffer start interface=sw-rojo filter-stream=yes
/tool sniffer packet print

# MAC ADDRESS FLAPPING DETECTION
/log print where message~"host"

# BROADCAST STORM DETECTION
/interface print stats where name=sw-rojo
```

### Laboratorio 3: Inter-VLAN Routing

#### Verificación de Direccionamiento
```bash
# CONFIGURACIÓN IP
/ip address print detail
/ip address print where interface~"vlan"

# ARP TABLE
/ip arp print
/ip arp print where interface=users-vlan11

# NEIGHBORS DISCOVERY
/ip neighbor print
```

#### Verificación DHCP
```bash
# CONFIGURACIÓN DHCP SERVER
/ip dhcp-server print detail
/ip dhcp-server network print
/ip pool print

# LEASES DHCP
/ip dhcp-server lease print
/ip dhcp-server lease print detail

# TESTING DHCP
/tool dhcp-relay add dhcp-server=10.10.0.1 interface=users-vlan11
```

#### Verificación Inter-VLAN Routing
```bash
# FORWARDING TABLE
/ip route print
/ip route print where dynamic=no

# TESTING CONNECTIVITY
/ping 10.10.0.100 src-address=10.10.0.1
/ping 172.16.1.1 src-address=10.10.0.1

# TRACEROUTE ANALYSIS
/tool traceroute 8.8.8.8 src-address=10.10.0.1
```

### Laboratorio 4: Routing Estático

#### Verificación de Rutas
```bash
# TABLA DE RUTAS COMPLETA
/ip route print detail
/ip route print where dst-address=0.0.0.0/0
/ip route print where dynamic=no

# TESTING RUTAS ESPECÍFICAS
/ping 10.20.0.1 count=5
/ping 10.30.0.1 count=5  
/ping 10.40.0.1 count=5
/ping 10.50.0.1 count=5

# PATH ANALYSIS
/tool traceroute 10.20.0.1
/tool traceroute 10.50.0.1
```

#### Verificación Load Balancing
```bash
# MULTIPLE PATH VERIFICATION
/ip route print where dst-address=0.0.0.0/0

# CONNECTION TRACKING
/ip firewall connection print

# BANDWIDTH DISTRIBUTION
/tool bandwidth-test address=10.20.0.1 direction=both duration=30s
```

### Laboratorio 5: OSPF Dinámico

#### Verificación OSPF Básica
```bash
# CONFIGURACIÓN OSPF
/routing ospf instance print detail
/routing ospf area print detail
/routing ospf interface print detail

# NEIGHBORS OSPF
/routing ospf neighbor print
/routing ospf neighbor print detail

# STATE MONITORING
/routing ospf neighbor monitor [find]
```

#### Verificación LSA Database
```bash
# LSA DATABASE
/routing ospf lsa print
/routing ospf lsa print where type=router
/routing ospf lsa print where type=network

# DETAILED LSA ANALYSIS
/routing ospf lsa print detail where originator=1.1.1.1
```

#### Verificación Convergencia OSPF
```bash
# RUTAS OSPF
/ip route print where dynamic=yes
/ip route print where ospf=yes

# SPF CALCULATION LOG
/log print where topics~"ospf"

# CONVERGENCE TESTING
/tool flood-ping 10.20.0.1 count=1000 interval=10ms
```

---

## Herramientas de Monitoreo Avanzadas

### Interface Monitoring

#### Real-time Interface Statistics
```bash
# TRAFFIC MONITOR
/tool traffic-monitor interface=sw-rojo duration=60

# GRAPHING SETUP
/tool graphing interface add interface=ether1 store-on-disk=yes
/tool graphing resource add store-on-disk=yes

# BANDWIDTH TEST
/tool bandwidth-test address=172.16.1.1 direction=both protocol=tcp duration=30s
```

#### Performance Analysis
```bash
# CPU Y MEMORIA
/system resource monitor duration=60

# INTERFACE LOAD
/interface monitor-traffic interface=ether1,ether2 duration=30

# QUEUE STATISTICS (si aplicable)
/queue tree print stats
```

### Network Discovery Tools

#### Neighbor Discovery
```bash
# CDP/LLDP EQUIVALENTE
/ip neighbor discovery-settings set discover-interface-list=all
/ip neighbor print

# MAC TELNET (entre MikroTik)
/tool mac-telnet 00:0C:42:xx:xx:xx
/tool mac-telnet-server set allowed-interface-list=all
```

#### Network Scanning
```bash
# ARP SCAN
/tool ip-scan interface=users-vlan11 address-range=10.10.0.0/24

# PING SWEEP
/tool flood-ping 10.10.0.0 count=1
```

### Packet Capture y Analysis

#### Sniffer Configuration
```bash
# BASIC PACKET CAPTURE
/tool sniffer start interface=users-vlan11 filter-stream=yes
/tool sniffer packet print

# FILTERED CAPTURE
/tool sniffer start interface=users-vlan11 \
  filter-ip-protocol=icmp \
  filter-stream=yes \
  streaming-enabled=yes

# CAPTURE TO FILE
/tool sniffer start interface=users-vlan11 \
  file-name=capture-vlan11 \
  file-limit=1000000
```

#### Traffic Analysis
```bash
# TORCH (REAL-TIME TRAFFIC)
/tool torch interface=users-vlan11 duration=30

# PROTOCOL DISTRIBUTION
/tool torch interface=users-vlan11 src-address=10.10.0.0/24

# TOP TALKERS
/tool torch interface=users-vlan11 duration=60
```

---

## Comandos de Troubleshooting Específicos

### Problemas de Conectividad

#### No Connectivity Between VLANs
```bash
# CHECK 1: VLAN CONFIGURATION
/interface bridge vlan print
/interface vlan print

# CHECK 2: IP ADDRESSING
/ip address print where interface~"vlan"

# CHECK 3: ROUTING
/ip route print
/ip route print where active=yes

# CHECK 4: FIREWALL
/ip firewall filter print
/ip firewall connection print where reply-dst-address~"10.10.0"

# TESTING SCRIPT
:foreach vlan in=[/interface vlan find] do={
  :local vlanname [/interface vlan get $vlan name]
  :local vlanip [/ip address get [find interface=$vlanname] address]
  :put ("Testing VLAN: " . $vlanname . " IP: " . $vlanip)
  /ping [:pick $vlanip 0 [:find $vlanip "/"]] count=3
}
```

#### OSPF Adjacency Problems
```bash
# CHECK 1: OSPF BASICS
/routing ospf instance print
/routing ospf area print
/routing ospf interface print

# CHECK 2: NEIGHBOR STATES
/routing ospf neighbor print
:foreach neighbor in=[/routing ospf neighbor find] do={
  :local state [/routing ospf neighbor get $neighbor state]
  :local address [/routing ospf neighbor get $neighbor address]
  :if ($state != "Full") do={
    :put ("WARNING: Neighbor " . $address . " in state: " . $state)
  }
}

# CHECK 3: AUTHENTICATION
/routing ospf area print detail
/routing ospf interface print detail where auth-key!=""

# CHECK 4: TIMERS
/routing ospf interface print where hello-interval!=10
```

#### Performance Issues
```bash
# CPU UTILIZATION CHECK
/system resource print
:if ([/system resource get cpu-load] > 80) do={
  :put "HIGH CPU LOAD DETECTED"
  /system resource monitor duration=10
}

# MEMORY CHECK
:local freemem [/system resource get free-memory]
:local totalmem [/system resource get total-memory]
:local memutil (($totalmem - $freemem) * 100 / $totalmem)
:put ("Memory utilization: " . $memutil . "%")

# INTERFACE ERRORS
/interface print stats where rx-error>0 or tx-error>0
```

### Scripts de Diagnóstico Automatizado

#### Health Check Script
```bash
# SCRIPT: system-health-check
:local errors 0

# CHECK INTERFACES
:foreach iface in=[/interface ethernet find] do={
  :local status [/interface ethernet get $iface running]
  :local name [/interface ethernet get $iface name]
  :if ($status != "true") do={
    :put ("ERROR: Interface " . $name . " is DOWN")
    :set errors ($errors + 1)
  }
}

# CHECK OSPF NEIGHBORS
:foreach neighbor in=[/routing ospf neighbor find] do={
  :local state [/routing ospf neighbor get $neighbor state]
  :if ($state != "Full") do={
    :set errors ($errors + 1)
  }
}

# CHECK ROUTES TO OTHER GROUPS
:local testroutes {"10.20.0.1";"10.30.0.1";"10.40.0.1";"10.50.0.1"}
:foreach route in=$testroutes do={
  :local result [/ping $route count=1]
  :if ($result = 0) do={
    :put ("ERROR: Cannot reach " . $route)
    :set errors ($errors + 1)
  }
}

:put ("Health Check Complete. Errors found: " . $errors)
```

#### Network Connectivity Matrix
```bash
# SCRIPT: connectivity-matrix
:local groups {"10.10.0.1";"10.20.0.1";"10.30.0.1";"10.40.0.1";"10.50.0.1"}
:local mygroup [/system identity get name]

:put ("=== CONNECTIVITY MATRIX FROM " . $mygroup . " ===")
:foreach target in=$groups do={
  :local result [/ping $target count=3]
  :local status
  :if ($result > 0) do={
    :set status ("OK (" . $result . "/3)")
  } else={
    :set status "FAIL"
  }
  :put ($target . " -> " . $status)
}
```

---

## Herramientas de Monitoreo Continuo

### SNMP Configuration
```bash
# HABILITAR SNMP
/snmp set enabled=yes contact="Network Admin" location="Lab"
/snmp community set public address=192.168.100.0/24

# MIB ESPECÍFICOS MIKROTIK
# 1.3.6.1.4.1.14988 = MikroTik OID base
# Interfaces: 1.3.6.1.2.1.2.2.1
# CPU: 1.3.6.1.4.1.14988.1.1.3.14
```

### Logging Configuration
```bash
# LOGGING AVANZADO
/system logging add topics=ospf,interface,system action=remote remote=192.168.100.100 remote-port=514
/system logging add topics=error,warning,critical action=email email-to=admin@universidad.edu

# LOG ROTATION
/system logging action set memory memory-lines=2000 memory-stop-on-full=no
```

### Alerting y Notifications
```bash
# EMAIL SETUP
/tool e-mail set server=mail.universidad.edu from=mikrotik@universidad.edu

# NETWATCH PARA MONITORING
/tool netwatch add host=172.16.1.1 interval=30s timeout=5s up-script="/tool e-mail send to=admin@universidad.edu subject=\"Core UP\" body=\"Core router is responding\""

# SCHEDULED HEALTH CHECKS
/system scheduler add name=daily-health-check interval=24h on-event="/system script run system-health-check"
```

---

## Templates de Testing por Escenario

### Test Suite Laboratorio 2 (VLANs)
```bash
# TEST 1: VLAN ISOLATION
:put "Testing VLAN isolation..."
:local result [/ping 10.11.0.1 src-address=172.16.1.2 count=1]
:if ($result > 0) do={:put "PASS: Management can reach users VLAN"} else={:put "FAIL: No connectivity to users VLAN"}

# TEST 2: TRUNK FUNCTIONALITY  
:put "Testing trunk functionality..."
/tool sniffer start interface=trunk-core filter-stream=yes duration=5
:delay 6
/tool sniffer packet print where vlan-id=10 or vlan-id=11
```

### Test Suite Laboratorio 5 (OSPF)
```bash
# COMPREHENSIVE OSPF TEST
:local ospfrouters {"1.1.1.1";"2.2.2.2";"3.3.3.3";"4.4.4.4";"5.5.5.5"}
:put "=== OSPF CONVERGENCE TEST ==="

:foreach router in=$ospfrouters do={
  :local neighbor [/routing ospf neighbor find where router-id=$router]
  :if ([:len $neighbor] > 0) do={
    :local state [/routing ospf neighbor get $neighbor state]
    :put ($router . " -> " . $state)
  } else={
    :put ($router . " -> NOT FOUND")
  }
}

# TEST ROUTE REDISTRIBUTION
:local connectedroutes [/ip route find where connect=yes]
:local ospfroutes [/ip route find where ospf=yes]
:put ("Connected routes: " . [:len $connectedroutes])
:put ("OSPF routes: " . [:len $ospfroutes])
```

---

## Comandos de Emergencia y Recovery

### Rapid Troubleshooting Commands
```bash
# QUICK STATUS CHECK
/interface print brief
/ip address print brief  
/ip route print count-only
/routing ospf neighbor print count-only

# EMERGENCY CONNECTIVITY
/ping 172.16.1.1 count=1
/ping 8.8.8.8 count=1

# RAPID CONFIGURATION BACKUP
/export compact file=emergency-backup
```

### Reset y Recovery Procedures
```bash
# SOFT RESET (PRESERVE LICENSE)
/system reset-configuration keep-users=yes no-defaults=yes skip-backup=yes

# INTERFACE RESET
/interface ethernet reset-counters ether1
/interface bridge port remove [find]
/interface bridge remove [find]

# ROUTING RESET
/ip route remove [find dynamic=no]
/routing ospf interface remove [find]
```