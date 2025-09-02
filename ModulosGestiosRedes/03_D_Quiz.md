Perfecto, aquí están los quizzes de opción múltiple para cada módulo:

## 📝 QUIZ MÓDULO 01: FUNDAMENTOS ROUTEROS Y CONFIGURACIÓN INICIAL

### Pregunta 1
**¿Cuál es el nivel de licencia mínimo en RouterOS que permite usar HotSpot?**
- A) Level 1
- B) Level 3 ✅
- C) Level 4
- D) Level 6

**Justificación:** Level 3 es el primer nivel que incluye funcionalidad HotSpot según la tabla de licencias.

### Pregunta 2
**¿Cuál es el puerto por defecto de Winbox?**
- A) 8080
- B) 8192
- C) 8291 ✅
- D) 8728

**Justificación:** El puerto 8291 es el puerto estándar de Winbox para conexiones.

### Pregunta 3
**¿Qué comando resetea completamente un dispositivo MikroTik?**
- A) /system reset
- B) /system reset-configuration ✅
- C) /reset all
- D) /system factory-reset

**Justificación:** /system reset-configuration es el comando correcto para resetear la configuración.

### Pregunta 4
**¿Cuál es la IP por defecto de un router MikroTik después del reset?**
- A) 10.0.0.1/24
- B) 192.168.1.1/24
- C) 192.168.88.1/24 ✅
- D) 172.16.0.1/24

**Justificación:** 192.168.88.1/24 es la dirección IP por defecto en ether1.

### Pregunta 5
**¿Qué interface de administración NO requiere configuración IP previa?**
- A) WebFig
- B) SSH
- C) Winbox (por MAC) ✅
- D) API REST

**Justificación:** Winbox puede conectarse directamente por MAC address sin necesidad de IP configurada.

### Pregunta 6
**¿Cuál es el usuario por defecto en RouterOS?**
- A) root
- B) admin ✅
- C) administrator
- D) mikrotik

**Justificación:** El usuario por defecto es "admin" sin contraseña inicial.

### Pregunta 7
**¿Qué servicio se recomienda deshabilitar primero por seguridad?**
- A) SSH
- B) Winbox
- C) Telnet ✅
- D) HTTPS

**Justificación:** Telnet transmite datos sin cifrar y debe deshabilitarse por seguridad.

### Pregunta 8
**¿Qué tecla se usa para autocompletar comandos en CLI?**
- A) Enter
- B) Space
- C) TAB ✅
- D) Ctrl+C

**Justificación:** La tecla TAB autocompleta comandos en la CLI de RouterOS.

### Pregunta 9
**¿Qué comando muestra los recursos del sistema?**
- A) /system resource print ✅
- B) /system info print
- C) /system status
- D) /resource print

**Justificación:** /system resource print muestra CPU, memoria y otros recursos del sistema.

### Pregunta 10
**¿En qué año se fundó MikroTik?**
- A) 1994
- B) 1996 ✅
- C) 1998
- D) 2000

**Justificación:** MikroTik fue fundada en 1996 en Letonia.

---

## 📝 QUIZ MÓDULO 02: CONFIGURACIÓN DE INTERFACES Y SWITCHING

### Pregunta 1
**¿Cuál es el MTU por defecto para interfaces Ethernet?**
- A) 1000 bytes
- B) 1492 bytes
- C) 1500 bytes ✅
- D) 9000 bytes

**Justificación:** 1500 bytes es el MTU estándar para Ethernet.

### Pregunta 2
**¿Qué protocolo usa RouterOS por defecto para prevenir loops en bridges?**
- A) STP
- B) RSTP ✅
- C) MSTP
- D) No usa ninguno

**Justificación:** RSTP (Rapid Spanning Tree Protocol) es el protocolo por defecto en bridges.

### Pregunta 3
**¿Cuál es el rango válido de VLAN IDs según IEEE 802.1Q?**
- A) 0-255
- B) 1-1024
- C) 1-4094 ✅
- D) 0-4096

**Justificación:** Los VLAN IDs utilizables van del 1 al 4094 (0 y 4095 son reservados).

### Pregunta 4
**¿Qué tipo de puerto VLAN transporta múltiples VLANs con tags?**
- A) Access
- B) Trunk ✅
- C) Native
- D) Hybrid

**Justificación:** Los puertos Trunk transportan múltiples VLANs usando tags 802.1Q.

### Pregunta 5
**¿Cuánto tiempo tarda RSTP en converger típicamente?**
- A) < 2 segundos ✅
- B) 15 segundos
- C) 30 segundos
- D) 50 segundos

**Justificación:** RSTP converge en menos de 2 segundos, mucho más rápido que STP tradicional.

### Pregunta 6
**¿Qué comando muestra la tabla MAC de un bridge?**
- A) /interface bridge mac print
- B) /interface bridge host print ✅
- C) /bridge mac-address print
- D) /interface bridge table print

**Justificación:** /interface bridge host print muestra la tabla de direcciones MAC aprendidas.

### Pregunta 7
**¿Qué valor tiene el PVID por defecto en un puerto bridge?**
- A) 0
- B) 1 ✅
- C) 10
- D) 100

**Justificación:** PVID (Port VLAN ID) por defecto es 1, correspondiente a la VLAN nativa.

### Pregunta 8
**¿Qué característica permite switching en hardware a wire-speed?**
- A) Software bridge
- B) Switch chip ✅
- C) CPU switching
- D) Virtual switching

**Justificación:** El switch chip permite forwarding en hardware sin usar CPU.

### Pregunta 9
**¿Cuál es el estado final de un puerto RSTP funcionando correctamente?**
- A) Learning
- B) Listening
- C) Forwarding ✅
- D) Blocking

**Justificación:** Forwarding es el estado operacional normal de un puerto RSTP activo.

### Pregunta 10
**¿Qué parámetro determina si un frame VLAN sale tagged o untagged?**
- A) Frame-types únicamente
- B) La configuración en bridge VLAN table ✅
- C) Solo el PVID
- D) El MTU del puerto

**Justificación:** La tabla VLAN del bridge especifica qué puertos son tagged o untagged para cada VLAN.

---

## 📝 QUIZ MÓDULO 03: ROUTING ESTÁTICO Y DINÁMICO

### Pregunta 1
**¿Cuál es la Administrative Distance por defecto de una ruta estática en RouterOS?**
- A) 0
- B) 1 ✅
- C) 10
- D) 110

**Justificación:** Las rutas estáticas tienen AD=1 por defecto en RouterOS.

### Pregunta 2
**¿Qué protocolo de routing utiliza el algoritmo Dijkstra?**
- A) RIP
- B) EIGRP
- C) OSPF ✅
- D) BGP

**Justificación:** OSPF es un protocolo Link State que usa el algoritmo Dijkstra SPF.

### Pregunta 3
**¿Cuál es el máximo hop count en RIP?**
- A) 10
- B) 15 ✅
- C) 16
- D) 255

**Justificación:** RIP permite máximo 15 hops; 16 se considera infinito (inalcanzable).

### Pregunta 4
**¿Qué área OSPF debe existir obligatoriamente?**
- A) Area 1
- B) Area 0 (Backbone) ✅
- C) Area 255
- D) Area 10

**Justificación:** El Area 0 (Backbone) es obligatoria y todas las demás áreas deben conectarse a ella.

### Pregunta 5
**¿Para qué se usa una Floating Route?**
- A) Balanceo de carga
- B) Ruta de respaldo ✅
- C) Routing dinámico
- D) Filtrado de rutas

**Justificación:** Las Floating Routes son rutas backup con mayor AD que se activan si falla la principal.

### Pregunta 6
**¿Qué tipo de protocolo es BGP?**
- A) Link State
- B) Distance Vector
- C) Path Vector ✅
- D) Hybrid

**Justificación:** BGP es un protocolo Path Vector que usa AS-Path para prevenir loops.

### Pregunta 7
**¿Cuál es el comando para agregar una ruta por defecto?**
- A) /ip route add default-gateway=X.X.X.X
- B) /ip route add dst-address=0.0.0.0/0 gateway=X.X.X.X ✅
- C) /ip route default X.X.X.X
- D) /routing add default X.X.X.X

**Justificación:** La sintaxis correcta usa dst-address=0.0.0.0/0 para especificar ruta por defecto.

### Pregunta 8
**¿Qué métrica usa OSPF para calcular el mejor camino?**
- A) Hop count
- B) Bandwidth
- C) Cost ✅
- D) Delay

**Justificación:** OSPF usa Cost basado en bandwidth (Cost = Reference BW / Interface BW).

### Pregunta 9
**¿Cuál es el AD de OSPF en RouterOS?**
- A) 90
- B) 100
- C) 110 ✅
- D) 120

**Justificación:** OSPF tiene Administrative Distance de 110 en RouterOS.

### Pregunta 10
**¿Qué característica define ECMP (Equal Cost Multi-Path)?**
- A) Rutas con diferente métrica
- B) Rutas con igual costo para balanceo ✅
- C) Solo rutas estáticas
- D) Rutas de diferentes protocolos

**Justificación:** ECMP permite usar múltiples rutas con el mismo costo para distribuir tráfico.

---

Cada quiz está diseñado para evaluación rápida con respuestas claras y justificaciones breves que refuerzan el aprendizaje del concepto clave.