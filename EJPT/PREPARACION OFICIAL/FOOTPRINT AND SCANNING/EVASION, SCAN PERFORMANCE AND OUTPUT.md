-----
## 🛡️ FIREWALL DETECTION AND IDS EVASION (Nmap)

### 1) ¿Qué es un Firewall y qué es un IDS?

**Firewall**

- Sistema que **permite o bloquea** tráfico según reglas (IP, puerto, protocolo, estado, aplicación…).
- Resultado típico para Nmap: puertos pueden aparecer como **open / closed / filtered**.
- Puede ser **stateless** (solo reglas) o **stateful** (entiende “conexiones” TCP).

**IDS/IPS**

- **IDS (Intrusion Detection System):** detecta actividad sospechosa y **alerta**.
- **IPS (Intrusion Prevention System):** además de detectar, puede **bloquear** o **alterar** tráfico.
- Pueden identificar patrones de escaneo (SYN scan, Xmas, etc.) y levantar alarmas aunque el firewall no bloquee.

---
## 2) Detección de firewall con `-sA` (ACK scan)

### ¿Cómo funciona?

- Nmap envía paquetes TCP con el flag **ACK** a un puerto.  
- En TCP “normal”, un ACK **sin haber establecido conexión** es raro.
- Lo importante aquí no es “abrir” el puerto, sino **ver si hay filtrado** en el camino.
### ¿Qué interpreta Nmap?

En un ACK scan, Nmap clasifica puertos como:

- **unfiltered** → el paquete llega y el host responde con **RST**
- **filtered** → **no hay respuesta** (o llega ICMP de filtrado) → suele indicar firewall/filtrado

📌 Ojo: con `-sA` **NO puedes saber si el puerto está open o closed**.  
Porque **open y closed** suelen responder igual (RST) ante un ACK inesperado, dependiendo del stack y el filtrado.

### ¿Para qué se usa entonces?

- Para **mapear reglas de filtrado** (qué puertos están filtrados y cuáles no).
- Para diferenciar:
    - “no responde porque está filtrado” vs “responde pero el puerto está cerrado”
- Muy útil cuando sospechas un **firewall stateful**, especialmente en entornos Windows (donde ICMP puede estar bloqueado y la vida es más triste).

Ejemplo típico:

`nmap -sA -p 80,445,3389 <IP>`

Te ayuda a ver si hay filtrado en puertos clásicos Windows (SMB/RDP).

---
## 4) Fragmentación con `-f` 

### ¿Qué hace exactamente?

- Nmap intenta enviar sondas en **paquetes IP fragmentados**.   
- Es decir, divide un paquete en varios fragmentos para que:
    - algunos dispositivos no puedan inspeccionarlo bien
    - o no reconstruyan (reassemble) correctamente el contenido
### ¿Por qué puede ayudar a evadir?

- Un firewall/IDS necesita **reensamblar** fragmentos para ver el contenido completo.
- Equipos antiguos o mal configurados:
    - inspeccionan solo el primer fragmento
    - fallan reensamblando
    - o aplican reglas incompletas
### Matices reales (importantes)

- Muchos sistemas modernos **bloquean fragmentación** o la manejan perfecto.
- Puede causar:
    - falsos negativos (tú crees que no hay nada, pero el scan fue “roto”)
    - ruido raro (y a veces más sospechoso)
- Alternativa para controlar tamaño:

`nmap --mtu 24 <IP>`

(Controlas la MTU de fragmentación; debe ser múltiplo de 8)

---
## 5) `-D` Decoy scan (señuelos)

`nmap -D RND:10 <IP>`

### ¿Para qué sirve?

- Envía el escaneo haciendo que parezca que viene de **varias IPs** (señuelos).
- Objetivo: **confundir logs** del IDS/firewall o del admin.

### Cómo se interpreta

- El objetivo verá tráfico “de muchas fuentes”, pero **solo una** es la real (la tuya).
- Útil para **ensuciar atribución**, no para “volverte invisible”.

📌 Limitación práctica:

- Si estás en una red donde solo tu IP enruta/responde, el decoy no siempre aporta mucho.
- En entornos monitorizados puede **cantar más** (muchas IPs escaneando a la vez = alerta).

---

## 6) `-g` o `--source-port` (puerto origen)

`nmap -g 53 <IP> # o nmap --source-port 53 <IP>`
### ¿Qué hace?

Fuerza a que Nmap envíe paquetes usando **un puerto origen concreto**.
### ¿Para qué sirve?

- Algunos firewalls tienen reglas del estilo:
    - “permito tráfico si _parece_ DNS (53)”
    - “permito tráfico desde 20/21 (FTP)”
    - “permito tráfico desde 80/443”
- Entonces, usar `-g 53` intenta “disfrazar” el tráfico para pasar filtros **mal configurados**.

📌 Matiz importante:

- Esto no convierte tráfico en DNS real, solo cambia el **source port**.
- En firewalls modernos con inspección stateful / L7, no suele colar.
---
## ⚙️ OPTIMIZANDO NMAP SCANS

**Timing & Performance**

Optimizar un escaneo con Nmap consiste en **controlar la velocidad y el comportamiento** del tráfico para:
- Pasar más **desapercibido**
- Evitar **DoS accidentales**
- Funcionar en **redes lentas o antiguas**
- O, al contrario, **ir rápido** cuando el entorno lo permite

---
## 🐢 ¿Por qué ralentizar un escaneo?

Ralentizar Nmap es útil cuando:
- Hay **IDS/IPS** que detectan escaneos agresivos
- La red es **antigua o inestable**
- No quieres provocar **denegaciones de servicio**
- Estás en una auditoría real (no CTF)

---
## 🚀 ¿Por qué acelerar un escaneo?

Acelerar Nmap es útil cuando:

- Estás en **CTFs / labs**    
- La red es **rápida y controlada**
- Quieres resultados rápidos
- No te importa el ruido

---
## ⏱️ Opciones de Timing & Performance (del `nmap -h`)

### `-T<0-5>` → Plantillas de timing

Controla el **comportamiento general** del escaneo.

|Nivel|Nombre|Uso|
|---|---|---|
|`-T0`|paranoid|Ultra lento, evasión extrema|
|`-T1`|sneaky|Muy lento, bajo ruido|
|`-T2`|polite|Reduce carga en la red|
|`-T3`|normal|Equilibrado (por defecto)|
|`-T4`|aggressive|Rápido, más detectable|
|`-T5`|insane|Muy rápido, muy ruidoso|

---
### `--host-timeout`

`--host-timeout 30m`

Tiempo máximo que Nmap dedicará a **un host** antes de abandonarlo. Útil para evitar quedarse “colgado” en hosts lentos o filtrados.

---

### `--scan-delay`

`--scan-delay 5s`

Introduce un **retardo fijo** entre cada sonda enviada.

Muy útil para:
- Evasión de IDS
- Redes frágiles
- Servicios sensibles

---
### `--min-rate`

`--min-rate 100`

Fuerza un **mínimo de paquetes por segundo**. Acelera el escaneo, aunque aumenta ruido y riesgo de detección.

---
### `--max-rate`

`--max-rate 50`

Limita el **máximo de paquetes por segundo**.

👉 Ideal para:

- No saturar la red
- Evitar DoS
- Escaneos “educados”

---
### `--max-retries`

`--max-retries 3`

Número máximo de **reintentos** por sonda.
👉 Menos retries = más rápido, pero menos fiable.

---
### `--initial-rtt-timeout`

`--initial-rtt-timeout 500ms`

Tiempo inicial de espera para respuestas.
👉 Ajustar esto ayuda en redes lentas o con alta latencia.

---
### `--max-rtt-timeout`

`--max-rtt-timeout 2s`

Tiempo máximo que Nmap esperará una respuesta.
👉 Evita esperas eternas.

---
### `--min-parallelism` / `--max-parallelism`

`--min-parallelism 10 --max-parallelism 100`

Controla cuántas sondas se envían **en paralelo**.
👉 Más paralelismo = más rápido pero más ruidoso.

----
## 📤 NMAP OUTPUT FORMATS (FORMATOS DE SALIDA)

Nmap permite guardar los resultados del escaneo en distintos formatos para:

- Documentar auditorías    
- Automatizar análisis
- Generar reportes
- Reutilizar resultados en otras herramientas

---
### 📄 `-oN` → Normal Output

`nmap -oN resultado.txt <IP>`

- Formato **legible por humanos**
- Igual que la salida por pantalla
- Ideal para:
    - Apuntes
    - Informes rápidos
    - Revisión manual

---
### 🧬 `-oX` → XML Output

`nmap -oX resultado.xml <IP>`

- Formato **XML estructurado**
- Pensado para:
    - Automatización
    - Parsing
    - Integración con otras herramientas
- Muy usado por:
    - Metasploit
    - Nessus
    - Scripts personalizados

---
### 📜 `-oS` → Script Kiddie Output 😈

`nmap -oS resultado.txt <IP>`

- Salida “estilizada”
- **No recomendada**
- Difícil de parsear
- Más estética que útil

---
### 🧾 `-oG` → Grepable Output

`nmap -oG resultado.gnmap <IP>`

- Formato optimizado para **grep, awk, cut**
- Ideal para:
    - Filtrar puertos abiertos
    - Procesar resultados por scripts
- Muy útil en pipelines de terminal

Ejemplo: `grep open resultado.gnmap`

---
### 📦 `-oA` → All Output Formats

`nmap -oA escaneo <IP>`

Genera **todos los formatos importantes a la vez**:

- `escaneo.nmap` → Normal
- `escaneo.xml` → XML
- `escaneo.gnmap` → Grepable

👉 Opción más usada en auditorías reales.

---
## 🧠 Resumen express

|Opción|Formato|Uso|
|---|---|---|
|`-oN`|Texto normal|Lectura humana|
|`-oX`|XML|Automatización|
|`-oS`|Script kiddie|Estético (evitar)|
|`-oG`|Grepable|Filtrado por terminal|
|`-oA`|Todos|Auditorías completas|

---
## 🎯 Consejo práctico

En pentesting real:

`nmap -sS -sV -sC -oA scan <IP>`

Así tienes:
- Resultados claros
- Datos para scripts
- Base para reportes