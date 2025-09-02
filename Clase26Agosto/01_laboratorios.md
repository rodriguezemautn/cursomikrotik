
## LABORATORIOS PASO A PASO

### LABORATORIO 1: CONFIGURACIÓN INICIAL DE ROUTEROS
**Duración:** 90 minutos prácticos
**Grupos:** 5 grupos de 4 alumnos
**Equipamiento:** 1 RouterBOARD RB951-2HnD por grupo

### PARTE 1: PREPARACIÓN INICIAL (20 minutos)

#### Paso 1: Descarga de Herramientas
1. Descargar WinBox desde https://mikrotik.com/download
2. Guardar winbox64.exe en el escritorio
3. Verificar que cada PC tenga cable ethernet

#### Paso 2: Conexión Física
1. Conectar cable ethernet desde PC al puerto ether2 del router
2. Verificar LED de link encendido
3. Encender el router (conectar alimentación)
4. Esperar 30 segundos para boot completo

#### Paso 3: Configuración de Red en PC
```
Windows:
1. Panel de Control → Centro de redes
2. Cambiar configuración del adaptador
3. Ethernet → Propiedades → IPv4
4. Obtener IP automáticamente
5. Aceptar y cerrar
```

### PARTE 2: PRIMER ACCESO (20 minutos)

#### Paso 4: Conexión con WinBox
1. Ejecutar winbox64.exe
2. Pestaña "Neighbors"
3. Click en botón [...] para buscar
4. Aparecerá el router con MAC address
5. Hacer click en la MAC address
6. Login: admin
7. Password: (dejar vacío)
8. Click "Connect"

#### Paso 5: Ventana de Configuración Inicial
```
Aparecerá mensaje "Do you want to use default configuration?"
- Click "Remove Configuration" 
- Confirmar con "Yes"
- El router se reiniciará
```

#### Paso 6: Reconexión Post-Reset
1. Esperar 30 segundos
2. En WinBox, click [...] nuevamente
3. Conectar por MAC address
4. Login: admin, sin contraseña

### PARTE 3: CONFIGURACIÓN BÁSICA (25 minutos)

#### Paso 7: Cambiar Identidad del Router
```
CLI:
/system identity set name=RouterG[X]

WinBox:
1. System → Identity
2. Name: RouterG[X] (donde X es número de grupo)
3. Apply → OK
```

#### Paso 8: Configurar Contraseña Admin
```
CLI:
/user set admin password=MikrotikG[X]

WinBox:
1. System → Users
2. Doble click en "admin"
3. Password: MikrotikG[X]
4. Confirm Password: MikrotikG[X]
5. OK
```

#### Paso 9: Crear Usuario de Trabajo
```
CLI:
/user add name=alumnoG[X] password=Lab2024 group=full

WinBox:
1. System → Users
2. Add (+)
3. Name: alumnoG[X]
4. Group: full
5. Password: Lab2024
6. Confirm: Lab2024
7. OK
```

### PARTE 4: CONFIGURACIÓN DE RED (25 minutos)

#### Paso 10: Configurar Dirección IP
```
CLI:
/ip address add address=192.168.[X].1/24 interface=ether2

WinBox:
1. IP → Addresses
2. Add (+)
3. Address: 192.168.[X].1/24
4. Interface: ether2
5. OK
```
*Donde [X] = 10, 20, 30, 40, 50 según grupo*

#### Paso 11: Configurar Pool DHCP
```
CLI:
/ip pool add name=dhcp_pool_G[X] ranges=192.168.[X].100-192.168.[X].200

WinBox:
1. IP → Pool
2. Add (+)
3. Name: dhcp_pool_G[X]
4. Addresses: 192.168.[X].100-192.168.[X].200
5. OK
```

#### Paso 12: Configurar DHCP Server
```
CLI:
/ip dhcp-server add name=dhcp_G[X] interface=ether2 address-pool=dhcp_pool_G[X] disabled=no

WinBox:
1. IP → DHCP Server
2. DHCP Setup
3. DHCP Server Interface: ether2
4. Next
5. DHCP Address Space: 192.168.[X].0/24
6. Next
7. Gateway: 192.168.[X].1
8. Next
9. Pool: 192.168.[X].100-192.168.[X].200
10. Next
11. DNS: 8.8.8.8, 8.8.4.4
12. Next
13. Lease Time: 10:00:00
14. Next → OK
```

#### Paso 13: Verificar Conectividad
```
En PC del grupo:
1. Renovar IP: ipconfig /release && ipconfig /renew
2. Verificar IP obtenida: ipconfig /all
3. Ping al router: ping 192.168.[X].1
4. Resultado esperado: 4 respuestas exitosas
```

### PARTE 5: SERVICIOS Y SEGURIDAD (15 minutos)

#### Paso 14: Configurar Servicios IP
```
CLI:
/ip service disable telnet,ftp,www,api
/ip service set winbox port=8291 address=192.168.[X].0/24
/ip service set ssh port=22 address=192.168.[X].0/24

WinBox:
1. IP → Services
2. Doble click en telnet → Disable
3. Doble click en ftp → Disable
4. Doble click en www → Disable
5. Doble click en winbox
   - Port: 8291
   - Available From: 192.168.[X].0/24
   - OK
```

#### Paso 15: Configurar NTP Client
```
CLI:
/system clock set time-zone-name=America/Buenos_Aires
/system ntp client set enabled=yes primary-ntp=129.6.15.28 secondary-ntp=132.163.96.1

WinBox:
1. System → Clock
2. Time Zone Name: America/Buenos_Aires
3. Apply
4. System → SNTP Client
5. Enabled: ✓
6. Primary NTP: 129.6.15.28
7. Secondary NTP: 132.163.96.1
8. OK
```

### PARTE 6: RESPALDO Y DOCUMENTACIÓN (5 minutos)

#### Paso 16: Crear Backup
```
CLI:
/system backup save name=backup-inicial-G[X]
/export file=config-inicial-G[X]

WinBox:
1. Files → Backup
2. Name: backup-inicial-G[X]
3. Password: (opcional)
4. Backup
```

#### Paso 17: Documentar Configuración
```
CLI para verificación:
/system identity print
/user print
/ip address print
/ip dhcp-server print
/ip pool print
/ip service print where disabled=no
```

### VERIFICACIÓN FINAL - CHECKLIST

- [ ] Router tiene nombre personalizado
- [ ] Contraseña admin configurada
- [ ] Usuario adicional creado
- [ ] IP LAN configurada
- [ ] DHCP Server funcionando
- [ ] PC obtiene IP por DHCP
- [ ] Ping exitoso PC → Router
- [ ] Servicios inseguros deshabilitados
- [ ] NTP configurado
- [ ] Backup creado

### TROUBLESHOOTING COMÚN

**Problema 1: No aparece router en WinBox**
- Verificar cable ethernet
- Verificar LEDs del puerto
- Deshabilitar firewall Windows temporalmente
- Usar "Legacy Mode" en WinBox

**Problema 2: No obtiene IP por DHCP**
- Verificar que DHCP server esté enabled
- Verificar pool de direcciones
- Verificar interface correcta
- Renovar IP en PC: `ipconfig /renew`

**Problema 3: Login incorrecto**
- Verificar mayúsculas/minúsculas
- Si olvidó contraseña: reset físico
- Reconectar por MAC después de cambiar password

---
