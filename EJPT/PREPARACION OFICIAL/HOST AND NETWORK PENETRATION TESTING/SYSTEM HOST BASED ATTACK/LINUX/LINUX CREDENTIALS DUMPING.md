----
# DUMPING LINUX PASSWORD HASHES

## ¿DÓNDE GUARDA LINUX LAS CUENTAS?

### 🔹 `/etc/passwd`

- Contiene **información de las cuentas**
- Es **legible por cualquier usuario**
- **NO** contiene las contraseñas reales

Ejemplo:

`root:x:0:0:root:/root:/bin/bash`

Campos importantes:

- `root` → usuario
- `x` → la contraseña **NO está aquí**
- `0:0` → UID y GID

📌 El campo `x` indica:

> “la contraseña está en otro sitio”

### 🔹 `/etc/shadow`

- Contiene los **hashes de las contraseñas**    
- **Solo accesible por root**
- Aquí está el material sensible 😈

Ejemplo:

`root:$6$saltsalt$hashhashhash:19000:0:99999:7:::`
## ¿POR QUÉ HAY DOS ARCHIVOS?

- `/etc/passwd` → información básica (necesaria para el sistema)
- `/etc/shadow` → seguridad

👉 Separarlos evita que cualquiera pueda leer hashes.

## PREFIJOS `$` EN `/etc/shadow` (MUY IMPORTANTE)

El **prefijo del hash** indica **el algoritmo usado**.

|Prefijo|Algoritmo|
|---|---|
|`$1$`|MD5|
|`$2a$`, `$2b$`, `$2y$`|Blowfish (bcrypt)|
|`$5$`|SHA-256|
|`$6$`|SHA-512|

## ¿QUÉ NOS DICE ESTO COMO ATACANTES?

- `$1$` (MD5) → **muy débil**, crackeo rápido
- `$2b$` (bcrypt) → **lento**, caro de crackear
- `$5$` / `$6$` → fuerte, pero crackeable con diccionarios

👉 **No se crackea “por fuerza bruta”**, se crackea:

- con diccionarios
- reglas
- tiempo
## DUMPING MANUAL DE HASHES (SIENDO ROOT)

Si ya eres root:

`cat /etc/shadow`
## DUMPING CON METERPRETER

Metasploit tiene un módulo específico para Linux.
### 🔹 Buscar el módulo

`search hashdump`
### 🔹 Usar el módulo de Linux

`use post/linux/gather/hashdump set SESSION <id> run`

📌 Este módulo:

- lee `/etc/shadow`
- devuelve:
    - usuario
    - hash
## ¿QUÉ HACER DESPUÉS CON LOS HASHES?

Normalmente:

- crackearlos con **hashcat**
- reutilizarlos en:
    - SSH
    - sudo
    - otros sistemas
- password reuse → movimiento lateral

se pueden crackear con:
- search crack_linux