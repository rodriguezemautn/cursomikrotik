# MÓDULO 1: FUNDAMENTOS DE MIKROTIK ROUTEROS
## Introducción y Configuración Inicial

---

### 1. INTRODUCCIÓN A MIKROTIK

#### 1.1 ¿Qué es MikroTik?
MikroTik es una empresa letona fundada en 1996 que desarrolla routers y sistemas inalámbricos para ISPs. Su producto principal es RouterOS, un sistema operativo basado en el kernel Linux que convierte hardware estándar o propietario en un router completo.

#### 1.2 RouterOS - Sistema Operativo de Red
**Características principales:**
- Sistema operativo independiente basado en Linux kernel 3.3.5
- Soporte para arquitecturas x86, ARM, MIPS, PowerPC, TILE
- Multiprocesamiento simétrico (SMP) para aprovechar múltiples núcleos
- Licenciamiento por niveles (L0-L6) según funcionalidades

**Funcionalidades clave:**
- Routing estático y dinámico (OSPF, BGP, RIP)
- Firewall stateful con filtrado avanzado
- QoS y gestión de ancho de banda
- VPN (PPTP, L2TP, SSTP, OpenVPN, IPSec)
- Wireless 802.11a/b/g/n/ac
- Hotspot y gestión de usuarios
- VLAN y bridging

#### 1.3 RouterBOARD RB951-2HnD
**Especificaciones del equipo:**
- CPU: AR9344 600MHz
- RAM: 128MB
- Storage: 128MB NAND
- 5 puertos Ethernet 10/100
- Wireless 802.11b/g/n 2.4GHz integrado
- Antena de 2.5dBi
- PoE en puerto 5
- Licencia Level 4

### 2. MÉTODOS DE ACCESO

#### 2.1 Acceso Inicial
RouterOS ofrece múltiples métodos de acceso:

**a) WinBox (Recomendado para principiantes)**
- Aplicación GUI propietaria de MikroTik
- Conexión por MAC (Layer 2) o IP (Layer 3)
- No requiere configuración IP inicial
- Descarga: mikrotik.com/download

**b) WebFig**
- Interface web integrada
- Acceso por navegador: http://192.168.88.1
- Requiere IP configurada

**c) SSH/Telnet**
- Acceso por línea de comandos
- SSH (puerto 22) - seguro
- Telnet (puerto 23) - no seguro

**d) Puerto Serial**
- Cable null-modem RS-232
- Configuración: 115200 bps, 8N1

#### 2.2 Configuración por Defecto
**Parámetros iniciales del RB951-2HnD:**
- IP LAN: 192.168.88.1/24
- DHCP Server: 192.168.88.2-254
- Usuario: admin
- Contraseña: (vacía)
- Bridge: ether2-ether5 + wlan1

### 3. ESTRUCTURA DE COMANDOS

#### 3.1 Sintaxis CLI
La línea de comandos sigue una estructura jerárquica:
```
/menu/submenu comando parametro=valor
```

**Comandos básicos:**
- `print` - muestra configuración
- `add` - agrega nueva entrada
- `set` - modifica entrada existente
- `remove` - elimina entrada
- `enable/disable` - activa/desactiva
- `export` - exporta configuración
- `..` - sube un nivel
- `/` - va a raíz

#### 3.2 Sistema de Numeración
Las entradas se identifican por números o nombres:
```
/interface print
/interface set 0 name=WAN
/interface set ether1 comment="Puerto WAN"
```

### 4. CONFIGURACIÓN INICIAL SEGURA

#### 4.1 Principios de Seguridad
1. **Cambiar contraseña admin inmediatamente**
2. **Crear usuarios con permisos específicos**
3. **Deshabilitar servicios no utilizados**
4. **Configurar firewall básico**
5. **Actualizar RouterOS**

#### 4.2 Grupos de Usuarios
RouterOS maneja permisos mediante grupos:
- **read** - solo lectura
- **write** - lectura y escritura
- **full** - acceso total

#### 4.3 Servicios IP
Controlar puertos abiertos:
```
/ip service
- www (80) - WebFig
- ssh (22) - SSH
- telnet (23) - Telnet
- ftp (21) - FTP
- winbox (8291) - WinBox
- api (8728) - API
```

### 5. RESPALDOS Y RECUPERACIÓN

#### 5.1 Tipos de Backup
**Backup Binario:**
- Archivo .backup
- Incluye toda la configuración
- Incluye usuarios y contraseñas
- Restauración exacta

**Export de Configuración:**
- Archivo .rsc (texto)
- Legible y editable
- No incluye usuarios
- Portable entre equipos

#### 5.2 Reset de Configuración
**Métodos de reset:**
1. Software: `/system reset-configuration`
2. Hardware: Botón reset al encender
3. NetInstall: Reinstalación completa

---
