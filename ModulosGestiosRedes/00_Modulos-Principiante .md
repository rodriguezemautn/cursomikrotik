# Curso Completo MikroTik: Gestión Operativa y Seguridad de Redes

## **Información General del Curso**

**Duración:** 16 semanas (64 horas académicas)  
**Modalidad:** Híbrida (Presencial + Homework)  
**Metodología:** Desarrollo de competencias  
**Prerequisitos:** Conocimientos básicos de redes TCP/IP

---

## **MÓDULO 1: Fundamentos RouterOS y Configuración Inicial**
*Duración: 2 semanas (8 horas)*

### **Competencias a Desarrollar:**
- Configura dispositivos MikroTik desde cero aplicando mejores prácticas de seguridad inicial
- Navega eficientemente en RouterOS utilizando múltiples interfaces

### **Contenidos:**
1. **Arquitectura RouterOS**
   - Historia y evolución de MikroTik
   - Licencias y versiones
   - Hardware compatible y CHR

2. **Interfaces de Administración**
   - Winbox, WebFig, CLI, SSH
   - Mobile app y API REST

3. **Configuración Inicial Segura**
   - Reset y primer acceso
   - Usuarios y contraseñas seguras
   - Servicios básicos y puertos

### **Actividades Prácticas:**

**Presencial (Aula - RB951-2HnD):**
- Reset completo del dispositivo
- Configuración inicial mediante Winbox
- Creación de usuarios con roles diferenciados
- Configuración de servicios básicos

**Homework Individual:**
- Instalación y configuración CHR en VM
- Documentación de proceso de hardening inicial
- Comparativa interfaces de administración

**Evaluación:**
- Lista de verificación configuración segura (40%)
- Quiz teórico fundamentos (30%)
- Documentación técnica proceso (30%)

---

## **MÓDULO 2: Configuración de Interfaces y Switching**
*Duración: 2 semanas (8 horas)*

### **Competencias a Desarrollar:**
- Configura y gestiona interfaces físicas y virtuales optimizando el rendimiento de red
- Implementa configuraciones de switching con VLANs y segmentación de red

### **Contenidos:**
1. **Gestión de Interfaces**
   - Tipos de interfaces (Ethernet, Wireless, Bridge, VLAN)
   - Configuración de parámetros físicos
   - Monitoreo y estadísticas

2. **Bridge y Switching**
   - Conceptos de switching en MikroTik
   - Hardware vs Software switching
   - STP, RSTP configuración

3. **VLANs y Segmentación**
   - 802.1Q tagging
   - VLAN interfaces
   - Inter-VLAN routing básico

### **Actividades Prácticas:**

**Presencial (Aula):**
- Configuración bridge entre puertos
- Implementación VLANs básicas
- Pruebas de conectividad entre VLANs

**Homework Individual:**
- Escenario GNS3: Red empresarial con 3 VLANs
- Configuración CHR como switch Layer 3
- Análisis de tráfico con packet sniffer

**Evaluación:**
- Laboratorio práctico switching (50%)
- Proyecto GNS3 documentado (35%)
- Evaluación teórica conceptos (15%)

---

## **MÓDULO 3: Routing Estático y Dinámico**
*Duración: 2 semanas (8 horas)*

### **Competencias a Desarrollar:**
- Diseña e implementa topologías de routing estático y dinámico según requerimientos de red
- Optimiza el rendimiento de routing aplicando técnicas avanzadas de configuración

### **Contenidos:**
1. **Routing Estático**
   - Tabla de rutas y métricas
   - Rutas por defecto y específicas
   - Balanceamiento de carga estático

2. **Protocolos de Routing Dinámico**
   - OSPF configuración y áreas
   - BGP básico y filtrado
   - RIP (casos específicos)

3. **Optimización de Routing**
   - Route filtering y route maps
   - Redistribución entre protocolos
   - Policy-based routing

### **Actividades Prácticas:**

**Presencial (Aula):**
- Configuración OSPF multi-área
- Implementación BGP entre grupos
- Troubleshooting routing avanzado

**Homework Individual:**
- Escenario GNS3: ISP con múltiples clientes
- Configuración policy routing
- Análisis convergencia protocolos

**Evaluación:**
- Examen práctico routing (45%)
- Proyecto complejo GNS3 (40%)
- Presentación optimización (15%)

---

## **MÓDULO 4: Wireless y Gestión de RF**
*Duración: 2 semanas (8 horas)*

### **Competencias a Desarrollar:**
- Configura redes wireless seguras optimizadas para diferentes escenarios de uso
- Implementa soluciones wireless empresariales con gestión centralizada

### **Contenidos:**
1. **Fundamentos Wireless MikroTik**
   - Modos de operación wireless
   - Configuración básica AP/Station
   - Wireless protocols y estándares

2. **Seguridad Wireless**
   - WPA2/WPA3 Enterprise
   - RADIUS integration
   - MAC filtering y access lists

3. **Wireless Avanzado**
   - CAPsMAN configuración
   - Mesh networking
   - Point-to-Point links

### **Actividades Prácticas:**

**Presencial (Aula):**
- Configuración AP con múltiples SSIDs
- Implementación WPA2 Enterprise
- Site survey básico con wireless

**Homework Individual:**
- Diseño red wireless empresarial CHR
- Configuración CAPsMAN simulado
- Plan de frecuencias y cobertura

**Evaluación:**
- Laboratorio wireless configuración (40%)
- Diseño red empresarial (35%)
- Quiz seguridad wireless (25%)

---

## **MÓDULO 5: Firewall y Seguridad Avanzada**
*Duración: 3 semanas (12 horas)*

### **Competencias a Desarrollar:**
- Diseña e implementa políticas de seguridad de red mediante firewall avanzado
- Aplica técnicas de hardening y protección contra amenazas de red

### **Contenidos:**
1. **Firewall Básico**
   - Chains y targets
   - Connection tracking
   - NAT y masquerading

2. **Seguridad Avanzada**
   - Address lists dinámicas
   - Port knocking
   - DDoS protection

3. **Hardening RouterOS**
   - Servicios innecesarios
   - User management avanzado
   - Logging y auditoría

### **Actividades Prácticas:**

**Presencial (Aula):**
- Configuración firewall corporativo
- Implementación IPS básico
- Simulación ataques y defensa

**Homework Individual:**
- Escenario forense con logs
- Configuración completa DMZ
- Análisis vulnerabilidades RouterOS

**Evaluación:**
- Examen práctico firewall (35%)
- Proyecto seguridad integral (40%)
- Análisis forense logs (25%)

---

## **MÓDULO 6: VPN y Conectividad Remota**
*Duración: 2 semanas (8 horas)*

### **Competencias a Desarrollar:**
- Implementa soluciones VPN seguras para conectividad site-to-site y remote access
- Configura túneles cifrados optimizados para diferentes escenarios empresariales

### **Contenidos:**
1. **IPSec VPN**
   - Site-to-site tunnels
   - Road warrior configuration
   - IKEv1 vs IKEv2

2. **Other VPN Technologies**
   - OpenVPN configuration
   - L2TP/IPSec
   - SSTP implementation

3. **VPN Troubleshooting**
   - Log analysis
   - Connection debugging
   - Performance optimization

### **Actividades Prácticas:**

**Presencial (Aula):**
- Configuración IPSec site-to-site
- Implementación OpenVPN server
- Troubleshooting conexiones VPN

**Homework Individual:**
- Escenario multi-site VPN GNS3
- Configuración road warrior CHR
- Análisis performance VPN

**Evaluación:**
- Laboratorio VPN (45%)
- Proyecto multi-site (40%)
- Troubleshooting casos (15%)

---

## **MÓDULO 7: QoS y Gestión de Tráfico**
*Duración: 2 semanas (8 horas)*

### **Competencias a Desarrollar:**
- Implementa políticas de QoS para optimizar el uso del ancho de banda
- Configura sistemas de control de tráfico basados en necesidades empresariales

### **Contenidos:**
1. **Simple Queue Management**
   - Bandwidth limitation
   - Priority configuration
   - Burst management

2. **Queue Tree Advanced**
   - Hierarchical queuing
   - PCQ (Per Connection Queue)
   - Global vs interface queues

3. **Traffic Analysis**
   - Traffic monitoring tools
   - Graphing and reporting
   - Capacity planning

### **Actividades Prácticas:**

**Presencial (Aula):**
- Configuración QoS empresarial
- Implementación traffic shaping
- Análisis patrones de tráfico

**Homework Individual:**
- Diseño QoS para ISP pequeño
- Configuración PCQ avanzado
- Monitoreo long-term tráfico

**Evaluación:**
- Configuración QoS práctica (40%)
- Análisis y diseño (35%)
- Monitoreo y reporting (25%)

---

## **MÓDULO 8: Monitoreo, Troubleshooting y Automatización**
*Duración: 3 semanas (12 horas)*

### **Competencias a Desarrollar:**
- Implementa sistemas de monitoreo proactivo y troubleshooting sistemático
- Desarrolla scripts de automatización para gestión operativa eficiente

### **Contenidos:**
1. **System Monitoring**
   - SNMP configuration
   - Syslog management
   - Email notifications

2. **Troubleshooting Methodology**
   - Systematic approach
   - Tools and utilities
   - Performance analysis

3. **RouterOS Scripting**
   - Basic scripting syntax
   - Scheduled tasks
   - API integration

### **Actividades Prácticas:**

**Presencial (Aula):**
- Configuración SNMP monitoring
- Desarrollo scripts básicos
- Troubleshooting casos reales

**Homework Individual:**
- Script automatización backups
- Integración sistema monitoreo
- Documentación procedimientos

**Evaluación:**
- Script funcional (30%)
- Caso troubleshooting (35%)
- Sistema monitoreo (35%)

---

## **PROYECTO INTEGRADOR FINAL**
*Duración: 2 semanas*

### **Competencia Principal:**
- Diseña, implementa y documenta una solución de red completa integrando todos los conocimientos adquiridos

### **Escenario:**
Implementar la infraestructura de red para una empresa mediana con:
- 3 sucursales conectadas
- 150 usuarios distribuidos
- Servicios críticos 24/7
- Requerimientos de seguridad altos
- Conectividad wireless y cableada

### **Entregables:**
1. Diseño de red documentado
2. Configuraciones completas
3. Plan de seguridad
4. Procedimientos operativos
5. Presentación ejecutiva

### **Evaluación:**
- Funcionamiento técnico (40%)
- Documentación (30%)
- Presentación (20%)
- Innovación y optimización (10%)

---

## **METODOLOGÍA DE EVALUACIÓN GENERAL**

### **Distribución de Calificaciones:**
- **Evaluaciones modulares:** 60%
- **Proyecto integrador:** 25%
- **Participación y homework:** 15%

### **Criterios de Competencias:**
- **Excelente (90-100):** Domina completamente y puede enseñar
- **Competente (75-89):** Aplica correctamente en escenarios complejos
- **En desarrollo (60-74):** Maneja conceptos básicos con apoyo
- **Insuficiente (<60):** Requiere refuerzo fundamental

### **Recursos Requeridos:**
- 5 RouterBoard RB951-2HnD
- Infraestructura virtualization para CHR
- Licencias GNS3 con imágenes RouterOS
- Acceso documentación oficial MikroTik
- Entorno laboratorio con conectividad Internet

