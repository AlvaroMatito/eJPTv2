----
# NETWORK FUNDAMENTALS

## ¿QUÉ ES UNA RED DE COMPUTADORES?

En redes de computadores, los **hosts** (ordenadores, servidores, routers, etc.) se comunican entre sí mediante el uso de **protocolos de red**.
Los protocolos:

- definen **cómo** se envían los datos
- definen **qué formato** tienen
- aseguran que **sistemas distintos** (hardware y software diferentes) puedan comunicarse correctamente

👉 Gracias a los protocolos, un Linux puede hablar con un Windows, un móvil con un servidor, etc.
## PROTOCOLOS DE RED (EJEMPLOS IMPORTANTES)

Hay muchos protocolos, cada uno para un propósito concreto:

### 🔹 Protocolos de aplicación

- **HTTP / HTTPS** → navegación web
- **FTP** → transferencia de archivos
- **SSH** → acceso remoto seguro
- **SMTP** → envío de correos
- **DNS** → resolución de nombres a IP
### 🔹 Protocolos de transporte

- **TCP** → comunicación fiable (web, SSH, FTP)
- **UDP** → comunicación rápida, no fiable (DNS, streaming)
### 🔹 Protocolos de red

- **IP** → direccionamiento y enrutamiento
- **ICMP** → mensajes de control (ping)
### 🔹 Protocolos de enlace

- **Ethernet**
- **ARP** → resolución IP ↔ MAC

## ¿CÓMO SE COMUNICAN LOS HOSTS?

La comunicación entre hosts se realiza mediante **paquetes**.
Un **paquete** es una unidad de datos que se envía por la red.
A nivel físico:

- los datos se transmiten como **señales eléctricas**, **ondas de radio** o **pulsos de luz**
- estas señales representan **bits (0 y 1)**

👉 El sistema receptor interpreta esas señales como información.

## ESTRUCTURA DE UN PAQUETE

Un paquete se divide principalmente en dos partes:
### 🔹 Header (cabecera)

Contiene **metadatos**, como:

- dirección IP origen
- dirección IP destino
- puerto origen y destino
- protocolo usado
- información de control

👉 Es la “dirección y las instrucciones”.
### 🔹 Payload

Contiene los **datos reales**:

- contenido de una web
- comandos
- archivos
- credenciales

👉 Es la “carta” que se envía.
## MODELO OSI (Open Systems Interconnection)

### ¿QUÉ ES EL MODELO OSI?

El **modelo OSI** es un **modelo teórico de 7 capas** que sirve para:

- entender cómo funciona la comunicación en red
- estandarizar el diseño de protocolos
- facilitar el troubleshooting
- dividir un problema complejo en partes pequeñas

👉 No es una implementación real, es un **modelo conceptual**.

## LAS 7 CAPAS DEL MODELO OSI

### 7️⃣ Capa de Aplicación

- Interacción directa con el usuario
- Protocolos de alto nivel

Ejemplos:

- HTTP
- FTP
- SSH
- SMTP
- DNS
### 6️⃣ Capa de Presentación

- Formato de los datos
- Cifrado / descifrado
- Compresión

Ejemplos:

- SSL / TLS
- codificación (ASCII, UTF-8)
    
### 5️⃣ Capa de Sesión

- Gestión de sesiones
- Apertura, mantenimiento y cierre de conexiones

Ejemplo:

- control de sesiones en conexiones persistentes
### 4️⃣ Capa de Transporte

- Comunicación extremo a extremo
- Control de flujo y errores

Protocolos:

- **TCP** (fiable)
- **UDP** (rápido, no fiable)
### 3️⃣ Capa de Red

- Direccionamiento lógico
- Enrutamiento de paquetes

Protocolos:

- IP
- ICMP
### 2️⃣ Capa de Enlace de Datos (Data Link)

- Comunicación dentro de la red local
- Direcciones MAC

Protocolos:

- Ethernet
- ARP
### 1️⃣ Capa Física

- Transmisión de bits
- Medio físico

Ejemplos:

- cables
- fibra óptica
- Wi-Fi
- señales eléctricas

---
# FIREWALL DETECTION AND IDS EVASION

## ¿QUÉ ES UN FIREWALL?

Un **firewall** es un **dispositivo o software** que:

- controla el tráfico de red
- **permite o bloquea conexiones**
- según reglas (IP, puerto, protocolo, estado…)

Sirve para:

- proteger sistemas internos
- bloquear accesos no autorizados
- filtrar tráfico entrante y saliente

📌 Ejemplo:

- “permito 80 y 443”
- “bloqueo todo lo demás”
## ¿QUÉ ES UN IDS?

Un **IDS (Intrusion Detection System)** es un sistema que:

- **detecta actividad sospechosa**
- analiza patrones de tráfico
- genera alertas (no bloquea)

Tipos:

- **NIDS** → tráfico de red
- **HIDS** → actividad en el host

📌 Diferencia clave:

- Firewall → **bloquea**
- IDS → **detecta y avisa**

(El IPS ya bloquea, pero eso es otro nivel)
## DETECTAR FIREWALLS CON NMAP

### 🔹 Escaneo SYN básico

`nmap -sS -Pn -F IP`

- `-sS` → SYN scan (semi-abierto)    
- `-Pn` → no hacer ping
- `-F` → escaneo rápido (puertos comunes)

📌 Si ves:

- **filtered** → probablemente hay firewall
## 🔥 DETECCIÓN DE FIREWALL CON ACK SCAN

### 🔹 `-sA` (MUY IMPORTANTE)

`nmap -sA -Pn IP`

👉 ¿Para qué sirve?

- **NO detecta puertos abiertos**
- detecta **si hay un firewall filtrando**

Resultados:

- `unfiltered` → no hay firewall
- `filtered` → hay firewall/filtrado

📌 Se usa mucho en **Windows**.
## EVASIÓN DE FIREWALL / IDS (TÉCNICAS)

## 1️⃣ FRAGMENTACIÓN DE PAQUETES

### 🔹 `-f`

`nmap -Pn -sS -sF -F -f IP`

👉 ¿Qué hace `-f`?

- divide los paquetes en **fragmentos pequeños**
- algunos firewalls/IDS:
    - no reensamblan bien
    - inspeccionan solo el primer fragmento

🎯 Objetivo:

- **evadir inspección profunda**
- confundir reglas simples
## 2️⃣ MTU (MAXIMUM TRANSMISSION UNIT)

### 🔹 `--mtu`

`nmap -Pn -sS -sF -F -f --mtu 8 IP`

👉 ¿Qué es MTU?

- **tamaño máximo de datos** que se envían en un paquete
- se mide en **bytes**
- debe ser múltiplo de 8

Ejemplo:

- MTU normal Ethernet ≈ 1500 bytes
- MTU bajo → más fragmentación

🎯 Objetivo:

- controlar **cómo se fragmentan los paquetes**
- evadir IDS que no manejan bien fragmentos raros
## 3️⃣ AÑADIR DATOS BASURA AL PAQUETE

### 🔹 `--data-length`

`nmap -Pn -sS -sF -F --data-length 200 IP`

👉 ¿Para qué sirve?

- añade **datos aleatorios** al paquete
- cambia el tamaño y firma del paquete

🎯 Objetivo:

- evadir IDS basados en firmas
- hacer que el tráfico parezca distinto al típico de Nmap
## 4️⃣ DECOY SCAN (SIMULAR OTRAS IPs)

### 🔹 `-D`

`nmap -Pn -sS -sF -F --data-length 200 -D IP1,IP2 IP_OBJETIVO`

👉 ¿Qué hace `-D`?

- envía paquetes desde:
    - tu IP real
    - **IPs falsas (decoys)**
    
🎯 Objetivo:

- confundir logs
- ocultar la IP real del atacante

📌 El firewall ve:
> “muchas IPs escaneando”
## 5️⃣ SPOOFING DE PUERTO ORIGEN

### 🔹 `-g`

`nmap -Pn -sS -sF -F --data-length 200 -g 53 -D IP1,IP2 IP`

👉 ¿Para qué sirve `-g 53`?

- usa **puerto origen 53 (DNS)**

🎯 Objetivo:

- algunos firewalls:
    - confían en DNS
    - permiten tráfico desde 53

👉 Parece tráfico legítimo.
## 🔥 COMBINACIÓN COMPLETA (EVASIÓN)

`nmap -Pn -sS -sF -F -f --mtu 8 --data-length 200 -g 53 -D IP1,IP2 IP`

Esto:
- fragmenta paquetes
- ajusta MTU
- añade ruido
- usa decoys
- simula tráfico DNS

👻 **Mucho más difícil de detectar**