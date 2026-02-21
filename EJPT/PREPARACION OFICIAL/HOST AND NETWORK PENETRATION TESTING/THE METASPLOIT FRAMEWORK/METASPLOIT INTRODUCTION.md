-------
## ¿QUÉ ES METASPLOIT?

**Metasploit** es una **plataforma open source de penetration testing** que permite:

- desarrollar
- probar
- ejecutar exploits
- automatizar ataques

👉 Es una de las herramientas **más usadas del mundo** en pentesting.

Características clave:

- **Open Source**
- Modular
- Extensible
- La comunidad puede **añadir módulos**
- Automatiza casi todas las fases del pentest
## ¿QUÉ BASE DE DATOS USA METASPLOIT?

Metasploit usa una **base de datos PostgreSQL** para almacenar:

- hosts descubiertos
- servicios
- vulnerabilidades
- credenciales
- sesiones
- resultados de exploits

👉 Esto permite:

- organizar mejor el pentest
- correlacionar información
- trabajar por proyectos (workspaces)
## EDICIONES DE METASPLOIT

### 🔹 Metasploit Framework

- Gratuito
- Open Source
- CLI
- El más usado en labs y certificaciones
### 🔹 Metasploit Express

- Comercial (ya discontinuado)
- Automatización básica
- Pensado para empresas pequeñas
### 🔹 Metasploit Pro

- Comercial
- Interfaz gráfica
- Reporting avanzado
- Automatización completa
- Pensado para empresas grandes
## TIPOS DE MÓDULOS EN METASPLOIT

### 🔴 Exploit

Código que **aprovecha una vulnerabilidad** para ejecutar código.
Ejemplo:
- buffer overflow
- RCE
- auth bypass
### 🟢 Payload

Código que **se ejecuta después del exploit**.
Ejemplos:
- reverse shell
- bind shell
- Meterpreter
### 🔵 Auxiliary

No explotan vulnerabilidades.
Sirven para:
- escaneo
- enumeración
- brute force
- sniffing
### 🟣 Encoder

Se usan para:
- ofuscar payloads
- evadir AV / IDS
- evitar bad characters
### ⚫ NOP

Instrucciones “No Operation”.
Se usan para:
- alinear payloads
- evitar crashes
- exploits de bajo nivel
## INTERFACES DE METASPLOIT

### 🔹 msfconsole (LA PRINCIPAL)

- Interfaz CLI    
- La más completa
- Uso diario

`msfconsole`
### 🔹 msfcli

- Interfaz antigua
- No interactiva
- Obsoleta (ya casi no se usa)
### 🔹 Armitage

- Interfaz gráfica
- Visual
- Colaborativa
- Basada en Metasploit
### 🔹 Web Interface

- Interfaz web (Metasploit Pro)
- Reporting y gestión
## TIPOS DE PAYLOADS
### 🔹 Non-Staged Payloads

- Se envían **en una sola pieza**
- Más grandes
- Más simples

Ejemplo:

`windows/meterpreter_reverse_tcp`
### 🔹 Staged Payloads

Se dividen en **dos partes**.
#### 🔸 Stager

- Pequeño
- Establece la conexión con el atacante
- Descarga el payload final

Ejemplo:

`windows/meterpreter/reverse_tcp`
#### 🔸 Stage

- Payload completo
- Se ejecuta tras el stager
- Normalmente:
    - Meterpreter
    - reverse shell

👉 Ventaja: payload inicial más pequeño.
## ¿QUÉ ES METERPRETER?

**Meterpreter** es un payload avanzado que:
- se ejecuta **en memoria**
- no toca disco (más sigiloso)
- se comunica mediante **sockets**
- ofrece control total del sistema

Funciones:
- dump hashes
- migrar procesos
- keylogging
- pivoting
- ejecutar comandos
## UBICACIÓN DE LOS MÓDULOS

`/usr/share/metasploit-framework/`

Estructura:
- `modules/exploits`
- `modules/payloads`
- `modules/auxiliary`
- `modules/post`
## WORKSPACES EN METASPLOIT

Los **workspaces** permiten separar proyectos.

👉 Muy útil para:
- distintos clientes
- distintos labs
- no mezclar información
### Comandos de Workspaces

`workspace            # listar` 
`workspace -a lab     # crear 
`workspace -d lab     # borrar` 
`workspace -r old new # renombrar`
### Datos almacenados por workspace

- `hosts` → máquinas detectadas
- `services` → servicios
- `vulns` → vulnerabilidades
- `creds` → credenciales
- `sessions` → sesiones activas
- `loot` → información extraída
## FASES DEL PENETRATION TESTING

### 1️⃣ Information Gathering

- OSINT    
- IPs
- dominios
- subdominios
### 2️⃣ Enumeration

- servicios
- versiones
- usuarios
- shares
### 3️⃣ Exploitation

- explotación de vulnerabilidades
- obtención de shell
### 4️⃣ Post-Exploitation

#### 🔹 Privilege Escalation

- pasar de user → root/admin
#### 🔹 Persistence

- mantener acceso
#### 🔹 Clearing Tracks

- borrar evidencias (teórico)
## BASE DE DATOS DE METASPLOIT

### 🔹 Inicializar BD

`msfdb init`
### 🔹 Reiniciar BD

`msfdb reinit`
### 🔹 Ver estado

`msfdb status`
## COMANDOS IMPORTANTES DE METASPLOIT

```search 
use 
set 
setg 
show options 
show payloads 
run 
exploit 
sessions 
sessions -i 
background```
