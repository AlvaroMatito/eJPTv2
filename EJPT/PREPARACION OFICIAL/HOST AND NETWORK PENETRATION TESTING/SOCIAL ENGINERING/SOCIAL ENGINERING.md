-----
introduccion
en el contexto de penetration testing social enginering is a technique used to manipulate individuals or employes within an organization to gain unauthorized acces to sensitive information, systems or facilities.

it exploits hyman psycology, trust, and vulnerabilities to deceive targets into performing actions that compromise security, either through information disclosure or by performing specific actions that may seem innocuous at first glance

social enginering attacks aim to byupass technical controls by targeting the weakest link in the chain the human element.

the premise of social engirering is to exploit th ehuman element, in other words, putting people or employees in situations where they will rely on their bases instincts and most common forms of social interaction like:
- the desire to be helpful
- the tendency to trust people
- the desire for approval
- the fear of getting in trouble
- avoiding conflict or arguments

SOCIAL ENGINERING && SOCIAL MEDIA

the adventage and adoption of social networking as a form of communication has vastly improved the ability and effectiveness og attackers (likewise pentesters) to perform social engineering attacks as employees/targets can be easily contacted by anyone in the world with ease.

furthermore, social networks hava also led to the rise of employees adverttly/inadvertently exposing a lot of private information that can be used by attackers in aid of their social engineering attasks (emails, phone numbres, addresses, etc)

tipos de ataques:
 |**Técnica**|**Descripción**|
|---|---|
|**Phishing**|Correos electrónicos, mensajes o sitios web engañosos diseñados para inducir a las víctimas a revelar información confidencial, como contraseñas, credenciales de cuentas o datos financieros.|
|**Spear Phishing**|Ataques de phishing dirigidos y personalizados para individuos o grupos específicos dentro de una organización, utilizando información o contexto personalizado para aumentar la credibilidad.|
|**Vishing (Phishing por Voz)**|Ataques de phishing realizados mediante llamadas telefónicas o mensajes de voz, en los que los atacantes suplantan a entidades legítimas (por ejemplo, soporte técnico o bancos) para extraer información sensible o manipular a la víctima.|
|**Smishing (Phishing por SMS)**|Ataques de phishing realizados a través de SMS o mensajes de texto, donde se engaña a las víctimas para que hagan clic en enlaces maliciosos o proporcionen información sensible haciéndose pasar por entidades de confianza.|
|**Pretexting**|Creación de un pretexto o escenario falso para ganarse la confianza de la víctima y extraer información sensible, a menudo suplantando a figuras de autoridad, compañeros de trabajo o proveedores de servicios.|
|**Baiting**|Atracción de las víctimas para que realicen una acción específica (por ejemplo, hacer clic en un enlace malicioso o abrir un archivo malicioso) ofreciendo incentivos llamativos como software gratuito, premios u oportunidades laborales.|
|**Tailgating**|Acceso físico no autorizado a áreas restringidas siguiendo a personas autorizadas, aprovechando normas sociales o la cortesía para entrar en instalaciones seguras sin autenticación adecuada.|

phising:
phising is one of the mos prevelant and effective social engineering attaacks used in penetration testing and red teaming. it typicaly involves the following steps.

1.Planning &Reconnaissance: Attackers research the target organization to identify potential targets, gather information about employees, and understand the organizations communication channels and protocols

2.Message Crafting: Attackers create deceptive emails or messages designed to mimic legitimate communications from trusted sources, such as colleagues, IT deparments, or financial institutions. These messages often include urgent or compelling languages to evoke a sense of urgency or fear.

3.delivery: attackers send phising emails or messages to targeted individuals within the organization, using techniques to bypass spam filters and security controls. They may also levereage social engineering tactics to increase the likelihood of recipients openning the message.

4.Deception & Manipulation: the phising messages contain malicious links, attachments, or requeest for sensitive information. Recipients are deceived into clicking on links, downloading attachments, or providing login credentials under false pretenses.

5.Exploitation: Once the victim interacts with the phissing message, attackers exploit culnerabilities in the targets systems or applications to gain unauthorized access, install malware or steal sensitive information.

SPEAR PHISING:
spear phising is a targetd form of phising attack that tailors malicious emails or messages to specific individuals or groups within an organization.

unlike tradicional phising attaks, which cast a wide net and aim to deceive as many recipients as possible, speat phishing attacks are haghly personalized and customized to exploit the unique characteristics, nterests, and relationships of th intended targets.

1.Target Selection & research: attackers carefully select their targets based on specific criteria, such as job roles, deparments, or organizational hierarchies.
extensive reconnaissance is conducted to gather information about the targets, including names, job titles, roles, responsabilities work relationships and personal interest.
publicly available sources, social media profiles, corporate directories, and leadked data may be mined to compile detailed profiles of targets.

2.Message Tailoring: using the gathered information, attackers craft highly personalized and convincing emails or messages designed to appear legitimate and trustworthy
the content of the messages may reference recent events, projects, or activities relevant to the targets role or interests to enhance credibility
Attackers may impersonate trusted individuals, such as collegues, supervisors, or external partners, to encrease the likelihood of the targets openning the messages and taking the desired actions.

3.Delivery: spear phising messages are delivered to the targeted individuals via email, social media, instant messaging platforms or other communication channels.
attackers employ tactics to bypass email security filters and anti-phising mechanisms, such as using compromised or spoofed email accounts, exploiting zero-day vulnerabilities, or levearaging trusted third-party sevices.

## **Introducción**

En el contexto del **penetration testing**, la **ingeniería social** es una técnica utilizada para **manipular a individuos o empleados** dentro de una organización con el objetivo de **obtener acceso no autorizado** a información sensible, sistemas o instalaciones.
La ingeniería social **explota la psicología humana**, la **confianza** y las **vulnerabilidades humanas** para engañar a los objetivos y lograr que realicen acciones que **comprometen la seguridad**, ya sea mediante la **divulgación de información**o la ejecución de acciones que **parecen inofensivas a primera vista**.
Los ataques de ingeniería social buscan **evadir los controles técnicos** atacando el **eslabón más débil de la cadena: el factor humano**.

La premisa de la ingeniería social es **explotar el comportamiento humano**, es decir, colocar a las personas o empleados en situaciones en las que recurren a sus **instintos básicos** y a las **formas más comunes de interacción social**, como:
- El deseo de ayudar
- La tendencia a confiar en otras personas
- El deseo de aprobación
- El miedo a meterse en problemas
- Evitar conflictos o discusiones
## **Ingeniería Social y Redes Sociales**

La **adopción masiva de las redes sociales** como medio de comunicación ha **mejorado enormemente la capacidad y efectividad** de los atacantes (y también de los pentesters) para llevar a cabo **ataques de ingeniería social**, ya que los empleados u objetivos pueden ser **contactados fácilmente desde cualquier parte del mundo**.

Además, las redes sociales han provocado que muchos empleados **expongan de forma consciente o inconsciente información privada**, que puede ser utilizada por atacantes para **facilitar ataques de ingeniería social**, como por ejemplo:

- Direcciones de correo electrónico
- Números de teléfono
- Direcciones físicas
- Información laboral y personal
## **Tipos de ataques de Ingeniería Social**

|**Técnica**|**Descripción**|
|---|---|
|**Phishing**|Correos electrónicos, mensajes o sitios web engañosos diseñados para inducir a las víctimas a revelar información confidencial, como contraseñas, credenciales de cuentas o datos financieros.|
|**Spear Phishing**|Ataques de phishing dirigidos y personalizados a individuos o grupos específicos dentro de una organización, utilizando información contextual para aumentar la credibilidad.|
|**Vishing (Phishing por voz)**|Ataques realizados mediante llamadas telefónicas o mensajes de voz, en los que los atacantes suplantan a entidades legítimas (soporte técnico, bancos, etc.) para obtener información sensible o manipular a la víctima.|
|**Smishing (Phishing por SMS)**|Ataques realizados a través de SMS o mensajes de texto, engañando a las víctimas para que hagan clic en enlaces maliciosos o revelen información sensible.|
|**Pretexting**|Creación de un escenario o pretexto falso para ganarse la confianza de la víctima y extraer información sensible, suplantando a figuras de autoridad, compañeros o proveedores.|
|**Baiting**|Atracción de las víctimas para que realicen una acción concreta (abrir un archivo, hacer clic en un enlace) ofreciendo incentivos llamativos como software gratuito, premios u ofertas laborales.|
|**Tailgating**|Acceso físico no autorizado a áreas restringidas siguiendo a personas autorizadas, aprovechando la cortesía o normas sociales.|

## **Phishing**

El **phishing** es uno de los **ataques de ingeniería social más comunes y efectivos** utilizados en **penetration testing y red teaming**. Normalmente sigue los siguientes pasos:
### **1. Planificación y Reconocimiento**

Los atacantes investigan la organización objetivo para:
- Identificar posibles víctimas
- Obtener información sobre empleados
- Comprender los canales de comunicación y protocolos internos
### **2. Creación del mensaje**

Los atacantes crean **correos o mensajes engañosos** que imitan comunicaciones legítimas de:
- Compañeros de trabajo
- Departamentos de IT
- Entidades financieras

Estos mensajes suelen incluir **lenguaje urgente o alarmista** para generar **miedo o urgencia**.
### **3. Entrega**

Los correos o mensajes de phishing se envían a individuos específicos, utilizando técnicas para:
- Evadir filtros de spam
- Sortear controles de seguridad
- Aumentar la probabilidad de que el mensaje sea abierto
### **4. Engaño y Manipulación**

Los mensajes contienen:
- Enlaces maliciosos
- Archivos adjuntos maliciosos
- Solicitudes de información sensible

La víctima es engañada para **hacer clic, descargar archivos o introducir credenciales**.
### **5. Explotación**

Una vez que la víctima interactúa:
- Se explotan vulnerabilidades del sistema
- Se instala malware
- Se roba información sensible
- Se obtiene acceso no autorizado
## **Spear Phishing**

El **spear phishing** es una forma **dirigida y altamente personalizada** de phishing, orientada a **individuos o grupos específicos** dentro de una organización.
A diferencia del phishing tradicional, que busca engañar al mayor número posible de personas, el spear phishing es **mucho más selectivo**, preciso y efectivo.
### **1. Selección del objetivo y Reconocimiento**

Los atacantes seleccionan cuidadosamente a sus objetivos en función de:
- Rol laboral
- Departamento
- Jerarquía organizativa

Se realiza una **recolección exhaustiva de información**, como:
- Nombre y cargo
- Responsabilidades
- Relaciones laborales
- Intereses personales

Las fuentes incluyen:
- Redes sociales
- Directorios corporativos
- Información pública o filtrada
### **2. Personalización del mensaje**

Con la información recopilada, los atacantes crean **mensajes altamente creíbles**, que:
- Hacen referencia a proyectos reales
- Mencionan eventos recientes
- Coinciden con los intereses del objetivo

Pueden **suplantar a compañeros, supervisores o socios externos** para aumentar la credibilidad y lograr que el objetivo **abra el mensaje y actúe**.
### **3. Entrega**

Los mensajes de spear phishing se envían mediante:
- Correo electrónico
- Redes sociales
- Mensajería instantánea
- Otros canales de comunicación

Los atacantes emplean técnicas para evadir defensas, como:
- Cuentas comprometidas o suplantadas
- Servicios de terceros legítimos
- Vulnerabilidades zero-day
- Técnicas avanzadas de evasión de filtros anti-phishing

----
## **PRETEXTING**

El **pretexting** es el proceso de **crear un pretexto o escenario falso** con el objetivo de **ganarse la confianza del objetivo**y **extraer información sensible**.  
Esto puede implicar la **suplantación de figuras de autoridad**, compañeros de trabajo o **proveedores de servicios**, manipulando a la víctima para que **revele datos confidenciales**.
Dicho de forma simple: consiste en **poner a una persona o empleado en una situación familiar y creíble** para conseguir que **divulgue información**.
A diferencia de otras formas de ingeniería social que se basan principalmente en el **engaño directo o la coerción**, el pretexting se apoya en la **creación de una narrativa o contexto falso**, diseñado específicamente para **establecer credibilidad y generar confianza** en el objetivo.
## **Características del Pretexting**

- **Falso pretexto:**  
    El atacante crea una **historia ficticia** para engañar al objetivo y hacerle creer que la interacción es **legítima y fiable**.  
    Este pretexto suele implicar la **suplantación de alguien con autoridad, conocimientos técnicos o una razón válida** para solicitar información o ayuda.
- **Establecimiento de confianza:**  
    El atacante utiliza el pretexto para **crear rapport y generar confianza** con la víctima.  
    Puede emplear técnicas de ingeniería social como **imitar el lenguaje, el tono o el comportamiento** del objetivo para generar **familiaridad y conexión**.
- **Manipulación emocional:**  
    El pretexting suele explotar **emociones humanas** como la curiosidad, el miedo, la urgencia o la empatía.  
    Al apelar a estas emociones, el atacante influye en la **toma de decisiones** del objetivo y aumenta la **probabilidad de que coopere**.
- **Obtención de información:**  
    Una vez establecida la confianza, el atacante intenta **extraer información sensible o privilegios de acceso**.  
    Esto se hace bajo la apariencia de una **necesidad legítima**, una incidencia o una situación urgente.
- **Mantenimiento de la coherencia:**  
    Para que el engaño funcione, el atacante mantiene el **pretexto coherente y creíble** durante toda la interacción.
- **Planificación e improvisación:**  
    Esto requiere **investigación previa**, planificación cuidadosa y la capacidad de **adaptarse a las respuestas del objetivo** sin perder credibilidad.
## **Ejemplos de Pretexting**

- **Tech Support Scam:**  
    El atacante se hace pasar por personal de soporte técnico y afirma que existe un problema urgente en el sistema del usuario, solicitando credenciales o acceso remoto para “solucionarlo”.
- **Job Interview Scam:**  
    Se simula una oferta de empleo o un proceso de selección para obtener información personal, documentos, credenciales o incluso pagos bajo excusas administrativas.
- **Emergency Situation:**  
    El atacante crea una situación de emergencia (problema legal, incidente de seguridad, fallo crítico) para generar **urgencia y presión**, reduciendo la capacidad crítica de la víctima.
## **Plantillas / Ejemplos de Pretexting**

### **Actualización del Departamento de IT Corporativo**

- **Pretexto:**  
    El atacante se hace pasar por un miembro del departamento de IT de la empresa y envía un correo electrónico a los empleados afirmando que el sistema de correo está siendo actualizado.  
    El mensaje indica que deben hacer clic en un enlace para actualizar la configuración y evitar interrupciones del servicio.
- **Objetivo:**  
    Engañar a los empleados para que accedan al enlace malicioso, que los redirige a un sitio de phishing donde se les solicita introducir sus credenciales de correo electrónico, permitiendo al atacante robar la información de inicio de sesión.
### **Ejemplo de plantilla (correo de pretexting)**

`<!-- PRETEXT OVERVIEW: Credential capture. $organization: Target organization. $evilurl: URL to cloned Office 365 portal. $evildomain: Spoofed domain.  Can be sent as helpdesk@domain.com. Don't forget to setup the mailbox for user replies! -->  <b>Subject: New Webmail - Office 365 Rollout</b> <br> <br> Dear colleagues, <br> <br> In an effort to continue to bring you the best available technology, $organization has implemented the newest version of Microsoft's Office 365 Webmail. Your existing emails, contacts, and calendar events will be seamlessly transferred to your new account. <br> <br> Visit the [new webmail website]($evilurl) and login with your current username and password to confirm your upgraded account. <br> <br> If you have additional questions or need clarification, please contact the Help Desk at helpdesk@$evildomain. <br> <br> Thank you,`
## **Librería de pretextos**

Repositorio con **plantillas y ejemplos educativos** de pretexting:  
**[http://github.com/L4bF0x/PhisingPretext/tree/master](https://github.com/L4bF0x/PhishingPretexts/tree/master/)**

---
## **PHISHING CON GOPHISH**

**GoPhish** es un **framework open source** diseñado para **pentesters** con el objetivo de **simular ataques de phishing**contra sus propias organizaciones.

Proporciona una **plataforma fácil de usar** para **crear, ejecutar y analizar campañas de phishing**, permitiendo evaluar la **susceptibilidad de una organización** a este tipo de ataques y **mejorar su postura de seguridad**.
## **Características de GoPhish**

- **Creación de campañas**  
    GoPhish permite crear **campañas de phishing personalizadas**, adaptadas a objetivos y destinatarios específicos.  
    Es posible lanzar **múltiples campañas** usando diferentes plantillas, contenidos de correo y listas de objetivos.
- **Editor de plantillas de correo electrónico**  
    Incluye un editor integrado **WYSIWYG (What You See Is What You Get)** que facilita el diseño de correos con **apariencia profesional**, imitando comunicaciones legítimas.
- **Gestión de objetivos**  
    Permite gestionar y segmentar listas de objetivos por **departamento, rol o ubicación**, posibilitando campañas **dirigidas y realistas**.
- **Creación de páginas de aterrizaje**  
    GoPhish permite crear **landing pages** que imitan portales de login o sitios legítimos.  
    Estas páginas pueden configurarse para **capturar credenciales**, información personal u otros datos sensibles.
- **Seguimiento y generación de informes**  
    Ofrece **monitorización en tiempo real** del progreso de las campañas:  
    aperturas de correos, clics en enlaces y envío de datos.  
    Incluye **informes detallados** para análisis posterior.
- **Programación y automatización**  
    Permite **programar campañas** para fechas y horas concretas o configurar **campañas recurrentes** para evaluaciones continuas.
## **Referencias y recursos**

- Sitio web: [https://getgophish.com/](https://getgophish.com/)
- Repositorio GitHub: [https://github.com/gophish/gophish](https://github.com/gophish/gophish)
- Guía de instalación: [https://docs.getgophish.com/user-guide/installation](https://docs.getgophish.com/user-guide/installation)
## **DEMO**

**Credenciales de acceso:**
- Usuario: `admin`
- Contraseña: `phishingpasswd`

Tras iniciar sesión correctamente, se accede al **dashboard de GoPhish**.
## **Paso 3: Configuración de un Sending Profile**

Una vez iniciado el servidor de GoPhish y accedido al panel de administración, el primer paso es **configurar un perfil de envío**.
1. Acceder al menú **Sending Profiles** desde la barra lateral.
2. Hacer clic en **New Profile**.
3. Configurar el perfil con los siguientes valores:
    - **Name:** red
    - **From:** info `<support@demo.ine.local>`
    - **Host:** localhost:25
    - **Username:** red@demo.ine.local
    - **Password:** penetrationtesting
4. Probar la configuración usando **Send Test Email**.
5. Enviar el correo de prueba a: `victim@demo.ine.local`.

La recepción correcta del correo (verificada desde Thunderbird) confirma que el **sending profile funciona correctamente**.

Finalmente, guardar el perfil.
## **Paso 4: Configuración de una Landing Page**

Para una campaña exitosa es necesario configurar una **página de aterrizaje** donde los objetivos serán redirigidos para la **captura de credenciales**.
1. Acceder al menú **Landing Pages**.
2. Hacer clic en **New Page**.
3. Asignar un nombre a la página.
4. Importar un sitio existente usando **Import Site**.
5. Especificar la URL: `http://localhost:8080`.

Tras la importación:
- Se puede modificar el diseño.
- Se configura la captura de **todos los datos enviados**.
- Se redirige al usuario a la página original (`localhost:8080`).

Guardar la landing page.
## **Paso 5: Configuración de una plantilla de correo**

1. Acceder al menú **Email Templates**.
2. Hacer clic en **New Template**.
3. Copiar el contenido del archivo **Password Reset Email.txt** (proporcionado en el Desktop).
4. Pegar el contenido en el cuerpo del correo.
5. Guardar la plantilla.
## **Paso 6: Configuración de usuarios y grupos**

1. El entorno proporciona el archivo **targets.csv** con los objetivos.
2. Acceder al menú **Users & Groups**.
3. Crear un nuevo grupo con **New Group**.
4. Importar los usuarios usando el archivo `targets.csv`.
5. Guardar los cambios.
## **Paso 7: Configuración de la campaña**

1. Acceder al menú **Campaigns**.
2. Crear una nueva campaña.
3. Seleccionar:
    - Plantilla de correo
    - Landing page        
    - Sending profile
    - Grupo de usuarios objetivo
4. Lanzar la campaña con **Launch Campaign**.

Al lanzar la campaña:
- Se accede al **dashboard de la campaña**
- Se muestran estadísticas en tiempo real.
## **Verificación**

- Los correos pueden verificarse desde el cliente Thunderbird.
- Al hacer clic en el enlace de phishing (**Reset Password**):
    - GoPhish registra el clic.
    - El dashboard confirma la interacción del usuario con la campaña.