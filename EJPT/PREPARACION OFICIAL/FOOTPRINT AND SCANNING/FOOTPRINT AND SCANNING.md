-----
## 1. ¿Qué son los **Network Protocols**?

Un **protocolo de red** es un conjunto de reglas que permiten que los dispositivos se comuniquen entre sí a través de una red.

Da igual que un equipo use **Windows, Linux, macOS, Android o iOS**:  
si todos siguen los mismos protocolos, **pueden hablar entre ellos** sin problema.

### ¿Para qué sirven?

Permiten:
- Enviar y recibir datos
- Establecer conexiones
- Garantizar que la información llegue correctamente
- Gestionar errores
- Identificar dispositivos
- Cifrar comunicaciones
### Ejemplos de protocolos:

| Protocolo  | Función                   |
| ---------- | ------------------------- |
| HTTP/HTTPS | Transferir páginas web    |
| FTP        | Transferencia de archivos |
| SMTP       | Envío de correos          |
| DNS        | Resolver nombres a IP     |
| TCP        | Comunicación fiable       |
| UDP        | Comunicación rápida       |

---
## 2. ¿Qué son los **paquetes de red**?

Los datos **no viajan en bloque**, sino **divididos en paquetes**. Un **paquete** es una unidad de información que se envía por la red.

### Estructura de un paquete

### 🔹 Header (cabecera)

Contiene información de control:

- IP de origen    
- IP de destino
- Puerto origen/destino
- Protocolo usado (TCP, UDP, etc.)
- Número de secuencia
- Flags (SYN, ACK, FIN...)

### 🔹 Payload (contenido)

Es el **dato real**:

- Texto
- Archivos
- Imágenes
- Vídeo
- Comandos
- Credenciales (si no hay cifrado 😏)

---

## 3. ¿Qué es el **Modelo OSI**?

El **Modelo OSI** (Open Systems Interconnection) es un modelo teórico que explica **cómo viajan los datos por una red** en **7 capas**.

### ¿Para qué sirve?

- Diseñar redes
- Entender protocolos
- Solucionar errores
- Organizar funciones
- Analizar ataques
- Estandarizar comunicaciones

---
## 4. Las **7 capas del Modelo OSI**

| Capa | Nombre       | Función               | Ejemplos      |
| ---- | ------------ | --------------------- | ------------- |
| 1    | Física       | Transmisión eléctrica | Cables, fibra |
| 2    | Enlace       | MAC, tramas           | Ethernet      |
| 3    | Red          | Enrutamiento          | IP, ICMP      |
| 4    | Transporte   | Control de flujo      | TCP, UDP      |
| 5    | Sesión       | Sesiones              | NetBIOS       |
| 6    | Presentación | Formato/cifrado       | SSL/TLS       |
| 7    | Aplicación   | Interacción usuario   | HTTP, FTP     |
- **Capa Física:** Transmite los bits a través del medio físico (cables, señales, fibra).
- **Capa de Enlace:** Gestiona la comunicación local usando direcciones MAC y tramas.
- **Capa de Red:** Se encarga del enrutamiento de paquetes mediante direcciones IP.
- **Capa de Transporte:** Controla la entrega de datos (fiabilidad, orden, velocidad).
- **Capa de Sesión:** Mantiene y gestiona las conexiones entre sistemas.
- **Capa de Presentación:** Formatea, cifra y comprime los datos.
- **Capa de Aplicación:** Permite la interacción directa con servicios de red.

----

## NETWORK LAYER (Capa de Red)

La **Capa de Red** se encarga de **enrutar los paquetes** desde el origen hasta el destino, incluso cuando están en **redes diferentes**.

Sus funciones principales son:

- Asignar direcciones IP
- Determinar rutas
- Encapsular paquetes
- Permitir comunicación entre redes
- Gestionar el tráfico

---
## Protocolos principales

### 🔹 IPv4 (Internet Protocol v4)

- Direcciones de **32 bits**
- Formato: `192.168.1.1`
- Aproximadamente 4.300 millones de IPs
- Usa NAT por escasez de direcciones

### 🔹 IPv6 (Internet Protocol v6)

- Direcciones de **128 bits**
- Formato: `2001:db8::1`
- Espacio de direcciones enorme
- Mejor seguridad y eficiencia
- No necesita NAT

---
## ICMP – Protocolo de diagnóstico

**ICMP** se usa para:

- Diagnosticar errores de red
- Comprobar conectividad
- Enviar mensajes de control
	```ping -c 1 10.10.16.38```

---

## Campos de la cabecera IPv4

La **cabecera IPv4** contiene la información necesaria para que el paquete llegue a su destino.

|Campo|Función|
|---|---|
|Version|Indica IPv4|
|IHL|Longitud de la cabecera|
|Total Length|Tamaño del paquete|
|Identification|Identifica fragmentos|
|Flags|Control de fragmentación|
|Fragment Offset|Posición del fragmento|
|TTL|Tiempo de vida|
|Protocol|TCP, UDP, ICMP|
|Header Checksum|Verificación|
|Source IP|IP origen|
|Destination IP|IP destino|

---
## 🔹 CAPA DE TRANSPORTE – ¿PARA QUÉ SIRVE?

La **capa de transporte** se encarga de garantizar la **comunicación extremo a extremo** entre aplicaciones que se ejecutan en diferentes dispositivos dentro de una red. Su función principal es **controlar cómo se envían los datos**, asegurando que lleguen completos, en el orden correcto y sin errores (si el protocolo lo permite).  
Además, gestiona la **segmentación de datos**, el **control de flujo**, la **detección de errores** y la **identificación de aplicaciones** mediante puertos.

---
## 🔹 TCP: PROTOCOLO ORIENTADO A CONEXIÓN

**TCP (Transmission Control Protocol)** es un protocolo **orientado a la conexión**, fiable y ordenado. Esto significa que antes de enviar datos, establece una conexión entre emisor y receptor, y se asegura de que los paquetes:
- Lleguen correctamente
- No se pierdan
- Mantengan el orden
- Se reenvíen si hay errores

TCP es ideal para servicios donde la **integridad de los datos es crítica**, como páginas web, correos electrónicos o transferencias de archivos.

---
## 🔹 ¿CÓMO GARANTIZA TCP QUE LOS DATOS LLEGUEN BIEN?

TCP usa varios mecanismos:

1. **Números de secuencia**: cada segmento tiene un identificador para mantener el orden.  
2. **ACK (Acknowledgment)**: el receptor confirma qué datos ha recibido.
3. **Retransmisión**: si un paquete no llega, TCP lo vuelve a enviar.
4. **Control de flujo**: evita saturar al receptor.

Gracias a esto, TCP ofrece una transmisión **fiable y ordenada**.

---
## 🔹 ESTABLECIMIENTO DE CONEXIÓN: THREE-WAY HANDSHAKE

Antes de enviar datos, TCP establece una conexión mediante el **Three-Way Handshake**:

1. **SYN** → El cliente solicita conexión.
2. **SYN-ACK** → El servidor acepta la solicitud.
3. **ACK** → El cliente confirma.

Tras este intercambio, la conexión queda establecida y se pueden enviar datos.

---
## 🔹 BANDERAS TCP: SYN, ACK, FIN, RST

Las **banderas (flags)** de TCP indican el estado de la conexión:

- **SYN (Synchronize)**: inicia la conexión.
- **ACK (Acknowledgment)**: confirma recepción de datos.
- **FIN (Finish)**: cierra la conexión de forma ordenada.
- **RST (Reset)**: cierra la conexión de forma abrupta por error.

### Estados:
- **Set (1)** → La bandera está activada.
- **Clear (0)** → La bandera está desactivada.

---
## 🔹 CABECERAS TCP E IP

Cada segmento TCP se encapsula dentro de un paquete IP.  
Esto significa que:

- **IP** se encarga del direccionamiento.
- **TCP** se encarga de la fiabilidad.

La **cabecera TCP** se añade **encima** de la cabecera IP e incluye:

- Puertos de origen y destino
- Número de secuencia
- ACK
- Flags
- Control de errores

---
## 🔹 PRINCIPALES PUERTOS TCP

Los puertos permiten identificar **qué servicio** se está usando:

| Puerto      | Servicio         | Descripción                         |
| ----------- | ---------------- | ----------------------------------- |
| 80          | HTTP             | Web sin cifrar                      |
| 443         | HTTPS            | Web cifrada                         |
| 21          | FTP              | Transferencia de archivos           |
| 22          | SSH              | Acceso remoto seguro                |
| 25          | SMTP             | Envío de correos                    |
| 110/995 tls | POP3             | Recepción de correos                |
| 445         | SMB              | Compartición de archivos en Windows |
| 3389        | RDP              | Escritorio remoto                   |
| 3306        | MySQL            | Base de datos                       |
| 8080        | HTTP alternativo | Servidores web                      |
| 27017       | MongoDB          | Base de datos NoSQL                 |
|             |                  |                                     |

----
## 🔹 UDP – PROTOCOLO NO ORIENTADO A CONEXIÓN

**UDP (User Datagram Protocol)** es un protocolo de la **capa de transporte** que se caracteriza por ser **no orientado a la conexión**, **ligero** y **rápido**. A diferencia de TCP, UDP **no establece una conexión previa** ni mantiene un estado de comunicación entre emisor y receptor.  Su objetivo principal es **enviar datos de forma eficiente**, sacrificando fiabilidad a cambio de velocidad.

UDP es un protocolo **sin estado**.  
Esto significa que no guarda información sobre conexiones previas ni sobre los paquetes enviados. Cada datagrama es tratado de forma independiente.  
Esto simplifica la comunicación y reduce la carga en los sistemas.

---
## 🔹 UDP NO GARANTIZA FIABILIDAD

UDP **no garantiza** que los datos:

- Lleguen al destino
- Lleguen en orden
- No se pierdan
- Se retransmitan si hay errores

Cada paquete (datagrama) se envía de forma independiente. Si se pierde, **no se recupera**. Si llegan desordenados, **no se reordenan**. Esto hace que UDP sea menos fiable, pero mucho más rápido que TCP.

---

## 🔹 UDP ES MÁS RÁPIDO Y LIGERO

UDP no usa:

- Three-Way Handshake
- Confirmaciones (ACK)
- Control de flujo
- Retransmisiones

Al eliminar todos estos mecanismos, se reduce la sobrecarga de red y el consumo de recursos.  
Esto lo convierte en un protocolo **ideal para aplicaciones donde la velocidad es más importante que la precisión**.

---
## 🔹 APLICACIONES EN TIEMPO REAL

UDP es perfecto para aplicaciones que necesitan **baja latencia**, como:

- Streaming de vídeo
- Videollamadas
- Juegos online
- VoIP (llamadas por internet)
- DNS

En estos casos, es mejor perder un paquete que esperar a que se retransmita, ya que el tiempo real es prioritario.

---
## 🔹 ¿PARA QUÉ SIRVE NETSTAT?

**netstat** es una herramienta de red que permite ver:

- Conexiones activas
- Puertos abiertos
- Servicios en escucha
- Estadísticas de red
### COMANDOS NETSTAT IMPORTANTES

### 📌 `netstat -antp`

Muestra:

- **a** → Todas las conexiones
- **n** → Direcciones en formato numérico
- **t** → Solo TCP
- **p** → Proceso asociado

Sirve para ver **conexiones TCP establecidas**, puertos abiertos y qué programa las usa.

---
### 📌 `netstat -ano` (Windows)

Muestra:

- **a** → Todas las conexiones
- **n** → Direcciones numéricas
- **o** → PID del proceso

Con este comando puedes identificar **qué proceso** está usando un puerto concreto. Ideal para cazar servicios sospechosos 👀💀

----
## 🔹 HOST DISCOVERY – ¿QUÉ ES?

El **Host Discovery** es la fase del reconocimiento en la que se identifica **qué dispositivos están activos** dentro de una red. Su objetivo es mapear la infraestructura para conocer:

1. Determinar host y dispositivos activos
2. Determinar topologia de la red
3. Determinar puertos abiertos
4. Determinar servicios que corren en los puertos abiertos

---
## 🔹 MAPEO DE RED

El mapeo de red consiste en determinar **qué dispositivos, hosts e infraestructura** están presentes en una red.  
Aquí se identifican:

- Equipos finales
- Servidores
- Gateways
- Dispositivos de red

Esto permite tener una **visión global** del entorno y entender cómo se comunica cada elemento.

---
## 🔹 TIPOS DE AUDITORÍA

### 🔒 Black Box

El auditor no tiene información previa.  
Se simula un ataque real desde fuera.
### ⚙️ Gray Box

Se tiene información parcial (credenciales, IPs, red interna).
### 🔓 White Box

Se conoce todo el sistema: arquitectura, código, configuraciones.

---
## 🔹 TTL (TIME TO LIVE)

El **TTL** indica cuántos saltos puede dar un paquete antes de ser descartado.  
También permite **inferir el sistema operativo** del host remoto.

### Valores típicos por sistema:

|Sistema|TTL inicial|
|---|---|
|Linux / Unix|**64**|
|Windows|**128**|
|Cisco|**255**|

----
## 🔹 HOST DISCOVERY TECHNIQUES

- **Ping (ICMP Echo Request)**  
    Se utiliza para comprobar si un host está activo enviando peticiones ICMP.  
    Además, el valor del **TTL** en la respuesta puede dar pistas sobre el sistema operativo.  
    También puede aplicarse a rangos de red para descubrir múltiples hosts.  
    **Comando típico:**
    
    `ping 192.168.1.1 nmap -sn 192.168.1.0/24`
    
- **ARP Scanning**  
    Permite identificar hosts activos dentro de una red local (LAN) mediante peticiones ARP.  
    Es muy efectivo incluso cuando ICMP está bloqueado por firewalls.  
    **Comandos típicos:**
    
    `arp-scan 192.168.1.0/24 nmap -sn -PR 192.168.1.0/24`
    
- **TCP SYN Ping**  
    Utiliza paquetes TCP con la bandera **SYN** para comprobar si un host está activo.  
    Si el host responde con **SYN-ACK**, significa que está encendido y el puerto está abierto.  
    Se usa el modo **-sS** de Nmap.  
    **Comando típico:**
    
    `nmap -PS80,443 192.168.1.0/24`
    
- **UDP Ping**  
    Envía paquetes UDP a puertos específicos.  
    Si el host responde con un mensaje **ICMP Port Unreachable**, significa que está activo.  
    Es útil cuando ICMP está filtrado.  
    **Comando típico:**
    
    `nmap -PU53 192.168.1.0/24`
    
- **TCP ACK Ping**  
    Envía paquetes TCP con la bandera **ACK** para detectar hosts incluso detrás de firewalls.  
    Si el host responde con un **RST**, se considera activo.  
    **Comando típico:**
    
    `nmap -PA80 192.168.1.0/24`
    
- **ICMP Timestamp / Address Mask**  
    Usa otros tipos de mensajes ICMP menos comunes para descubrir hosts.  
    Algunos sistemas los permiten aunque bloqueen el ping tradicional.  
    **Comando típico:**
    
    `nmap -sn -PE -PP -PM 192.168.1.0/24`

---
## 🔹 PING SWEEPS

El **Ping Sweep** es una técnica de **Host Discovery** utilizada para detectar **hosts activos** dentro de una red.  
Consiste en enviar múltiples **ICMP Echo Request (ping)** a un rango de direcciones IP y analizar quién responde.

Su objetivo es identificar **qué equipos están encendidos** antes de pasar a fases más avanzadas como escaneo de puertos o enumeración de servicios.

---
## 🔹 MENSAJES ICMP UTILIZADOS

- **ICMP Echo Request**
    - **Type: 8**
    - **Code: 0**  
        Es el mensaje que se envía al host para comprobar si está activo.
        
- **ICMP Echo Reply**
    - **Type: 0**
    - **Code: 0**  
        Es la respuesta del host indicando que está encendido y accesible.
        
Si el host está activo, devuelve un **Echo Reply**.  
Si está apagado o inaccesible, **no se recibe respuesta**.

---
## 🔹 CUANDO NO HAY RESPUESTA

Si un host no responde, pueden ocurrir varias cosas:

- El equipo está **apagado**
- ICMP está **bloqueado por firewall**
- Existe un **proxy o sistema de filtrado**
- El host ignora peticiones ICMP

Por eso, ausencia de respuesta **no siempre significa que esté apagado**. Por eso, se suele combinar con **ARP, TCP SYN o UDP ping**.

---
## 🔹 CASO ESPECIAL: WINDOWS

En muchos sistemas **Windows**, el firewall bloquea por defecto los **ICMP Echo Request**.  
Esto puede provocar falsos negativos, haciendo pensar que el host está inactivo cuando en realidad está encendido.

---
## 🔹 COMANDOS TÍPICOS

### 📌 Ping a un host

`ping 192.168.1.1`

---
### 📌 Ping Sweep con Nmap

`nmap -sn 192.168.1.0/24`

Escanea toda la red enviando ICMP Echo Request.

---
### 📌 Ping Broadcast

`ping -b 192.168.1.255`

Envía un ping a toda la red usando broadcast.  
Todos los hosts que respondan estarán activos.  
⚠️ Puede generar mucho tráfico y no siempre está permitido.

---
### 📌 fping

**fping** es una herramienta optimizada para hacer **ping a múltiples hosts rápidamente**.  
Permite escanear rangos completos con mayor velocidad que el ping tradicional.

Sirve para:

- Detectar hosts activos
- Automatizar ping sweeps
- Monitorizar disponibilidad

Ejemplo:

`fping -a -g 192.168.1.0/24`

- **-a** → muestra solo hosts activos
- **-g** → genera el rango de IPs

---
## 🔹 HOST DISCOVERY CON NMAP

Nmap es una de las herramientas más potentes para el **descubrimiento de hosts** y el análisis de redes. Permite identificar dispositivos activos, servicios, sistemas operativos y estructura de red. Su eficacia depende de **cómo se configuren los parámetros** y del nivel de sigilo o agresividad deseado.

---
## 🔹 TIPOS DE ESCANEO

Nmap ofrece varios tipos de escaneo según el objetivo:

- **ICMP Ping Scan (-sn)**  
    Detecta hosts activos sin escanear puertos.  
    Ideal para reconocimiento inicial.
    
- **TCP SYN Scan (-sS)**  
    Envía paquetes SYN y analiza respuestas.  
    Rápido, sigiloso y muy usado.
    
- **TCP Connect Scan (-sT)**  
    Establece conexión completa.  
    Más ruidoso, pero funciona sin privilegios.
    
- **UDP Scan (-sU)**  
    Detecta servicios UDP.  
    Más lento y menos fiable.
    
- **Aggressive Scan (-A)**  
    Incluye detección de SO, versiones y scripts.  
    Muy completo, pero muy visible.

---
## 🔹 INTENSIDAD DEL ESCANEO

La intensidad depende de la **velocidad**, **precisión** y **nivel de detección**:

- **Escaneos stealth**  
    Lentos, discretos, evitan IDS/IPS.  
    Se usan en entornos vigilados.
- **Escaneos agresivos**  
    Rápidos y completos.  
    Ideales en entornos controlados (laboratorios, auditorías internas).

Ejemplo:

`nmap -T1 192.168.1.1   # Muy sigiloso  nmap -T5 192.168.1.1   # Muy agresivo`

---
## 🔹 DESCUBRIMIENTO DE HOSTS

Nmap permite detectar qué hosts están activos usando:

- ICMP Echo
- TCP SYN
- UDP
- ARP

Ejemplo:

`nmap -sn 192.168.1.0/24`

Este comando solo detecta hosts activos, sin escanear puertos.

---
## 🔹 RESOLUCIÓN DNS

Por defecto, Nmap intenta resolver IPs a nombres de dominio.

- **Activar DNS:**  
    Útil para identificar servidores y servicios.
- **Desactivar DNS (-n):**  
    Acelera el escaneo y evita fugas de información.

Ejemplo:

`nmap -n 192.168.1.1`

---
## 🔹 IP O RANGO DE IPS

Nmap acepta:

- IP individual
- Rangos CIDR
- Listas de IP
- Archivos de entrada

Ejemplos:

`nmap 192.168.1.1   nmap 192.168.1.0/24   nmap -iL targets.txt`

---
## 🔹 FORMATOS DE SALIDA

Nmap permite exportar resultados en distintos formatos:

- **Normal (-oN)** → Texto legible
- **XML (-oX)** → Para herramientas automáticas
- **Grepable (-oG)** → Filtrado rápido
- **All (-oA)** → Genera todos los formatos

Ejemplo:

`nmap -oA escaneo 192.168.1.1`

---
## 🔹 PARÁMETROS IMPORTANTES Y CUÁNDO USARLOS

### 📌 `-sn`

Descubre hosts sin escanear puertos.  
Ideal para reconocimiento inicial.

### 📌 `-sS`

Escaneo SYN sigiloso.  
Requiere privilegios.

### 📌 `-sT`

Conexión completa TCP.  
Más visible, pero funciona sin root.

### 📌 `-sU`

Escaneo UDP.  
Lento, útil para DNS, SNMP, VoIP.

### 📌 `-p-`

Escanea todos los puertos (1-65535).  
Más completo, más lento.

### 📌 `-sV`

Detecta versiones de servicios.  
Clave para buscar vulnerabilidades.

### 📌 `-O`

Detecta sistema operativo.  
Requiere múltiples respuestas del host.

### 📌 `-A`

Escaneo agresivo completo.  
Solo en entornos controlados.

### 📌 `-T0 a -T5`

Controla velocidad e intensidad.  
Más rápido = más ruido.

### 📌 `--open`

Muestra solo puertos abiertos.  
Resultados más limpios.

### 📌 `-Pn`

Asume que el host está activo.  
Útil cuando ICMP está bloqueado.

### 📌 `--f`

Divide los paquetes en trozos más pequeños.  
Esto hace que algunos firewalls, IDS o sistemas de detección tengan más difícil analizar el tráfico.
Con -mtu puedes elegir el tamaño exacto de los paquetes.

### 📌 `-PE` (ICMP Echo Request)

Envía ping ICMP clásico.

- Rápido
- Sencillo
- Fácilmente bloqueado

`nmap -sn -PE 192.168.1.0/24`

---

### 📌 `-PP` (ICMP Timestamp)

Usa mensajes de tiempo ICMP.

- Funciona cuando el ping normal está bloqueado
- Menos común → más sigiloso

`nmap -sn -PP 192.168.1.0/24`

---

### 📌 `-PM` (ICMP Address Mask)

Solicita la máscara de red.

- Útil en redes antiguas
- Puede revelar estructura de red

`nmap -sn -PM 192.168.1.0/24`

---

### 📌 `-PS` (TCP SYN Ping)

Envía paquetes **SYN** a puertos específicos.

- Si responde con **SYN-ACK**, el host está activo
- Muy útil cuando ICMP está bloqueado
- Bastante sigiloso

`nmap -PS80,443 192.168.1.0/24`

---

### 📌 `-PA` (TCP ACK Ping)

Envía paquetes **ACK**.

- Si responde con **RST**, el host está activo
- Puede atravesar algunos firewalls
- No depende de puertos abiertos

`nmap -PA80 192.168.1.0/24`

---

### 📌 `-PU` (UDP Ping)

Envía paquetes UDP.

- Si el host responde con **ICMP Port Unreachable**, está activo
- Más lento
- Útil para evadir filtros TCP

`nmap -PU53 192.168.1.0/24`

---

### 📌 `-PR` (ARP Ping – solo LAN)

Descubrimiento por ARP.

- El más fiable en redes locales
- Ignora firewalls
- Devuelve MAC + fabricante

`nmap -sn -PR 192.168.1.0/24`

---

### 📌 `-Pn`

Desactiva el host discovery.

- Asume que todos los hosts están activos
- Útil si todo está filtrado
- Genera más ruido

`nmap -Pn 192.168.1.1`

---
## 🔹 CUÁNDO USAR CADA TÉCNICA

|Escenario|Mejor opción|
|---|---|
|Red local (LAN)|`-PR`|
|ICMP bloqueado|`-PS`, `-PA`, `-PU`|
|Firewall estricto|`-PA`|
|Reconocimiento rápido|`-sn`|
|Todo filtrado|`-Pn`|
|Evasión IDS|`-PP`, `-PM`|

---
## 🔹 CÓMO AFECTAN LOS PARÁMETROS AL OBJETIVO

|Objetivo|Configuración|
|---|---|
|Sigilo|-sS -T1 -Pn|
|Rapidez|-T5|
|Precisión|-sV -O|
|Enumeración completa|-A|
|Bypass firewall|-Pn -PS -PA|
|Redes internas|-PR|

------
## 🔹 HOST DISCOVERY CON FIREWALL (NMAP)

Cuando hay un firewall, el **ping clásico (ICMP)** suele estar bloqueado.  
Por eso hay que usar técnicas alternativas para **detectar hosts activos** sin depender de ICMP.

---

### 🥇 `-PS` → TCP SYN Ping

Envía paquetes **SYN** a puertos comunes (80, 443, etc.).

`nmap -sn -PS80,443 192.168.1.0/24`

📌 Funciona bien si:

- El firewall permite tráfico web
- Hay servicios TCP accesibles

Si el host responde con **SYN-ACK**, está activo.

---

### 🥈 `-PA` → TCP ACK Ping

Envía paquetes **ACK**.

`nmap -sn -PA80 192.168.1.0/24`

📌 Útil cuando:

- SYN está bloqueado
- El firewall responde con **RST**

Si hay RST → host activo.

---

### 🥉 `-PU` → UDP Ping

Envía paquetes UDP (por ejemplo al puerto 53 – DNS).

`nmap -sn -PU53 192.168.1.0/24`

📌 Funciona si:

- TCP está filtrado
- UDP no está bloqueado

Si responde con ICMP _Port Unreachable_, el host está activo.