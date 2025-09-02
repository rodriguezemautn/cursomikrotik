

# 📘 **Router en la Nube (CHR) - RouterOS - Documentación de MikroTik**

Creado por Normunds R., actualizado por última vez por Serhii T. el 10 de junio de 2025 • 17 minutos de lectura

---

## **Router en la Nube (CHR)**

El **Router en la Nube (CHR, por sus siglas en inglés)** es una versión de RouterOS diseñada para ejecutarse como una máquina virtual. Soporta la arquitectura x86 de 64 bits y puede utilizarse en la mayoría de los hipervisores populares, como VMWare, Hyper-V, VirtualBox, KVM y otros. El CHR incluye todas las funciones de RouterOS habilitadas por defecto, pero tiene un modelo de licencias diferente al de otras versiones de RouterOS.

---

## **Requisitos del Sistema**

- **Versión del paquete:** RouterOS v6.34 o superior  
- **CPU del host:** 64 bits con soporte de virtualización  
- **RAM:** 256 MB o más  
- **Disco:** 128 MB o más  

**Limitaciones por versión:**
- **RouterOS v6:** El tamaño máximo del disco virtual soportado es de 16 GB  
- **RouterOS v7:** La cantidad máxima de RAM y espacio en disco está limitada por el kernel Linux 5.6.3 y depende del hardware específico.

**Cálculo aproximado de RAM mínima necesaria:**
- **RouterOS v6:**  
  `RAM = 128 + [8 × (CANTIDAD_CPU) × (CANTIDAD_INTERFACES - 1)]`
- **RouterOS v7:**  
  `RAM = 256 + [8 × (CANTIDAD_CPU) × (CANTIDAD_INTERFACES - 1)]`

> **Nota:** Se recomienda asignar al menos **1024 MiB de RAM** para instancias CHR.

---

## **Plataformas en las que se ha probado el CHR**

- VirtualBox 6 en Linux y OS X  
- VMWare Fusion 7 y 8 en OS X  
- VMWare ESXi 6.5 y superior  
- Qemu 2.4.0.1 en Linux y OS X  
- Hyper-V en Windows Server 2008r2, 2012 y Windows 10  
  > *(Solo se soporta actualmente la máquina virtual Hyper-V de Generación 1)*  
- Xen Server 7.1  

> ⚠️ **Advertencia:** No se soportan hipervisores que proporcionen paravirtualización.

---

## **Interfaces de red y disco soportadas en varios hipervisores**

| Hipervisor | Red | Disco |
|----------|-----|-------|
| **ESXi** | vmxnet3, E1000 | IDE, SCSI paravirtualizado de VMware, LSI Logic SAS, LSI Logic Parallel |
| **Hyper-V** | Adaptador de red, Adaptador de red heredado | IDE, SCSI |
| **Qemu/KVM** | Virtio, E1000, vmxnet3 (opcional) | IDE, SATA, Virtio |
| **VirtualBox** | E1000, rtl8193 | IDE, SATA, SCSI, SAS |

> ⚠️ **Nota:** Los controladores SCSI en Hyper-V y ESXi solo pueden usarse para discos secundarios. ¡La imagen del sistema debe usarse con un controlador IDE!

> ⚠️ **Advertencia:** No se recomienda usar la interfaz de red E1000 si existen opciones sintéticas mejores disponibles en un hipervisor específico.

---

## **Cómo instalar un sistema RouterOS virtual con imágenes CHR**

Ofrecemos 4 tipos diferentes de imágenes de disco virtual para elegir. Ten en cuenta que son solo imágenes de disco y no puedes ejecutarlas directamente.

- Imagen de disco RAW (archivo `.img`)  
- Imagen de disco VMWare (archivo `.vmdk`)  
- Imagen de disco Hyper-V (archivo `.vhdx`)  
- Imagen de disco VirtualBox (archivo `.vdi`)  

### **Pasos para instalar CHR**

1. Descarga la imagen de disco virtual para tu hipervisor desde la sección **Cloud Hosted Router**.
2. Crea una máquina virtual invitada.
3. Usa el archivo de imagen descargado como unidad de disco virtual.
4. Inicia la máquina virtual CHR.
5. Inicia sesión en tu nuevo CHR. El usuario predeterminado es **'admin'**, sin contraseña.

> **Nota importante:** Las instancias CHR pueden clonarse y copiarse, pero la copia recordará el periodo de prueba anterior, por lo que no podrás extender el tiempo de prueba haciendo una copia. Sin embargo, puedes licenciar ambos sistemas por separado. Para crear un nuevo sistema de prueba, debes hacer una instalación limpia y reconfigurar RouterOS.

---

## **Guías de instalación de CHR**

- VMWare Fusion/Workstation, ESXi 6.5 y superior  
- VirtualBox  
- Hyper-V  
- Amazon Web Services (AWS)  
- Hetzner Cloud  
- Instalación en Linode  
- Google Compute Engine  
- ProxMox  
- Vultr  

---

## **Licencias del CHR**

El CHR (Router en la Nube) tiene **4 niveles de licencia**:

| Licencia | Límite de velocidad | Precio |
|---------|----------------------|--------|
| **Free** | 1 Mbit | GRATIS |
| **P1 (perpetual-1)** | 1 Gbit | $45 |
| **P10 (perpetual-10)** | 10 Gbit | $95 |
| **P-Unlimited (perpetual-unlimited)** | Ilimitado | $250 |

Se ofrece una **prueba gratuita de 60 días** para todos los niveles de licencia de pago. Para obtener esta prueba, debes tener una cuenta en MikroTik.com, ya que toda la gestión de licencias se realiza allí.

- **Perpetual (Perpetua):** Es una licencia de por vida (compra una vez, úsala para siempre).  
- Es posible transferir una licencia perpetua a otra instancia CHR.  
- Una instancia CHR en ejecución indicará cuándo debe acceder al servidor de cuentas para renovar su licencia.  
- Si no puede renovarla, funcionará como si el periodo de prueba hubiera terminado y no permitirá actualizar RouterOS.

> **Importante:** Después de licenciar un sistema en prueba, debes ejecutar manualmente el comando `/system license renew` en el CHR para activarla. Si no lo haces antes de la fecha límite, terminará la prueba y tendrás que reinstalar CHR, solicitar una nueva prueba y luego licenciarla con la licencia adquirida.

---

### **Licencias de pago**

#### **P1 (perpetual-1)**
- Permite que el CHR funcione indefinidamente.  
- Limitado a **1 Gbps de subida por interfaz**.  
- Todas las demás funciones están disponibles sin restricciones.  
- Puede actualizarse a P10 o P-Unlimited.  
- Al comprar la actualización, la licencia anterior queda disponible para usar más tarde.

#### **P10 (perpetual-10)**
- Permite que el CHR funcione indefinidamente.  
- Limitado a **10 Gbps de subida por interfaz**.  
- Todas las demás funciones están disponibles sin restricciones.  
- Puede actualizarse a P-Unlimited.  
- Al comprar la actualización, la licencia anterior queda disponible para usar más tarde.

#### **P-Unlimited (perpetual-unlimited)**
- Nivel más alto de licencia.  
- Permite que el CHR funcione indefinidamente.  
- **Sin limitaciones impuestas.**

---

### **Licencias gratuitas**

Existen varias formas de usar y probar el CHR sin costo.

#### **Free**
- Permite que el CHR funcione **indefinidamente**.  
- Limitado a **1 Mbps de subida por interfaz**.  
- Todas las funciones están disponibles sin restricciones.  
- Solo debes descargar la imagen del disco desde nuestra página de descargas y crear una máquina virtual.

#### **Prueba de 60 días**
- Además de la versión gratuita limitada, puedes probar las velocidades superiores de las licencias P1, P10 o P-Unlimited durante **60 días**.
- Necesitas una cuenta en MikroTik.com.  
- Desde tu router, solicita el nivel de licencia deseado para prueba. Esto asociará el ID del router a tu cuenta y habilitará la compra.
- Todos los equivalentes de licencias de pago están disponibles para prueba.
- Tras 60 días, el menú de licencias mostrará "Limited upgrades" (actualizaciones limitadas), lo que significa que ya no se podrán actualizar versiones de RouterOS.

> **Recomendación:** Si planeas comprar la licencia, hazlo dentro de los 60 días posteriores al vencimiento de la prueba. Si no compras nada en **2 meses después del vencimiento**, el dispositivo dejará de aparecer en tu cuenta. Deberás hacer una nueva instalación de CHR para poder comprar.

> **Para solicitar una licencia de prueba**, ejecuta el comando:  
> `/system license renew`  
> Se te pedirá el nombre de usuario y contraseña de tu cuenta en mikrotik.com.

> **Licencia vencida:** Significa que el CHR no pudo renovar su licencia antes de la fecha "deadline-at", o que terminó el periodo de prueba de 60 días. Aunque el router sigue funcionando, **no se pueden hacer actualizaciones de software**.  
> Solo se puede licenciar una instancia con licencia vencida usando una **Prepaid Key**.

---

### **Prepaid Key (Clave Prepagada)**

Una **Prepaid Key** es un tipo de clave de licencia que puedes comprar por adelantado para productos MikroTik, como el CHR, o convertirla en una clave de licencia para aplicarla al ID de software de un sistema x86. Te permite comprar una licencia sin asignarla inmediatamente a un dispositivo específico. Una vez que tienes una Prepaid Key, puedes usarla para actualizar un CHR o convertirla más tarde en una licencia asignada al ID del dispositivo.

> **Nota:** En ciertos proveedores en la nube (como Linode), es posible que dos máquinas tengan el mismo ID de sistema. Para evitarlo, después del primer arranque, ejecuta:  
> `/system license generate-new-id`  
> **¡Solo puedes usar este comando mientras el CHR tenga una licencia gratuita!** Si ya tienes una licencia de pago o de prueba, **no lo uses**, porque no podrás actualizar tu clave actual.

> 🔒 **IP/Cloud requiere una licencia perpetua para CHR.**

---

## **Cómo comprar una Prepaid Key para licenciar un CHR**

1. Ve a [mikrotik.com](https://mikrotik.com) e inicia sesión en tu cuenta.
2. Accede a la sección **"Purchase a RouterOS License Key"**.
3. Elige el nivel de licencia deseado.
4. Selecciona el tipo de clave: **"Prepaid key"**.
5. Indica la cantidad de claves prepago que deseas comprar.
6. Selecciona características opcionales (si las necesitas).
7. Haz clic en **"Place key in the cart"**.
8. Haz clic en **"Proceed to checkout"** para completar la compra.

### **Revisa y completa tu compra**
- Revisa los detalles del pedido.
- Paga con tarjeta de crédito (CC) o PayPal.

✅ ¡Felicitaciones! Has comprado con éxito una Prepaid Key.

---

## **Obtención y actualización de la licencia**

Después de la configuración inicial, una instancia CHR recibe una licencia de prueba gratuita. Puedes actualizar esta licencia a un nivel superior desde tu cuenta MikroTik. Toda la gestión de licencias (incluyendo actualizaciones) se realiza en el servidor de cuentas.

---

### **Actualización inicial de Free a P1 o superior**

La primera actualización desde el nivel gratuito a uno superior requiere el **registro de la instancia CHR en el servidor de cuentas**.

#### **Pasos:**
1. Ingresa tu nombre de usuario y contraseña de MikroTik.com.
2. Selecciona el nivel de licencia deseado.

Como resultado, el **ID del sistema CHR** se asociará a tu cuenta y se creará una **prueba de 60 días** para ese ID.

---

### **Actualizar la licencia usando WinBox**

1. Ve a **System → License**.
2. Haz clic en **"Upgrade"**.
3. Ingresa tus credenciales de MikroTik.com.
4. Selecciona el nivel de licencia.
5. Confirma.

> ⚠️ No puedes actualizar a P-Unlimited si ya es el nivel más alto.

---

### **Actualizar la licencia usando la línea de comandos**

```bash
[admin@MikroTik]>/system license print
system-id: 6lR1ZP/utuJ
level: free

[admin@MikroTik]>/system/license/renew
account: mymikrotikcomaccount
password: ***************
level: p1
status: done

[admin@MikroTik]>/system/license/print
system-id: 6lR1ZP/utuJ
level: p1
limited-upgrades: no
next-renewal-at: 2024-08-25 13:18:06
deadline-at: 2024-09-24 13:18:06
```

---

## **Pago**

Para pasar de una licencia de prueba a una de pago:
1. Ve a tu cuenta MikroTik.
2. En la sección **"CHR LICENCES"**, haz clic en **"All CHR keys"**.
3. Se mostrará la lista de tus instancias CHR y sus licencias.
4. Haz clic en **"Upgrade"**.
5. Elige el nivel de licencia deseado (puede ser diferente al de prueba).
6. Haz clic en **"Upgrade"**.

- Si tienes **Prepaid Keys**, puedes usarlas: **"Pay using Prepaid key"**.
- Si no, haz clic en **"Proceed to checkout"**.
- Elige tu método de pago: **tarjeta de crédito o PayPal**.

---

## **Actualización de la licencia**

En el menú **System → License**, el router muestra:
- `next-renewal-at`: momento en que intentará renovar.
- `deadline-at`: fecha límite para renovar.

El router intentará comunicarse con `licence.mikrotik.com` cada hora después de `next-renewal-at`. Si no lo logra antes de `deadline-at`, considerará que la licencia ha expirado y **bloqueará actualizaciones de software**. Sin embargo, seguirá funcionando con el mismo nivel.

Tras una comunicación exitosa, las fechas se actualizan.

---

## **Actualizar el nivel de una licencia perpetua**

Puedes actualizar:
- De **P1 → P10 o P-Unlimited**
- De **P10 → P-Unlimited**

Al comprar la actualización al precio completo, la licencia anterior queda disponible para usar más tarde.

> **Importante:** Para actualizar una licencia perpetua, primero debes **transferir la licencia anterior a otro CHR**. Esto evita perderla durante la actualización.

---

## **Transferencia de licencia**

Las instalaciones CHR están vinculadas directamente a tu cuenta en MikroTik.com. Puedes transferir una licencia perpetua a otra instancia CHR bajo la **misma cuenta**.

> ❌ **No se pueden transferir licencias a otra cuenta.**  
> ❌ **No se puede transferir a una instancia expirada.**  
> ✅ **Las Prepaid Keys sí pueden transferirse entre cuentas.**  
> ❌ Las Prepaid Keys obtenidas como regalo en entrenamientos **no son transferibles.**

### **Pasos para transferir:**
1. Registra la nueva máquina con el mismo comando:  
   `/system license renew`
2. Ambas instancias deben aparecer en **"All CHR keys"**.
3. Haz clic en **"Transfer"**.
4. Selecciona el ID de destino.
5. Confirma.

> ⚠️ Si ves el error: *"This key is not eligible for transfer..."*, significa que no tienes una instancia CHR en modo prueba para transferir.

---

## **Adaptadores de red virtuales**

- **RouterOS v6:** No soporta Fast Path.
- **RouterOS v7:** Soporta Fast Path para adaptadores `vmxnet3` y `virtio-net`.

---

## **Solución de problemas**

### **Ejecución en VMware ESXi**

#### **Cambio de MTU**
ESXi soporta MTU hasta 9000 bytes (jumbo frames). Debes ajustar ESXi para permitirlo. Las interfaces agregadas **antes** del cambio de MTU seguirán con el MTU antiguo. Debes eliminarlas y volver a agregarlas.

**Ejemplo:**
```bash
[admin@chr-vm]> interface ethernet print
Flags: X - disabled, R - running, S - slave
 #    NAME       MTU       MAC-ADDRESS            ARP
 0 R  ether1     9000      00:0C:29:35:37:5C      enabled
 1 R  ether2     1500      00:0C:29:35:37:66      enabled
```

---

### **Usar bridge en Linux**

Si el bridge de Linux tiene **IGMP snooping**, puede bloquear tráfico IPv6 (MLD). Desactívalo:

```bash
echo 0 > /sys/class/net/vmbr0/bridge/multicast_snooping
```

---

### **Paquetes no pasan desde invitados**

**Problema:** Tras configurar interfaces (VLAN, EoIP, bridge, etc.) en el CHR, deja de pasar tráfico.

**Solución:** Revisa la configuración de seguridad del hipervisor:
- Permite spoofing de MAC.
- Permite etiquetas VLAN.
- Ajusta los permisos de puertos o switches virtuales.

---

### **Uso de VLANs en CHR en diferentes hipervisores**

En algunos hipervisores, debes configurar las VLANs **primero en el hipervisor**.

#### **ESXi**
Habilita el **modo promiscuo** en el port group o switch virtual.

🔗 Documentación: [https://kb.vmware.com/kb/1004099](https://kb.vmware.com/kb/1004099)

#### **Hyper-V**
Consulta la documentación oficial:
- [https://technet.microsoft.com/en-us/library/cc816585(v=ws.10).aspx#Anchor_2](https://technet.microsoft.com/en-us/library/cc816585(v=ws.10).aspx#Anchor_2)
- [https://blogs.msdn.microsoft.com/adamfazio/2008/11/14/understanding-hyper-v-vlans/](https://blogs.msdn.microsoft.com/adamfazio/2008/11/14/understanding-hyper-v-vlans/)
- [https://www.aidanfinn.com/?p=10164](https://www.aidanfinn.com/?p=10164)

---

### **Linode**
Al crear múltiples Linodes con el mismo tamaño de disco, pueden tener el **mismo System ID**. Para evitarlo:

```bash
/system license generate-new-id
```

> Ejecuta este comando **tras el primer arranque y antes de solicitar la licencia**.

---

## **Herramientas de invitado (Guest tools)**

### **VMWare**

#### **Sincronización de tiempo**
- Habilitar desde GUI: *"Synchronize guest time with host"*.
- Por defecto, no se sincroniza si el invitado está adelantado más de ~5 segundos.

#### **Operaciones de energía**
- `poweron` y `resume`: scripts ejecutados tras encendido/reanudación.
- `poweroff` y `suspend`: scripts ejecutados antes de apagar/suspender.
- Si el script tarda >30 seg. o falla, la operación falla.
- Si falla, reintentar ignora el error.
- Salida de errores guardada en archivos (ej. `poweroff-script.log`).
- Pueden habilitarse/deshabilitarse desde GUI o consola.

#### **Quiescing / Backup**
- Solo si se solicita.
- `freeze`: antes de congelar el sistema de archivos.
- `freeze-fail`: si falla el freeze.
- `thaw`: tras tomar la instantánea.
- Tiempo máximo: 60 segundos.
- Si falla, se aborta el backup.
- Discos FAT32 no se congelan.
- Salida de errores guardada.

#### **Información del invitado**
- Información de red, disco y SO se reporta cada 30 segundos.
- Estadísticas de memoria desactivadas por defecto.
- Puedes controlar el orden de interfaces con:
  - `guestinfo.exclude-nics`
  - `guestinfo.primary-nics`
  - `guestinfo.low-priority-nics`

---

### **Aprovisionamiento (Provisioning)**

Puedes usar **ProcessManager** de la API Vim para ejecutar scripts. Hay bindings en Python.

- **Estructura principal:** `GuestProgramSpec`
- `programPath`: `'inline'` o `'import'`
  - `'inline'`: argumentos = texto del script
  - `'import'`: argumentos = ruta del archivo
- Tras ejecutar, obtienes un **JobID único**.
- Puedes monitorear con `ListProcessesInGuest`.
- Salida guardada solo si falla (archivo `vix_job_$JobID$.txt`).
- Tiempo máximo: 120 segundos.
- Comando alternativo: `vmrun runScriptInGuest`
- `Invoke-VMScript` (PowerCLI) **no soportado**.
- Transferencia de archivos **no soportada**.

---

### **Ejemplo en Python**

```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-

import sys, time
from pyVim import connect
from pyVmomi import vmodl, vim

def runInline(content, vm, creds, source):
    if isinstance(source, list):
        source = '\n'.join(source)
    ps = vim.vm.guest.ProcessManager.ProgramSpec(
        programPath='console',
        arguments=source
    )
    return content.guestOperationsManager.processManager.StartProgramInGuest(vm, creds, ps)

def runFromFile(content, vm, creds, fileName):
    ps = vim.vm.guest.ProcessManager.ProgramSpec(
        programPath='import',
        arguments=fileName
    )
    return content.guestOperationsManager.processManager.StartProgramInGuest(vm, creds, ps)

# ... (resto del código para conexión, búsqueda de VM, etc.)
```

---

### **KVM**

- Agente QEMU disponible.
- Comandos soportados: `guest-info`, `guest-file-*`, `guest-network-get-interfaces`.
- Ejecutar scripts: `guest-exec`.
  - Si `path` está definido: ejecuta el archivo.
  - Si no, usa `input-data` como script.
  - Si `capture-output`: devuelve salida en Base64.
- Monitorear con `guest-exec-status`.
- Canal adicional disponible: `chr.provision_channel`.

---
