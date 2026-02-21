----
## PHASE 1: DEVELOP A SECURITY POLICY

### Definir el propósito y alcance de la política de seguridad

Se establece **para qué existe la política** y **a qué sistemas, usuarios y datos aplica**.  
Define límites claros para evitar ambigüedades y asegurar una aplicación coherente en toda la organización.

### Control de acceso (Access Control)

Define **quién puede acceder a qué recursos** y bajo qué condiciones.  
Incluye principios como **mínimo privilegio**, gestión de roles y control de accesos físicos y lógicos.

### Auditoría y rendición de cuentas (Audit and Accountability)

Establece mecanismos para **registrar, monitorizar y revisar actividades** del sistema.  
Permite **trazabilidad**, detección de incidentes y asignación de responsabilidades.

### Gestión de la configuración (Configuration Management)

Asegura que los sistemas mantengan **configuraciones seguras y aprobadas**.  
Incluye control de cambios, versiones, parches y prevención de configuraciones inseguras.

### Identificación y autenticación (Identification and Authentication)

Define cómo los usuarios y sistemas **se identifican y prueban su identidad**.  
Incluye contraseñas, MFA, certificados y políticas de autenticación robustas.

### Integridad del sistema y de la información (System and Information Integrity)

Garantiza que los sistemas y datos **no sean alterados de forma no autorizada**.  
Incluye protección contra malware, validación de entradas, hashing y monitorización de integridad.

### Mantenimiento (Maintenance)

Define cómo se realizan **tareas de mantenimiento de forma segura**.  
Incluye actualizaciones, parches, accesos temporales y mantenimiento remoto controlado.

## PHASE 2: SECURITY AUDITING WITH LYNIS

### ¿qué es Lynis?

**Lynis** es una herramienta de **auditoría de seguridad para sistemas Linux y Unix**.  
Se utiliza para analizar el sistema en busca de **configuraciones inseguras, malas prácticas, vulnerabilidades conocidas y debilidades**.  
Es muy usada en **security auditing**, hardening y cumplimiento de estándares.

No explota vulnerabilidades: **analiza y recomienda mejoras**.
### ¿para qué se usa en una auditoría?

Lynis permite:

- evaluar la **postura de seguridad del sistema**
- detectar **configuraciones débiles**
- comprobar permisos, servicios, usuarios y logs
- apoyar procesos de **hardening** y cumplimiento

Encaja perfectamente en la **fase técnica de una auditoría de seguridad**.
### cómo funciona Lynis

Lynis ejecuta una serie de **checks locales** sobre el sistema:

- sistema operativo
- servicios activos
- configuraciones del kernel
- permisos de archivos
- usuarios y autenticación
- logging y auditoría

Al final genera:

- advertencias
- sugerencias de mejora
- un **security score**
### uso básico de Lynis

- **instalar Lynis (si no está instalado):**
`sudo apt install lynis`

- **ejecutar una auditoría completa:**

`sudo lynis audit system`
👉 Debe ejecutarse como **root** para obtener resultados completos.
### resultados del análisis

Durante y al final del escaneo, Lynis muestra:

- **Warnings** → problemas de seguridad importantes
- **Suggestions** → mejoras recomendadas
- **Hardening index** → nivel general de seguridad

Los resultados también se guardan en:
`/var/log/lynis.log`
### interpretación en una auditoría

- Las **warnings** indican riesgos que deben corregirse con prioridad.
- Las **suggestions** ayudan a mejorar la seguridad progresivamente.
- El informe sirve como base para:
    - recomendaciones
    - planes de remediación
    - auditorías de seguimiento

## PHASE 3: CONDUCT PENETRATION TEST

### Objetivo

Validar la **efectividad de las acciones de remediación** implementadas tras la auditoría de seguridad mediante la realización de un **penetration test controlado**.  
El objetivo es comprobar si las vulnerabilidades detectadas previamente han sido corregidas y si existen **nuevos riesgos**.
### 1. Ejecución (Execution)

Durante esta fase se realizan pruebas técnicas para identificar vulnerabilidades reales explotables.

- **Escaneo de red (Network Scan)**  
    Identificación de hosts activos, puertos abiertos y servicios expuestos.
- **Escaneo de vulnerabilidades (Vulnerability Scanning)**  
    Detección de vulnerabilidades conocidas en sistemas y servicios.
- **Pruebas sobre aplicaciones web (Web Application Testing)**  
    Análisis de aplicaciones web en busca de fallos como inyecciones, XSS, autenticación débil o configuraciones inseguras.

### 2. Validación de la remediación (Validating Remediation)

Se comparan los resultados del pentest actual con auditorías o pruebas anteriores.

- **Comparación de resultados**  
    Verificación de que las vulnerabilidades previas han sido mitigadas correctamente.    
- **Detección de nuevas vulnerabilidades**  
    Identificación de riesgos introducidos tras cambios o actualizaciones en los sistemas.
### 3. Elaboración de informes (Reporting)

Se documentan de forma clara los resultados del penetration test.

- **Resumen ejecutivo (Executive Summary)**  
    Visión general del estado de seguridad para perfiles no técnicos.
- **Metodología (Methodology)**  
    Técnicas, herramientas y alcance del pentest realizado.
- **Hallazgos (Findings)**  
    Vulnerabilidades identificadas, impacto y evidencia.
- **Recomendaciones (Recommendations)**  
    Acciones concretas para corregir y mejorar la seguridad.
