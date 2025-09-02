# Configuraciones Específicas por Router
## Laboratorio MikroTik - Módulos 1, 2 y 3

---

## Router CORE (RB951-2HnD) - Configuración del Instructor

### Configuración Inicial CORE

```bash
# PASO 1: Reset y configuración básica
/system reset-configuration no-defaults=yes skip-backup=yes

# PASO 2: Configuración de identidad y usuario
/system identity set name=RB951-CORE
/user add name=admin-core group=full password=CoreAdmin2024!
/user disable admin

# PASO 3: Configuración de interfaces físicas
/interface ethernet set ether1 name=wan comment="Internet-RUT"
/interface ethernet set ether2 name=switch-trunk comment="Trunk-Switch"

# PASO 4: Bridge principal
/interface bridge add name=bridge-core protocol=rstp stp-enabled=yes

# PASO 5: Configuración WAN (hacia Internet)
/ip dhcp-client add interface=wan disabled=no comment="Internet-DHCP"

# PASO 6: VLANs trunk hacia switch
/interface bridge port add bridge=bridge-core interface=switch-trunk frame-types=admit-only-vlan-tagged
```

### Configuración VLAN y Routing CORE

```bash
# PASO 7: Configurar VLANs para cada grupo
/interface vlan add name=vlan10 vlan-id=10 interface=bridge-core comment="Grupo-ROJO"
/interface vlan add name=vlan20 vlan-id=20 interface=bridge-core comment="Grupo-VIOLETA"
/interface vlan add name=vlan30 vlan-id=30 interface=bridge-core comment="Grupo-AZUL"
/interface vlan add name=vlan40 vlan-id=40 interface=bridge-core comment="Grupo-NARANJA"
/interface vlan add name=vlan50 vlan-id=50 interface=bridge-core comment="Grupo-AMARILLO"

# PASO 8: VLAN Table configuration
/interface bridge vlan-filtering set bridge-core vlan-filtering=yes
/interface bridge vlan add bridge=bridge-core tagged=switch-trunk,bridge-core vlan-ids=10,20,30,40,50

# PASO 9: Direcciones IP para enlaces P2P
/ip address add address=172.16.1.1/30 interface=vlan10 comment="P2P-ROJO"
/ip address add address=172.16.2.1/30 interface=vlan20 comment="P2P-VIOLETA"
/ip address add address=172.16.3.1/30 interface=vlan30 comment="P2P-AZUL"
/ip address add address=172.16.4.1/30 interface=vlan40 comment="P2P-NARANJA"
/ip address add address=172.16.5.1/30 interface=vlan50 comment="P2P-AMARILLO"

# PASO 10: Rutas estáticas hacia redes de cada grupo (para Laboratorio 4)
/ip route add dst-address=10.10.0.0/16 gateway=172.16.1.2 comment="Redes-ROJO" disabled=yes
/ip route add dst-address=10.20.0.0/16 gateway=172.16.2.2 comment="Redes-VIOLETA" disabled=yes
/ip route add dst-address=10.30.0.0/16 gateway=172.16.3.2 comment="Redes-AZUL" disabled=yes
/ip route add dst-address=10.40.0.0/16 gateway=172.16.4.2 comment="Redes-NARANJA" disabled=yes
/ip route add dst-address=10.50.0.0/16 gateway=172.16.5.2 comment="Redes-AMARILLO" disabled=yes
```

### Configuración OSPF CORE (Laboratorio 5)

```bash
# PASO 11: OSPF Configuration
/routing ospf instance set default router-id=10.255.255.1
/routing ospf area add name=backbone area-id=0.0.0.0 instance=default

# PASO 12: OSPF Interfaces
/routing ospf interface add interface=vlan10 area=backbone cost=10 network-type=point-to-point
/routing ospf interface add interface=vlan20 area=backbone cost=10 network-type=point-to-point
/routing ospf interface add interface=vlan30 area=backbone cost=10 network-type=point-to-point
/routing ospf interface add interface=vlan40 area=backbone cost=10 network-type=point-to-point
/routing ospf interface add interface=vlan50 area=backbone cost=10 network-type=point-to-point

# PASO 13: Default route redistribution
/routing ospf instance set default redistribute-default=as-type-1 redistribute-connected=as-type-2

# PASO 14: Services configuration
/ip service disable telnet,ftp,www
/ip service set ssh port=2222
/ip service set winbox port=8192 address=172.16.0.0/12
```

---

## Configuración Grupo ROJO (VLAN 10-11)

### Laboratorio 1: Configuración Inicial ROJO

```bash
# RESET INICIAL
/system reset-configuration no-defaults=yes skip-backup=yes

# CONFIGURACIÓN BÁSICA
/system identity set name=RB951-ROJO
/user add name=admin-rojo group=full password=RojoAdmin2024!
/user disable admin

# CONFIGURACIÓN NTP Y TIMEZONE
/system clock set time-zone-name=America/Argentina/Buenos_Aires
/system ntp client set enabled=yes primary-ntp=pool.ntp.org

# INTERFACES FÍSICAS
/interface ethernet set ether1 name=trunk-core comment="Enlace-Core"
/interface ethernet set ether2 name=pc1 comment="PC-Admin"
/interface ethernet set ether3 name=pc2 comment="PC-Usuario"
/interface ethernet set ether4 name=wifi-ap comment="WiFi-AP"
/interface ethernet set ether5 name=testing comment="Testing-Port"

# SERVICIOS SEGUROS
/ip service disable telnet,ftp,www
/ip service set ssh port=2222
/ip service set winbox port=8192
```

### Laboratorio 2: Switching y VLANs ROJO

```bash
# BRIDGE PRINCIPAL
/interface bridge add name=sw-rojo protocol=rstp stp-enabled=yes

# CONFIGURACIÓN VLAN FILTERING
/interface bridge set sw-rojo vlan-filtering=yes

# PUERTOS DEL BRIDGE
/interface bridge port add bridge=sw-rojo interface=trunk-core frame-types=admit-only-vlan-tagged comment="Trunk-Core"
/interface bridge port add bridge=sw-rojo interface=pc1 frame-types=admit-only-untagged-and-priority-tagged pvid=11 comment="Access-Admin"
/interface bridge port add bridge=sw-rojo interface=pc2 frame-types=admit-only-untagged-and-priority-tagged pvid=11 comment="Access-User"
/interface bridge port add bridge=sw-rojo interface=wifi-ap frame-types=admit-only-untagged-and-priority-tagged pvid=11 comment="Access-WiFi"
/interface bridge port add bridge=sw-rojo interface=testing frame-types=admit-all pvid=11 comment="Testing-Port"

# VLAN TABLE
/interface bridge vlan add bridge=sw-rojo tagged=trunk-core,sw-rojo vlan-ids=10 comment="Management-VLAN"
/interface bridge vlan add bridge=sw-rojo tagged=trunk-core,sw-rojo untagged=pc1,pc2,wifi-ap,testing vlan-ids=11 comment="Users-VLAN"

# VLAN INTERFACES
/interface vlan add name=mgmt-vlan10 vlan-id=10 interface=sw-rojo comment="Management"
/interface vlan add name=users-vlan11 vlan-id=11 interface=sw-rojo comment="Users-Network"

# STP OPTIMIZACIÓN
/interface bridge port set [find interface=pc1] edge=yes point-to-point=yes
/interface bridge port set [find interface=pc2] edge=yes point-to-point=yes
/interface bridge port set [find interface=wifi-ap] edge=yes point-to-point=yes
```

### Laboratorio 3: Inter-VLAN Routing ROJO

```bash
# DIRECCIONES IP
/ip address add address=172.16.1.2/30 interface=mgmt-vlan10 comment="P2P-Core-Link"
/ip address add address=10.10.0.1/24 interface=users-vlan11 comment="Users-Gateway"

# DHCP SERVER VLAN USUARIOS
/ip pool add name=pool-rojo-users ranges=10.10.0.100-10.10.0.200
/ip dhcp-server add name=dhcp-rojo interface=users-vlan11 address-pool=pool-rojo-users lease-time=8h disabled=no
/ip dhcp-server network add address=10.10.0.0/24 gateway=10.10.0.1 dns-server=8.8.8.8,8.8.4.4 domain=rojo.local

# RUTA POR DEFECTO
/ip route add dst-address=0.0.0.0/0 gateway=172.16.1.1 comment="Default-via-Core"

# FIREWALL BÁSICO INTER-VLAN
/ip firewall filter add chain=forward action=accept connection-state=established,related comment="Allow-Established"
/ip firewall filter add chain=forward action=accept src-address=10.10.0.0/24 comment="Allow-Users-Out"
/ip firewall filter add chain=forward action=drop comment="Drop-All-Other"

# NAT (si es necesario para testing)
/ip firewall nat add chain=srcnat action=masquerade out-interface=mgmt-vlan10 comment="NAT-to-Core"
```

### Laboratorio 4: Routing Estático Completo ROJO

```bash
# RUTAS ESPECÍFICAS A OTROS GRUPOS
/ip route add dst-address=10.20.0.0/16 gateway=172.16.1.1 comment="To-Violeta"
/ip route add dst-address=10.30.0.0/16 gateway=172.16.1.1 comment="To-Azul"  
/ip route add dst-address=10.40.0.0/16 gateway=172.16.1.1 comment="To-Naranja"
/ip route add dst-address=10.50.0.0/16 gateway=172.16.1.1 comment="To-Amarillo"

# FIREWALL ACTUALIZADO PARA INTER-GROUP
/ip firewall filter add chain=forward action=accept src-address=10.10.0.0/24 dst-address=10.0.0.0/8 place-before=2 comment="Allow-Inter-Group"
```

### Laboratorio 5: OSPF Dinámico ROJO

```bash
# CONFIGURACIÓN OSPF
/routing ospf instance set default router-id=1.1.1.1
/routing ospf area add name=area-rojo area-id=0.0.0.1 instance=default

# OSPF EN INTERFACES
/routing ospf interface add interface=mgmt-vlan10 area=backbone cost=10 network-type=point-to-point
/routing ospf interface add interface=users-vlan11 area=area-rojo cost=100 passive=yes

# REDISTRIBUCIÓN DE REDES CONECTADAS
/routing ospf instance set default redistribute-connected=as-type-2

# DISABLE RUTAS ESTÁTICAS (reemplazadas por OSPF)
/ip route disable [find comment~"To-"]
```

---

## Template Genérico para Otros Grupos

### Variables por Grupo

| Variable | VIOLETA | AZUL | NARANJA | AMARILLO |
|----------|---------|------|---------|----------|
| `ROUTER_ID` | 2.2.2.2 | 3.3.3.3 | 4.4.4.4 | 5.5.5.5 |
| `GROUP_NAME` | VIOLETA | AZUL | NARANJA | AMARILLO |
| `VLAN_MGMT` | 20 | 30 | 40 | 50 |
| `VLAN_USERS` | 21 | 31 | 41 | 51 |
| `P2P_SUBNET` | 172.16.2.0/30 | 172.16.3.0/30 | 172.16.4.0/30 | 172.16.5.0/30 |
| `P2P_LOCAL` | 172.16.2.2/30 | 172.16.3.2/30 | 172.16.4.2/30 | 172.16.5.2/30 |
| `P2P_REMOTE` | 172.16.2.1 | 172.16.3.1 | 172.16.4.1 | 172.16.5.1 |
| `USERS_NET` | 10.20.0.0/24 | 10.30.0.0/24 | 10.40.0.0/24 | 10.50.0.0/24 |
| `USERS_GW` | 10.20.0.1/24 | 10.30.0.1/24 | 10.40.0.1/24 | 10.50.0.1/24 |
| `DHCP_RANGE` | 10.20.0.100-200 | 10.30.0.100-200 | 10.40.0.100-200 | 10.50.0.100-200 |
| `AREA_ID` | 0.0.0.2 | 0.0.0.3 | 0.0.0.4 | 0.0.0.5 |

### Script Template para Adaptación

```bash
# VARIABLES DE CONFIGURACIÓN - ADAPTAR POR GRUPO
:local ROUTER_ID "X.X.X.X"
:local GROUP_NAME "GRUPO_NAME"
:local VLAN_MGMT XX
:local VLAN_USERS XX
:local P2P_LOCAL "172.16.X.2/30"
:local P2P_REMOTE "172.16.X.1"
:local USERS_GW "10.XX.0.1/24"
:local USERS_NET "10.XX.0.0/24"
:local DHCP_START "10.XX.0.100"
:local DHCP_END "10.XX.0.200"

# CONFIGURACIÓN INICIAL
/system identity set name=("RB951-" . $GROUP_NAME)
/user add name=("admin-" . [:lower $GROUP_NAME]) group=full password=($GROUP_NAME . "Admin2024!")

# RESTO DE LA CONFIGURACIÓN USANDO VARIABLES...
```

---

## Configuraciones de Verificación y Testing

### Comandos de Estado por Laboratorio

#### Laboratorio 1 - Verificación
```bash
# Verificar identidad y usuarios
/system identity print
/user print

# Verificar servicios
/ip service print where disabled=no

# Verificar conectividad básica
/ping 8.8.8.8 count=3
```

#### Laboratorio 2 - Verificación
```bash
# Verificar bridge y STP
/interface bridge print
/interface bridge port print
/interface bridge monitor sw-rojo

# Verificar VLANs
/interface bridge vlan print
/interface vlan print

# Verificar MAC learning
/interface bridge host print
```

#### Laboratorio 3 - Verificación
```bash
# Verificar direcciones IP
/ip address print

# Verificar DHCP
/ip dhcp-server lease print
/ip dhcp-server print

# Testing inter-VLAN
/ping 10.10.0.1 interface=users-vlan11
```

#### Laboratorio 4 - Verificación
```bash
# Verificar tabla de rutas
/ip route print

# Testing conectividad inter-group
/ping 10.20.0.1 count=3
/ping 10.30.0.1 count=3

# Traceroute para verificar path
/tool traceroute 10.20.0.1
```

#### Laboratorio 5 - Verificación
```bash
# Verificar OSPF neighbors
/routing ospf neighbor print

# Verificar LSA database
/routing ospf lsa print

# Verificar rutas OSPF
/ip route print where dynamic=yes
```

---

## Configuraciones de Rollback y Recovery

### Scripts de Limpieza entre Laboratorios

```bash
# LIMPIEZA COMPLETA PARA NUEVO LABORATORIO
/ip route remove [find dynamic=no comment!=""]
/routing ospf interface remove [find]
/routing ospf area remove [find area-id!=0.0.0.0]
/ip firewall filter remove [find]
/ip firewall nat remove [find]
/ip dhcp-server remove [find]
/ip pool remove [find]
/ip address remove [find interface~"vlan"]
/interface vlan remove [find]
/interface bridge vlan remove [find]
/interface bridge port remove [find]
/interface bridge remove [find]
```

### Backup Scripts
```bash
# CREAR BACKUP ANTES DE CADA LABORATORIO
/export file=("backup-lab" . [/system clock get date] . "-" . [/system identity get name])

# CREAR CHECKPOINT CON TIMESTAM
/system backup save name=("checkpoint-" . [/system clock get date] . "-" . [/system clock get time])
```