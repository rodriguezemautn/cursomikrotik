# Módulo 1: Fundamentos RouterOS y Configuración Inicial

## Información del Módulo

**Modalidad:** Híbrida (Presencial + Homework)  
**Prerequisitos:** Conocimientos básicos de redes TCP/IP  

## Competencias a Desarrollar

Al finalizar este módulo, el estudiante será capaz de:

1. **Configurar dispositivos MikroTik desde cero aplicando mejores prácticas de seguridad inicial**
2. **Navegar eficientemente en RouterOS utilizando múltiples interfaces de administración**

## Contenidos Teóricos

### 1. Arquitectura RouterOS

#### 1.1 Historia y Evolución de MikroTik

MikroTik es una empresa letona fundada en 1996 por John Trully y Arnis Riekstins. Inicialmente desarrollaron software para redes inalámbricas, evolucionando hacia una solución integral de routing.

**Cronología Importante:**
- **1996:** Fundación de MikroTik
- **1997:** Primera versión de RouterOS
- **2002:** Introducción del hardware RouterBOARD
- **2009:** Lanzamiento de RouterOS v4
- **2013:** RouterOS v6 con mejoras significativas
- **2021:** RouterOS v7 con arquitectura renovada
- **2024:** RouterOS v7.x consolidado como versión estable

#### 1.2 Licencias y Versiones

RouterOS utiliza un sistema de licencias por niveles que determina las funcionalidades disponibles:

**Niveles de Licencia:**

| Nivel | Interfaces | Túneles | Queues | HotSpot | OSPF | BGP | MPLS |
|-------|------------|---------|---------|---------|------|-----|------|
| Level 1 | 1 | 1 | 1 | ✗ | ✗ | ✗ | ✗ |
| Level 3 | 1 | 1 | 1 | ✓ | ✗ | ✗ | ✗ |
| Level 4 | Unlimited | Unlimited | Unlimited | ✓ | ✓ | ✗ | ✗ |
| Level 5 | Unlimited | Unlimited | Unlimited | ✓ | ✓ | ✗ | ✗ |
| Level 6 | Unlimited | Unlimited | Unlimited | ✓ | ✓ | ✓ | ✓ |

**Tipos de Versión:**
- **Current:** Versión con últimas características (puede tener bugs)
- **Stable:** Versión probada y estable para producción
- **Long-term:** Versión con soporte extendido
- **Testing:** Versión beta para pruebas

#### 1.3 Hardware Compatible y CHR

**RouterBOARD Hardware:**
- **Serie hEX:** Routers de escritorio (RB750, RB760iGS)
- **Serie wAP:** Access Points compactos
- **Serie cAP:** Access Points ceiling mount
- **Serie CCR:** Cloud Core Routers para ISPs
- **Serie CRS:** Cloud Router Switches

**CHR (Cloud Hosted Router):**
- Versión virtualizada de RouterOS
- Compatible con VMware, VirtualBox, Hyper-V
- Licencias específicas para virtualización
- Ideal para testing y laboratorios

### 2. Interfaces de Administración

#### 2.1 Winbox

**Características:**
- Cliente gráfico nativo para Windows
- Conexión directa por MAC address
- Interface intuitiva con drag & drop
- Acceso completo a todas las funciones

**Conexión Inicial:**
1. Descargar desde mikrotik.com
2. Conectar cable Ethernet
3. Usar "..." para descubrir dispositivos
4. Conectar por MAC address

**Ventajas:**
- No requiere configuración IP previa
- Interface gráfica completa
- Múltiples ventanas simultáneas
- Copy & paste de configuraciones

#### 2.2 WebFig

**Características:**
- Interface web HTML5
- Acceso desde cualquier navegador
- Responsive design
- Funcionalidad completa via web

**Acceso:**
- URL: `https://192.168.88.1` (IP por defecto)
- Usuario: admin (sin contraseña inicialmente)
- Puerto HTTPS 443 o HTTP 80

**Limitaciones:**
- Requiere configuración IP previa
- Menor velocidad que Winbox
- Algunas funciones avanzadas limitadas

#### 2.3 Command Line Interface (CLI)

**Acceso:**
- SSH: `ssh admin@192.168.88.1`
- Telnet: `telnet 192.168.88.1`
- Console: Puerto serie

**Comandos Básicos:**
```bash
# Navegación
/system identity print
/interface print
/ip address print

# Autocompletado
[TAB] - completa comandos
? - ayuda contextual

# Paths absolutos y relativos
/ip/address/add address=10.0.0.1/24 interface=ether1
/ip address
add address=10.0.0.1/24 interface=ether1
```

**Ventajas CLI:**
- Máxima velocidad de configuración
- Scripteable y automatizable
- Acceso remoto eficiente
- Menor uso de ancho de banda

#### 2.4 Mobile App

**MikroTik App (iOS/Android):**
- Monitoreo básico
- Configuraciones simples
- Troubleshooting remoto
- Notificaciones push

#### 2.5 API REST

**Características:**
- Interface programática
- Acceso via HTTP/HTTPS
- JSON responses
- Automatización avanzada

**Ejemplo uso API:**
```bash
# Obtener interfaces
curl -u admin: http://192.168.88.1/rest/interface

# Agregar dirección IP
curl -u admin: -X PUT http://192.168.88.1/rest/ip/address \
  -d '{"interface":"ether1","address":"10.0.0.1/24"}'
```

### 3. Configuración Inicial Segura

#### 3.1 Reset y Primer Acceso

**Reset Completo:**
1. **Hardware Reset:**
   - Mantener botón reset durante boot
   - LEDs parpadean juntos = reset completo
   
2. **Software Reset:**
   ```bash
   /system reset-configuration no-defaults=yes skip-backup=yes
   ```

**Estado Post-Reset:**
- Usuario: admin (sin contraseña)
- IP: 192.168.88.1/24 en ether1
- DHCP server activo en LAN
- Todos los servicios habilitados

#### 3.2 Usuarios y Contraseñas Seguras

**Creación de Usuario Administrador:**
```bash
# Crear nuevo usuario admin
/user add name=netadmin group=full password=ComplexPass123!

# Verificar usuario creado
/user print

# Deshabilitar usuario 'admin' default
/user disable admin
```

**Grupos de Usuario:**
- **full:** Acceso completo al sistema
- **read:** Solo lectura
- **write:** Lectura y escritura (sin políticas sensibles)
- **policy:** Control de políticas específicas

**Políticas Granulares:**
```bash
# Crear grupo custom
/user group add name=monitoring policy=read,test,winbox,web,!local,!telnet,!ssh,!ftp,!reboot,!write,!policy,!password,!web,!sniff,!sensitive,!api,!romon,!dude

# Usuario para monitoreo
/user add name=monitor group=monitoring password=MonitorPass456!
```

#### 3.3 Servicios Básicos y Puertos

**Servicios por Defecto:**
```bash
# Ver servicios activos
/ip service print

# Servicios típicamente habilitados:
# - telnet (puerto 23)
# - ftp (puerto 21)
# - www (puerto 80)
# - ssh (puerto 22)
# - www-ssl (puerto 443)
# - api (puerto 8728)
# - winbox (puerto 8291)
# - api-ssl (puerto 8729)
```

**Hardening de Servicios:**
```bash
# Deshabilitar servicios innecesarios
/ip service disable telnet,ftp

# Cambiar puertos por defecto
/ip service set ssh port=2222
/ip service set winbox port=8192

# Restringir acceso por IP
/ip service set ssh address=192.168.1.0/24
/ip service set winbox address=10.0.0.0/8,192.168.0.0/16

# Certificados para SSL
/certificate add name=https-cert common-name=router.local
/certificate sign https-cert
/ip service set www-ssl certificate=https-cert
```

## Mejores Prácticas de Seguridad Inicial

### 1. Checklist de Configuración Segura

**□ Cambio de Credenciales:**
- [ ] Contraseña admin cambiada
- [ ] Usuario admin por defecto deshabilitado
- [ ] Usuario administrador nuevo creado

**□ Servicios:**
- [ ] Servicios innecesarios deshabilitados
- [ ] Puertos por defecto cambiados
- [ ] Restricciones de acceso por IP aplicadas

**□ Configuración Básica:**
- [ ] Identity del dispositivo configurado
- [ ] Timezone configurado
- [ ] NTP client habilitado
- [ ] Logging configurado

**□ Red:**
- [ ] Direccionamiento IP planificado
- [ ] DHCP server configurado apropiadamente
- [ ] Firewall básico implementado

### 2. Configuración Inicial Recomendada

```bash
# 1. Configurar identity
/system identity set name=RouterPrincipal-01

# 2. Configurar timezone y NTP
/system clock set time-zone-name=America/Argentina/Buenos_Aires
/system ntp client set enabled=yes primary-ntp=pool.ntp.org

# 3. Configurar logging
/system logging add topics=error,warning,critical action=memory
/system logging add topics=firewall action=memory

# 4. Backup inicial
/export file=config-inicial

# 5. Usuario y contraseña segura
/user set admin password=TempPass123!
/user add name=netadmin group=full password=AdminPass456!
/user disable admin

# 6. Servicios básicos
/ip service disable telnet,ftp
/ip service set winbox port=8192 address=192.168.88.0/24
/ip service set ssh port=2222 address=192.168.88.0/24

# 7. Firewall básico
/ip firewall filter add chain=input action=accept connection-state=established,related
/ip firewall filter add chain=input action=accept src-address=192.168.88.0/24
/ip firewall filter add chain=input action=drop
```

## Comandos Esenciales de Referencia

### Navegación y Ayuda
```bash
# Navegación
/                    # Root del sistema
..                   # Subir un nivel
/system clock        # Path absoluto
clock                # Path relativo

# Ayuda
?                    # Ayuda del contexto actual
print ?              # Ayuda del comando print
tab                  # Autocompletar
```

### Gestión de Configuración
```bash
# Export/Import
/export file=backup-config
/import file=backup-config

# Reset
/system reset-configuration

# Identity
/system identity set name=MiRouter
```

### Usuarios y Acceso
```bash
# Usuarios
/user print
/user add name=usuario password=pass group=full
/user disable usuario

# Servicios
/ip service print
/ip service disable telnet
/ip service set ssh port=2222
```

### Información del Sistema
```bash
# Recursos
/system resource print

# Versión
/system package print

# Interfaces
/interface print

# Direcciones IP
/ip address print
```

## Troubleshooting Común

### Problemas de Conectividad

**No puedo conectar con Winbox:**
1. Verificar cable de red
2. Usar MAC address para conectar
3. Verificar que Winbox esté en modo discovery
4. Reset del dispositivo si es necesario

**Perdí la contraseña:**
1. Reset hardware del dispositivo
2. Conectar via MAC address
3. Reconfigurar desde cero

**No responde por IP:**
1. Verificar configuración IP
2. Verificar conectividad física
3. Usar console o MAC address
4. Verificar firewall rules

### Problemas de Servicios

**SSH no conecta:**
```bash
# Verificar estado del servicio
/ip service print where name=ssh

# Verificar puerto y restricciones
/ip service set ssh port=22 address=0.0.0.0/0 disabled=no
```

**Winbox muy lento:**
1. Usar dirección IP en lugar de MAC
2. Verificar versión de Winbox
3. Verificar recursos del dispositivo

## Recursos Adicionales

### Enlaces de Referencia
- **Documentación Oficial:** https://help.mikrotik.com/docs
- **Wiki MikroTik:** https://wiki.mikrotik.com
- **Foro Oficial:** https://forum.mikrotik.com
- **Training:** https://mikrotik.com/training

### Herramientas Útiles
- **Winbox:** Cliente gráfico principal
- **The Dude:** Monitoreo de red
- **NetInstall:** Instalación masiva
- **Graphing:** Monitoreo gráfico incorporado

### Certificaciones Relacionadas
- **MTCNA:** MikroTik Certified Network Associate
- **MTCRE:** MikroTik Certified Routing Engineer
- **MTCTCE:** MikroTik Certified Traffic Control Engineer