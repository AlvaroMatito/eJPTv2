-----
## **HTTP (Hypertext Transfer Protocol)**

- **HTTP (Hypertext Transfer Protocol)** es un **protocolo de la capa de aplicación**, **sin estado (stateless)**, utilizado para la transmisión de recursos como datos de aplicaciones web y funciona sobre **TCP**.
- Fue diseñado específicamente para la comunicación entre **navegadores web** y **servidores web**.
- HTTP utiliza una arquitectura **cliente–servidor**, donde:
    - El **cliente** es el navegador web.
    - El **servidor** es el servidor web.
- Los recursos se identifican de forma única mediante una **URL / URI**.
- HTTP tiene **dos versiones principales**:
    - **HTTP/1.0**
    - **HTTP/1.1**
- **HTTP/1.1** es la versión más utilizada y mejora a HTTP/1.0 en aspectos como:
    - **Reutilización de la misma conexión** (keep-alive)
    - **Solicitud de múltiples recursos (URI)** sobre una única conexión
## **HTTP Protocol Basics**

Durante la comunicación HTTP, el cliente y el servidor **intercambian mensajes**, que se clasifican principalmente en:
- **HTTP Requests (Solicitudes)**
- **HTTP Responses (Respuestas)**

El flujo básico es el siguiente:
1. El **cliente envía una solicitud HTTP** al servidor.
2. El **servidor procesa la solicitud**.
3. El **servidor devuelve una respuesta HTTP** al cliente.
## **Estructura de un mensaje HTTP**

Un mensaje HTTP (request o response) está compuesto por dos partes principales:
### **Cabeceras (Headers)**

- Contienen **metadatos** sobre la comunicación.
- Incluyen información como:
    - Tipo de contenido
    - Cookies
    - Autenticación
    - User-Agent
    - Longitud del contenido
### **Cuerpo (Body)**

- Contiene los **datos reales** enviados en la petición o respuesta.
- Puede incluir:
    - Datos de formularios
    - JSON
    - XML
    - Archivos
    - Credenciales (en algunos casos)

----
## **HTTP REQUEST COMPONENTS**

Una **HTTP Request** está compuesta por varias partes fundamentales que permiten al servidor entender **qué recurso se solicita**, **cómo se solicita** y **qué datos se envían**.
## **Request Line**

La **Request Line** es la **primera línea** de una petición HTTP y contiene **tres componentes principales**:
- **Método HTTP**  
    Indica el tipo de petición que se está realizando.  
    Ejemplos: `GET`, `POST`, `PUT`, `DELETE`.
- **URL**  
    Es la **dirección del recurso** al que el cliente desea acceder.
- **Versión de HTTP**  
    Indica la **versión del protocolo HTTP** que se está utilizando (por ejemplo, `HTTP/1.1`)
    
Ejemplo:

`GET /login HTTP/1.1`
## **Request Headers**

Las **cabeceras de la petición** proporcionan **información adicional** sobre la solicitud.  
Algunas cabeceras comunes incluyen:
- **User-Agent**  
    Información sobre el cliente que realiza la petición (navegador, sistema operativo, etc.).
- **Host**  
    Nombre del host o dominio del servidor al que se envía la petición.
- **Accept**  
    Tipos de contenido que el cliente puede manejar en la respuesta (HTML, JSON, XML, etc.).
- **Authorization**  
    Credenciales de autenticación, si la aplicación lo requiere.
- **Cookie**  
    Información almacenada en el lado del cliente que se envía al servidor en cada petición para mantener sesión o estado.
## **Request Body**

Algunos métodos HTTP, como **POST** o **PUT**, incluyen un **cuerpo de la petición** donde se envían datos al servidor.
- El body suele contener:
    - Datos de formularios
    - JSON
    - XML
- Se utiliza para:
    - Envío de credenciales
    - Creación o modificación de recursos
    - Envío de información compleja
Ejemplo:

`{   "username": "admin",
`	"password": "admin123" 
`}`

## **HTTP REQUEST METHODS**

Los **métodos HTTP** proporcionan una forma estandarizada para que **clientes y servidores** se comuniquen e interactúen con los recursos en la web.  
La elección del método adecuado depende del **tipo de operación** que se quiera realizar sobre un recurso.

- **GET** es el **método por defecto** cuando realizas una solicitud a una aplicación web (por ejemplo, al visitar una URL en el navegador).

### **Métodos HTTP comunes**

|**Método**|**Función**|
|---|---|
|**GET**|Se utiliza para **recuperar datos del servidor**. Solicita el recurso indicado en la URL y **no modifica el estado del servidor**. Es **seguro e idempotente**, lo que significa que repetir la misma petición no debería producir efectos secundarios.|
|**POST**|Se utiliza para **enviar datos al servidor para su procesamiento**. Los datos se envían normalmente en el **cuerpo de la petición**. **Puede cambiar el estado del servidor** y **no es idempotente**.|
|**PUT**|Se utiliza para **crear o actualizar un recurso** en la URL especificada. **Reemplaza completamente el recurso** con la información enviada en el cuerpo. Si el recurso no existe, puede crearlo.|
|**DELETE**|Se utiliza para **eliminar un recurso** del servidor. Tras una petición DELETE exitosa, el recurso **deja de estar disponible** en esa URL.|
|**PATCH**|Se utiliza para **modificar parcialmente un recurso**. A diferencia de PUT, **solo actualiza campos específicos**en lugar de reemplazar el recurso completo.|
|**HEAD**|Similar a GET, pero **solo devuelve las cabeceras de la respuesta**, no el cuerpo. Se usa para comprobar la existencia de un recurso o metadatos como fechas de modificación.|
|**OPTIONS**|Se utiliza para **obtener información sobre las opciones de comunicación** disponibles para un recurso, como **qué métodos HTTP están permitidos**.|

## **HTTP RESPONSES**

Cuando el servidor recibe una petición HTTP, responde con una **HTTP Response**, que también está compuesta por cabeceras y un cuerpo.
## **Response Headers**

Las **cabeceras de la respuesta** proporcionan información adicional sobre la respuesta enviada por el servidor. Algunas cabeceras comunes son:
- **Content-Type**  
    Indica el tipo de contenido devuelto (HTML, JSON, XML, etc.).
- **Content-Length**  
    Tamaño del cuerpo de la respuesta en bytes.
- **Set-Cookie**  
    Se utiliza para **establecer cookies en el navegador del cliente**, que se enviarán en peticiones posteriores.
- **Cache-Control**  
    Define las **directivas de cacheo** y el comportamiento de la caché.
## **Response Body**

El **cuerpo de la respuesta** contiene el **contenido real** devuelto por el servidor.
Ejemplos:
- HTML de una página web
- Datos en JSON de una API
- XML
- Archivos
## **Códigos de estado HTTP comunes**

|**Código**|**Significado**|
|---|---|
|**200 OK**|La solicitud se realizó correctamente y el servidor devolvió el contenido esperado.|
|**301 Moved Permanently**|El recurso se ha movido **de forma permanente** a otra URL.|
|**302 Found**|Redirección **temporal** a otra URL (hoy en día suelen preferirse 303 o 307).|
|**400 Bad Request**|La solicitud es incorrecta o está mal formada (error del cliente).|
|**401 Unauthorized**|Se requiere autenticación válida para acceder al recurso.|
|**403 Forbidden**|El servidor entiende la petición, pero el cliente **no tiene permisos**.|
|**404 Not Found**|El recurso solicitado **no existe**.|
|**500 Internal Server Error**|Error interno del servidor al procesar la solicitud.|

## **Directivas HTTP Cache-Control**

|**Directiva**|**Significado**|
|---|---|
|**Public**|La respuesta puede almacenarse en caché por **cualquier caché intermedia** y compartirse entre usuarios.|
|**Private**|La respuesta es para **un único usuario** y no debe almacenarse en cachés intermedias.|
|**no-cache**|La respuesta **puede almacenarse**, pero debe **revalidarse con el servidor** antes de usarse.|
|**no-store**|La respuesta **no debe almacenarse en ningún sitio**, ni en caché ni en el navegador.|
|**max-age=<segundos>**|Define el **tiempo máximo** que una respuesta puede permanecer en caché antes de revalidarse.|

-----
HTTPS 

- **Ahora que ya entiendes cómo funciona HTTP**, vamos a explorar **cómo se protege/asegura**.
- **Por defecto, las peticiones HTTP se envían en texto plano**, por lo que pueden ser **interceptadas o manipuladas fácilmente** por un atacante durante el trayecto hasta su destino.
- Además, **HTTP no proporciona una autenticación fuerte** entre las dos partes que se comunican (cliente y servidor).
- **HTTPS (Hypertext Transfer Protocol Secure)** es una versión segura del protocolo HTTP, utilizada para **transmitir datos entre el navegador del usuario y un sitio web o aplicación web**.
- **HTTPS añade una capa extra de seguridad** cifrando los datos que se transmiten por Internet, lo que los hace más seguros y los protege frente a **accesos no autorizados e interceptaciones**.
- - **HTTPS también se conoce comúnmente como HTTP Secure (HTTP seguro).**
- **HTTPS es la forma recomendada de usar y configurar HTTP**, y consiste en ejecutar HTTP **sobre SSL/TLS**.
- **SSL (Secure Sockets Layer)** y **TLS (Transport Layer Security)** son **protocolos criptográficos** utilizados para proporcionar **comunicación segura** a través de una red informática, normalmente Internet.
- Son **esenciales para establecer una conexión segura y cifrada** entre el navegador o la aplicación de un usuario y un servidor web.

**Ventajas de HTTPS**

- **Cifrado de los datos en tránsito**: Uno de los principales beneficios de HTTPS es el cifrado de los datos durante la transmisión. Cuando los datos se envían a través de una conexión HTTPS, se cifran utilizando algoritmos criptográficos fuertes. Esto garantiza que, incluso si un atacante intercepta los datos mientras están en tránsito, no pueda descifrarlos ni leer su contenido.
- **Protección contra la interceptación (eavesdropping)**: HTTPS evita que partes no autorizadas espíen los datos intercambiados entre el navegador del usuario y el servidor web. Esto es especialmente crucial cuando los usuarios introducen información sensible, como credenciales de inicio de sesión, números de tarjetas de crédito o datos personales.

**Consideraciones de seguridad de HTTPS**

- **HTTPS no protege contra fallos de la aplicación web**: diversos ataques a aplicaciones web seguirán funcionando independientemente del uso de SSL/TLS. (Ataques como **XSS** y **SQLi** seguirán siendo efectivos).
- **La capa adicional de cifrado solo protege los datos intercambiados entre el cliente y el servidor**, pero **no detiene ataques dirigidos contra la propia aplicación web**.

----
## **WEBSITE CRAWLING & SPIDERING**

## **CRAWLING**

El **crawling** es el proceso de **navegar manual o semiautomáticamente** por una aplicación web, siguiendo enlaces, enviando formularios e iniciando sesión (cuando es posible), con el objetivo de **mapear y catalogar la aplicación web** y sus **rutas de navegación internas**.

El crawling suele ser una técnica **pasiva**, ya que la interacción con el objetivo se realiza únicamente a través de **recursos públicamente accesibles**, sin forzar comportamientos anómalos.
Para facilitar este proceso, podemos utilizar el **crawler pasivo de Burp Suite**, que permite:
- Mapear la aplicación web
- Identificar rutas, parámetros y endpoints
- Comprender mejor cómo está estructurada la aplicación y cómo funciona
## **SPIDERING**

- **El spidering** es el proceso de **descubrir automáticamente nuevos recursos (URLs)** dentro de una aplicación o sitio web.
- Normalmente comienza con una lista inicial de URLs objetivo, conocidas como **seeds (semillas)**.
- A partir de estas semillas, el **spider**:
    - Visita las URLs iniciales
    - Identifica los hipervínculos presentes en las páginas
    - Añade las nuevas URLs descubiertas a la lista
    - Repite el proceso de forma **recursiva**

- El spidering puede ser bastante **ruidoso**, ya que genera un gran número de solicitudes automáticas al servidor.  
    Por este motivo, se considera una técnica de **recolección activa de información**.    
- Podemos utilizar el **Spider de OWASP ZAP** para **automatizar el spidering** de una aplicación web, permitiendo:
    - Mapear la estructura completa de la aplicación
    - Descubrir recursos ocultos o no enlazados directamente
    - Comprender mejor el funcionamiento interno de la aplicación

----
## **CTF – Resolución**

## **Flag 1 – Path Traversal**

Vulnerabilidad de **Path Traversal** explotada a través de un parámetro vulnerable:

`file=../../../../../flag.txt`

→ Se accede a un archivo fuera del directorio permitido y se obtiene **flag1**.
## **Flag 2 – Directory Enumeration**

Enumeración de directorios con **gobuster**:

`gobuster dir -u http://target.ine.local -w wordlist.txt`

→ Se descubre el directorio oculto:

`/secured/`

Acceso directo al archivo:

`/secured/flag.txt`

→ Se obtiene **flag2**.
## **Flag 3 – Fuerza bruta en formulario HTTP**

Ataque de fuerza bruta con **Hydra** contra un formulario de login:

`hydra -L users.txt -P password.txt target.ine.local http-post-form "/login:username=^USER^&password=^PASS^:F=Invalid username or password"`

Credenciales válidas obtenidas:

`guest : butterfly1`

→ Acceso exitoso y obtención de **flag3**.
## **Flag 4 – SQL Injection en login**

Inyección SQL en el campo de autenticación:

`' or 1=1 --`

→ Bypass del login  
→ Acceso como **admin**  
→ Se obtiene **flag4**.

---