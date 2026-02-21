-----
## 🔎 PORT SCANNING WITH NMAP

Nmap es una herramienta fundamental para **enumerar puertos, servicios y hosts** en una red.  
Por defecto, **realiza descubrimiento de hosts** antes de escanear (envía pings y sondas).
### 🛰️ Descubrimiento de host
- Nmap intenta comprobar si el host está activo usando **ICMP, TCP o ARP**
- Si quieres **evitar esta fase** y forzar el escaneo aunque el host no responda:

`nmap -Pn <IP>`

➡️ `-Pn` = No ping, asume que el host está activo.

---
### ⚡ Escaneo rápido de puertos

`nmap -F <IP>`

- `-F` (Fast scan)
- Escanea **solo los 100 puertos más comunes**
- Ideal para reconocimiento rápido

---
### 🎯 Selección de puertos

`nmap -p 80,443 <IP> nmap -p 1-1000 <IP> nmap -p- <IP>`

- `-p` → Indica puertos específicos
- `-p-` → Escanea **todos los puertos (1–65535)**
- Se pueden usar rangos y listas

---
## 🕵️‍♂️ Escaneo SYN: `-sS`

El escaneo **SYN** es uno de los más usados porque es **rápido, eficaz y relativamente sigiloso**.
### ¿Cómo funciona?

Nmap envía un paquete **SYN** (inicio de conexión TCP):

|Respuesta|Estado del puerto|
|---|---|
|`SYN-ACK`|🟢 Puerto **abierto**|
|`RST`|🔴 Puerto **cerrado**|
|Sin respuesta|🟡 **Filtrado** (firewall)|

Nmap **no completa la conexión**, por eso se le llama _half-open scan_.

---
### 🚨 Ruido y detección

- Nmap sin parámetros:
    - Hace descubrimiento de host
    - Escanea varios puertos
    - Genera tráfico detectable

➡️ Es **ruidoso** y puede ser detectado por **firewalls, IDS o SIEM**.

----
## 🧠 DETECCIÓN DE SERVICIOS Y SISTEMA OPERATIVO CON NMAP

Una vez sabemos **qué puertos están abiertos**, el siguiente paso lógico es descubrir:

- **Qué servicios** se están ejecutando
- **Qué versiones** usan
- **Qué sistema operativo** tiene el objetivo

Esto es clave para encontrar **vulnerabilidades reales (CVEs)** y preparar un ataque efectivo.

---
### 🔍 Detección de servicios y versiones: `-sV`

`nmap -sV <IP>`

Permite identificar:

- El **servicio** (Apache, SSH, FTP, etc.)
- La **versión exacta** (ej: Apache 2.4.49)
- A veces el **lenguaje o framework** (PHP, Python, Node.js…)

Esto es oro puro para buscar **exploits conocidos**.

---
### ⚙️ Intensidad de detección: `--version-intensity`

Controla **cuánto se esfuerza Nmap** en detectar versiones.

`--version-intensity 0   # Mínimo (rápido, menos preciso) --version-intensity 9   # Máximo (lento, muy preciso)`

|Nivel|Características|
|---|---|
|0–2|Rápido, pocos datos|
|3–6|Equilibrado|
|7–9|Muy detallado, más ruido|

---
### 🖥️ Detección del sistema operativo: `-O`

`nmap -O <IP>`

Nmap analiza la forma en que el sistema responde a paquetes para identificar:

- Linux / Windows / BSD / Router
- A veces incluso la **versión aproximada**

---
### 🤔 Adivinar el SO si no está claro: `--osscan-guess`

`nmap -O --osscan-guess <IP>`

Fuerza a Nmap a **dar una suposición**, aunque no esté 100% seguro.

---
## 🧩 NMAP SCRIPTING ENGINE (NSE)

El **Nmap Scripting Engine (NSE)** permite ejecutar scripts en los objetivos para:

- Detectar **vulnerabilidades**
- Enumerar **servicios**
- Obtener **información del sistema**
- Automatizar tareas de reconocimiento

Los scripts ya vienen incluidos con Nmap.

---
### 📁 ¿Dónde están los scripts?

`ls -al /usr/share/nmap/scripts/`

Ahí se almacenan **todos los scripts NSE**. Puedes filtrar por tecnología o servicio:

`ls /usr/share/nmap/scripts/ | grep http ls /usr/share/nmap/scripts/ | grep ftp ls /usr/share/nmap/scripts/ | grep smb ls /usr/share/nmap/scripts/ | grep php ls /usr/share/nmap/scripts/ | grep mongo`

---
## 🗂️ Categorías de scripts NSE

Los scripts están organizados por **categorías** según su función:

|Categoría|Para qué sirve|
|---|---|
|**auth**|Pruebas de autenticación (bypass, credenciales débiles)|
|**broadcast**|Descubrimiento de dispositivos en la red|
|**brute**|Fuerza bruta de credenciales|
|**default**|Scripts básicos y seguros|
|**discovery**|Enumeración de servicios y recursos|
|**dos**|Pruebas de denegación de servicio|
|**exploit**|Explotación de vulnerabilidades|
|**external**|Usa servicios externos|
|**fuzzer**|Envío de datos aleatorios|
|**intrusive**|Scripts invasivos|
|**malware**|Detecta backdoors|
|**safe**|No afecta al sistema|
|**version**|Mejora detección de versiones|
|**vuln**|Busca vulnerabilidades|

---
## ⚡ Scripts por defecto: `-sC`

`nmap -sC <IP>`

Ejecuta los **scripts más comunes y seguros**.  
Ideal para:

- Enumeración inicial
- Obtener información clave
- Detectar fallos básicos

---
## 🔎 ¿Qué tipo de información pueden obtener los scripts?

Los NSE pueden revelar:

- Versiones de **servicios**
- **Usuarios** del sistema
- **Shares SMB**
- **Directorios web**
- **Vulnerabilidades conocidas (CVEs)**
- **Versiones del kernel**
- **Sistema operativo**
- **CMS usados** (WordPress, Joomla…)
- **Frameworks** (PHP, Node, Django)
- **APIs expuestas**
- **Backdoors**
- **Configuraciones débiles**

---
## 📖 Ayuda de un script

`nmap --script-help=http-enum`

Muestra:

- Qué hace el script
- Qué puertos usa
- Qué información obtiene
- Qué argumentos acepta

---
## ▶️ Ejecutar un script concreto

`nmap --script http-enum <IP>`

Ejecuta solo el script que tú quieras. También puedes usar categorías:

`nmap --script vuln <IP> nmap --script auth <IP>`

---
## 🧪 Ejemplo potente

`nmap -sS -sV -p 80,443 --script http-enum,http-vuln* <IP>`

## CTF:
- en las cabeceras con ctrl + c -> network o con curl -i
- en el robots.txt -> /secret-info/flag.txt
- en el servidor ftp -> como anonymous -> get flag.txt y get creds.txt
- en el mysql -> mysql -h ip -u db_admin -p -> password de creds.txt -> show databases;
