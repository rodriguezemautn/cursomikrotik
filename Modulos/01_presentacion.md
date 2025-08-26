
## DOCUMENTO 3: PRESENTACIÓN DE DIAPOSITIVAS

### DIAPOSITIVA 1: PORTADA
```
GESTIÓN DE REDES MIKROTIK
Módulo 1: Fundamentos de RouterOS

Ingeniería de Sistemas de Información
Gestión Operativa y Seguridad en Redes
2025
Emanuel Rodriguez
```

### DIAPOSITIVA 2: OBJETIVOS DE LA SESIÓN
```
OBJETIVOS DE APRENDIZAJE

Al finalizar esta sesión serás capaz de:
✓ Identificar los componentes de RouterOS
✓ Acceder al router por diferentes métodos
✓ Realizar configuración inicial segura
✓ Implementar servicios básicos de red
✓ Crear respaldos de configuración

```

### DIAPOSITIVA 3: ¿QUÉ ES MIKROTIK?
```
MIKROTIK - LA EMPRESA

• Fundada en 1996 en Latvia
• Desarrolla routers y sistemas wireless
• +500,000 routers vendidos anualmente
• Presente en +190 países

Productos principales:
→ RouterOS (Software)
→ RouterBOARD (Hardware)
→ The Dude (Monitoreo)

[Incluir logo MikroTik]
```

### DIAPOSITIVA 4: ROUTEROS
```
ROUTEROS - SISTEMA OPERATIVO

Basado en Linux kernel 3.3.5

[Diagrama circular con características:]
- Routing
- Firewall
- QoS
- VPN
- Wireless
- Hotspot
- VLAN
- Monitoring

"Un router completo en cualquier hardware"
```

### DIAPOSITIVA 5: NUESTRO EQUIPO
```
RouterBOARD RB951-2HnD

[Imagen del equipo]

Especificaciones:
• CPU: 600MHz AR9344
• RAM: 128MB
• Storage: 128MB NAND  
• Puertos: 5x Ethernet 10/100
• Wireless: 802.11b/g/n 2.4GHz
• Licencia: Level 4
• PoE: Puerto 5

```

### DIAPOSITIVA 6: MÉTODOS DE ACCESO
```
¿CÓMO CONECTARSE AL ROUTER?

[Tabla comparativa:]
┌─────────┬──────────┬───────────┬──────────┐
│ Método  │ Puerto   │ Seguridad │ Inicial  │
├─────────┼──────────┼───────────┼──────────┤
│ WinBox  │ 8291     │ ★★★★☆     │ SÍ (MAC) │
│ WebFig  │ 80       │ ★★☆☆☆     │ NO       │
│ SSH     │ 22       │ ★★★★★     │ NO       │
│ Telnet  │ 23       │ ★☆☆☆☆     │ NO       │
│ Serial  │ Console  │ ★★★★★     │ SÍ       │
└─────────┴──────────┴───────────┴──────────┘

Recomendado: WinBox para GUI, SSH para CLI
```

### DIAPOSITIVA 7: WINBOX
```
WINBOX - HERRAMIENTA PRINCIPAL

[Screenshot de WinBox con anotaciones:]

1. Neighbors: Descubre routers
2. Connect To: IP o MAC
3. Login: Credenciales
4. Connect: Iniciar sesión

Ventajas:
✓ No requiere IP inicial (MAC)
✓ Interface gráfica intuitiva
✓ Multiplataforma (Wine)
✓ Sesiones múltiples
```

### DIAPOSITIVA 8: CONFIGURACIÓN POR DEFECTO
```
CONFIGURACIÓN INICIAL RB951

[Diagrama de red:]
Internet ← ether1 (DHCP Client)
         ↓
    RouterBoard
    192.168.88.1
         ↓
Bridge (ether2-5 + wlan1)
    DHCP Server
  192.168.88.2-254

Usuario: admin
Password: (vacía)
SSID: MikroTik
```

### DIAPOSITIVA 9: RESET Y MODOS
```
TIPOS DE RESET

1. Soft Reset (mantiene usuarios):
   /system reset-configuration

2. Hard Reset (configuración fábrica):
   /system reset-configuration no-defaults=yes

3. Reset Físico:
   [Imagen botón reset]
   Mantener presionado al encender

4. NetInstall:
   Reinstalación completa vía red
```

### DIAPOSITIVA 10: ESTRUCTURA CLI
```
LÍNEA DE COMANDOS

Estructura jerárquica tipo directorio:

/
├── interface
│   ├── ethernet
│   ├── wireless
│   └── bridge
├── ip
│   ├── address
│   ├── route
│   └── firewall
└── system
    ├── identity
    └── users

Navegación: cd, .., /, TAB completion
```

### DIAPOSITIVA 11: COMANDOS BÁSICOS
```
COMANDOS ESENCIALES

Visualizar:
print         - Lista elementos
print detail  - Vista detallada
monitor      - Monitoreo tiempo real

Configurar:
add          - Agregar nuevo
set          - Modificar existente
remove       - Eliminar
enable       - Activar
disable      - Desactivar

Gestión:
export       - Exportar config
import       - Importar config
backup       - Respaldo binario
```

### DIAPOSITIVA 12: SEGURIDAD BÁSICA
```
CHECKLIST DE SEGURIDAD

□ Cambiar contraseña admin
□ Crear usuarios con permisos específicos
□ Deshabilitar servicios no usados
□ Limitar acceso por IP a servicios
□ Configurar firewall básico
□ Actualizar RouterOS
□ Crear backup inicial
□ Documentar cambios

"La seguridad no es un producto,
sino un proceso continuo"
```

### DIAPOSITIVA 13: USUARIOS Y GRUPOS
```
GESTIÓN DE USUARIOS

Grupos predefinidos:
┌──────────┬────────────────────────┐
│ read     │ Solo lectura           │
│ write    │ Lectura + Escritura    │
│ full     │ Acceso total           │
└──────────┴────────────────────────┘

Políticas disponibles:
• local    • telnet   • ssh
• ftp      • reboot   • read
• write    • policy   • test
• winbox   • password • web
• sniff    • api      • sensitive

Mejor práctica: Crear usuarios específicos
```

### DIAPOSITIVA 14: SERVICIOS IP
```
CONTROL DE SERVICIOS

[Diagrama de puertos:]

┌─────────────────────────────┐
│     SERVICIOS ACTIVOS       │
├─────────┬──────┬────────────┤
│ SSH     │ 22   │ ✓ Seguro   │
│ WinBox  │ 8291 │ ✓ GUI      │
├─────────┴──────┴────────────┤
│     DESHABILITAR            │
├─────────┬──────┬────────────┤
│ Telnet  │ 23   │ ✗ Inseguro │
│ FTP     │ 21   │ ✗ Texto    │
│ WWW     │ 80   │ ✗ HTTP     │
│ API     │ 8728 │ ✗ No usado │
└─────────┴──────┴────────────┘

Limitar por IP origen cuando sea posible
```

### DIAPOSITIVA 15: DIRECCIONAMIENTO IP
```
ESQUEMA DE DIRECCIONAMIENTO

Plan para laboratorio:
┌────────┬─────────────────┬──────────────┐
│ Grupo  │ Red LAN         │ DHCP Pool    │
├────────┼─────────────────┼──────────────┤
│ 1      │ 192.168.10.0/24 │ .100-.200    │
│ 2      │ 192.168.20.0/24 │ .100-.200    │
│ 3      │ 192.168.30.0/24 │ .100-.200    │
│ 4      │ 192.168.40.0/24 │ .100-.200    │
│ 5      │ 192.168.50.0/24 │ .100-.200    │
└────────┴─────────────────┴──────────────┘

Gateway: .1
DNS: 8.8.8.8, 8.8.4.4
```

### DIAPOSITIVA 16: DHCP SERVER
```
CONFIGURACIÓN DHCP

Componentes necesarios:
1. IP Address en interface
2. Pool de direcciones
3. DHCP Server
4. Network configuration

[Flujo visual:]
Cliente → DISCOVER → OFFER → REQUEST → ACK

Parámetros entregados:
• IP Address
• Subnet Mask
• Default Gateway
• DNS Servers
• Lease Time
```

### DIAPOSITIVA 17: BACKUP Y EXPORT
```
TIPOS DE RESPALDO

BINARY BACKUP (.backup)
✓ Respaldo completo
✓ Incluye usuarios/passwords
✓ Restauración exacta
✓ Específico del equipo

EXPORT (.rsc)
✓ Texto legible
✓ Editable
✓ No incluye usuarios
✓ Portable

Mejor práctica: Usar ambos
```

### DIAPOSITIVA 18: LABORATORIO
```
ACTIVIDAD PRÁCTICA

Tareas a completar:
1. Reset y acceso inicial
2. Configuración de identidad
3. Gestión de usuarios
4. Direccionamiento IP
5. Servidor DHCP
6. Seguridad básica
7. Respaldo

Tiempo: 90 minutos
Grupos: 5 equipos
Evaluación: Checklist
```

### DIAPOSITIVA 19: TROUBLESHOOTING
```
PROBLEMAS COMUNES

No aparece en WinBox:
→ Verificar cable
→ Verificar LEDs
→ Modo Legacy
→ Firewall Windows

No obtiene IP:
→ DHCP enabled?
→ Pool correcto?
→ Interface correcta?
→ ipconfig /renew

Login incorrecto:
→ Mayúsculas/minúsculas
→ Reset físico si necesario
→ Conectar por MAC
```

### DIAPOSITIVA 20: RECURSOS
```
RECURSOS Y SIGUIENTE CLASE

Documentación:
• wiki.mikrotik.com
• forum.mikrotik.com
• Manual PDF RouterOS

Herramientas:
• WinBox
• The Dude
• NetInstall

Próxima sesión:
→ VLANs y Bridging
→ Ruteo estático
→ Preparar: Topología 3 routers
```

### DIAPOSITIVA 21: RESUMEN
```
CONCLUSIONES

HOY APRENDIMOS:
✓ Arquitectura RouterOS
✓ Métodos de acceso
✓ Configuración inicial
✓ Seguridad básica
✓ Servicios de red
✓ Respaldos

PUEDEN AHORA:
• Configurar un router desde cero
• Implementar DHCP
• Asegurar acceso
• Documentar cambios

¡Práctica hace al maestro!
```

### DIAPOSITIVA 22: PREGUNTAS
```
¿PREGUNTAS?

[Imagen de fondo con logo MikroTik]

Contacto:
profesor@universidad.edu
Horario de consulta: [Especificar]

Siguiente clase: [Fecha]
Tarea: Revisar documentación VLANs
```

---
