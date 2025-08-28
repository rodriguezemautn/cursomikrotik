
# 📘 **CHR: Instalación en VirtualBox - RouterOS - Documentación de MikroTik**

Creado por Serhii T., actualizado por última vez el 31 de julio de 2024 • 3 minutos de lectura

---

## **Instalación de CHR en VirtualBox**

Guía para instalar el **Router en la Nube (CHR)** en **Oracle VM VirtualBox**.

🎥 **Instrucción en video disponible** (enlace no incluido en el texto original)

---

### **1. Descargar VirtualBox**
Instala la versión más reciente de VirtualBox desde el sitio web oficial:  
👉 [https://www.virtualbox.org](https://www.virtualbox.org)

---

### **2. Descargar la imagen del CHR**
Descarga y descomprime la versión más reciente (Stable o Testing) de la imagen **VDI** del **CHR (Cloud Hosted Router)** desde el sitio web de MikroTik:  
👉 [https://mikrotik.com/download](https://mikrotik.com/download)  
(Selecciona la opción **"Cloud Hosted Router"** → **"VirtualBox disk image (.vdi)"**)

---

## **Paso 1: Crear una Máquina Virtual Nueva**

1. Abre la aplicación **VirtualBox**.
2. Haz clic en **"Nueva"** para crear una nueva máquina virtual.

### **Nombre y Sistema Operativo**
- **Nombre:** Ingresa un nombre para tu máquina virtual (por ejemplo: `MikroTik_CHR`).
- **Tipo:** Selecciona **Linux**.
- **Versión:** Selecciona **Otro Linux (64 bits)**.

📌 Haz clic en **Siguiente**.

---

## **Paso 2: Configurar el Tamaño de Memoria**

### **Tamaño de Memoria**
- Asigna memoria a la máquina virtual.
- **Se recomienda asignar al menos 256 MB de RAM** (especialmente desde RouterOS 7.15.1 en adelante).

### **Procesadores**
- Selecciona la cantidad deseada de CPUs (núcleos).  
  ✅ Recomendado: 1 o 2 núcleos para entornos de prueba.

📌 Haz clic en **Siguiente**.

---

## **Paso 3: Crear un Disco Duro Virtual**

1. En la sección **Disco Duro Virtual**:
   - Selecciona **"Usar un archivo de disco duro existente"**.
   - Haz clic en el icono de carpeta para **agregar** la imagen VDI descargada.
   - Navega hasta la ubicación del archivo `.vdi` y selecciónalo.
   - Haz clic en **Elegir**.

📌 Haz clic en **Siguiente**.

Revisa la configuración y haz clic en **Finalizar**.

---

## **Paso 4: Configurar la Máquina Virtual**

1. Selecciona la máquina virtual recién creada.
2. Haz clic en **Configuración**.

### **Sistema**
- Ve a la pestaña **Sistema**.
- En la sección **Orden de arranque**, **desmarca** las opciones:
  - **Disquete (Floppy)**
  - **Óptico (Optical)**

### **Procesador**
- Ve a la pestaña **Procesador**.
- Asegúrate de que la cantidad de CPUs sea la deseada.

### **Red**
- Ve a la pestaña **Red**.

#### **Adaptador 1**
- **Habilita el adaptador de red**.
- **Adjuntar a:** Selecciona una de las siguientes opciones según tu configuración de red:
  - **Adaptador puente (Bridged Adapter):** Para que el CHR obtenga una IP directa de tu red local (recomendado para acceso directo).
  - **NAT:** Para que el CHR use la IP del host (útil si solo necesitas acceso saliente).

> ✅ Recomendación: Usa **Adaptador puente** si deseas acceder al CHR desde otros dispositivos de la red.

---

## **Paso 5: Iniciar la Máquina Virtual**

1. Haz clic en **Iniciar** para arrancar la máquina virtual.
2. La VM se encenderá y cargará RouterOS.

### **Inicio de sesión**
- Después de que la VM se reinicie, verás el mensaje de inicio de sesión del CHR.
- Las credenciales predeterminadas son:
  - **Usuario:** `admin`
  - **Contraseña:** *(vacía / sin contraseña)*

---

### **Configuración inicial**
- Configura el CHR según tus necesidades de red utilizando:
  - La **línea de comandos (CLI)** de MikroTik
  - O **WebFig** (acceso vía navegador web)
  - También puedes usar **WinBox** (descárgalo desde mikrotik.com)

---

✅ **¡Felicitaciones!** Has instalado correctamente **MikroTik CHR en VirtualBox**.  
Ahora puedes proceder a configurar tus ajustes de red y aprovechar todas las funciones de **RouterOS**.

---

> 🔗 **Documentación oficial:** [https://help.mikrotik.com/docs/display/ROS/CHR+installing+on+VirtualBox](https://help.mikrotik.com/docs/display/ROS/CHR+installing+on+VirtualBox)

---
