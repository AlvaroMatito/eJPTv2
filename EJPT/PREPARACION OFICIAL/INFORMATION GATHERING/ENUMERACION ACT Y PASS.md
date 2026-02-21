------
## PARTE 1: ENUMERACIÓN

 La **enumeración** es la fase en la que recopilamos información detallada sobre un sistema, como servicios activos, usuarios, versiones de software y configuraciones internas. También permite obtener datos sobre las **personas que trabajan en la empresa**, como correos electrónicos, cargos o hábitos digitales. Esta información es clave para diseñar ataques de **phishing** o crear **archivos maliciosos** creíbles que aumenten las probabilidades de éxito. Sin una buena enumeración, los ataques suelen ser poco realistas y fáciles de detectar. En el hacking ético, esta fase permite simular amenazas reales de forma precisa y controlada.
 
***¿Qué información estamos buscando?***

**Recolección de información pasiva**

- Identificación de direcciones IP e información DNS.
- Identificación de nombres de dominio y datos de propiedad del dominio.
- Identificación de direcciones de correo electrónico y perfiles en redes sociales.
- Identificación de las tecnologías web utilizadas en los sitios objetivo.    
- Identificación de subdomínios.

**Recolección de información activa**

- Descubrimiento de puertos abiertos en los sistemas objetivo.
- Conocer la infraestructura interna de la red u organización objetivo.
- Enumeración de información de los sistemas objetivo.
 
------
## ENUMERACIÓN PASIVA:
Buscamos principalmente:
- Direcciones IP
- Directorios ocultos a buscadores
- Nombres
- Direcciones de correo electrónico
- Números de teléfono
- Direcciones físicas
- Tecnologías web utilizadas
------
**Host (DNS Lookup):** herramienta para obtener información de un dominio. Permite ver si usa proxy/firewall como Cloudflare (suelen aparecer dos IPs), direcciones IPv4/IPv6, servidores de correo (MX) y DNS asociados.

**Redes sociales:** buscar Facebook, Instagram, LinkedIn, etc. para obtener nombres, correos, ubicaciones y posibles vectores de ingeniería social.

**robots.txt:** archivo que indica a los buscadores qué rutas no indexar. Puede revelar carpetas ocultas, paneles de admin, backups y tecnología usada (ej. WordPress, PHP).

**sitemap.xml:** archivo que muestra la estructura del sitio. Revela rutas, autores, categorías y páginas que a veces no aparecen en la web.

**BuiltWith / Wappalyzer:** herramientas para identificar tecnologías, CMS, frameworks, servidores, plugins y subdominios usados por la web.

**WhatWeb (terminal):** muestra versiones, cabeceras HTTP, CMS, plugins y tecnologías desde consola.

**HTTrack:** permite descargar una web completa para analizar su estructura, archivos, rutas y código offline.  
 - Instalación:`sudo apt-get install webhttrack`
--------
**WHOIS:** protocolo de Internet que consulta bases de datos para obtener información de un dominio. Permite ver el **registrador**, fechas de registro y expiración, **emails y teléfonos de contacto**, datos del propietario (_registrant_), y los **name servers**.

- **Name Server (DNS):** servidores que indican a qué IP apunta un dominio. Básicamente son los que traducen el nombre de la web en una dirección IP.
- **Información del dominio:** muestra datos como el **Domain ID**, el servidor con el que fue registrado, estado del dominio, provincia/estado, país y contactos administrativos.
- **Registrant Contact Information:** información del propietario del dominio (empresa o persona), incluyendo nombre, correo, teléfono y dirección. <-- esto es oro en phising
- **WHOIS online:** también se puede usar desde páginas web sin necesidad de terminal.

------
**NETCRAFT:** herramienta para enumerar información sobre un dominio.

- **Qué muestra:** certificados SSL/TLS, tecnologías usadas, _nameservers_, fechas de registro, sistema operativo, historial de alojamiento e IPs (incluyendo si usa Cloudflare).
- **Vulnerabilidades:** avisa si el sitio usa versiones inseguras (por ejemplo, TLS antiguo).
- **Tecnologías:** detecta _web trackers_, bases de datos y medidas de seguridad.
- **Historial:** permite ver cambios de servidor, IPs antiguas y evolución del sitio.
- **Cómo usarlo:** entrar en **netcraft.com → Services → Internet Data Mining**.
- **Ventaja:** recopila más información que WHOIS y de forma más visual.

----

**Reconocimiento DNS:** fase pasiva donde no hacemos requests directos al servidor. Buscamos información como IPs, servidores de correo, dominios y subdominios.

- **dnsrecon:** herramienta para enumerar registros DNS de un dominio.  
	Busca:
	
	- **A:** IP IPv4 del dominio
	- **AAAA:** IP IPv6
	- **MX:** servidores de correo
	- **NS:** servidores DNS
	- **SOA:** información del dominio (servidor principal, email admin, tiempos)  
	Uso: `dnsrecon -d sitioweb.org`

**DNSDumpster:** herramienta web parecida a dnsrecon pero más visual y organizada.  
Permite ver la jerarquía de la red, si el sitio está protegido por Cloudflare, subdominios y relaciones entre servidores.  
Genera un **gráfico descargable en PNG**, muy útil para auditorías e informes.

----

**Web Application Firewall (WAF):** sistema de seguridad que filtra y bloquea ataques contra aplicaciones web.

- **wafw00f:** herramienta de reconocimiento que detecta si una web está protegida por un WAF y cuál es (Cloudflare, Akamai, F5, etc.).
- **Qué indica:** si existe un WAF y el tipo exacto de protección usada.
- **Uso:** `wafw00f sitioweb.org`
- **Repositorio:** disponible en GitHub.

---

**Subdomain Enumeration (Sublist3r):** técnica para descubrir subdominios de un dominio.

- **Sublist3r:** herramienta de GitHub que enumera subdominios usando múltiples motores de búsqueda y fuerza bruta.
- **Qué hace:** busca subdominios como `mail.`, `admin.`, `dev.`, `api.` etc.
- **Instalación:** `sudo apt-get install sublist3r`
- **Uso:** `sublist3r -d sitioweb.org`
- **Guardar resultados:** usar `-o archivo.txt` para exportar el output.
- **Consejo:** usar VPN para evitar captchas de Google.

----

**Google Dorks:** uso avanzado del buscador con operadores para filtrar información sensible.

- **site:dominio.com** limita los resultados a un único dominio.
- **inurl:admin / inurl:forum** busca palabras dentro de la URL. Útil para encontrar paneles de administracion, foros o rutas con posibles credenciales.
- **Regex (*.ine.com):** permite buscar subdominios filtrados usando expresiones regulares.
- **intitle:admin** busca páginas que tengan “admin” en el título, ideal para localizar paneles de administración.
- **filetype:pdf** muestra archivos de un tipo concreto (PDF, DOC, XLS, etc.).
- **cache:ine.com** muestra versiones antiguas de páginas guardadas en caché.
- **Exploit-DB Google Hacking DB:** en exploit-db.com → google-hacking-database hay muchos ejemplos de dorks.
- **Otros operadores:** también existen `intext:`, `related:`, `define:` y más.

----

**OSINT (Open Source Intelligence):** recopilación de información usando fuentes públicas como Google, redes sociales, bases de datos, foros, WHOIS, DNS, PDFs y herramientas online.

- **Para qué sirve:** obtener datos de empresas o personas sin interactuar directamente con sus sistemas.
- **Ejemplos:** Google Dorks, LinkedIn, Shodan, Netcraft, WHOIS, DNSDumpster.

----

**Email Harvesting (theHarvester):** técnica para recolectar correos, subdominios y nombres desde fuentes públicas.

- **theHarvester:** herramienta de GitHub que busca **emails**, **subdominios** y **nombres** usando motores como Google, Bing, LinkedIn, Shodan, etc.
	- **Para qué sirve:** obtener contactos reales para OSINT, phishing, ingeniería social o reconocimiento.
- **Uso básico:**  
		`theHarvester -d sitioweb.org -b google`
- **Parámetros importantes:**
	- **-d** → dominio objetivo
	- **-b** → motor de búsqueda (google, bing, linkedin, shodan, etc.)
	- **-l** → límite de resultados
	- **-f** → guarda el resultado en archivo
	- **-s** → inicio de resultados
- **Qué obtiene:** correos, subdominíos, hosts y nombres públicos.

-----
## ENUMERACIÓN ACTIVA:

La **enumeración activa** es una fase del proceso de auditoría de seguridad en la que se interactúa directamente con el sistema objetivo para obtener información técnica relevante. A diferencia de la enumeración pasiva, aquí se envían solicitudes, escanéos y consultas controladas al objetivo. Su finalidad es identificar servicios en ejecución, versiones de software, usuarios, recursos accesibles y configuraciones expuestas. Para ello se emplean herramientas como Nmap, Gobuster o enum4linux. Con esta información se evalúan posibles vectores de ataque. Es una etapa clave para comprender la superficie real de exposición del sistema.

-------
### DNS Zone Transfer (AXFR)

Un **DNS Zone Transfer** es un mecanismo legítimo que permite a un servidor DNS secundario copiar toda la información de la zona desde el servidor primario. El problema aparece cuando este proceso no está bien protegido, ya que un atacante puede solicitar la transferencia y obtener **todas las entradas DNS del dominio**, incluyendo subdominios, servidores internos, correos y direcciones IP. Esto proporciona una visión completa de la infraestructura de la organización, facilitando la enumeración de objetivos y posibles vectores de ataque.

---
### ¿Cómo funciona la resolución DNS?

Cuando un sistema quiere traducir un nombre de dominio a una IP, primero consulta su **caché local**. Si no hay respuesta, pregunta al servidor DNS configurado. Este servidor sigue una jerarquía: empieza desde los **servidores raíz (.)**, pasa por los **TLD** (.com, .org, etc.) y finalmente llega al servidor autoritativo del dominio. Cada nivel va redirigiendo la consulta hasta obtener la IP correcta.

---
### Registros DNS más comunes

- **A**: Asocia un dominio con una dirección IPv4.
- **AAAA**: Igual que A, pero para direcciones IPv6.
- **NS**: Indica los servidores DNS autoritativos del dominio.
- **MX**: Define los servidores de correo del dominio.
- **CNAME**: Alias de un dominio hacia otro.
- **TXT**: Información en texto (SPF, DKIM, verificación, etc.).
- **HINFO**: Información del host (hardware y sistema operativo).
- **SOA**: Registro principal de la zona (responsable, serial, tiempos).
- **SRV**: Servicios disponibles y sus puertos (ej: SIP, LDAP).
- **PTR**: Resolución inversa (IP → dominio).

Estos registros revelan cómo está estructurado un dominio y qué servicios expone.

---
### DNS Interrogation

La **interrogación DNS** consiste en enviar consultas directas a los servidores DNS para extraer información sobre el dominio: servidores, subdominios, servicios y configuraciones. Es una parte clave de la enumeración activa en auditorías de seguridad.

---
### Herramientas de enumeración DNS

- **DNSSDumpster**: Obtiene servidores DNS, subdominios, direcciones IP y un mapa visual de la infraestructura.
- **dnsrecon**: Similar a DNSSDumpster, pero desde terminal.
- **dnsenum**: Realiza enumeración activa y fuerza bruta de subdominios.
- **fierce**: Busca subdominios y rangos de IP asociados a un dominio.

Ejemplo:

`fierce -dns zonetransfer.me`


---
### Archivo hosts

El archivo **hosts** asocia manualmente nombres de dominio con direcciones IP. Se usa para resoluciones locales sin consultar a un DNS. Tu definición es correcta: contiene **nombres de host y sus IP correspondientes**.

---
### Comando AXFR

`dig axfr @nsztm1.dig.ninja zonetransfer.me`

Este comando intenta realizar una **transferencia de zona completa**.  
Si tiene éxito, devuelve todos los registros DNS del dominio: subdominios, correos, servidores, etc. Es una fuga crítica de información.

---
### Importancia para la auditoría

Todos los dominios y servicios descubiertos deben comprobarse desde el exterior.  
Si son accesibles, **entran en el alcance (scope) del ataque**, ampliando la superficie vulnerable de la organización.

----
### Descubrimiento de hosts con Netdiscover

Antes de escanear puertos, es necesario identificar qué dispositivos hay en la red. **Netdiscover** es una herramienta que utiliza peticiones **ARP** para descubrir hosts activos en una red local. Se usa, por ejemplo, con:

`netdiscover -i eth0 -r 192.168.2.0/24`

Esto permite obtener las **direcciones IP**, **MAC** y, en algunos casos, el **nombre del dispositivo**. Es muy útil en redes internas donde los hosts no responden a ICMP.

---
### ¿Qué es Nmap?

**Nmap** es una herramienta de escaneo de red utilizada para identificar **puertos abiertos, cerrados o filtrados**, los **servicios** que se ejecutan, sus **versiones**, el **sistema operativo** y posibles vulnerabilidades mediante scripts. Es fundamental en auditorías de seguridad y pruebas de penetración.

---
### Modos de escaneo más comunes

- **-sS**: Escaneo SYN (rápido y sigiloso).
- **-sV**: Detecta versiones de servicios.
- **-sN**: Escaneo NULL (evade ciertos firewalls).
- **-sT** :TCP Connect → Usa la conexión completa. Más detectable.
- **-sA** : ACK Scan → Detecta firewalls y reglas de filtrado.
- **-sW** : Window Scan → Variante del ACK para identificar estados de puertos.
- **-sM** : Maimon Scan → Usa flags FIN/ACK para evadir filtros antiguos.
- **-O**: Detección del sistema operativo.
- **-sU**: Escaneo de puertos UDP.

### ¿Para qué sirve -Pn?

Desactiva la detección de host. Nmap asume que la máquina **está activa** aunque no responda a ping. Es útil cuando el firewall bloquea ICMP.
### ¿Para qué sirve -n?

Evita la resolución DNS. Hace el escaneo **más rápido** y reduce el ruido en red.

---
### Nmap Scripting Engine (NSE)

Nmap permite ejecutar scripts con `--script` para tareas como:

- Enumeración
- Detección de vulnerabilidades
- Fuerza bruta
- Descubrimiento de servicios

Ejemplo:

`--script vuln`

---
### Ejemplos de uso

Escaneo completo de puertos TCP:

`nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn 192.168.111.36 -oG allports`

Escaneo de servicios y scripts:

`nmap -sCV -p 22,80,443 192.168.111.36 -oN targeted`

Escaneo UDP:

`nmap -sU --top-ports 100 --open -T5 -v -n 10.129.22.165`

---
### Escaneos de evasión

- **-sN** → NULL Scan  
    Paquete sin flags TCP.
- **-sF** → FIN Scan  
    Solo flag FIN.
- **-sX** → Xmas Scan  
    Flags FIN, PSH y URG.

Estos intentan evadir firewalls mal configurados.

---
### Escaneos UDP

- **-sU** → UDP Scan  
    Escaneo de servicios UDP (DNS, SNMP, TFTP, etc.). 

---
### Escaneos especiales

- **-sI** → Idle Scan  
    Escaneo anónimo usando un “zombie”.
- **-sY** → SCTP INIT  
    Para protocolos SCTP.
- **-sZ** → SCTP COOKIE  
    Variante de SCTP.

---
### Escaneos de descubrimiento

- **-sn** → Host Discovery  
    Solo detecta hosts activos, no puertos.
- **-Pn** → No ping  
    Asume que el host está activo.
    
---
### Escaneos de información

- **-sV** → Detección de versiones
- **-O** → Detección de sistema operativo
- **-sC** → Scripts por defecto
- **-A** → Escaneo agresivo (OS + scripts + versiones)

---
### Escaneos de puertos

- **-p-** → Todos los puertos
- **--top-ports 100** → Puertos más comunes
- **--open** → Solo puertos abiertos

---
### Escaneos de velocidad

- **-T0** a **-T5**  
	|Nivel|Nombre|Agresividad|Uso típico|
	|**-T0**|Paranoid|Muy baja|Máxima evasión, extremadamente lento|
	|**-T1**|Sneaky|Baja|Evasión con algo más de velocidad|
	|**-T2**|Polite|Media-baja|No satura la red|
	|**-T3**|Normal|Media|Equilibrado|
	|**-T4**|Aggressive|Alta|Rápido, ruidoso|
	|**-T5**|Insane|Muy alta|Ultra rápido, muy detectable|

PRUEBA: 
- encontrar el robots.txt
- usar whatweb 
- usar wpscan -> como es wordpress buscamos directorios interesantes 
- usar gobuster gobuster dir -u http://target.ine.local -w /usr/share/wordlists/dirb/big.txt -x bak,old,zip,sql,~,php~ -> para buscar bakups
- usar httrack para descargar la web y ver mirroring en archivos con nombres diferentes -> xlpnoseque 
