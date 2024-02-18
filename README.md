# Historia de AWS

- 2002 lo lanzaron internamente.
- 2003 la infraestructura de Amazon es una de las mejores, así que nace la idea de mercado.
- 2004 lanzan al público SQS.
- 2006 relanzan al público SQS, S3 y EC2.
- 2007 lanzado en Europa (lo usan empresas como Dropbox, Netflix, la NASA y AirBnB).

Actualmente AWS lidera los servicios de Cloud Computing, por delante de Microsoft y Google.

AWS tiene infraestructura global, es por esto que AWS nos brinda regiones, zonas de disponibilidad, data centers y puntos de presencia. Se puede encontrar más información [aquí](infrastructure.aws).


## AWS Regions

- AWS tiene regiones en todo el mundo.
- Los nombres pueden ser us-east-1, eu-west-3, etc.
- Una región es un cluster de data centers.
- Muchos de los servicios de AWS son region-scoped.

Para seleccionar una región hay que tener en cuenta los siguientes factores:

- **Datos del gobierno y requerimientos legales:** Los datos nunca deben dejar una región sin nuestro permiso explícito.
- **Proximidad con los clientes:** Para reducir la latencia.
- **Servicios disponibles dentro de una región:** Los nuevos servicios y sus nuevas features no están disponibles en cada región.


## Availability Zones

- Cada región tiene muchas zonas de disponibilidad (usualmente 3, el mínimo es 3 y el máximo 6).
- Cada zona de disponibilidad es uno o más data centers discretos con poder redundante, redes y conectividad.
- Están separados el uno del otro, así que los problemas que se puedan presentar estarán aislados.


## Points of Presence (Edge Locations)

- Amazon tiene más de 400 puntos de presencia en más de 90 ciudades entre más de 40 países.
- El contenido entregado a los usuarios finales tiene baja latencia.


# IAM

Es para asociar usuarios y grupos, ya que nos permite dar identidad y administración de acceso a nuestros usuarios, es un servicio global.

La cuenta root creada por defecto no debería ser un usuario ni ser compartido. Los usuarios son personas dentro de nuestra organización y pueden ser agrupados.

Los usuarios o grupos pueden tener asignados documentos JSON (más conocidos como políticas) que describe los permisos para cada uno de los recursos que queremos asignar.

```json
{
	"Version": "2023-12-15",
	"Statement": [
		{
			"Effect": "Allow",
			"Action": "ec2:Describe*",
			"Resource": "*"
		},
		{
			"Effect": "Allow",
			"Action": "elasticloadbalancing:Describe*",
			"Resource": "*"
		},
		{
			"Effect": "Allow",
			"Action": [
				"cloudwatch:ListMetrics",
				"cloudwatch:GetMetricStatistics",
				"cloudwatch:Describe*"
			],
			"Resource": "*"
		}
	]
}
```


## Policies

Estas políticas restringen el acceso a los usuarios pertenecientes al grupo que estamos modificando. También es posible asignar una política directamente a un usuario.

```json
{
	"Version": "2012-10-17",
	"Id": "S3-Account-Permissions",
	"Statement": [
		{
			"Sid": "1",
			"Effect": "Allow",
			"Principal": {
				"AWS": ["arn:aws:iam::123456789012:root"]
			},
			"Action": [
				"s3:GetObject",
				"s3:PutObject"
			],
			"Resource": ["arn:aws:s3:::mybucket/*"]
		}
	]
}
```

La estructura de las políticas consiste en:

- **Version:** Versión de la política, siempre incluir "2012-10-17".
- **Id:** Un identificador para la política (opcional).
- **Statement:** Uno o más statements individuales (requerido).

Los statements consisten en:

- **Sid:** Un identificador del statement (opcional).
- **Effect:** Si queremos permitir o denegar acceso (Allow, Deny).
- **Principal:** Qué cuenta/usuario/rol es al que le estamos aplicando la política.
- **Action:** Lista de acciones a las que esta política brinda o bloquea el acceso.
- **Resource:** Lista de recursos a los que la acción es aplicada.
- **Condition:** Condiciones  para cuando la política está en efecto (opcional).


## Password Policy

- Contraseñas fuertes = mayor seguridad para nuestra cuenta.
- En AWS podemos configurar la política de contraseñas para:
	- Setear una longitud mínima para nuestra contraseña.
	- Requerir tipos de caracteres específicos:
		- Incluír letras mayúsculas.
		- Letras minúsculas.
		- Números.
		- Caracteres no alfabéticos.
	- Permitir o no a los usuarios IAM cambiar su propia contraseña.
	- Requerir a los usuarios cambiar su contraseña después de cierto tiempo (expiración de la contraseña).
	- Prevenir el re-uso de las contraseñas.


## Multi Factor Authentication - MFA

- Los usuarios tienen acceso a nuestra cuenta y pueden posiblemente cambiar configuraciones o eliminar recursos en nuestra cuenta de AWS.
- Queremos proteger nuestra cuenta root y usuarios IAM.
- MFA = contraseña que tú sabes + mi propio dispositivo de seguridad.
- El principal beneficio de la autenticación multi factor es que si la contraseña es hackeada o adivinada, la cuenta no está comprometida.

### Opciones en AWS para MFA

#### Virtual MFA device
- Google Authenticator (sólo teléfonos).
- Authy (multi-dispositivo).

#### Universal 2nd Factor (U2F) Security Key
- YubiKey (memoria para dar acceso proveída por un tercero). Soporta múltiples roots y usuarios IAM usando sólo una key de seguridad.
- Hardware Key Fob MFA Device también de un tercero (Gemalto).
- Hardware Key Fob MFA Device for AWS GovBloud (US) de un tercero (SurePassID) para Cloud de gobiernos.


## Access Keys

- Para acceder a AWS tenemos 3 opciones:
	- AWS Management Console (protegida con contraseña + MFA).
	- AWS Command Line Interface (CLI) protegida con access keys.
	- Software Developer Kit (SDK) para código, también protegida con access keys.
- Los access keys son generados mediante la consola de AWS.
- Los usuarios administran sus propias access keys.
- Las access keys son secretas, como una contraseña, así que no hay que compartirlas.
- Access key ID != username y secret access key != password.


## AWS CLI

- Es una herramienta que nos permite interactuar con los servicios de AWS utilizando comandos en nuestra línea de comandos.
- Acceso directo a las APIs públicas de los servicios de AWS.
- Puedes desarrollar scripts para administrar los recursos.
- Es una alternativa a usar el AWS Management Console.

Para configurar nuestro acceso tenemos que hacer lo siguiente:

```bash
aws configure

# Introducir el Access Key ID y el Secret Access Key
# Colocar el nombre de la región por defecto

# Para listar los usuarios IAM
aws iam list-users
```


## AWS SDK

- Es el Software Development Kit que nos brinda AWS para desarrollar nuestras aplicaciones.
- Muchas librerías para acceder a las APIs.
- Nos permite acceder y administrar nuestros servicios de AWS.
- Embebido en nuestra aplicación.
- Soporta JavaScript, Python, PHP, .NET, Ruby, Java, Go, NodeJS, C++, entre otras cosas.


## Roles for services

- Algunos servicios de AWS necesitarán realizar acciones en tu nombre. Para hacer esto, asignaremos permisos al servicio de AWS con los roles de IAM.
- Hay algunos roles comunes como:
	- EC2 Instance Roles.
	- Lambda Function Roles.
	- Roles for CloudFormation.


## Security Tools

- IAM Credentials Report (account-level) es un reporte que lista todas las cuentas de usuario y el estado de sus múltiples credenciales.
- IAM Access Advisor (user-level) muestra el servicio de permisos otorgados a un usuario y cuándo fueron accedidos por última vez estos servicios. Podemos utilizar esta información para revisar nuestras políticas.


## Mejores prácticas con IAM

- No usar el usuario root excepto para configurar la cuenta de AWS.
- Un usuario físico = un usuario en AWS.
- Asignar usuarios a grupos y asignar permisos a los grupos.
- Crear una política fuerte para las contraseñas.
- Usar y hacer cumplir el uso de MFA.
- Crear y usar roles para dar permisos a los servicios de AWS.
- Usar access keys para accesos programáticos (CLI/SDK).
- Auditar permisos de tu cuenta usando IAM Credentials Report y IAM Access Advisor.
- No compartir nunca los usuarios IAM ni los access keys.


## Shared Responsibility Model for IAM

### AWS
- Infraestructurra (seguridad de la red global).
- Configuración y análisis de vulnerabilidad.
- Validación de cumplimiento.

### Nosotros
- Usuarios, grupos, roles, administración de políticas y monitoreo.
- Habilitar MFA para todas las cuentas.
- Rotar todas las keys a menudo.
- Utilizar las herramientas de IAM para aplicar los permisos apropiados.
- Analizar los patrones de acceso y revisar los permisos.

Si un usuario IAM no tiene acceso a la sección de facturación debemos ir con nuestro usuario root a la opción Account y activar los usuarios IAM para acceder a esta sección.


# EC2

- Es uno de los servicios más populares de AWS.
- EC2 = Elastic Compute Cloud = Infrastructure as a Service.
- Principalmente consiste en:
	- Alquilar máquinas virtuales (EC2).
	- Guardar data en discos virtuales (EBS).
	- Distribuir carga entre máquinas (ELB).
	- Escalar los servicios usando un grupo de auto-escalada (ASG).
- Para conocer como funciona EC2 es fundamental entender cómo funciona la nube.


## EC2 sizing y opciones de configuración

- **Sistemas operativos:** Linux, Windows o Mac OS.
- Qué tanta capacidad de computo en poder y núcleos (CPU).
- Qué tanta memoria (RAM).
- Qué tanto espacio de almacenamiento:
	- Anclado a la red (EBS & EFS).
	- Hardware (EC2 Instance Store).
- **Tarjeta de red**: Velocidad de la tarjeta, dirección IP pública.
- **Reglas de firewall:** Grupo de seguridad.
- **Comando de arranque (configurar la primer ejecución):** Data de usuario EC2.

### Data de usuario EC2
- Es posible arrancar nuestras instancias usando un EC2 User data script.
- Utilizar estos comandos de arranque significa lanzar comandos cuando nuestra máquina enciende.
- Este script sólo se ejecuta una vez la instancia enciente.
- Data de usuario EC2 es usada para automatizar tareas de encendido tales como:
	- Instalar actualizaciones.
	- Instalas software.
	- Descargar archivos frecuentes desde internet.
	- Cualquier cosa en la que puedas pensar.
- Estos scripts se ejecutan con el usuario root.


## Grupos de seguridad

- Los grupos de seguridad son fundamentales en la seguridad de red en AWS.
- Estos controlan cómo es permitido el tráfico dentro y fuera de nuestras instancias EC2.
- Los grupos de seguridad sólo contienen reglas para permitir acceso.
- Las reglas de los grupos de seguridad pueden ser referenciadas por IP o por grupo de seguridad.
- Los grupos de seguridad actúan como un firewall en las instancias EC2.
- Estos regulan:
	- Acceso a los puertos.
	- Autorizan rangos de IP - IPv4 e IPv6.
	- Control de la red entrante (desde otra a la instancia).
- Pueden ser asociadas a varias instancias.
- Bloqueado en una combinación de región/VPC.
- Vive afuera de EC2 - si el tráfico es bloqueado la instancia EC2 no lo verá.
- Es bueno mantener separado un grupo de seguridad del acceso SSH.
- Si tu aplicación no es accesible (time out), entonces es un problema del grupo de seguridad.
- Si tu aplicación da un error de  "connection refused", entonces es un error de la aplicación o no ha sido lanzada.
- Todas las reglas de entrada son bloqueadas por defecto.
- Todas las reglas de salida son autorizadas por defecto.


## Puertos clásicos que debes conocer

- 22 = SSH (Secure Shell) - acceder a una instancia Linux.
- 21 = FTP (File Transfer Protocol) - cargar archivos en el mismo archivo compartido.
- 22 = SFTP (Secure File Transfer Protocol) - cargar archivos usando SSH.
- 80 = HTTP - acceso no seguro de sitios web.
- 443 = HTTPS - acceso deguro de sitios web.
- 3389 = RDP (Remote Desktop Protocol) - acceder a una instancia Windows.


## Roles IAM en instancias EC2

Es importante tener en cuenta que si queremos por ejemplo que nuestra instancia EC2 pueda consultar los usuarios de IAM, tendremos que ir a AWS IAM y crear un rol que de estos permisos para asignarlo a nuestra instancia. Esto nos permitirá utilizar AWS CLI sin necesidad de configurar las credenciales para realizar consultas.


## EBS

- Un volumen EBS (Elastic Block Store) es un disco de red donde puedes anclar tus instancias mientras ellas están corriendo.
- Esto le permite a nuestras instancias tener data persistente, incluso después de eliminar/terminar las instancias.
- Pueden ser montadas sólo en una instancia a la vez (en el nivel CCP).
- Están vinculadas a una zona de disponibilidad específica.
- Son discos de red:
	- Usan la red para comunicarse con la instancia, esto significa que puede haber un poco de latencia.
	- Pueden ser deasociadas de una instancia EC2 para agregarse a otra instancia fácilmente teniendo en cuenta la zona de disponibilidad.

Cuando creamos un volumen EBS podemos configurar que se elimine cuando le dan "Terminate" a la instancia a la que se encuentra asociado:

- Por defecto el EBS raíz es eliminado cuando se termina una instancia.
- Por defecto, cualquier otro volumen EBS no es eliminado cuando se termina una instancia.

### EBS Snapshots
- Hacer un backup (snapshot)  de tu volumen EBS en un punto del tiempo.
- No es necesario desasociar el volumen para hacer un snapshot, pero es recomendado.
- Los snapshots se pueden copiar entre diferences AZs o regiones.

#### EBS Snapshots Features
- EBS Snapshot Archive:
	- Mueve un snapshot a un "archive tier" que es 75% más barato.
	- Toma entre 24 a 72 horas para restaurar el archive tier.
- Papelera de reciclaje para EBS Snapshots:
	- Configura reglas para mantener los snapshots eliminados para que puedas recuperarlos después de una eliminación accidental.
	- Retención específica (desde 1 día hasta 1 año).
- Fast Snapshot Restore (FSR):
	- Fuerza la inicialización completa del snapshot para no tener latencia en el primer uso.

### Tipos de volúmenes EBS
Hay 6 tipos de volúmenes cuando hablamos de EBS:

- **gp2/gp3 (SSD):** Propósitos generales de volumen SSD que tiene un precio y rendimiento balanceado para la variedad de cargas de trabajo.
- **io 1/io2 Block Express (SSD):** Más alto rendimiento de volumen SSD para misiones críticas de baja latencia y altas cargas de trabajo.
- **st1 (HDD):** Precio más bajo de volumen HDD designado para acceso frecuente, cargas de trabajo de alto rendimiento.
- **sc1 (HDD):** Precio más bajo de volumen HDD designado para acceso poco frecuente de cargas de trabajo.

Los volúmenes EBS son caracterizados en tamaño, carga de trabajo e IOPS (I/O Ops Per Sec). Tener en cuenta que sólo gps/gp3 e io1/io2 Block Express pueden ser usados como volúmenes de inicio.

### EBS Multi-Attach
- Podemos asociar el mismo volumen EBS a múltiples instancias EC2 en la misma AZ.
- Cada instancia tiene permisos completos para lectura y escritura en el volumen de alto rendimiento.


## AMIs

- AMI = Amazon Machine Image.
- Las AMIs son personalizaciones de una instancia EC2:
	- Puedes agregar tu propio software, configuraciones, sistema operativo, monitoreo, etc.
	- Rápido tiempo de boot/configuración porque todo nuestro software está precargado.
- Las AMIs son construídas para una región específica (y puede ser copiada entre regiones).
- Puedes lanzar una instancia EC2 desde:
	- **AMI Pública:** Proveída por AWS.
	- **Nuestra propia AMI:** Nosotros las hacemos y mantenemos por nuestra cuenta.
	- **Una AMI de AWS Marketplace:** Una AMI que alguien más creó (y potencialmente vendió).

### ¿Cómo construir nuestra AMI?
- Iniciar una instancia EC2 y personalizarla.
- Detener la instancia (para integridad de datos).
- Construir una AMI - esto también creará un snapshot EBS.
- Crear una instancia con otra AMI.


## EFS

- Es un NFS administrado (network file system) que puede ser montado en muchos EC2.
- EFS trabaja con instancias EC2 en múltiples AZs.
- Altamente disponible, escalable y caro (3 x gp2), pago por uso.
- Se usan los grupos de seguridad para controlar el acceso a EFS.
- Compatible con AMIs basadas en Linux (no Windows).

### EFS - Clases de rendimiento y almacenamiento
- EFS Scale:
	- 1000 clientes NFS de forma concurrente, más de 10GB/s.
	- Red de sistema de archivos que crete hasta Petabyte, automáticamente.
- Modo rendimiento:
	- Propósito general (por defecto) - Latencia sensitiva.
	- Máximo I/O - mayor latencia, altamente paralelo (big data, procesamiento).
- Modo completo:
	- Bursting - 1TB = 50MB/s.
	- Provisionado - configurar tu tamaño de espacio, GB/s o TB en almacenamiento.
	- Elástico - automáticamente escala basado en las cargas de trabajo.


## EBFS vs EFS
- Volúmenes EBS:
	- Una instancia (excepto multi-attach io1/io2).
	- Están disponibles para sólo la AZ que seleccionamos.
	- **gp2:** IO crece si el tamaño del disco crece.
	- **gp3 e io1:** Puede crecer el IO independientemente.
	- Para migrar un volumen EBS a través de otro AZ:
		- Tomar un snapshot.
		- Restaurar el snapshot en otra AZ.
		- Los backups de BS usan IO y no deberías de ejecutarlos mientras tu aplicación tiene mucho tráfico.
		- Los volúmenes EBS raíz son eliminados por defecto cuando una instancia EC2 es eliminada.
- EFS:
	- Montar cientos de instancias en varias AZs.
	- EFS comparte archivos de sitios web (WordPress).
	- Sólo para instancias Linux (POSIX).
	- EFS tiene un precio mayor que EBS.
	- Puedes usar el EFS-IA leverage para ahorrar costos.


# Escabilidad y alta disponibilidad

- Escalabilidad significa que una aplicación o sistema puede manejar mayores cargas adaptándose.
- Hay 2 tipos de escalabilidad:
	- Escalabilidad vertical.
	- Escalabilidad horizontal (elasticidad).
- Escalabilidad está conectada pero es diferente con la alta disponibilidad.


## Escalabilidad vertical

- Significa incrementar el tamaño de la instancia.
- Por ejemplo, nuestra aplicación corre en una instancia t2.micro.
- Escalar esta aplicación verticalmente significa correrla en una t2.large.
- La escalabilidad vertical es muy común para los sistemas no distribuidos, tales como una base de datos.
- RDS, ElastiCache son servicios que pueden escalar verticalmente.
- Hay límites usualmente de cuánto puedes escalar verticalmente (límites de hardware).


## Escalabilidad horizontal

- Significa incrementar el número de instancias o sistemas para nuestra aplicación.
- Escalabilidad horizontal implica sistemas distribuidos.
- Es muy común para aplicaciones web y aplicaciones modernas.
- Es fácil escalar horizontalmente gracias a las ofertas de cloud como Amazon EC2.


## Alta disponibilidad

- Usualmente va de la mano con la escalabilidad horizontal.
- Significa correr nuestra aplicación o sistema en al menos 2 data centers (zonas de disponibilidad).
- El objetivo de esta es lograr mantenerse cuando un data center se pierde.
- La alta disponibilidad puede ser pasiva (parar RDS con múltiples AZ por ejemplo).
- También puede ser activa (para escalabilidad horizontal).


# ELB

## ¿Por qué usar balanceadores de carga?

- Distribuye la carga en múltiples instancias.
- Expone un sólo punto de acceso (DNS) de nuestra aplicación.
- Manejo más fácil de fallas en las instancias.
- Hace chequeos regulares de nuestras instancias.
- Provee la terminación SSL (HTTPS) para nuestros sitios web).
- Alta disponibilidad entre las zonas.
- Tráfico público separado del tráfico privado.
- Un ELB es un balanceador de carga administrado.
	- AWS garantiza que va a estar funcionando.
	- AWS se preocupa por las actualizaciones, mantenimiento y alta disponibilidad.
	- AWS provee sólo unas opciones de configuración.
- Cuesta menos que armar nuestro propio balanceador de carga, pero va a ser mucho más eficiente al final.
- Está integrado con muchos servicios que AWS ofrece:
	- EC2, EC2 Auto Scaling Groups, Amazon ECS.
	- AWS Certificate Manager (ACM), CloudWatch.
	- Route 53, AWS WAF, AWS Global Accelerator.


## Health Checks

- Los chequeos de los balanceadores de carga son cruciales.
- Habilitan los balanceadores de carga para saber si las instancias que tienen en tráfico están disponibles para responder solicitudes.
- Es hecho en un puerto y una ruta (/health es común).
- Si la respuesta no es 200 (OK), entonces la instancia tiene problemas.


## Tipos de balanceadores de carga

- Hay 4 tipos de balanceadores de carga en AWS.
- **Classic Load Balancer** (v1 - generación anterior), acepta HTTP, HTTPS, TCP y SSL (TCP), ya no disponible.
- **Application Load Balancer** (v2 - nueva generación), acepta HTTP, HTTPS, WebSocket.
- **Network Load Balancer** (v2 - nueva generación), acepta TCP, TLS (TCP), UDP.
- **Gateway Load Balancer**.


## Application Load Balancer (ALB)

- Es capa 7 (HTTP).
- Balancea carga de múltiples aplicaciones HTTP a través de máquinas (target groups).
- Balancea múltiples aplicaciones en la misma máquina (por ejemplo contenedores).
- Soporte para HTTP/2 y WebSocket.
- Soporta redirecciones (desde HTTP o HTTPS por ejemplo).
- Tablas de routing para diferentes target groups:
	- Routing basado en el path en la URL.
	- Routing basado en el hostname en la URL.
	- Routing basado en Query String, Headers.
- Es genial para microservicios y aplicaciones basadas en contenedores (por ejemplo Docker y Amazon ECS).
- Tiene un puerto de mapeo para redirigir a un puerto dinámico en ECS.
- En comparación, necesitaríamos múltiples Classic Load Balancers (ya no disponible) por aplicación.

### Target Groups
- Instancias EC2 (pueden ser administradas por un grupo de auto escalada) - HTTP.
- Tareas ECS (administrado por el mismo ECS) - HTTP.
- Funciones Lambda - solicitudes HTTP traducidas en un evento JSON.
- Direcciones IP - deben ser IPs privadas.

### Bueno saber
- Hostname fijo (XXX.region.rlb.amazonaws.com).
- Los servces de aplicación no ven la IP del cliente directamente.
	- La verdadera IP del cliente es insertada en el header X-Forwarded-For.
	- También podemos obtener el puerto (X-Forwarded-Port) y el proto (X-Forwarded-Proto).


## Network Load Balancer (v2)

- Son capa 4 y nos permiten:
	- Utilizar tráfico TCP y UDP en nuestras instancias.
	- Responde a millones de solicitudes por segundos.
	- Menos latencia (100ms vs 400ms de ALB).
- NLB tiene una IP estática por AZ, y soporta asignar IP elásticas.
- NLB son usadas par el rendimiento extremo, con tráfico TCP o UDP.
- No incluído en el AWS free tier.

### Target Groups
- Instancias EC2.
- Direcciones IP - deben ser IPs privadas.
- Application Load Balancer.
- Health Checks soportan los protocolos TCP, HTTP y HTTPS.


## Gateway Load Balancer

- Despliega, escala y administra una flota de dispositivos virtuales de red de terceros en AWS.
- Por ejemplo Firewalls, detección de intrusión y sistemas de prevención, manipulación de payloads, etc.
- Trabaja en capa 3 (capa de red) - IP Packets.
- Combina las siguientes funciones:
	- Transparent Network Gateway - una entrada/salida para todo el tráfico.
	- Load Balancer - distribuye el tráfico en nuestros dispositivos virtuales.
- Usa el protocolo GENEVE en el puerto 6081.

### Target Groups
- Instancias EC2.
- Direcciones IP - deben ser IPs privadas.


## Sticky Sessions (afinidad de sesión)

- Es posible implementar esta afinidad para que el mismo cliente siempre sea redireccionado a la misma instancia detrás de un balanceador de carga.
- Esto funciona para Classic Load Balancer, Application Load Balancer y Network Load Balancer.
- La "cookie" es usada para esto, tiene una fecha de expiración que nosotros podemos controlar.
- Caso de uso: Estar seguros que el usuario no pierda sus datos de sesión.
- Esto podría causar que la carga en nuestras instancias no sea equitativa.

### Nombres de cookies
- Application-base Cookies:
	- Cookie personalizada.
	- Generada por el target.
	- Puede incluir cualquier atributo personalizado requerido por la aplicación.
	- El nombre de la cookie debe ser diligenciado individualmente por cada target group.
	- No usar AWSALB, AWSALBAPP o AWSALBTG (reservado para uso por ELB).
- Application cookie:
	- Generado por el balanceador de carga.
	- El nombre de la cookie es AWSALBAPP.
- Duration-based Cookies
	- Cookie generada por el balanceador de carga.
	- El nombre de la cookie es AWSALB para ALB, AWSELB para CLB.


## SSL/TLS - Bases

- Un certificado SSL permite el tráfico entre nuestros clientes y nuestro balanceador de carga para ser encriptado en tránsito (encriptación in-flight).
- SSL se refiere a Secure Sockets Layer, usado para encriptar conexiones.
- TLS se refiere a Transport Layer Security, el cual es una nueva versión.
- Actualmente, los certificados TLS son principalmente usados, pero las personas siguen prefiriendo SSL.
- Los certificados SSL públicos son emitidos por las autoridades de certificados (Certificate Authorities).
- Los certificados SSL tienen una fecha de expiración (nosotros la configuramos) y deben ser renovados.
- Podemos administrar nuestros certificados usando ACM (AWS Certificate Manager).

### Server Name Indication (SNI)
- Resuelve el problema de cargar múltiples certificados SSL en un servidor web.
- Es un protocolo más nuevo, y requiere que el cliente indique el hostname del target server en el llamado inicial del SSL.
- El servidor entonces encontrará el certificado correcto, o retornará uno por defecto.
- **Classic Load Balancer (v1)**:
	- Soporta sólo un certificado SSL.
	- Debe usar múltiples CLB para múltiples hostname con múltiples certificados SSL.
- **Application Load Balancer (v2)**:
	- Soporta múltiples listeners con múltiples certificados SSL.
	- Usa SNI para hacerlo funcionar.
- **Network Load Balancer (v2)**:
	- Soporta múltiples listeners con múltiples certificados SSL.
	- Usa SNI para hacerlo funcionar.


## Connection Draining

- Connection Draining para CLB.
- Deregistration Delay para ALB y NLB.
- Dará un tiempo para completar las solicitudes mientras las instancias están fallando en el chequeo.
- Detiene el envío de nuevas solicitudes a la instancia EC2 que esta en de-registering.
- Puede ser deshabilitado.


## Auto Scaling Group (ASG)

- En tiempo real, la carga de nuestros sitios web o aplicaciones puede cambiar.
- En la nube, tú puedes crear e incrementar servidores bastante rápido.
- La meta de ASG es:
	- Escalar las instancias EC2 para cuando incrementa la carga.
	- Remover las instancias cuando la carga decrementa,
	- Asegurarnos de tener un mínimo y un máximo número de instancias EC2 corriendo.
	- Automáticamente registra nuevas instancias al balanceador de carga.
	- Recrear una instancia EC2 en casi de que una previa esté eliminada.
- ASG es gratis (sólo pagas por las instancias que se crean y usan).

### Group Attributes
- Un Launch Template:
	- AMI + tipo de instancia.
	- EC2 User Data.
	- Volúmenes EBS.
	- Security Groups.
	- SSH Key Pair.
	- Roles IAM para nuestras instancias EC2.
	- Información de red y subredes.
	- Información del balanceador de carga.
- Mínimo y máximo definido de capacidad.
- Políticas de escalado.

### Alarmas CloudWatch y Scaling
- Es posible escalar ASG basado en alarmas de CloudWatch.
- Una alarma monitorea una métroca (tales como promedio de CPU, o una métrica personalizada).
- Las métricas como promedio de CPU son computadas por las instancias ASG.
- Basado en la alarma:
	- Podemos crear políticas de incremento para nuestras instancias.
	- Podemos crear políticas de eliminación para nuestras instancias.

### Políticas de escalada
- Escalado dinámico:
	- **Target Tracking Scaling:**
		- Simple de configurar.
		- Ejemplo: Queremos un promedio de ASG CPU del 40%.
	- **Simple / Step Scaling:**
		- Cuando una alarma de CloudWatch es ejecutada (por ejemplo CPU > 70%) añadir 2 unidades.
		- Cuando una alarma de CloudWatch es ejecutada (por ejemplo CPU < 30%) entonces eliminar 1.
- **Scheduled Scaling:**
	- Anticipar una base de escalada en patrones de uso conocidos.
	- Ejemplo: Incrementar la capacidad mínima de 10 a 5 de la tarde los Viernes.
- **Predictive scaling:** Continuamente hace escalado  dependiendo de la carga.

#### Buenas métricas para escalada
- **CPUUtilization:** Promedio de uso de CPU en nuestras instancias.
- **RequestCountPerTarget:** Estar seguros de que el número de solicitudes por instancia EC2 sea estable.
- **Average Network In / Out:** Si tu aplicación es basada en redes.
- Cualquier métrica personalizada (podemos usarlas con CloudWatch).

### Scaling Cooldowns
- Después de que una actividad de escalada pasa, entramos en el periodo de cooldown (por defecto 300 segundos).
- Durante el periodo de cooldown, el ASG no lanzará o terminará instancias adicionales (para permitir estabilizarse a las métricas).
- **Advertencia:** Usar una AMI lista para reducir el tiempo de configuración y así poder atender solicitudes rápidamente y reducir el período de cooldown.


## Instance Refresh

- El objetivo es actualizar nuestro Launch Template y entonces recrear todas las instancias EC2.
- Para esto necesitamos usar la función Instance Refresh nativa.
- Se puede setear un mínimo de porcentaje para nuestro sistema healthy.


# RDS

- Es un servicio de bases de datos relacionales.
- Es un servicio de bases de datos administrado para usar SQL como el lenguaje de nuestros queries.
- Nos permite crear bases de datos en la nube que pueden ser administradas por AWS:
	- Postgres.
	- MySQL.
	- MariaDB.
	- Oracle.
	- Microsoft SQL Server.
	- Aurora (base de datos propia de AWS).


## ¿Por qué usar RDS y no desplegar nuestra base de datos en EC2?

- RDS es un servicio administrado:
	- Provisionamiento automátizado, parcheo del sistema operativo.
	- Backups continuos y restauración en un tiempo específico.
	- Dashboards para monitoreo.
	- Read replicas para un rendimiento de lectura mejorado.
	- Configuración multi AZ para DR (Disaster Recovery).
	- Ventanas de mantenimiento para actualizaciones.
	- Capacidad de escalado vertical y horizontal.
	- Almacenamiento con backup por EBS (gp2 o io1).
- No se puede acceder a nuestras instancias con SSH.


## Storage Auto Scaling

- Nos ayuda a incrementar nuestro almacenamiento en nuestra instancia RDS automáticamente.
- Cuando RDS detecta que nos estamos quedando sin almacenamiento, el servicio escala automáticamente.
- Evitar escalamiento manual para nuestro almacenamiento de base de datos.
- Tienes que setear un máximo de almacenamiento (límite máximo para almacenamiento de base de datos).
- Automáticamente modifica el almacenamiento si:
	- El espacio libre es menor al 10% del almacenamiento configurado.
	- Bajo almacenamiento en al menos 5 minutos.
	- Han pasado 6 horas desde la última modificación.
- Son muy útiles para aplicaciones con cargas de trabajo impredecibles.


## Read Replicas

- Son parecidas al maestro esclavo en bases de datos ya que nos permite tener backups de sólo lectura que tendrán la misma información que nuestra base de datos principal.
- Se pueden tener hasta 15 Read Replicas.
- Dentro de un AZ, entre AZ o entre regiones.
- La réplica es asíncrona, es decir que las lecturas son eventualmente consistentes.
- Las réplicas pueden ser promovidas a su propia base de datos.
- Las aplicaciones deben actualizar el connection string para aprovechar las réplicas de lectura.

### Costos de red
- En AWS hay un costo de red cuando la data va deste un AZ a otro.
- Para las Read Replicas de RDS en la misma región tú no pagas ese monto.


## Multi AZ (Disaster Recovery)

- Replicación síncrona, es decir que inmediatamente nuestra aplicación escribe algo en nuestra base de datos principal, esto será replicado en nuestra réplica.
- Un nombre DNS - la aplicación automáticamente apuntará a la réplica si por ejemplo nuestra base de datos principal falla.
- Incrementamos la disponibilidad.
- Conmutación de error en caso de pérdida de AZ, pérdida de red, o fallas en almacenamiento o nuestra instancia.
- No hay intervención manual en las aplicaciones.
- No usado para escalar.
- Podemos montar nuestras Read Replicas como Multi AZ si queremos.


## ¿Cómo pasar de Single-AZ a Multi-AZ?

- No se requiere detener la base de datos, por lo que es una operación de cero tiempo de inactividad.
- Sólo hacemos clic en "Modify" en la base de datos.
- Lo siguiente que sucederá internamente es:
	- Será tomado un snapshot (backup).
	- Una nueva base de datos es restaurada desde el snapshot en un nuevo AZ.
	- La sincronización es establecida entre las 2 bases de datos.


## Amazon Aurora

- Es una tecnología propia de AWS (no es open source).
- Postgres y MySQL son soportadas como una base de datos Aurora (esto significa que los drivers funcionará si Aurira es una base de datos Postgres o MySQL).
- Aurora es "AWS cloud optimized" y tiene 5x rendimiento mejorado sobre MySQL en RDS, y sobre 3x el rendimiento de Postgres en RDS.
- El almacenamiento de Aurora crece en incrementos de 10GB automáticamente, y puede hacerlo hasta 128TB.
- Puede tener hasta 15 réplicas y el proceso de replicación es más rápido que el de MySQL.
- Aurora cuesta más que RDS (20% más) pero es más eficiente.

### Alta disponibilidad y Read Scaling
- 6 copias de tu data entre 3 AZ:
	- 4 copias de 6 necesitadas para escritura.
	- 3 copias de 6 necesarias para lectura.
	- El almacenamiento está entre 100s de volúmenes.
- Una instancia Aurora hace las escrituras (master).
- Automático failover para la master en menos de 30 segundos.
- Soporta replicación entre regiones.

### Características de Aurora
- Failover automático.
- Backup y recuperación
- Isolación y seguridad.
- Cumplimiento de la industria.
- Botón push para escalado.
- Parcheo automatizado con 0 tiempo de inactividad.
- Monitoreo avanzado.
- Mantenimiento de rutina.
- Backtrack: Restaurar datos en cualquier punto en el tiempo sin usar backups.


## Seguridad en RDS y Aurora

- **At-rest encryption**:
	- La base de datos maestra y sus réplicas son encriptadas con AWS KMS - debe ser definido al momento de lanzarlas.
	- Si la master no está encriptada, entonces las réplicas tampoco lo estarán.
	- Para encriptar una base de datos no encriptada, hay que tomar un snapshot de la BD y restaurarla como encriptada.
- **In-flight encryption:** TLS-ready por defecto, usa los certificados raíz de client-side de AWS TLS.
- **IAM Authentication:** Roles IAM para conectaros a nuestra base de datos (en vez de nombre de usuario y contraseña).
- **Security Groups:** Controla el acceso de red a las instancias.
- No disponible SSH excepto en RDS personalizadas.
- Los logs de auditoría pueden ser habilitados y enviados a CloudWatch para retención.


## Proxy RDS

- Proxy de base de datos completamente administrado por RDS.
- Le permite a las aplicaciones acceder y compartir las conexiones de BD establecidas en la base de datos.
- Eficiencia de la base de datos mejorada reduciendo el estrés en los recursos de bases de datos (por ejemplo CPU, RAM, etc) y minimiza las conexiones abiertas (y timeouts).
- Serverless, autoscaling y alta disponibilidad (multi-AZ).
- Tiempo de failover reducido en RDS y Aurora de hasta un 66%.
- Soporta RDS (MySQL, PostgreSQL, MariaDB, MS SQL Server) y Aurora (MySQL y PostgreSQL).
- No hay cambio de código requerido para la mayoría de aplicaciones.
- Obliga la autenticación IAM para nuestra base de datos, y guarda las credenciales de forma segura in AWS Secrets Manager.
- Proxy RDS es nunca accesible publicamente (debe ser accedido desde VPC).


## ElastiCache

- Del mismo modo que RDS administra las bases de datos relacionales...
- ElastiCache administra Redis o Memcached.
- Caches son bases de datos en memoria con un rendimiento realmente alto y baja latencia.
- Ayuda a reducir la carga de las bases de datos de cargas de trabajo de lectura intensivas.
- Ayuda hacer tu aplicación stateless.
- AWS se preocupa por el mantenimiento y parcheo del sistema operativo, optimizaciones, configuración, monitoreo y la recuperación en fallas y backups.
- Usar ElastiCache implica muchos cambios en el código de la aplicación.

### Redis vs Memcached
#### Redis
- Multi AZ con failover automático.
- Las Read Replicas escalan lecturas y tienen alta disponilidad.
- Durabilidad de datos usando la persistencia AOF.
- Opciones de backup y restauración.
- Soporta Sets y Sets ordenados.

#### Memcached
- Múltiples nodos de partición de datos.
- No hay alta disponibilidad (replicación).
- No es persistente.
- No hay backups ni restauración.
- Arquitectura multihilos.

### Consideraciones de implementación de caching y estrategias
- ¿Es seguro cachear datos? Los datos podrían estar no actualizados, eventualmente consistente.
- ¿ Es efectivo el caching para nuestros datos?
	- Patrón: Cambios de datos lentos, pocas key son frecuentemente necesarias.
	- Antipatrones: Cambios de datos rápidamente, todos los key frecuentemente necesarios.
- Es buena la data estructurada para el caching?

#### Palabras finales de wisdom
- Lazy Loading / Cache es fácil de implementar y funciona en muchas situaciones, especialmente en la parte de lectura.
- Write-through es usualmente combinado con Lazy Loading para los queries or cargas de trabajo que se benefician desde esta optimización.
- TTL es usualmente no una mala idea, excelto cuando tu usas Write-through. Agrega un valor sensible a tu aplicación.
- Sólo cachear los datos que tiene sentido (perfiles de usuario, blogs, etc).


# ¿Qué es DNS?

- Domain Name System se encarga de traducir un hostname amigable para las personas en la IP de una máquina.
- www.google.com = 172.217.18.36.
- DNS es una estructura de nombre jerárquica.


## Terminología

- Top Level Domain (TLD): .com, .us, .in, .gov, .org...
- Second Level Domain (SLD): amazon.com, google.com...


# Route 53

- Es altamente disponible, escalable y completamente administrado y Authoritative DNS.
	- Authoritative = el cliente (nosotros) podemos actualizar los DNS.
- Habilidad de revisar el estado de nuestros recursos.
- Es el único servicio de AWS que provee 100% de disponibilidad SLA.
- 53 es una referencia al puerto tradicional DNS.


## Tipos de registros

- **A** - mapea un hostname a una IPv4.
- **AAAA** - mapea un hostname a una IPv6.
- **CNAME** - mapea un hostname hacia otro hostname.
	- El target es un nombre de dominio el cual debe tener un registro A o AAAA.
	- No se puede crear un registro CNAME al inicio de un namespace DNS.
	- Ejemplo: no se puede crear para example.com, pero sí para www.example.com.
- **NS** - Name Servers para la zona de hosting.


## Hosted Zones

- Un contenedor de registros que define cómo es la ruta de tráfico hacia un dominio y sus subdominios.
- **Public Hosted Zones** - contiene registros que especifican cómo es la ruta de tráfico en Internet (nombres de dominio públicos).
- **Private Hosted Zones** - contiene registros que especifican cómo es la ruta de tráfico entre uno y más VPCs (nombres de dominio privados).
- Se paga $0.50 por mes por zona de hosting.


## CNAME vs Alias

- Los recursos de AWS (Balanceadores de carga, CloudFront) exponen un hostname de AWS, por ejemplo lb1-1234.us-east-2.elb.amazonaws.com y tú quieres myapp.mydomain.com.
- CNAME:
	- Apunta un hostname a cualquier otro hostname (app.mydomain.com => blabla.anything.com).
	- Sólo para dominios que no son de raíz (aka.someting.mydomain.com).
- Alias:
	- Apunta un hostname a un recurso AWS (app.mydomain.com => blabla.amazonaws.com).
	- Funciona para dominios raíz y no raíz (aka.mydomain.com).
	- Libre de cargos.
	- Chequeos health nativos.


## Servicios AWS que pueden ser asociados a un Alias

- Elastic Load Balancers.
- CloudFront Distributions.
- API Gateway.
- Ambientes Elastic Beanstalk.
- S3 Websites.
- VPC Interface Endpoints.
- Global Accelerator.
- Route 53 en la misma zona de hosteo.


## Políticas de enrutamiento

- Define cómo responde Route 53 a queries DNS.
- No confundirnos con la palabra "Routing":
	- No es lo mismo como un routing de un balanceador de carga que enruta el tráfico.
	- DNS no enrunta ningún tráfico. sólo responde a queries DNS.
- Route 53 soporta las siguientes políticas de enrutamiento:
	- Simple.
	- Weighted.
	- Failover.
	- Latency based.
	- Geolocation.
	- Multi-Value Answer.
	- Geoproximity (usando la feature Route 53 Traffic Flow).

### Simple
- Enruta el tráfico a un único recurso.
- Puede especificar múltiples valores en el mismo registro.
- Si múltiples valores son retornados, uno random es seleccionado por el cliente.
- Cuando Alias está activado, sólo podemos especificar un recurso de AWS.
- No puede ser asociado a Health Checks.

### Weighted
- Controla el porcentaje de las solicitudes que van a cada uno de los recursos.
- Asignar un peso relativo a cada recurso:
	- $traffic(\%) = \frac{Weight for a specific record}{Sum of all the weights for all records}$.
	- Los pesos no deben superar el 100%.
- Los registros DNS deben tener el mismo nombre y tipo.
- Puede ser asociado con Health Checks.
- Casos de uso: balanceo de carga entre regions, testing de nuevas versiones de aplicaciones, etc.
- Asignar un peso de 0 a un registro para detener el envío de tráfico a un recurso.
- Si todos los registros tienen un peso de 0, entonces todos los registros van a ser retornados equitativamente.

### Latency based
- Redirecciona al recurso que tiene la menor latencia hacia nosotros.
- Muy útil cuando la latencia para usuarios es una prioridad.
- La latencia está basada en el tráfico entre usuarios y Regiones AWS.
- Puede ser asociado con Health Checks (tiene capa de failover).

### Health Checks
- Los Health Checks HTTP están sólo para recursos públicos.
- Health Check => DNS Failover automatizado:
	- Health Checks que monitorean un endpoint (aplicación, servidor, otro recurso AWS).
	- Health Checks que monitorean otros Health Checks (Health Checks calculados).
	- Health Checks que monitorean alarmas CloudWatch (muy útil para recursos privados).

### Geolocation
- Este enrutamiento está basado en la ubicación del usuario.
- Especifica la ubicación por continente, país o por estado.
- Se debe crear un registro "Default" (en caso de que no haga match con ninguna ubicación).
- Casos de uso: Ubicación de sitio web, restringir distribución de contenido, balanceo de carga.
- Puede ser asociado con Health Checks.

### Geoproximity
- Enrutar tráfico a nuestros recursos basado en la ubicación geográfica de usuarios y recursos.
- Capacidad de desviar más tráfico a los recursos según el sesgo definido.
- Para cambiar el tamaño de la región geográfica, se especifican valores bias:
	- Para expandir (1 a 99) - más tráfico al recurso.
	- Para disminuir (-1 a -99) - menos tráfico al recurso.
- Los recursos pueden ser:
	- Recursos AWS (especificar la región AWS).
	- Recursos no AWS (especificar latitud y longitud).
- Debemos usar Route 53 Traffic Flow (avanzado) para usar esta opción.

### Flujo de tráfico
- Simplifica el proceso de creación y mantenimiento de registros en configuraciones largas y complejas.
- Editor visual para administrar árboles de decisión de enrutamiento complejos.
- Las configuraciones pueden ser guardadas como una política de flujo de tráfico.

### Enrutamiento basado en IP
- Se hace basado en las direcciones IP de los clientes.
- Se provee una lista de CIDRs para nuestros clientes y los endpoints correspondientes.
- Casos de uso: Optimizar el rendimiento, reducir los costos de red.

### Multi-Value
- Se usa cuando se hace enrutamiento de tráfico para múltiples recursos.
- Route 53 retorna múltiples valores/recursos.
- Puede ser asociado con Health Check.


# VPC

- Es algo que debemos conocer en profundidad para las certificaciones de AWS.
- Es importante conocer para esta certificación:
	- VPC, subredes, internet gateways y NAT gateways.
	- Grupos de seguridad, Red ACL (NACL), flujos de logs VPC.
	- VPC Peering, VPC Endpoints.
	- Site to Site VPN y conexión directa.


## ¿Qué es VPC?

- VPC es una red privada para publicar nuestros recursos (recursos regionales).
- Las subredes nos permiten particionar nuestra red dentro de la VPC (recurso por zona de disponibilidad).
- Una subred pública es una subred que es accesible desde internet.
- Una subred privada es una subred que no es accesible desde internet.
- Para definir el acceso a internet y entre subredes, usamos **Route Tables**.


## Internet Gateway y NAT Gateways

- **Internet Gateway** ayuda a nuestras instancias VPC a conectarse a internet.
- Las subredes públicas tienen una ruta al internet gateway.
- **NAT Gateways** (administrados por AWS) y **NAT Instances** permiten a nuestras instancias a conectarse a subredes privadas para acceder a internet mientras permanecen privadas.


## Network ACL y grupos de seguridad

- **NACL (Network ACL)**:
	- Un firewall que controla el tráfico desde y hacia la subred.
	- Puede tener reglas para permitir o denegar.
	- Están conectados al nivel de Subred.
	- Las reglas sólo incluyen direcciones IP.
- **Grupos de seguridad**:
	- Un firewall que controla el tráfico desde y hacia una ENI / Instancia EC2.
	- Puede tener reglas sólo para permitir.
	- Las reglas incluyen direcciones IP y otros grupos de seguridad.

### Network ACLs vs Grupos de seguridad
| Grupo de seguridad | NACL |
|--|--|
| Opera a nivel de instancia. | Opera a nivel de subred. |
| Soporta sólo reglas para permitir. | Soporta reglas para permitir y denegar. |
| Es stateful: El tráfico de retorno es automáticamente permitido, independientemente de las reglas. | Es stateless: El tráfico de retorno debe estar explícitamente permitido por las reglas.  |
| Se evalúan todas las reglas antes de decidir hacia donde se permite el tráfico. | Se procesan las reglas por orden cuando se decide hacia dónde se permite el tráfico. |
| Se aplica a una instancia sólo si alguien especifica el grupo de seguridad cuando se lanza una instancia, o se asocia un grupo de seguridad con la instancia luego. | Automáticamente aplica a todas las instancias en la subred a la que está asociada. |


## VPC Flow Logs

- Captura información sobre el tráfico de IP que va hacia nuestras interfaces:
	- **VPC** Flow Logs.
	- **Subnet** Flow Logs.
	- **Elastic Network Interface** Flow Logs.
- Ayuda a monitorear y controlar los problemas de conectividad, por ejemplo:
	- Subredes a internet.
	- Subredes a subredes.
	- Internet a subredes.
- Captura información de red desde interfaces administradas por AWS también: Elastic Load balancers, ElastiCache, RDS, Aurora, etc.
- Los datos de VPC Flow Logs pueden ir a S3, CloudWatch Logs, y Kinesis Data Firehose.


## VPC Peering

- Conecta 2 VPC de forma privada usando la red de AWS.
- Hace que luego se comporten como si estuvieran en la misma red.
- No debe haber overlapping CIDR (rango de direcciones IP).
- No es transitiva (debe ser establecida por cada VPC que necesita comunicarse con la otra).


## VPC Endpoints

- Nos permiten conectarnos a servicios AWS usando una red privada en vez de una red pública www.
- Esto nos da mayor seguridad y menos latencia para acceder a los servicios AWS.
- VPC Endpoint Gateway sólo para S3 y DynamoDB.
- VPC Endpoint Interface para el resto.
- Sólo se usa dentro de nuestra VPC.


## VPN Site to Site y conexión directa

- **Site to Site VPN**:
	- Conecta una VPN on-premises a AWS.
	- La conexión es automáticamente encriptada.
	- Pasa por la internet pública.
- **Direct Connect (DX)**:
	- Establece una conexión física entre on-premises y AWS.
	- La conexión es privada, segura y rápida.
	- Pasa por una red privada.
	- Toma al menos un mes establecerla.


## Últimos comentarios

- **VPC:** Virtual Private Cloud.
- **Subnets:** Asociadas a una zona de disponibilidad específica, partición de red de la VPC.
- **Internet Gateway:** A nivel de VPC, provee acceso a internet.
- **NAT Gateway / Instances:** Brinda acceso a internet a subredes privadas.
- **NACL:** Stateless, reglas de subred para entrada y salida.
- **Grupos de seguridad:** Stateful, opera en instancias EC2 o ENI.
- **VPC Peering:** Conecta 2 VPC sin superposición de rangos de IP, no es transitivo.
- **VPC Endpoints:** Provee acceso privado a servicios AWS en la VPC.
- **VPC Flow Logs:** Logs de tráfico de red.
- **Site to Site VPN:** VPN a través del internet público entre datacenters on-premises y AWS.
- **Direct Connect:** Conexión privada directa a AWS.


## Three Tier Architecture

![Imagen Three Tier Architecture](https://miro.medium.com/v2/resize:fit:871/1*kJ9n3PVWJQrnScK1Q9O_DA.png)

### LAMP Stack en EC2
- **Linux:** Sistema operativo para instancias EC2.
- **Apache:** Servidor web que corre en Linux (EC2).
- **MySQL:** Base de datos en RDS.
- **PHP:** Lógica de aplicación (corriendo en EC2).
- Se puede agregar Redis / Memcached (ElastiCache) para incluir tecnología de cacheo.
- Para guardar datos de la aplicación y software de forma local: Disco EBS (root).
