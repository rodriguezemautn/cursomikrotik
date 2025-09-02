# Presentación Módulo 1: Fundamentos RouterOS y Configuración Inicial

## Estructura de Presentación (PowerPoint/Google Slides)

---

## **DIAPOSITIVA 1: Título**
```
MÓDULO 1: FUNDAMENTOS ROUTEROS Y CONFIGURACIÓN INICIAL

Curso Avanzado MikroTik
Gestión Operativa y Seguridad de Redes

Profesor: [Nombre]
Duración: 2 semanas (8 horas)
```

---

## **DIAPOSITIVA 2: Objetivos del Módulo**
```
🎯 COMPETENCIAS A DESARROLLAR

Al finalizar este módulo podrás:

✅ Configurar dispositivos MikroTik desde cero aplicando 
   mejores prácticas de seguridad inicial

✅ Navegar eficientemente en RouterOS utilizando 
   múltiples interfaces de administración

🔧 METODOLOGÍA: Aprendizaje práctico con equipos reales
```

---

## **DIAPOSITIVA 3: Agenda del Módulo**
```
📅 CONTENIDOS

SEMANA 1:
• Arquitectura RouterOS
• Interfaces de Administración  
• Configuración Inicial Segura

SEMANA 2:
• Laboratorios Prácticos
• Hardening de Seguridad
• Backup y Documentación

🏠 HOMEWORK: CHR + Documentación
```

---

## **DIAPOSITIVA 4: Historia MikroTik**
```
🏛️ HISTORIA Y EVOLUCIÓN

1996  🚀 Fundación MikroTik (Letonia)
      Fundadores: John Trully & Arnis Riekstins

1997  📡 Primera versión RouterOS (wireless focus)

2002  🔧 Introducción hardware RouterBOARD

2009  ⚡ RouterOS v4 (major update)

2013  🌟 RouterOS v6 (performance boost)

2021  🚀 RouterOS v7 (nueva arquitectura)

2024  ✨ RouterOS v7.x estable actual
```

---

## **DIAPOSITIVA 5: ¿Por qué MikroTik?**
```
💰 VENTAJAS COMPETITIVAS

COSTO/BENEFICIO
• Hardware económico vs. competencia
• Licencias sin renovación anual
• Funcionalidades enterprise a precio SOHO

FLEXIBILIDAD
• Mismo OS en todo hardware
• Virtualización (CHR) incluida
• Configuración granular completa

ESCALABILIDAD  
• Desde SOHO hasta ISP
• Mismos conceptos, diferentes escalas
• Upgrade path claro
```

---

## **DIAPOSITIVA 6: Arquitectura RouterOS**
```
🏗️ ARQUITECTURA DEL SISTEMA

┌─────────────────────────┐
│    APLICACIONES         │ ← Winbox, WebFig, CLI
├─────────────────────────┤
│    RouterOS KERNEL      │ ← Routing, Switching, Firewall
├─────────────────────────┤
│    LINUX KERNEL         │ ← Sistema operativo base
├─────────────────────────┤
│    HARDWARE LAYER       │ ← RouterBOARD, CHR, x86
└─────────────────────────┘

• Kernel propio optimizado para networking
• Base Linux para estabilidad
• Abstracción de hardware uniforme
```

---

## **DIAPOSITIVA 7: Tipos de Hardware**
```
🔧 HARDWARE MIKROTIK

ROUTERBOARD (Hardware Dedicado)
• hEX series → SOHO/Branch office
• wAP series → Wireless access points  
• CRS series → Managed switches
• CCR series → ISP/Enterprise core

CHR (Cloud Hosted Router)
• Virtualización en VM
• Mismas funcionalidades
• Licencias específicas virtuales
• Ideal para testing/lab

x86 COMPATIBLE
• PC estándar con RouterOS
• Mayor flexibilidad hardware
• Más costoso que RouterBOARD
```

---

## **DIAPOSITIVA 8: Sistema de Licencias**
```
🏷️ LICENCIAS ROUTEROS

NIVELES DE FUNCIONALIDAD:
Level 1  → 1 interface  | Básico SOHO
Level 3  → 1 interface  | + HotSpot  
Level 4  → Unlimited   | + OSPF
Level 5  → Unlimited   | + OSPF (same as L4)
Level 6  → Unlimited   | + BGP + MPLS

TIPOS DE LICENCIA:
• Perpetua (sin renovación)
• Incluida con hardware RouterBOARD
• CHR: licencias especiales virtuales
• x86: compra separada necesaria

💡 Hardware RouterBOARD = Licencia incluida
```

---

## **DIAPOSITIVA 9: Versiones RouterOS**
```
📦 TIPOS DE VERSION

CURRENT BRANCH
• Últimas funcionalidades
• Puede contener bugs
• Para testing/laboratorio

STABLE BRANCH  
• Probada exhaustivamente
• RECOMENDADA para producción
• Balance funcionalidad/estabilidad

LONG-TERM BRANCH
• Soporte extendido
• Actualizaciones solo críticas
• Para entornos ultra-estables

TESTING BRANCH
• Beta releases
• Solo para desarrolladores
```

---

## **DIAPOSITIVA 10: Interfaces de Administración**
```
🖥️ MÉTODOS DE ACCESO

WINBOX 🏆 (RECOMENDADO)
• Cliente gráfico Windows/Wine
• Conexión por MAC (sin IP)
• Interface completa e intuitiva
• Drag & drop configurations

WEBFIG 🌐
• Interface web HTML5
• Cualquier navegador moderno
• Responsive design
• Requiere IP configurada

CLI 💻 (AVANZADO)
• SSH, Telnet, Console
• Máxima velocidad configuración
• Scripteable y automatizable
• Learning curve elevada
```

---

## **DIAPOSITIVA 11: Winbox - Características**
```
🏆 WINBOX: LA HERRAMIENTA PRINCIPAL

VENTAJAS ÚNICAS:
✅ Descubrimiento automático dispositivos
✅ Conexión por MAC (sin configurar IP)
✅ Múltiples ventanas simultáneas
✅ Copy/paste configuraciones
✅ Drag & drop para configurar
✅ Safe mode automático

DESCARGA:
🌐 mikrotik.com/download
📱 Portable (no requiere instalación)
🍷 Compatible con Wine (Linux/Mac)

💡 SIEMPRE usar última versión disponible
```

---

## **DIAPOSITIVA 12: WebFig vs Winbox**
```
⚖️ COMPARACIÓN INTERFACES

WEBFIG                    WINBOX
────────────────────────────────────────
✅ Cualquier OS           ❌ Solo Windows/Wine
✅ No instalar nada       ❌ Descarga requerida  
✅ Acceso remoto fácil    ✅ Conexión por MAC
❌ Requiere IP config     ✅ No requiere IP
❌ Más lento              ✅ Muy rápido
❌ Funciones limitadas    ✅ Funcionalidad completa

RECOMENDACIÓN:
🏆 Winbox para configuración diaria
🌐 WebFig para acceso remoto/emergencia
💻 CLI para automatización/scripting
```

---

## **DIAPOSITIVA 13: CLI - Command Line**
```
💻 COMMAND LINE INTERFACE

ACCESO:
• SSH: ssh admin@192.168.88.1
• Telnet: telnet 192.168.88.1  
• Console: Puerto serie

ESTRUCTURA:
/system/identity    ← Path absoluto
identity           ← Path relativo  
..                 ← Subir nivel
/                  ← Root

AYUDA:
?                  ← Contexto actual
print ?            ← Ayuda comando
[TAB]             ← Autocompletar

💡 CLI = Máxima eficiencia para expertos
```

---

## **DIAPOSITIVA 14: API y Mobile**
```
📱 OTRAS INTERFACES

API REST
• HTTP/HTTPS programmatic access
• JSON responses
• Automatización avanzada
• Integration con sistemas externos

MIKROTIK MOBILE APP
• iOS y Android
• Monitoreo básico
• Configuraciones simples  
• Push notifications
• Troubleshooting remoto

💡 API para automation, Mobile para monitoreo
```

---

## **DIAPOSITIVA 15: Configuración Por Defecto**
```
🏭 ESTADO POST-RESET

NETWORK:
• IP: 192.168.88.1/24 (ether1)
• DHCP Server: 192.168.88.100-200
• DNS: Resolver habilitado
• NAT: Configurado automáticamente

SECURITY:  
• Usuario: admin (SIN contraseña) ⚠️
• Servicios: TODOS habilitados ⚠️
• Firewall: Básico para WAN ⚠️

WIRELESS:
• SSID: MikroTik-XXXXXX
• Sin contraseña ⚠️
• Open access

🚨 CONFIGURACIÓN INSEGURA - Requiere hardening
```

---

## **DIAPOSITIVA 16: Reset Procedures**
```
🔄 MÉTODOS DE RESET

HARDWARE RESET:
1️⃣ Desconectar alimentación
2️⃣ Mantener botón RESET presionado
3️⃣ Conectar alimentación (mantener reset)
4️⃣ Esperar: LEDs parpadean individualmente
5️⃣ Todos LEDs parpadean juntos → SOLTAR
6️⃣ Dispositivo reinicia automáticamente

SOFTWARE RESET:
/system reset-configuration no-defaults=yes

NETINSTALL MODE:
• Mantener reset >10 segundos
• Para reinstalación completa OS
• Requiere herramienta NetInstall
```

---

## **DIAPOSITIVA 17: Primer Acceso Seguro**
```
🔒 HARDENING INICIAL OBLIGATORIO

PASO 1: CAMBIAR CREDENCIALES
• Contraseña admin temporal
• Crear usuario administrativo nuevo  
• Deshabilitar usuario admin default

PASO 2: SERVICIOS
• Deshabilitar telnet, ftp
• Cambiar puertos SSH, Winbox
• Configurar restricciones IP

PASO 3: SISTEMA
• Configurar identity
• Timezone y NTP
• Logging básico

⚠️ NUNCA dejar configuración por defecto
```

---

## **DIAPOSITIVA 18: Gestión de Usuarios**
```
👥 SISTEMA DE USUARIOS

GRUPOS PREDEFINIDOS:
• full     → Acceso completo
• read     → Solo lectura
• write    → Lectura/escritura básica

POLÍTICAS GRANULARES:
• local, telnet, ssh, ftp
• reboot, write, policy  
• password, web, sniff
• winbox, api, test

MEJORES PRÁCTICAS:
✅ Usuario admin personalizado
✅ Grupos con mínimos privilegios
✅ Contraseñas complejas
❌ NUNCA usuario default en producción
```

---

## **DIAPOSITIVA 19: Contraseñas Seguras**
```
🔐 POLÍTICA DE CONTRASEÑAS

REQUISITOS MÍNIMOS:
• 12+ caracteres
• Mayúsculas, minúsculas, números
• Símbolos especiales
• No palabras diccionario
• No información personal

EJEMPLOS SEGUROS:
✅ MyR0ut3r!2024#Secure
✅ L@b$Setup_MikroTik2024
✅ Adm1n!NetW0rk&2024

EJEMPLOS INSEGUROS:
❌ admin, password, 123456
❌ mikrotik, router
❌ password123

🔄 Cambiar cada 90 días en producción
```

---

## **DIAPOSITIVA 20: Servicios de Red**
```
🌐 SERVICIOS POR DEFECTO

SEGUROS PARA PRODUCCIÓN:
✅ SSH (puerto 22) → Cambiar puerto
✅ HTTPS/WebFig (443) → OK
✅ Winbox (8291) → Cambiar puerto  
✅ API-SSL (8729) → Para scripts

INSEGUROS - DESHABILITAR:
❌ Telnet (23) → Texto plano
❌ FTP (21) → Credenciales planas
❌ HTTP/WebFig (80) → Sin cifrado

RESTRICCIÓN ACCESO:
• Configurar Available From
• Solo redes administrativas
• Nunca 0.0.0.0/0 en producción
```

---

## **DIAPOSITIVA 21: Configuración Sistema Básica**
```
⚙️ CONFIGURACIÓN SISTEMA

IDENTITY:
• Nombre descriptivo del dispositivo
• Incluir ubicación/función
• Ej: "CoreRouter-DataCenter-01"

TIMEZONE & NTP:
• Configurar zona horaria local
• Habilitar cliente NTP
• Usar pool.ntp.org o servidores locales
• Logs con timestamp correcto

LOGGING:
• Configurar tópicos importantes
• error, warning, critical → memory
• info, debug solo para troubleshooting
• Rotation automática logs
```

---

## **DIAPOSITIVA 22: Backup y Recovery**
```
💾 RESPALDOS DE CONFIGURACIÓN

TIPOS DE BACKUP:
• .backup → Archivo binario completo
  - Incluye certificados, usuarios
  - Password protected
  - Restaura configuración idéntica

• .rsc (Export) → Archivo texto plano
  - Comandos RouterOS script
  - Editable, versionable
  - Portable entre dispositivos

MEJORES PRÁCTICAS:
✅ Backup antes de cambios importantes
✅ Exportar configuración regularmente
✅ Almacenar backups en ubicación segura
✅ Probar procedimientos recovery
```

---

## **DIAPOSITIVA 23: LABORATORIO 1 - Overview**
```
🧪 LABORATORIO PRÁCTICO 1

OBJETIVOS:
• Reset completo dispositivo RB951-2HnD
• Primer acceso con Winbox
• Configuración identity y sistema básico

EQUIPAMIENTO:
• 1 RouterBOARD RB951-2HnD por grupo
• Laptops con Winbox instalado
• Cables Ethernet

DURACIÓN: 30 minutos

ENTREGABLES:
✅ Device reseteado y accesible
✅ Identity configurado
✅ Configuración sistema básica aplicada
```

---

## **DIAPOSITIVA 24: LABORATORIO 2 - Usuarios**
```
🧪 LABORATORIO PRÁCTICO 2

OBJETIVOS:
• Crear usuarios con roles diferenciados
• Configurar grupos personalizados
• Probar diferentes niveles de acceso

USUARIOS A CREAR:
• netadmin (grupo full)
• monitor (grupo read)  
• operador01 (grupo custom operator)

POLÍTICAS CUSTOM:
• Grupo operator: read, write, test, web, winbox
• SIN: local, ssh, policy permissions

DURACIÓN: 30 minutos
```

---

## **DIAPOSITIVA 25: LABORATORIO 3 - Servicios**
```
🧪 LABORATORIO PRÁCTICO 3

OBJETIVOS:
• Auditar servicios activos
• Deshabilitar servicios inseguros
• Cambiar puertos por defecto
• Configurar restricciones de acceso

HARDENING CHECKLIST:
❌ Deshabilitar: telnet, ftp
🔧 Cambiar puertos: SSH→2222, Winbox→8192
🔒 Restricciones IP: Solo LAN access
🔐 Certificado HTTPS personalizado

DURACIÓN: 45 minutos

VERIFICACIÓN: Probar acceso con nuevos puertos
```

---

## **DIAPOSITIVA 26: Comandos Esenciales**
```
💻 COMANDOS DE REFERENCIA

NAVEGACIÓN:
/                     # Root del sistema
..                   # Subir un nivel  
/system/identity     # Path absoluto
print               # Mostrar configuración

CONFIGURACIÓN BÁSICA:
/system identity set name="MiRouter"
/system clock set time-zone-name=America/Argentina/Buenos_Aires  
/system ntp client set enabled=yes primary-ntp=pool.ntp.org

USUARIOS:
/user add name=admin2 group=full password="SecurePass123!"
/user disable admin

BACKUP:
/export file=mi-config
```

---

## **DIAPOSITIVA 27: Troubleshooting Común**
```
🔧 PROBLEMAS COMUNES Y SOLUCIONES

NO CONECTA WINBOX:
✅ Verificar cable Ethernet
✅ Usar MAC address (no IP)
✅ Probar en puerto ether1
✅ Reset si es necesario

PERDÍ CONTRASEÑA:
✅ Reset hardware completo
✅ Conectar por MAC sin password
✅ Reconfigurar usuarios

SERVICIO NO RESPONDE:
✅ Verificar puerto configurado
✅ Verificar Available From
✅ Verificar firewall rules
✅ Verificar usuario/password
```

---

## **DIAPOSITIVA 28: Lista Verificación Seguridad**
```
✅ CHECKLIST CONFIGURACIÓN SEGURA

USUARIOS Y ACCESO:
□ Contraseña admin cambiada
□ Usuario administrador nuevo creado
□ Usuario admin default deshabilitado
□ Grupos con políticas mínimas

SERVICIOS:
□ Telnet y FTP deshabilitados
□ Puertos SSH y Winbox cambiados
□ Restricciones IP aplicadas
□ HTTPS con certificado propio

SISTEMA:
□ Identity descriptivo configurado
□ Timezone y NTP configurados
□ Logging básico habilitado
□ Backup de configuración creado
```

---

## **DIAPOSITIVA 29: Homework Individual**
```
🏠 TRABAJO INDIVIDUAL

INSTALACIÓN CHR:
• Descargar CHR desde mikrotik.com
• Instalar en VirtualBox o VMware
• Aplicar misma configuración que laboratorio

DOCUMENTACIÓN:
• Proceso de instalación CHR paso a paso
• Comparativa interfaces administración
• Diferencias CHR vs Hardware físico
• Procedimientos hardening aplicados

ENTREGABLES:
📄 Documento técnico completo
💾 Export de configuración CHR
🖼️ Screenshots verificación

FECHA LÍMITE: Próxima clase
```

---

## **DIAPOSITIVA 30: Evaluación del Módulo**
```
📊 EVALUACIÓN Y CRITERIOS

DISTRIBUCIÓN CALIFICACIONES:
• Lista verificación configuración segura (40%)
• Quiz teórico fundamentos (30%)
• Documentación técnica proceso (30%)

CRITERIOS COMPETENCIAS:
• Excelente (90-100): Configura independientemente
• Competente (75-89): Aplica con guía mínima
• En desarrollo (60-74): Requiere supervisión
• Insuficiente (<60): Requiere refuerzo

PRÓXIMO MÓDULO:
🔗 Configuración Interfaces y Switching
📅 Semana próxima
🎯 VLANs y segmentación de red
```

---

## **DIAPOSITIVA 31: Recursos Adicionales**
```
📚 RECURSOS DE APRENDIZAJE

DOCUMENTACIÓN OFICIAL:
🌐 help.mikrotik.com/docs
📖 wiki.mikrotik.com  
💬 forum.mikrotik.com

HERRAMIENTAS:
🖥️ Winbox (cliente principal)
🔍 The Dude (monitoreo red)
⚙️ NetInstall (instalación masiva)

CERTIFICACIONES:
🎓 MTCNA: MikroTik Certified Network Associate
🎓 MTCRE: MikroTik Certified Routing Engineer
🎓 MTCTCE: MikroTik Traffic Control Engineer

📧 CONTACTO: [email del profesor]
💬 CONSULTAS: Horario de oficina [horario]
```

---

## **DIAPOSITIVA 32: Preguntas y Respuestas**
```
❓ PREGUNTAS Y RESPUESTAS

¿Dudas sobre los conceptos vistos?

¿Problemas durante los laboratorios?

¿Consultas sobre el homework?

¿Sugerencias para próximas clases?

📧 También pueden contactarme por email
💬 Foro del curso disponible 24/7

¡Gracias por su participación! 👏

PRÓXIMA CLASE:
🔗 Módulo 2: Configuración Interfaces y Switching
```

---

## **Notas para el Docente**

### **Timing Sugerido por Diapositiva:**
- Diapositivas 1-3: 5 minutos (introducción)
- Diapositivas 4-8: 15 minutos (historia y arquitectura)
- Diapositivas 9-14: 20 minutos (interfaces administración)
- Diapositivas 15-22: 25 minutos (configuración segura)
- Diapositivas 23-26: 30 minutos (laboratorios)
- Diapositivas 27-32: 15 minutos (troubleshooting y cierre)

### **Puntos Clave para Enfatizar:**
1. **Seguridad desde el primer momento** - nunca dejar configuración por defecto
2. **Winbox como herramienta principal** pero conocer alternativas
3. **Backup siempre antes de cambios importantes**
4. **Documentación es parte integral del proceso**
5. **Práctica hands-on es esencial para aprendizaje**

### **Elementos Visuales Recomendados:**
- Screenshots de Winbox en cada laboratorio
- Diagramas de topología de red
- Iconos para diferentes tipos de servicios
- Colores: Verde para seguro, Rojo para inseguro, Azul para información
- Gráficos comparativos entre interfaces

### **Adaptaciones por Audiencia:**
- **Principiantes:** Más tiempo en navegación Winbox
- **Intermedios:** Enfocarse en CLI y mejores prácticas
- **Avanzados:** Emphasis en automatización y scripting

Esta presentación está diseñada para ser completamente interactiva con las actividades de laboratorio integradas.