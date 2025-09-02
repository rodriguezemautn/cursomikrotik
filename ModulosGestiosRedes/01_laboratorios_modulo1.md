# Laboratorios - Módulo 1: Fundamentos RouterOS

## Información General

**Equipamiento:** RouterBOARD RB951-2HnD por grupo (máximo 4 estudiantes)  
**Duración:** 4 sesiones de 2 horas cada una  
**Modalidad:** Grupos de trabajo colaborativo  

---

## LABORATORIO 1: Reset Completo y Primer Acceso
**Duración:** 30 minutos  
**Objetivos:** Familiarización con el hardware y procedimientos de reset

### Preparación del Entorno

**Materiales Necesarios:**
- 1 RouterBOARD RB951-2HnD por grupo
- Cables Ethernet CAT5e/CAT6
- Laptops con Winbox instalado
- Fuente de alimentación PoE o adapter

**Topología del Laboratorio:**
```
[Laptop Estudiante] ---- [ether1] RB951-2HnD [ether2-5] ---- [Switch Aula]
                                    |
                              [Wireless LAN]
```

### Paso a Paso - Reset Completo

#### Paso 1: Identificación del Hardware

1. **Inspeccionar el RouterBOARD RB951-2HnD:**
   - Localizar puertos Ethernet (5 puertos)
   - Identificar LED de estado (PWR, Act, WiFi)
   - Ubicar botón de reset
   - Verificar antena wireless

2. **Verificar alimentación:**
   - Conectar fuente de alimentación
   - Verificar LED PWR encendido
   - Observar secuencia de boot (LEDs parpadeando)

#### Paso 2: Reset por Hardware

1. **Desconectar alimentación** del dispositivo

2. **Mantener presionado el botón RESET**

3. **Conectar alimentación** manteniendo presionado reset

4. **Esperar secuencia de LEDs:**
   - Primeros 5 segundos: LEDs Act parpadean individualmente
   - Después de 5 segundos: Todos los LEDs Act parpadean juntos
   - **SOLTAR botón reset cuando todos parpadeen juntos**

5. **Verificar reset completo:**
   - Dispositivo reinicia automáticamente
   - LED PWR permanece encendido
   - LEDs Act se apagan tras boot completo

> **⚠️ IMPORTANTE:** Si no se suelta el botón cuando todos los LEDs parpadean juntos, el dispositivo entrará en NetInstall mode.

#### Paso 3: Verificar Estado Post-Reset

1. **Conectar laptop al puerto ether1**

2. **Configurar adaptador de red laptop:**
   - IP automática (DHCP)
   - Verificar obtención de IP en rango 192.168.88.x

3. **Verificar conectividad:**
   ```bash
   ping 192.168.88.1
   ```

**Estado esperado post-reset:**
- IP Gateway: 192.168.88.1/24
- DHCP Server: Activo en rango 192.168.88.100-200
- Usuario: admin (sin contraseña)
- Servicios: Todos habilitados

---

## LABORATORIO 2: Configuración Inicial con Winbox
**Duración:** 45 minutos  
**Objetivos:** Dominar el uso de Winbox y configuración básica

### Paso a Paso - Primer Acceso con Winbox

#### Paso 1: Descubrimiento y Conexión

1. **Abrir Winbox** en laptop

2. **Hacer clic en "..." (botón Neighbors)**
   - Winbox busca automáticamente dispositivos MikroTik
   - Aparecerá el RB951 en la lista

3. **Identificar el dispositivo:**
   - MAC Address: XX:XX:XX:XX:XX:XX
   - IP Address: 192.168.88.1
   - Board: RB951-2n
   - Version: 7.xx

4. **Conectar usando MAC Address:**
   - Hacer doble clic en MAC address (NO en IP)
   - Ventaja: conexión directa sin configuración IP
   - Login: admin
   - Password: (dejar vacío)
   - Clic en "Connect"

#### Paso 2: Interfaz Winbox - Navegación

1. **Explorar ventana principal:**
   - **Menu lateral izquierdo:** Todas las funciones organizadas
   - **Área de trabajo central:** Configuraciones y estados
   - **Status bar:** Información de conexión y recursos

2. **Verificar información del sistema:**
   - **System → Resource:**
     - Uptime, CPU load, Memory usage
     - Architecture, Board name
   - **System → RouterBoard:**
     - Model, Serial number, Firmware

3. **Navegar en el menú:**
   - **Interface:** Configuración de puertos
   - **IP:** Configuración de red (Address, DHCP, Firewall)
   - **System:** Configuración del sistema
   - **Tools:** Herramientas de diagnóstico

#### Paso 3: Configuración Identity

1. **Navegar a System → Identity**

2. **Configurar nombre del router:**
   ```
   Name: Grupo01-RB951-Lab
   ```
   (Reemplazar "01" por número del grupo)

3. **Aplicar cambios:**
   - Clic en "OK"
   - Verificar cambio en title bar de Winbox

#### Paso 4: Configuración Básica de Seguridad

1. **Cambiar contraseña admin temporal:**
   - **System → Users**
   - Seleccionar usuario "admin"
   - **Password:** `TempLab123!`
   - Clic "OK"

2. **Configurar timezone:**
   - **System → Clock**
   - **Time Zone Name:** `America/Argentina/Buenos_Aires`
   - Clic "Apply"

3. **Habilitar NTP client:**
   - **System → SNTP Client**
   - **Enabled:** ✓ Check
   - **Primary NTP:** `pool.ntp.org`
   - Clic "OK"

---

## LABORATORIO 3: Creación de Usuarios con Roles Diferenciados
**Duración:** 30 minutos  
**Objetivos:** Implementar control de acceso granular

### Paso a Paso - Gestión de Usuarios

#### Paso 1: Crear Usuario Administrador

1. **Navegar a System → Users**

2. **Agregar nuevo usuario:**
   - Clic en "+"
   - **Name:** `netadmin`
   - **Group:** `full`
   - **Password:** `AdminLab456!`
   - Clic "OK"

3. **Verificar usuario creado:**
   - Usuario aparece en la lista
   - Group: full
   - Status: active

#### Paso 2: Crear Usuario de Solo Lectura

1. **Agregar usuario monitoreo:**
   - Clic en "+"
   - **Name:** `monitor`
   - **Group:** `read`
   - **Password:** `MonitorLab789!`
   - Clic "OK"

#### Paso 3: Crear Grupo Personalizado

1. **Navegar a System → User Groups**

2. **Crear grupo "operator":**
   - Clic en "+"
   - **Name:** `operator`
   - **Policies:** Seleccionar:
     - ✓ read
     - ✓ write  
     - ✓ test
     - ✓ web
     - ✓ winbox
     - ✗ local (sin acceso a archivos locales)
     - ✗ ssh (sin acceso SSH)
     - ✗ policy (sin cambio de políticas)
   - Clic "OK"

3. **Crear usuario con grupo personalizado:**
   - **System → Users → "+"**
   - **Name:** `operador01`
   - **Group:** `operator`
   - **Password:** `OperLab321!`
   - Clic "OK"

#### Paso 4: Probar Diferentes Accesos

1. **Desconectarse de Winbox actual**
   - File → Quit (o cerrar ventana)

2. **Probar acceso usuario "monitor":**
   - Conectar nuevamente por MAC
   - User: `monitor`
   - Password: `MonitorLab789!`
   - **Verificar limitaciones:** Solo puede ver configuraciones, no modificar

3. **Probar acceso usuario "operador01":**
   - Conectar con credenciales de operador
   - **Verificar permisos:** Puede modificar configuraciones básicas
   - **Intentar cambiar usuarios:** Debe fallar (sin policy permission)

4. **Volver a usuario "netadmin"** para continuar laboratorio

#### Paso 5: Deshabilitar Usuario Admin Por Defecto

1. **Con usuario netadmin conectado:**
   - **System → Users**
   - Seleccionar usuario "admin"
   - Clic en "D" (Disable) en toolbar
   - Usuario aparece en gris (disabled)

> **⚠️ IMPORTANTE:** Solo deshabilitar "admin" después de verificar que "netadmin" funciona correctamente.

---

## LABORATORIO 4: Configuración de Servicios Básicos
**Duración:** 45 minutos  
**Objetivos:** Hardening de servicios y configuración segura

### Paso a Paso - Gestión de Servicios

#### Paso 1: Auditar Servicios Activos

1. **Navegar a IP → Services**

2. **Revisar servicios habilitados por defecto:**
   ```
   telnet    puerto 23    ← INSEGURO
   ftp       puerto 21    ← INSEGURO  
   www       puerto 80    ← OK para LAN
   ssh       puerto 22    ← OK pero cambiar puerto
   www-ssl   puerto 443   ← PREFERIDO sobre www
   api       puerto 8728  ← Para scripts
   winbox    puerto 8291  ← Principal para admin
   api-ssl   puerto 8729  ← API seguro
   ```

#### Paso 2: Deshabilitar Servicios Innecesarios

1. **Deshabilitar servicios inseguros:**
   - Seleccionar "telnet" → Clic "D" (Disable)
   - Seleccionar "ftp" → Clic "D" (Disable)
   - Servicios aparecen con ícono de "disabled"

2. **Deshabilitar servicios no necesarios:**
   - Seleccionar "api" → Clic "D" (solo si no se usarán scripts)
   - Mantener "api-ssl" habilitado

#### Paso 3: Cambiar Puertos Por Defecto

1. **Cambiar puerto SSH:**
   - Doble clic en servicio "ssh"
   - **Port:** `2222`
   - Clic "OK"

2. **Cambiar puerto Winbox:**
   - Doble clic en servicio "winbox"  
   - **Port:** `8192`
   - Clic "OK"

#### Paso 4: Configurar Restricciones de Acceso

1. **Restringir acceso SSH a LAN:**
   - Doble clic en "ssh"
   - **Available From:** `192.168.88.0/24`
   - Clic "OK"

2. **Restringir acceso Winbox:**
   - Doble clic en "winbox"
   - **Available From:** `192.168.88.0/24`
   - Clic "OK"

3. **Configurar HTTPS con certificado:**
   - **System → Certificates → "+"**
   - **Name:** `https-cert`
   - **Common Name:** `router.local`
   - **Key Size:** `2048`
   - Clic "OK"
   
   - Seleccionar certificado creado → "S" (Sign)
   - **IP → Services** → Doble clic "www-ssl"
   - **Certificate:** `https-cert`
   - Clic "OK"

#### Paso 5: Configurar Logging Básico

1. **Navegar a System → Logging**

2. **Configurar logs importantes:**
   - **Rules tab → "+"**
   - **Topics:** `error,warning,critical`
   - **Action:** `memory`
   - Clic "OK"

3. **Agregar log de acceso:**
   - **"+"**
   - **Topics:** `system,info`  
   - **Action:** `memory`
   - Clic "OK"

#### Paso 6: Verificar Configuración

1. **Probar acceso con nuevo puerto Winbox:**
   - Desconectar Winbox actual
   - Conectar especificando puerto: `192.168.88.1:8192`

2. **Probar SSH con nuevo puerto:**
   ```bash
   ssh -p 2222 netadmin@192.168.88.1
   ```

3. **Verificar HTTPS:**
   - Abrir navegador: `https://192.168.88.1`
   - Aceptar certificado autofirmado

---

## LABORATORIO 5: Backup y Documentación
**Duración:** 20 minutos  
**Objetivos:** Crear backup de configuración y documentar cambios

### Paso a Paso - Backup y Export

#### Paso 1: Crear Backup Completo

1. **Files → Backup:**
   - **Name:** `Grupo01-Inicial-Lab`
   - **Password:** `BackupLab123!`
   - Clic "Backup"
   - Archivo .backup aparece en lista

2. **Crear Export (texto plano):**
   - **New Terminal** (botón terminal en toolbar)
   ```bash
   /export file=Grupo01-Config-Export
   ```
   - Archivo .rsc aparece en lista

#### Paso 2: Descargar Backups

1. **Seleccionar archivo .backup:**
   - Clic derecho → "Download"
   - Guardar en laptop del grupo

2. **Seleccionar archivo .rsc:**
   - Clic derecho → "Download" 
   - Abrir con editor de texto para revisar

#### Paso 3: Documentar Cambios Realizados

**Crear documento de configuración aplicada:**

```markdown
# Configuración Aplicada - Grupo XX

## Información del Dispositivo
- Modelo: RB951-2HnD
- RouterOS: v7.xx
- Identity: GrupoXX-RB951-Lab

## Usuarios Configurados
- netadmin: Administrador principal (grupo full)
- monitor: Usuario solo lectura (grupo read)  
- operador01: Usuario operativo (grupo operator custom)
- admin: DESHABILITADO por seguridad

## Servicios Configurados
- SSH: Puerto 2222, acceso desde 192.168.88.0/24
- Winbox: Puerto 8192, acceso desde 192.168.88.0/24
- HTTPS: Puerto 443 con certificado autofirmado
- DESHABILITADOS: telnet, ftp

## Configuración Sistema
- Timezone: America/Argentina/Buenos_Aires
- NTP: Habilitado (pool.ntp.org)
- Logging: Configurado para errores y sistema

## Backups Creados
- Grupo01-Inicial-Lab.backup (con password)
- Grupo01-Config-Export.rsc (texto plano)
```

---

## Verificación y Testing

### Lista de Verificación Final

**□ Reset y Acceso:**
- [ ] Reset completo ejecutado correctamente
- [ ] Conexión Winbox por MAC address funcional
- [ ] Estado post-reset verificado

**□ Usuarios:**
- [ ] Usuario netadmin creado (grupo full)
- [ ] Usuario monitor creado (grupo read)
- [ ] Usuario operador01 creado (grupo operator)
- [ ] Usuario admin deshabilitado
- [ ] Pruebas de acceso con diferentes usuarios exitosas

**□ Servicios:**
- [ ] Telnet y FTP deshabilitados
- [ ] SSH puerto cambiado a 2222
- [ ] Winbox puerto cambiado a 8192
- [ ] Restricciones de IP aplicadas
- [ ] HTTPS con certificado configurado

**□ Sistema:**
- [ ] Identity configurado
- [ ] Timezone configurado
- [ ] NTP habilitado
- [ ] Logging configurado

**□ Backups:**
- [ ] Backup completo creado
- [ ] Export de configuración creado
- [ ] Archivos descargados
- [ ] Documentación completada

### Comandos de Verificación

**Para ejecutar en Terminal de Winbox:**

```bash
# Verificar usuarios
/user print

# Verificar servicios
/ip service print

# Verificar sistema
/system identity print
/system clock print  
/system ntp client print

# Verificar archivos
/file print

# Ver configuración completa
/export compact
```

### Resolución de Problemas Comunes

**No puedo conectar con Winbox:**
1. Verificar cable Ethernet
2. Verificar LED de enlace en puerto
3. Usar MAC address, no IP
4. Probar reset si persiste problema

**Olvidé la contraseña del nuevo usuario:**
1. Reset completo del dispositivo
2. Reconfigurar desde usuario admin sin password
3. Importar backup previo si existe

**Servicios no responden en nuevos puertos:**
1. Verificar configuración en IP → Services
2. Verificar firewall (debe estar vacío en configuración básica)
3. Probar desde IP permitida según restricciones

---

## Preparación para Homework

### Información para Trabajos Individuales

**Archivos a entregar:**
- Backup de configuración (.backup)
- Export de configuración (.rsc)
- Documento de configuración aplicada
- Screenshots de verificación

**CHR Installation Guide:**
- Descargar CHR desde mikrotik.com
- Instalar en VirtualBox/VMware
- Aplicar configuración similar al laboratorio
- Documentar diferencias encontradas

**Próxima sesión:**
- Traer documentación de homework completado
- Estar preparados para comparar configuraciones
- Revisar laboratorio de interfaces y switching