----
## WHAT ARE SYSTEM / HOST-BASED ATTACKS

Los **system/host-based attacks** son ataques dirigidos **a un sistema o host específico**, normalmente asociados a:

- Un **sistema operativo concreto** (Windows, Linux, macOS).
- Un **equipo interno** de la red (servidor o workstation).
### Características clave

- Se realizan **después de obtener acceso a la red** (fase post-explotación).
- El objetivo es **explotar el propio sistema operativo**, no servicios públicos.
- No dependen de aplicaciones como:
    - Web servers
    - Bases de datos
    - Servicios expuestos a Internet
### ¿Para qué se usan?

- Atacar **ordenadores o portátiles de empleados**.
- Explotar:
    - Vulnerabilidades del sistema operativo
    - Configuraciones inseguras        
    - Permisos mal definidos
- Muy utilizados para:
    - **Escalada de privilegios**
    - **Movimiento lateral**
    - Persistencia en la red
👉 En resumen: cuando ya estás _dentro_, aquí es donde haces daño de verdad.
## OVERVIEW OF WINDOWS VULNERABILITIES

Windows **no es seguro por defecto**. Requiere una **configuración adecuada** para minimizar riesgos.
### Problemas habituales de seguridad en Windows

- ❌ **No viene endurecido (hardening) de serie**
- ⏳ **Retrasos en el parcheo** de vulnerabilidades críticas
- 🧓 **Sistemas obsoletos en producción**
    - Muchas empresas aún usan **Windows 7**
- 🔄 **Demasiadas versiones**
    - Dificulta mantener todos los sistemas actualizados
- 🔌 **Susceptible a ataques físicos**
    - USBs maliciosos
    - Keyloggers
    - Malware por dispositivos externos
    
## COMMON WINDOWS VULNERABILITY TYPES

Tipos de vulnerabilidades más comunes en sistemas Windows:

- **Information Disclosure**  
    Filtración de información sensible (usuarios, hashes, configuraciones).
- **Buffer Overflow**  
    Sobrescritura de memoria que puede llevar a ejecución de código.
- **Remote Code Execution (RCE)**  
    Ejecución de comandos de forma remota sin autenticación o con credenciales débiles.
- **Privilege Escalation**  
    Elevar privilegios desde usuario normal a administrador o SYSTEM.
- **Denial of Service (DoS)**  
    Provocar que el sistema o servicio deje de responder.

## FREQUENTLY EXPLOITED WINDOWS SERVICES

Servicios de Windows **frecuentemente explotados** en entornos reales:

- **SMB (Server Message Block)**  
    Compartición de archivos e impresoras (EternalBlue te saluda 👋).    
- **RDP (Remote Desktop Protocol)**  
    Acceso remoto. Objetivo clásico de brute force y exploits.
- **Microsoft IIS**  
    Servidor web de Windows. Vulnerable si está mal configurado o desactualizado.
- **WebDAV**  
    Extensión HTTP para gestión remota de archivos. Puede permitir uploads maliciosos.
- **WinRM (Windows Remote Management)**  
    Administración remota basada en HTTP/S. Muy útil para post-explotación si hay credenciales.