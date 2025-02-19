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


# S3

- Es uno de los bloques de construccion principales de AWS.
- Es conocido como un almacenamiento "infinitamente escalable".
- Muchos sitios web utilizan Amazon S3 como su base.
- Muchos servicios de AWS utilizan S3 como una integración.


## Casos de uso

- Backups y almacenamiento.
- Disaster Recovery.
- Guardar archivos.
- Almacenamiento de nube híbrido.
- Hosting de aplicaciones.
- Análisis de big data.
- Sitios web estáticos.


## Buckets

- Amazon S3 permite a las personas almacenar objetos (archivos) en buckets (directorios).
- Los buckets deben tener un nombre unico globalmente (entre todas las regiones de todas las cuentas).
- Los buckets son definidos a nivel de región.
- S3 se ve como un servicio global, pero los buckets son creados en una región.
- A tener en cuenta para el nombramiento:
	- No uppercase, no underscore.
	- Longitud de 3 a 63 caracteres.
	- No una IP.
	- Debe iniciar con letras minúsculas o números.
	- No debe iniciar con el prefijo **xn- -**.
	- No debe finalizar con el sufijo **-s3alias**.


## Objects

- Los objetos (archivos) tienen una llave.
- La llave (key) es la ruta completa:
	- s3://my-bucket/my-file.txt.
- La key es compuesta de un prefijo + nombre del objeto.
	- s3://my-bucket/my_folder1/another_folder/my_file.txt.
- No está el concepto de directorios entre buckets.
- Sólo llaves con nombres muy largos que contienen slashes.
- Los valores de los objetos son el contenido del cuerpo:
	- El tamaño máximo de los objetos es 5TB (5000GB).
	- Si se está cargando más de 5GB, se debe usar "multi-part upload".
- Metadata (lista de textos keys / value pairs - sistema o metadata de usuario).
- Tags (key unicode / value pair) - útil para seguridad / ciclo de vida.
- Version ID (si el versionamiento esta habilitado).


## Seguridad - políticas de buckets

- **User-Based**:
	- Políticas IAM - cuáles llamados API deben ser permitidos para un usuario específico desde IAM.
- **Resource-Based**:
	- Políticas Bucket - reglas asignadas al bucket desde la consola de S3 - permite entre cuentas.
	- Lista de control de acceso de objeto (ACL) -finer grain (puede ser deshabilitado).
	- Lista de control de acceso de bucket (ACL) - menos común (puede ser deshabilitado).
- **Nota**: Un usuario de IAM puede acceder a un objeto S3 si:
	- Los permisos de usuario IAM lo permiten o la política del recurso lo permite.
	- Y no hay denegación explícita.
- **Encriptación**: Encripta los objetos en Amazon S3 usando llaves de encriptación.
- Las políticas al final son un JSON:
	- **Resources:** Buckets y objetos.
	- **Effect:** Allow / Deny.
	- **Actions:** Conjunto de APIs que se permiten o bloquean.
	- **Principal:** La cuenta o el usuario al que se le aplica la política.

```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Sid": "PublicRead",
			"Effect": "Allow",
			"Principal": "*",
			"Action": [
				"s3:GetObject"
			],
			"Resource": [
				"arn:aws:s3:::examplebucket/*"
			]
		}
	]
}
```


## Website Hosting

- S3 puede hostear sitios web estáticos y dar acceso a ellos en internet.
- La URL del sitio web será alguna de las siguientes (dependiendo de la región):
	- http://bucket-name.s3-website-aws-region.amazonaws.com
	- http://bucket-name.s3-website.aws-region.amazonaws.com
- Si tenemos un error **403 Forbidden**, tenemos que hace agurarnos de que las políticas de nuestro Bucket permiten la lectura al público.


## Versionamiento

- Podemos versionar nuestros archivos en Amazon S3.
- Está habilitado a nivel de Bucket.
- La misma llave sobreescribirá el cambio de version: 1, 2, 3...
- La mejor practica es versionar nuestros Buckets.
	- Protege contra eliminaciones sin intención (habilita el restauramiento de una versión).
	- Podemos regresar fácilmente a una versión previa.
- **Nota:**
	- Cualquier archivo que no tenga una versión antes de habilitar el control de versiones tendrá una versión "nula".
	- Suspender el versionamiento no elimina la versiones anteriores.


## Réplicas (CRR y SRR)

- Se debe habilitar el versionamiento en los buckets de origen y destino.
- Cross-Region Replication (CRR).
- Same-Region Replication (SRR).
- Los buckets pueden estar en diferentes cuentas AWS.
- La copia es asíncrona.
- Se deben dar los permisos apropiados IAM a S3.
- Casos de uso:
	- CRR - cumplimiento, acceso de baja latencia entre regiones, réplicas entre cuentas.
	- SRR - agrega logs entre múltiples buckets, replicación en vivo entre cuentas de producción y test.
- **Nota:**
	- Despues de habilitar las réplicas, sólo los nuevos objetos son replicados.
	- Opcionalmente, se pueden replicar objetos existentes usando **S3 Batch Replication**.
		- Replica objetos existentes y objetos que tuvieron fallas en su replicación.
	- Para operaciones de eliminación:
		- Se puede replicar los marcadores delete desde el origen al destino (configuración opcional).
		- Las eliminaciones con una versión ID no son replicadas (para evitar eliminaciones maliciosas).
	- No hay "chaining" en la replicación:
		- Si el bucket 1 tiene réplicas en el bucket 2, que tiene replicación en el bucket 3, entonces los objetos creados en bucket 1 no son replicados en bucket 3.


## Clases de almacenamiento

- Amazon S3 Standard - propósitos generales.
- Anazon S3 Standard - acceso infrecuente (IA).
- Amazon S3 One Zone - acceso infrecuente.
- Amazon S3 Glacier Instante Retrieval.
- Amazon S3 Glacier Flexible Retrieval.
- Amazon S3 Glacier Deep Archive.
- Amazon S3 Intelligent Tiering.
- Puedes cambiar las clases manualmente o usando **S3 Lifecycle configurations**.

### Durabilidad y disponibilidad.
- Durabilidad:
	- Alta durabilidad (99.999999999% o 11 9's) de objetos a través de las multiples AZ.
	- Si se guardan 10.000.000 objetos con Amazon S3, puedes esperar en promedio a incurrir una pérdida de un sólo objeto cada 10.000 años.
	- Igual para todas las clases de almacenamiento
- Disponibilidad:
	- Mide la disponibilidad de un servicio.
	- Varía dependiendo de la clase de almacenamiento.
	- Ejemplo: S3 estándar tiene 99.99% de disponibilidad = no disponible 53 minutos al año.

### S3 Standard - propósito general
- 99.99% de disponibilidad.
- Usado para acceso de data frecuente.
- Baja latencia y alto rendimiento.
- Sostiene 2 fallas simultáneas en las instalaciones.
- Casos de uso: Análisis Big Data, aplicación de videojuegos, distribución de contenido.

### S3 Standard - acceso infrecuente
- Para data que es accedida poco frenente, pero requiere rápido acceso cuando se necesita.
- Menor costo que S3 Standard.

### S3 Standard - acceso infrecuente (IA)
- 99.99% de disponibilidad.
- Casos de uso: Recuperación de desastre, backups.

### S3 One Zone - acceso infrecuente
- Alta durabilidad (99.999999999%) en un sólo AZ, data perdida cuando la AZ se destruye.
- 99.5% de disponiblidad.
- Casos de uso: Guardar copias de backups secundario de data on-premise, o data que se puede recrear.

### S3 Glacier
- Bajo costo en almacenamiento de objetos por archivamiento/backup.
- Precio: precio por almacenamiento + costo por obtención de objetos.

### S3 Glacier Instant Retrieval
- Recuperación en milisegundos, ideal para data que es accedida una vez por trimestre.
- Mínima duración de almacenamiento de 90 días

### S3 Glacier Flexible Retrieval
- Expedido (1 a 5 minutos), estándar (3 a 5 horas), Bulk (5 a 12 horas).
- Mínima duración de almacenamiento de 90 días.

### S3 Glacier Deep Archive - para término largo de almacenamiento
- Estándar (12 horas), Bulk (48 horas).
- Mínima duración de almacenamiento de 180 horas.

### S3 Intelligent-Tiearing
- Pequeño monitoreo mensual y tarifa de clasificación automática.
- Mueve objetos automáticamente entre Access Tiers basado en el uso.
- No hay cargos de recuperacion en S3 Intelligent-Tiering.
- Frequent Accesstier (automático): tier por defecto.
- Infrequent Access tier (automático): objetos no accedidos por 30 días.
- Archive Instant Access tier (automático): objetos no accedidos por 90 días.
- Archive Access tier (opcional): configurable desde 90 días hasta 700+ días.
- Deep Archive Access tier (opcional): configurable desde 180 días hasta 700+ días.


# AWS CLI, SDK, roles IAM y políticas

## Metadata de instancia EC2 (IMDS)

- AWS EC2 Instance Metadata (IMDS) es muy poderosa pero a la vez una de las opciones menos conocidas por desarrolladores.
- Permite a las instancias EC2 "aprender sobre ellas mismas" sin usar ningún rol IAM para ese propósito.
- La URL es http://169.254.169.254/latest/meta-data.
- Puedes obtener el nombre del rol IAM desde la metadata, pero no puedes obtener la política IAM.
- Metadata = Information sobre la instancia EC2.
- Userdata = Lanzar script de la instancia EC2.

### IMDSv2 vs IMDSv1
- IMDSv1 es accesdida directamente http://169.254.169.254/latest/meta-data.
- IMDSv2 es más segura y es realizada en 2 pasos.
	- Obtener el Session Token (validez limitada) - usando headers y PUT `$ TOKEN='curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"'`.
	- Usando el Session Token en los llamados IMDSv2 - usando encabezados `curl http://169.254.169.254/latest/meta-data/profile -H "X-aws-ec2-metadata-token: $TOKEN"`.


## Perfiles AWS CLI

```bash
# Ver credenciales (se ven todas las creadas)
cat credentials

# Configurar credenciales
aws configure
<Nuestro-Access-Key-ID>
<Nuestro-Secret-Access-Key>
<Nombre-Region-Por-Defecto>
<Formato-Salida>

# Configurar un perfil en específico (puede ser nuevo)
aws configure --profile my-other-aws-account
<Nuestro-Access-Key-ID>
<Nuestro-Secret-Access-Key>
<Nombre-Region-Por-Defecto>
<Formato-Salida>

# Ver datos configurados
cat config

# Listar buckets S3
aws s3 ls

# Listar buckets S3 desde un perfil específico
aws s3 ls --profile my-other-aws-account
```


## AWS CLI con MFA

- Para usar MFA con el CLI, debemos crear una sesión temporal.
- Para hacerlo, debemos ejecutar el llamado al API **STS GetSessionToken**.
- **aws sts get-session-token** --serial-number arn-de-dispositivo-mfa --token-code codigo-desde-token --duration-seconds 3600.

```json
// Respuesta
{
	"Credentials": {
		"SecretAccessKey": "secret-access-key",
		"SessionToken": "temporary-session-token",
		"Expiration": "expiration-date-time",
		"AccessKeyId": "access-key-id"
	}
}
```


## AWS SDK

- ¿Qué pasaría si necesitamos ejecutar acciones en AWS directamente desde nuestras aplicaciones? (Sin usar el CLI).
- En estos casos podemos usar el SDK (Software Development Kit).
- Los SDKs oficiales son:
	- Java.
	- .NET.
	- Node.js.
	- PHP.
	- Python (boto3 / botocore).
	- Go.
	- Ruby.
	- C++.
- Tenemos que usar el SDK cuando programamos para servicios como DynamoDB.
- Un hecho gracioso... AWS CLI utiliza el SDK de Python (boto3).
- El examen espera que uno sepa cuándo debe utilizar un SDK.
- Bueno saber: si no se especifica o configura una región por defecto, entonces us-east-1 sera seleccionada por defecto.


## Límites AWS (Quotas)

- Rangos de limites en API:
	- **DescribeInstances** API para EC2 tiene un límite de 100 llamados por segundo.
	- **GetObject** en S3 tiene un limite de 5500 GET por segundo por prefijo.
	- Para errores intermitentes implementar Exponential Backoff.
	- Para errores consistentes solicitar un aumento del límite máximo del API.
- Cuotas de servicio (límites de servicio):
	- Correr instancias estandar On-Demand: 1152 vCPU.
	- Puedes solicitar el incremento del límite de servicio abriendo un ticket.
	- Puedes solicitar un incremento de la cuota de servicio usando el API **Service Quotas**.

### Exponential Backoff (para cualquier servicio AWS)
- Si estamos obteniendo intermitente la excepción **ThrottlingException**, usar Exponential Backoff.
- Mecanismo de reintento incluído en los llamados API del SDK.
- Debemos implementarlo nosotros mismos si estamos usando el API de AWS tal como esta o en casos especificos.
	- Debe implementarse sólo en reintentos en errores 5xx y limitaciones del servidor.
	- No implementarlo en errores de cliente 4xx.

En resumen lo que nos permite el Exponential Backoff es que en cada reintento se va duplicando el tiempo de espera, esto nos permite tener cada vez menos carga en el servidor, lo que permitira que el servidor reciba tantas request como sea posible.


## AWS CLI Credentials Provicer Chain

- El CLI va a buscar credenciales en este orden:
	1. Opciones de línea de comando - --region, --output y --profile.
	2. Variables de entorno - AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY y AWS_SESSION_TOKEN.
	3. Archivo CLI de credenciales - aws configure c:\Users\user\.aws\credentials en Windows, ~/.aws/credentials en Linux.
	4. Archivo de configuracion CLI - aws configure c:\Users\user\.aws\config en Windows, ~/.aws/config en Linux.
	5. Contenedor de credenciales - para tareas ECS.
	6. Perfil de instancia de credenciales - para perfiles de instancias EC2.

### Escenario AWS Credentials
- Una aplicación desplegada en una instancia EC2 esta usando variables de entorno con credenciales desde un usuario IAM para llamar el API de Amazon S3.
- El usuario IAM tiene permisos S3FullAccess.
- La aplicación sólo usa un bucket S3, así que acorde a las mejores prácticas:
	- Un rol IAM y un perfil de la instancia EC2 fue creado para la instancia EC2.
	- El rol fue asignado con los permisos mínimos para acceder ese bucket S3.
- El perfil de instnacia IAM fue asignado a la instancia EC2, pero sigue teniendo acceso a todos los buckets S3, ¿por qué?
	- La cadena de credenciales sigue dando prioridad a las variables de entorno.

### Mejores prácticas para AWS Credentials
- Sobre todo, nunca guardar las credenciales AWS en nuestro código.
- La mejor práctica es heredar las credencias desde la cadena de credenciales.
- Si estamos trabajando en AWS, usar roles IAM.
	- => Roles de instancias EC2 para instancias EC2.
	- => Roles ESC para tareas ECS.
	- => Roles Lambda para funciones Lambda.
- Si estamos trabajando por fuera de AWS, utilizar variables de entorno / perfiles nombrados.


## Llamados API para logueo en AWS

- Cuando llamamos la API HTTP de AWS, nosotros logueamos la solicitud, asi AWS puede identificarnos, usando nuestras credenciales AWS (access key y secret key).
- **Nota:** Algunas request a Amazon S3 no necesitan el logueo.
- Si usas el SDK o el CLI, los llamados HTTP son firmados por nosotros.
- Podemos firmar un llamado HTTP a AWS usando Signature v4 (SigV4).

### Ejemplo llamados SigV4
- Opción Header (firma en el Authorization header).
- Query String, ex: URL S3 pre-firmada (firma en X-Amz-Signature).


# S3 avanzado

## Storage Classes

- Podemos transicionar objetos entre diferentes clases de almacenamiento.
- Para objetos infrequentemente accedidos, moverlos al **Standard IA**.
- Para objetos archivados a los que no necesitamos acceder de forma veloz, podemos moverlos a **Glacier** o **Glacier Deep Archive**.
- Mover los objetos puede ser automatizado usando **Lifecycle Rules**.

### Lifecycle Rules
- Acciones de transición - configurar objetos para transicionar a otra clase de almacenamiento.
	- Mover objetos a Standard IA 60 días después de su creación.
	- Mover a Glacier para archivar después de 6 meses.
- Acciones de expiración - configurar objetos para expirarlos (eliminarlos) después de cierto tiempo.
	- Archivos de logs de acceso pueden ser configurados para eliminar después de 365 días.
	- Puede ser usado para eliminar versiones viejas de archivos (si el versionamiento esta habilitado).
	- Puede ser usado para eliminar cargas Multi-Part incompletas.
- Las reglas pueden ser creadas para cierto prefijo (ejemplo: s3://mybucket/mp3/*).
- Las reglas pueden ser creadas para ciertos Tags de objetos (ejemplo: Department: Finance).


## S3 Event Notifications

- S3:ObjectCreated, S3:ObjectRemoved, S3:ObjectRestore, S3:Replication...
- Filtrando por posible nombre de objeto (*.jpg).
- Caso de uso: Generar thumbnails de imágenes cargadas a S3.
- Se pueden crear tantos eventos S3 como sea necesario.
- Las notificaciones de eventos S3 normalmente reconocen los eventos en segundos pero puede tomar algunas veces uno o más minutos.

### IAM Permissions
- Son requeridos permisos IAM para configurar estas notificaciones de eventos.
- Dependiendo de si usamos SNS o SQS o una función Lambda tendremos que configurar una política de acceso del recurso para que se ejecuten correctamente.

### Event Bridge
Se puede configurar el Event Bridge (el cual recibe todos los eventos que se ejecutan), y una vez recibidos los eventos podemos destinarlo a hasta 18 servicios AWS como destino.


## Rendimiento S3

- Amazon S3 automaticamente escala los rangos de solicitudes, latencia entre 100-200 ms.
- Nuestra aplicacion puede alcanzar hasta al menos 3500 PUT/COPY/POST/DELETE o 5500 GET/HEAD solicitudes por segundo por prefijo en un bucket.
- No hay limites de número de prefijos en un bucket.
- Ejemplo (ruta objeto => prefijo):
	- bucket/folder1/sub1/file => /folder1/sub1.
- Para carga Multi-Part:
	- Recomendada para archivos > 100mb, debe usarse para archivos > 5gb.
	- Puede ayudar a paralelizar las cargas.
- Aceleracion de transferencia S3:
	- Aumenta la velocidad de transferencia transfiriendo archivos a una ubicación de borde de AWS que reenviará los datos al depósito de S3 en la región de destino.
	- Compatible con carga multi-part.


## S3 Select y Glacier Select

- Retorna menos data usando SQL para mejorar el rendimiento de filtro del servidor.
- Puede filtrar por filas y columnas.
- Menos transferencia de red, menos costo de CPU.


## Tags en objetos S3 y metadata

- S3 User-Designed Object Metadata:
	- Cuando cargamos un objeto, podemos asignar metadata.
	- Parejas nombre-valor (key-value).
	- Los nombres de user-defined metadata deben empezar con "x-amz-meta-".
	- Amazon S3 guarda las keys de esta metadata en minúsculas.
	- La metadata puede ser obtenida cuando se obtiene un objeto.
- S3 Object Tags:
	- Parejas Key-value para objetos en Amazon S3.
	- Útil para permisos detallados (sólo acceso especifico a objetos con tags especificos).
	- Útil para propósitos analíticos (usando S3 Analytics para agrupar por tags).
- No se puede buscar la metadata del objeto y tags.
- En vez de esto, debes usar una base de datos externa como un índice de búsqueda como DynamoDB.


# Seguridad en S3

## Encriptación en S3

- Podemos encriptar objetos en los buckets de S3 usando uno de 4 métodos:
	- Server-Side Encryption (SSE).
		- Server-Side Encryption con Amazon S3-Managed Keys (SSE-S3) - habilitado por defecto.
			- Encripta los objetos S3 usando llaves administrados y propias de AWS.
			- El tipo de encriptación es **AES-256**.
			- Debe configurarse el header "x-amz-server-side-encryption":"AES256".
			- Habilitado por defecto para nuevos buckets y nuevos objetos.
		- Server-Side Encryption con KMS Keys almacenadas en AWS KMS (SSE-KMS).
			- Aprovechar AWS Key Management Service (AWS KMS) para administrar llaves de encriptación.
			- Ventajas de KMS: control de usuario + auditoría del uso de las llaves con CloudTrail.
			- El objeto es encriptado del lado del servidor.
			- Se debe configurar el header "x-amz-server-side-encryption":"aws:kms".
			- Si se utiliza SSE-KMS, podemos ser impactados por los limites de KMS.
			- Cuando se hace una carga, se llama de forma automatica el API GenerateDataKey KMS.
			- Cuando se descarga, llama automáticamente el API Decrypt KMS.
			- Cuenta para la cuota de KMS por segundo (5500, 10000, 30000 solicitudes basado en la region).
			- Se puede incrementar la cuoda de solicitudes usando Service Quotas Console.
		- Server-Side Encryption con Customer-Provided Keys (SSE-C).
			- Cuando quieres administrar tus propias llaves de encriptación.
			- Utiliza llames completamente administradas por el cliente fuera de AWS.
			- Amazon S3 no guarda la llave de encriptación que nosotros le proveemos.
			- HTTPS debe ser usado.
			- La llave de encriptación debe ser proveída en los headers HTTP, para cada llamado HTTP realizado.
	- Client-Side Encryption.
		- Usa librerías de clientes tales como Amazon S3 Client-Side Encryption Library.
		- Los clientes deben encriptar la data por ellos mismos antes de enviarla a Amazon S3.
		- Los clientes deben desencriptar la data ellos mismos cuando la obtienen desde Amazon S3.
		- El cliente administra completamente las llaves y el ciclo de encriptación.
	- Amazon S3 Encriptyon in transit (SSL/TLS).
		- La encriptación en vuelo es también llamada SSL/TLS.
		- Amazon S3 expone 2 endpoints:
			- HTTP Endpoint - no encriptado.
			- HTTPS Endpoint - encriptación en vuelo.
		- HTTPS es recomentado.
		- HTTPS es obligatorio en SSE-C.
		- La mayoría de los clientes usarian HTTPS por defecto.
- Es importante entender cuales son para cuáles situaciones en el exámen.


## S3 CORS

- Cross Origin Resource Sharing (CORS).
- Origin = esquema (protocolo) + host (dominio) + puerto.
	- Ejemplo: htps://www.example.com (el puerto implicado es 443 para HTTPS, 80 para HTTP).
- Buscador web basado en el mecanismo de permitir request de otros orígenes mientras se visita el origen principal.
- Si un cliente hace una solicitud cross-origin en nuestro bucket S3, necesitamos habilitar el correcto header CORS.
- Es una pregunta muy popular en el examen.
- Puedes permitir para orígenes específicos o para todos los orígenes.


## MFA Delete

- El MFA (Multi-Factor Authentication) obliga a los usuarios a generar un código en un dispositivo (usualmente un celular o hardware) antes de hacer operaciones importantes en S3.
- MFA será requerido para:
	- Eliminar de forma permanente una versión de un objeto.
	- Suspender el versionamiento en el bucket.
- MFA no sera requerido para:
	- Habilitar el versionamiento.
	- Listar las versiones eliminadas.
- Para usar el MFA Delete, el versionamiento debe de estar habilitado en el bucket.
- Sólo la cuenta raíz (el propietario) puede habilitar/deshabilitar el MFA Delete.

```bash
# Habilitar MFA Delete
aws s3api put-bucket-versioning --bucket nuestro-bucket --versioning-configuration Status=Enabled,MFADelete=Enabled --mfa "nuestro-arn codigo-mfa" --profile nuestro-perfil

# Deshabilitar MFA Delete
aws s3api put-bucket-versioning --bucket nuestro-bucket --versioning-configuration Status=Enabled,MFADelete=Disabled --mfa "nuestro-arn codigo-mfa" --profile nuestro-perfil
```


## Access Logs

- Para propósitos de auditoría, tú podrías querer guardar logs de todo el acceso a los buckets S3.
- Cualquier solicitud hecha a S3, desde cualquier cuenta, autorizada o denegada, será guardada en los logs dentro de otro bucket S3.
- Los datos pueden ser analizados utilizando herramientas de análisis de datos.
- El bucket para guardar los logs debe de estar en la misma región AWS.

### Advertencias
- No configurar el bucket principal como el bucket para guardar logs y el bucket a ser monitoreado, ya que generará un bucle infinito y nuestro bucket crecerá exponencialmente.


## Amazon S3 Pre-Signed URLs

- Se pueden generar las URLs pre-firmadas utilizando la consola de S3, AWS CLI o SDK.
- La expiración de las URLs:
	- Consola S3 - de 1 minuto a 720 minutos (12 horas).
	- AWS CLI - configura la expiración con el parámetro --expires-in en segundos (por defecto 3600 segundos, máximo 604800 segundos - 168 horas).
- Los usuarios dan una URL pre-firmada heredando los permisos del usuario que generó la URL para GET / PUT.


## Access Points

- Los puntos de acceso nos permiten segmentar el acceso por usuario, supongamos que los usuarios del área financiera necesitan acceder a los archivos de financias, con el access point lo podemos hacer fácilmente.
- Simplifican la administración de seguridad de nuestros buckets.
- Cada punto de acceso tiene:
	- Su propio DNS (origen de Internet o VPC).
	- Una política de punto de acceso (similar a las politicas de los buckets).


# AWS CloudFront

- Content Delivery Network (CDN).
- Mejora el rendimiento de lectura, y el contenido es cacheado.
- Mejora la experiencia de usuario.
- 216 puntos de presencia de manera global.
- Protección DDoS (gracias a worldwide), integración con Shield, AWS Web Application Firewall.


## Orígenes

- Bucket S3.
	- Para distribuir archivos y cachearlos.
	- Seguridad mejorada con CloudFront Origin Access Control (OAC).
	- OAC está reemplazando Origin Access Identity (OAI).
	- CloudFront puede ser usado como entrada (para cargar archivos a S3).
- Custom Origin (HTTP).
	- Balanceador de carga.
	- Instancia EC2.
	- Sitio S3 (debe ser habilitado primero. elbucket como un S3 de sitio web estático).
	- Cualquier backend HTTP que queramos.


## Caching

- El caché vive en cada ubicación de CloudFront.
- CloudFront identifica cada objeto en el cache usando el Cache Key.
- Desea maximizar la proporción de aciertos de caché para minimizar las solicitudes al origen.
- Se puede invalidar parte del cache usando el API CreateInvalidation.

### Cache Key
- Un identificador único para cada objeto en el caché.
- Por defecto, consiste en el hostname + porción de la URL del recurso.
- Puedes agregar otros elementos (encabezados HTTP, cookies, query strings) al Cache Key usando CloudFront Cache Policies.

### Cache Policy
- Cache basada en:
	- Encabezados HTTP: None - Whitelist.
	- Cookies: None - Whitelist - Include All-Except - All.
	- Query Strings: None - Whitelist - Include All-Except - All.
- Controla el TTL (0 segundos a 1 año), puede ser modificado por origen usando el encabezado Cache-Control, encabezado Expires...
- Podemos crear nuestra propia política sin usar las políticas administradas predefinidas.

### Origin Request Policy
- Especifica valores que queremos incluir en las solicitudes de origen sin incluir en ellas el Cache Key (no hay contenido cacheado duplicado).
- Podemos incluir:
	- Encabezados HTTP: None - Whitelist - todos los headers de visualizador.
	- Cookies: None - Whitelist - Include All-Except - All.
	- Query Strings: None - Whitelist - Include All-Except - All.
- Habilidad de agregar encabezados HTTP CloudFront y endabezados poersonalizados a una solicitud al origen que no fueron incluidas en la solicitud de visualizador.
- Crear nuestras propias políticas o usar las políticas administradas predefinidas.


## Cache Invalidations

- En caso que actualicemos el origen back-end, CloudFront no sabe sobre esto y sólo refrescará el contenido después de que la TTL expira.
- Sin embargo, podemos obligar a refrescar la cache completa o una parte de esta ejecutando un CloudFront Invalidation.
- Podemos invalidar todos los archivos (*) o un path en específico (/images/*).


## Cache Behaviors

- Configure diferentes ajustes para un patrón de ruta de URL determinado.
- Ejemplo: Un comportamiento específico de cacheo para los archivos images/*.jpg en nuestro servidor de origen web.
- Enrutar a diferentes tipos de grupos de orígenes basado en el tipo de contenido o el patrón de ruta.
	- /images/*.
	- /api/*.
	- /* (comportamiento de cacheo por defecto).
- Cuando se agregan nuevos comportamientos de cacheo, el comportamiento de cacheo por defecto es siempre el último en ser procesado y siempre es /*.


## Geo Restricción

- Podemos restringir quién accede a nuestra distribución.
	- Allowlist: Permite a los usuarios acceder al contenido sólo si están en uno de los paíes en una lista de países aprobados.
	- Blocklist: Previene el acceso de los usuarios a nuestro contenido si están en uno de los países en una lista de países baneados.
- El país es determinado usando una base de datos Geo-IP de terceros.


## URL firmadas / Cookies firmadas

- Queremos distribuir contenido compartido pago a usuarios premium alrededor del mundo.
- Podemos usar CloudFront Signed Url / Cookie. Adjuntamos la política con:
	- Incluye expiración URL.
	- Incluye rangos de IP desde los que se puede acceder a la data.
	- Firmantes de confianza (en los que las cuentas AWS pueden crear URLs firmadas).
- ¿Cuánto tiempo debe ser válida mi URL?
	- Contenido compartido (películas, música): Hacerlo corto (unos cuántos minutos).
	- Contenido privado (privado para el usuario): Podemos hacerlo por años.
- Signed URL = acceso a archivos individuales (una URL firmada por archivo).
- Signed Cookies = acceso a múltiples archivos (una cookie firmada para muchos archivos).


## Costos

- CloudFront está en todo el mundo.
- Los costos de la data por ubicacion puede variar.
- Puede reducir la cantidad de ubicaciones para reducir costos.
- Hay tres clases de costos:
	1. Price Class All: Todas las regiones - mejor rendimiento.
	2. Price Class 200: La mayoría de regiones, pero excluye las regiones más costosas.
	3. Price Class 100: Sólo las regiones más baratas.


## Origen múltiple

- Enruta a diferentes tipos de orígenes basado en el tipo de contenido.
- Basado en el patron de la ruta:
	- /images/*.
	- /api/*.
	- /*.


## Grupos de origen

- Para incrementar la alta disponibilidad y hacer conmutación de error.
- Origin Group: Un origen primario y uno secundario.
- Si el primario falla, el segundo es usado.


## Field Level Encryption

- Protege la información sensigle del usuario a través de la pila de aplicación.
- Agrega una capa adicional de seguridad con HTTPS.
- Información sensible encriptada en un punto cercano al usuario.
- Usa encriptación asimétrica.
- Uso:
	- Especifica los campos a configurar en solicitudes POST que tú quieres que sean encriptados (hasta 10 campos).
	- Especifica la llave pública para encriptarlos.


## Real Time Logs

- Obtiene las solicitudes recibidas en tiempo real por CloudFront enviadas a Kinesis Data Streams.
- Monitorea, analiza y toma acción basado en el rendimiento de entrega de contenido.


# ECS, ECR y Fargate

## ¿Qué es Docker?

- Es una plataforma de desarrollo de software para desplegar apps.
- Las aplicaciones están empaquetadas en contenedores que pueden correr en cualquier sistema operativo.
- Las apps corren igual, sin importar dónde están corriendo:
	- Cualquier máquina.
	- No hay problemas de compatibilidad.
	- Comportamiento predecible.
	- Menos trabajo.
	- Fácil de mantener y desplegar.
	- Funciona con cualquier lenguaje, cualquier sistema operativo, cualquier tecnología.
- Caso de uso: arquitectura de microservicios, levantar y cambiar apps desde on-premises hacia AWS cloud.


## ¿Dónde se guardan las imágenes de Docker?

- Son almacenadas en repositorios de Docker.
- Docker Hub (https://hub.docker.com).
	- Repositorio público.
	- Se pueden encontrar imágenes base para muchas tecnologías o sistemas operativos (Ubuntu, MySQL, etc).
- Amazon ECR (Amazon Elastic Container Registry):
	- Repositorio privado.
	- Repositorios públicos (Amazon ECR Public Gallery https://gallery.ecr.aws).


## Docker vs Máquinas virtuales

- Docker es "más o menos" una tecnología de virtualización, pero no exactamente.
- Los recursos son compartidos con el host => muchos contenedores en un servidor.


## Administración de contenedores de Docker en AWS

- Amazon Elastic Container Service (Amazon ECS)-
	- Plataforma de contenedores propia de Amazon.
- Amazon Elastic Kubernetes Service (Amazon EKS).
	- Kubernetes administrados por Amazon (open source).
- AWS Fargate
	- Plataforma de contenedores Serverless propia de Amazon.
	- Funciona con ECS y con EKS.
- Amazon ECR.
	- Tienda de imágenes de contenedores.


## Amazon ECS - EC2 Launch Type

- ECS = Elastic Container Service.
- Lanzar contenedores de Docker en AWS = Lanzar tareas ECS en Clusters ECS.
- EC2 Launch Type: debemos provisionar y mantener la infraestructura (las instancias EC2).
- Cada instancia EC2 debe correr el agente ECS para registrar en el Clúster ECS.
- AWS se encarga de encender/apagar los contenedores.


## Amazon ECS - Fargate Launch Type

- Lanzar los contenedores Docker en AWS.
- No provisionamos la infraestructura (lo hay instancias EC2 para administrar).
- Todo es Serverless.
- Podemos crear definiciones de tareas.
- AWS sólo corre tareas ECS para nosotros basado en la CPU/RAM que necesitemos.


## Amazon ECS - Roles IAM para ECS

- EC2 Instance Profile (sólo para EC2 Launch Type):
	- Usado para el agente ECS.
	- Hacer llamados al API del servicio ECS.
	- Enviar logs del contenedor a los logs de CloudWatch.
	- Descargar imágenes de Docker desde ECR.
	- Referencia a los datos sensibles en Secrets Manager o SSM Parameter Store.
- ECS Task Role:
	- Permite tener un rol específico a cada tarea.
	- Usa diferentes roles para los diferentes servicios ECS que ejecutemos.
	- Task Role está definido en la definición de tarea.


## Amazon ECS - Load Balancer Integrations

- **Application Load Balancer** soportado y funciona para la mayoría de casos de uso.
- **Network Load Balancer** recomendado sólo para casos de uso de altas cargas/rendimiento, o para emparejar con AWS Private Link.
- **Classic Load Balancer** soportado pero no recomendado (no hay actualizaciones, no Fargate).


## Amazon ECS - Data Volumes (EFS)

- Monta EFS file systems en las tareas ECS.
- Trabaja con tanto EC2 como Fargate Launch Types.
- Las tareas que se ejecutan en cualquier AZ compartirán la misma data en el EFS file system.
- Fargate + EFS = Serverless.
- Casos de uso: almacenamiento compartido en múltiples AZ para nuestros contenedores.
- Nota: Amazon S3 no puede ser montado como un file system.


## ECS Service Auto Scaling

- Automáticamente incrementar/decrementar el número deseado de tareas ECS.
- Amazon ECS Auto Scaling utiliza AWS Application Auto Scaling.
	- Promedio de uso de CPU en servicio ECS.
	- Promedio de uso de memoria en servicio ECS - escalar en RAM.
	- Recuento de solicitudes de ALB por objetivo - métrica desde ALB.
- Target Tracking - escalado basado en un valor específico de las métricas de CloudWatch.
- Step Scaling - escalas basadas en una alarma especificada en CloudWatch.
- Scheduled Scaling - escalas basadas en una fecha/tiempo específico (cambios predecibles).
- ECS Service Auto Scaling (nivel de tarea) $\ne$ EC2 Auto Scaling (nivel de instancia EC2).
- Fargate Auto Scaling es mucho más fácil de configurar (porque es Serverless).


## ECS Rolling Updates

- Cuando actualizamos de la v1 a la v2, podemos controlar cuántas tareas pueden iniciarse y detenerse, y en qué orden.


## Tareas ECS invocadas por Event Bridge

Podemos tener un bucket en S3, y cada que por medio del API o el SDK se obtenga un objeto en este bucket podemos lanzar un evento a EventBridge, este ejecuta una tarea en ECS la cuál guarda un registro en DynamoDB.


## Amazon ECS Task Definitions

- Las tareas son metadatos en formato JSON para decirle a ECS cómo correr un contenedor de Docker.
- Contiene información crucial, como:
	- Nombre de la imagen.
	- Puerto abierto entre el contenedor y el hist.
	- Memoria y CPU requerida.
	- Variables de entorno.
	- Información de red.
	- Rol IAM.
	- Configuración de logueo (por ejemplo CloudWatch).
- Se pueden definir hasta 10 contenedores en una definición de tarea.


## Amazon ECS - Task Placements

- Las estrategias de asignación de tareas son el mejor esfuerzo.
- Cuando Amazon ECS coloca tareas, utiliza el siguiente proceso para seleccionar las instancias de contenedores:
	1. Identificar las instancias que satisfacen los requisitos de CPU, memoria y puerto en la definición de tarea.
	2. Identificar las instancias que satisfacen las restricciones de colocación de tareas.
	3. Identificar las instancias que satisfacen las estrategias de colocación de tareas.
	4. Seleccionar las instancias para la colocación de tareas.


## Amazon ECR

- ECR = Elastic Container Registry.
- Guarda y administra las imágenes de Docker en AWS.
- Repositorios privados y públicos (Amazon ECR Public Gallery https://gallery.ecr.aws).
- Totalmente integrado con ECS, respaldado por Amazon S3.
- El acceso es controlado mediante IAM (error de permisos => política).
- Soporta el escaneo de vulnerabilidad de imágenes, versionamiento, tags, ciclo de vida en imágenes, etc.

### AWS CLI
```bash
# Loguearse
aws ecr get-login-password --region <region> | docker login --username AWS --pasword-stdin <aws_account_id>.dkr.ecr.<region>.amazonaws.com

# Ver contraseña generada
aws ecr get-login-password --region <region>

# Push
docker push <aws-account_id>.drk.ecr<region>.amazonaws.com/demo:latest

# Pull
docker pull <aws-account_id>.drk.ecr<region>.amazonaws.com/demo:latest
```


## AWS CoPilot

- Herramienta CLI para construir, liberar y operar en producción aplicaciones en contenedores.
- Ejecutar nuestras apps en AppRunner, ECS y Fargate.
- Nos ayuda a enfocarnos en construir las apps en lugar de enfocarse en la infraestructura.
- Provisiona toda la infraestructura requerida para apps en contenedores (ECS, VPC, ELB, ECR, etc).
- Despliegues automatizados con un comando usando CodePipeline.
- Despliegues a muchos ambientes.
- Solución de problemas, logs, health status, etc.


## Amazon EKS

- Amazon EKS = Amazon Elastic Kubernetes Service.
- Es una forma de lanzar clusters de Kubernetes administrados en AWS.
- Kubernetes es un sistema open-source para despliegue automáticos, es calamiento y administración de aplicaciones en contenedores (usualmente Docker).
- Es una alternativa a ECS, similar objetivo pero diferente API.
- EKS soporta EC2 si queremos desplegar nodos de trabajo o Fargate para desplegar contenedores Serverless.
- Caso de uso: Si nuestra compañía ya está utilizando Kubernetes on-premises o en otra nube, y queremos migrar a AWS utilizando Kubernetes.
- Kubernetes es agnóstico en nube (es decir que puede ser utilizado en cualquier proveedor de servicios de computación en la nube).

### Tipo de nodos
- Managed Node Groups.
	- Crea y administra nodos (instancias EC2) para nosotros.
	- Los nodos son parte de un Auto Scaling Group (ASG) administrado por EKS.
	- Soporta instancias On-Demand o Spot.
- Self-ManagedNodes.
	- Los nodos son creados por nosotros y registrados en el clúster de EKS y administrados por un ASG.
	- Podemos preconstruir un AMI - Amazon EKS Optimized AMI.
	- Soporta instancias On-Demand o Spot.
- AWS Fargate.
	- No requiere mantenimiento, los nodos no son administrados.


# AWS Elastic Beanstalk

- Es una vista centralizada en el desarrollador para despliegue de una aplicación en AWS.
- Utiliza todos los componentes que hemos visto antes: EC2, ASG, ELB, RDS, etc.
- Servicio administrado:
	- Automáticamente modifica la capacidad de provisionamiento, balanceos de carga, escalamiento, tiene monitoreo de salud de la aplicación, etc.
	- Sólo el código de la aplicación es la responsabilidad del desarrollador.
- Seguimos teniendo control total sobre la configuración.
- BeanStalk es gratuito pero debemos pagar por todas las instancias subyacentes.


## Componentes

- Aplicación: colección de componentes de Elastic BeanStalk (ambientes, versiones, configuraciones, etc).
- Versión de aplicación: una iteración de nuestro código.
- Ambiente:
	- Colección de recursos AWS corriendo una versión de nuestra aplicación (sólo una versión de aplicación al tiempo).
	- Tiers: nivel de entorno de servidor web y nivel de entorno de trabajo.
	- Podemos crear múltiples ambientes (dev, test, prod, etc).


## Plataformas soportadas

- Go.
- Java SE.
- Java con Tomcat.
- .NET Core en Linux.
- .NET en Windows Server.
- Node.js.
- PHP.
- Python.
- Ruby.
- Packer Builder.
- Single Container Docker.
- Multi-container Docker.
- Preconfigured Docker.


## Modos de despliegue

- All at once (desplegar todo de una vez) - el más rápido, pero las instancias no están disponibles para servir el tráfico por un tiempo (downtime).
- Rolling: actualiza unas pocas instancias en un tiempo (bucket), y entonces se mueve al siguiente bucket una vez el primero está completamente bien.
- Rolling with additional batches: como el anterior, pero activa nuevas instancias para mover el lote (de modo que la aplicación anterior todavía esté disponible).
- Immutable: activa nuevas instancias en un nuevo ASG, despliega la versión a estas instancias y entonces intercambia todas las instancias una vez están estables.
- Blue Green: crea un nuevo ambiente y se cambia a otro cuando ya está listo.
- Traffic Splitting: canary testing - envía un pequeño porcentaje del tráfico a una nueva implementación.


## Beanstalk CLI

- Podemos instalar un CLI adicional llamado "EB CLI" el cual nos permite trabajar con Beanstalk desde el CLI fácilmente.
- Los comandos básicos son:
	- eb create.
	- eb status.
	- eb health.
	- eb events.
	- eb logs.
	- eb open.
	- eb deploy.
	- eb config.
	- eb terminate.

### Proceso de despliegue
- Describir dependencias (requirements.txt para Python, package.json para Node.js).
- Empaquetar el código en un .zip, y describir las dependencias.
	- Python: requirements.txt.
	- Node.js: package.json.
- Consola: cargar el archivo zip (crea una nueva versión de la app) y después desplegar.
- CLI: crea una nueva versión de la app usando el CLI (carga el zip), y después desplegar.
- Elastic Beanstalk desplegará el zip en cada instancia EC2, resolverá las dependencias e iniciará la aplicación.


## Beanstalk Lifecycle

- Puede almacenar hasta 1000 versiones de aplicaciones.
- Si no eliminamos las versiones anteriores, no podremos desplegar más.
- Para eliminar gradualmente las versiones antiguas de la aplicación, podemos utilizar un lifecycle policy.
	- Basado en tiempo (versiones viejas son removidasa).
	- Basado en espacio (cuando tienes muchas versiones).
- Las versiones que actualmente son usadas no serán eliminadas.
- Opción de no eliminar el paquete fuente en S3 para evitar la pérdida de datos.


# AWS CloudFormation

- Es una forma declarativa de delinear nuestra infraestructura de AWS, para cualquier recurso (la mayoría son soportados).
- Por ejemplo, con una plantilla de CloudFormation, podemos decir:
	- Quiero un grupo de seguridad.
	- Quiero 2 instancias EC2 usando este grupo de seguridad.
	- Quiero 2 Elastic IPs para estas instancias EC2.
	- Quiero un bucket S3.
	- Quiero un balanceador de carga (ELB) al frente de estas instancias EC2.
- Después CloudFormation crea todo esto por nosotros, en el orden correcto, con la configuración exacta que le especificamos.


## Beneficios

- Infraestructura como código.
	- Los recursos no son creados manualmente, lo cual es excelente para nuestro control.
	- El código puede estar controlado por versiones por ejemplo usando Git.
	- Los cambios de la infraestructura se revisan a través del código.
- Costos.
	- Cada recurso en la pila es tagueado con un identificados, así nosotros podemos verlo fácilmente cuánto cuesta.
	- Podemos estimar los costos de nuestros recursos usando la plantilla de CloudFormation.
	- Estrategias de ahorro: en desarrollo, podríamos automatizar la eliminación de las plantillas a las 5 p.m. y crearlas nuevamente a las 8 a.m. de forma segura.
- Productividad.
	- Habilidad de destruir y crear una infraestructura en la nube.
	- Generación automática del diagrama para nuestros templates.
	- Programación declarativa (no se necesita ordenamiento y orquestación).
- Separación de problemas: crear muchas pilas para muchas apps, y muchas capas.
	- Pilas VPC.
	- Pilas de red.
	- Pilas de app.
- No reinventar la rueda.
	- Aprovechar las plantillas existentes en la web.
	- Aprovechar la documentación.


## ¿Cómo funciona CloudFormation?

- Las plantillas deben ser cargadas en S3 y entonces referenciarlas en CloudFormation.
- Para actualizar una plantilla, no podemos editar la ya existente. Tenemos que cargar una nueva versión de la plantilla a AWS.
- Las pilas son identificadas por un nombre.
- Al eliminar una pila se elimina cada artefacto singular que fue creado por CloudFormation.


## Desplegar plantillas de CloudFormation

- Forma manual.
	- Editar plantillas en Application Composer o el editor de código.
	- Utilizar la consola para los parámetros de entrada.
- Forma automatizada.
	- Editar plantillas en un archivo YAML.
	- Usar AWS CLI para desplegar las plantillas, o usar una herramienta CD (Continuous Delivery).
	- La forma recomendada cuando queremos automatizar nuestro flujo.


## Bloques de construcción

- Componentes de las plantillas:
	- AWSTemplateFormatVersion - identifica las capacidades del template "2010-09-09".
	- Description - comentarios sobre la plantilla.
	- Resources (OBLIGATORIO) - nuestros recursos AWS declarados en la plantilla.
	- Parameters - las entradas dinámicas para nuestra plantilla.
	- Mappings - las variables estáticas para nuestra plantilla.
	- Outputs - referencias a lo que ha sido creado.
	- Conditions - lista de condiciones para realizar la creación de recursos.
- Template's Helpers
	- Referencias.
	- Funciones.

**Ejemplo:**
```yaml
AWSTemplateFormatVersion: "2010-09-09"

Description: Template for Lambda Sample.

Parameters:
  EnvName:
    Description: Name of an environment. 'dev', 'staging', 'prod' and any name.
    Type: String
    AllowedPattern: ^.*[^0-9]$
    ConstraintDescription: Must end with non-numeric character.

  LambdaHandlerPath:
    Description: Path of a Lambda Handler.
    Type: String
    AllowedPattern: ^.*[^0-9]$
    ConstraintDescription: Must end with non-numeric character.

Resources:
  LambdaRole:
    Type: AWS::IAM::Role
    Properties:
      RoleName: lambda-role
      AssumeRolePolicyDocument:
        Statement:
          - Action:
              - sts:AssumeRole
            Effect: Allow
            Principal:
              Service:
                - lambda.amazonaws.com
        Version: "2012-10-17"
      ManagedPolicyArns:
        - arn:aws:iam::aws:policy/AWSLambdaExecute
        - arn:aws:iam::aws:policy/AmazonS3FullAccess
        - arn:aws:iam::aws:policy/AmazonDynamoDBFullAccess
        - arn:aws:iam::aws:policy/AmazonKinesisFullAccess
      Path: /

  LambdaFunction:
    Type: AWS::Lambda::Function
    Metadata:
      guard:
        SuppressedRules:
          - LAMBDA_INSIDE_VPC
          - LAMBDA_FUNCTION_PUBLIC_ACCESS_PROHIBITED
    Properties:
      FunctionName: !Sub lambda-function-${EnvName}
      Description: LambdaFunction using python3.12.
      Runtime: python3.12
      Code:
        ZipFile: |
          import json

          def lambda_handler(event, context):
              print(json.dumps(event))
              return {
                  'statusCode': 200,
                  'body': json.dumps('Hello from Lambda!')
              }
      Handler: !Sub ${LambdaHandlerPath}
      MemorySize: 128
      Timeout: 10
      Role: !GetAtt LambdaRole.Arn
      Environment:
        Variables:
          ENV: !Ref EnvName
          TZ: UTC

Outputs:
  LambdaRoleARN:
    Description: Role for Lambda execution.
    Value: !GetAtt LambdaRole.Arn
    Export:
      Name: LambdaRole

  LambdaFunctionName:
    Value: !Ref LambdaFunction

  LambdaFunctionARN:
    Description: Lambda function ARN.
    Value: !GetAtt LambdaFunction.Arn
    Export:
      Name: !Sub LambdaARN-${EnvName}
```

### Intrinsic Functions
- Fn::Ref.
- Fn::GetAtt.
- Fn::FindInMap.
- Fn::ImportValue.
- Fn::Join.
- Fn::Sub.
- Fn::ForEach.
- Fn::ToJsonString.
- Funciones condicionales (Fn::If, Fn::Not, Fn::Equals, etc).
- Fn::Base64.
- Fn::Cidr.
- Fn::GetAZs.
- Fn::Select.
- Fn::Split.
- Fn::Transform.
- Fn::Length.

### Condition Functions
- Fn::And.
- Fn::Equals.
- Fn::If.
- Fn::Not.
- Fn::Or.


## Rollbacks

- Si la creación de la pila falla:
	- Por defecto: todo se elimina. Lo podemos ver en los logs.
	- Esta opción se puede deshabilitar y podremos debuguear para saber qué pasó.
- Falla la actualización de una pila:
	- La pila vuelve a su última versión estable.
	- Podemos ver en los logs qué pasó y los mensajes de errores.
- Error en el rollback: arreglar nuestros recursos manualmente, y luego llamar la API ContinueUpdateRollback desde la consola.


## Service Role

- Roles IAM que permiten a CloudFormation crear/actualizar/eliminar recursos de las pilas por su cuenta.
- Da la habilidad a los usuarios de crear/actualizar/eliminar los recursos de las pilas incluso si no tienen permisos para trabajar con los recursos en la pila.
- Casos de uso:
	- Queremos lograr el principio de privilegio mínimo.
	- Pero no queremos dar al usuario todos los permisos requeridos para crear recursos en la pila.
- El usuario debe tener los permisos iam:PassRole.


## Capabilities

- **CAPABILITY_NAMED_IAM** y **CAPABILITY_IAM**:
	- Deben estar habilitados cuando nuestra plantilla de CloudFormation está creando o actualizando recursos IAM (usuario IAM, rol, grupo, política, llaves de acceso, o perfil de instancia).
	- Especificar **CAPABILITY_NAMED_IAM** si los recursos son nombrados.
- **CAPABILITY_AUTO_EXPAND**:
	- Necesario cuando nuestra plantilla CloudFormation incluye Macros o pilas anidadas (pilas dentro de pilas) para realizar transformaciones dinámicas.
	- Estás reconociendo que tu plantilla puede cambiar antes de implementarla.
- **InsufficientCapabilitiesException**:
	- Excepción que generará CloudFormation si las capacidades no se han aceptado al implementar una plantilla (medida de seguridad).


## Política de eliminación

- Controla qué pasa cuando la plantilla de CloudFormation es eliminada o cuando un recurso es removido desde una plantilla de CloudFormation.
- Medida de seguridad extra para preservar y respaldar los recursos.
- Por defecto DeletionPolicy=Delete.
	- El valor Delete no funcionará en un bucket S3 si el bucket no está vacío.
- DeletionPolicy=Retain.
	- Especifica qué recursos preservar en caso de que suceda una eliminación en CloudFormation.
- DeletionPolicy=Snapshot.
	- Crea un snapfot final antes de eliminar el recurso.
	- Ejemplos de recursos soportados:
		- Volumen EBS, ElastiCache, Cluster, ElastiCache ReplicationGroup.
		- RDS DBInstance, RDS DBCluster, Redshift Cluster, Neptune DBCluster, DocumentDB DBCluster.


## Política de pila

- Durante una actualización en una pila de CloudFormation, todas las acciones de actualización son permitidas en todos los recursos (por defecto).
- Una política de pila es un documento JSON que define las acciones de actualización que están permitidas en recursos específicos durante las actualizaciones de pila.
- Protege los recursos de actualizaciones no intencionales.
- Cuando se configura una Stack Policy, todos los recursos en la pila son protegidos por defecto.
- Especifica un ALLOW (permitir) específico para los recursos que queremos permitir que sean actualizados.


## Recursos personalizados

- Son usados para:
	- Definir recursos que no son soportados aún por CloudFormation.
	- Definir el provisionamiento personalizado de recursos que pueden estar por fuera de CloudFormation (recursos on-premises, recursos de terceros).
	- Tener scripts personalizados para ejecutar durante la creación/actualización/eliminación mediante funciones Lambda (ejecutar una función Lambda para limpiar un bucket S3 antes de eliminarlo).


# AWS Integration & Messaging: SQS, SNS y Kinesis

- Cuando queremos desplegar múltiples aplicaciones, ellas inevitablemente necesitan comunicarse entre ellas.
- Hay 2 patrones de comunicación de aplicaciones.
	1. Comunicación sincrónica (aplicación a aplicación).
	2. Asíncrono / basado en eventos (aplicación a cola y después a aplicación).


## Amazon SQS

- Es un contenedor de mensajes enviados por productores (los que envían los mensajes) y estos son enviados a los consumidores (destinatarios).
- Standard Queue:
	- Oferta más antigua.
	- Servicio totalmente administrado, usado para aplicaciones desacopladas.
	- Atributos:
		- Rendimiento ilimitado, número de mensajes en cola ilimitado.
		- Retención de los mensajes: 4 días, máximo 14.
		- Latencia baja (<10ms en publicar y recibir).
		- Limitación de 256kb por mensaje enviado.
	- Puede tener mensajes duplicados.
- Producing Messages:
	- Producidos para SQS usando el SDK (API SendMessage).
	- El mensaje es persistente en SQS hasta que un consumer lo elimina.
	- Retención de mensajes: por defecto 4 días, hasta 14.
- Consuming Messages:
	- Consumers (corriendo en instancias EC2, servidores o funciones Lambda).
	- Sondea SQS por mensajes (puede recibir hasta 10 mensajes al tiempo).
	- Procesa los mensajes (ejemplo: insertar el mensaje en una base de datos RDS).
	- Elimina los mensajes usando la API DeleteMessage.
- Multiple EC2 Instances Consumers:
	- Los consumers reciben y procesan los mensajes en paralelo.
	- Al menos una entrega.
	- Mejor esfuerzo en ordenamiento de mensajes.
	- Los consumers eliminan los mensajes después de procesarlos.
	- Podemos escalar los consumers horizontalmente para mejorar el rendimiento de procesamiento.
- Seguridad en SQS:
	- Encriptación:
		- Encriptación in-flight usando API HTTPS.
		- Encriptación at-rest usando llaves KMS.
		- Encriptación client-side si el cliente quiere hacer la encriptación/desencriptación por su cuenta.
	- Control de acceso: políticas IAM para regular el acceso al API de SQS.
	- Políticas de acceso SQS (similar a las políticas de los bucket S3):
		- Útil para acces de cuentas cruzadas a las colas SQS.
		- Util para permitir a otros servicios (SNS, S3, etc) escribir en una cola SQS.

### Dead Letter Queue (DLQ)
- Si un consumidor falla en el procesamiento de un mensaje, con l Visibility Timeout, el mensaje regresa a la cola.
- Podemos configurar el límite de cuántas veces un mensaje puede regresar a la cola.
- Después de que el límite MaximumReceives es excedido, los mensajes van al DLQ.
- Muy útil para debuguear.
- El DLQ de una cola FIFO debe también ser una cola FIFO.
- El DQL de una cola estándar debe ser también una cola estándar.
- Debemos asegurarnos de procesar los mensajes en la DLQ antes de que expiren.
- Redrive to Source:
	- Característica que nos ayuda a consumir los mensajes en el DLQ para entender qué hay mal con ellos.
	- Cuando nuestro código es arreglado, podemos redireccionar los mensajes desde el DLQ de regreso a la cola fuente (o cualquier otra cola) en lotes sin escribir código personalizado.

### Delay Queues
- Retiene un mensaje (los consumers no lo ven inmediatamente) hasta por 15 minutos.
- Por defecto es 0 segundos (el mensaje está disponible de forma instantánea).
- Se puede colocar un valor por defecto a nivel de cola.
- Puede anular el valor por defecto en el envío usando el parámetro DelaySeconds.

### Long Polling
- Cuando un consumer solicita mensajes desde la cola, puede opcionalmente esperar por que los mensajes lleguen si no hay en la cola.
- Decrementa el número de llamados al API hechos por SQS mientras incrementa la eficiencia y baja la latencia de nuestra aplicación.
- El tiempo de espera puede ser entre 1 segundo y 20 (20 segundos preferiblemente).
- Long Polling es preferido sobre Short Polling.
- Puede ser habilitado a nivel de cola o a nivel del API usando ReceiveMessageWaitTimeSeconds.

### Extended Client
- El tamaño de los mensajes es 256kb, como enviar mensajes largos.
- Utiliza la librería de Java SQS Extended Client.

### Método de API que debemos conocer
- CreateQueue para crear una cola (MessageRetentionPeriod es un parámetro que podemos enviar para establecer cuánto tiempo debe mantenerse un mensaje en la cola antes de ser descartado), DeleteQueue para eliminar una cola y todos sus mensajes.
- PurgeQueue: elimina todos los mensajes en la cola.
- SendMessage para enviar mensajes (DelaySeconds para hacer una espera antes de enviar los mensajes), ReceiveMessage para recibir mensajes y DeleteMessage para borrar un mensaje.
- MaxNumberOfMessages: por defecto 1, máximo 10 (para API ReceiveMessage).
- ReceiveMessageWaitTimeSeconds: Long Polling.
- ChangeMessageVisibility: cambiar el timeout de mensajes.
- APIs por lote para SendMessage, DeleteMessage, ChangeMessageVisibility ayuda a bajar los costos.

### Colas FIFO
- FIFO = First In First Out (ordenamiento de mensajes en la cola).
- Limitado para 300 msg/s sin lotes, 3000 msg/s con lotes.
- Exactamente una capacidad de envío (eliminando duplicados).
- Los mensajes son procesados en orden por el consumer.
- Deduplication:
	- El intérvalo de desduplicación es de 5 minutos.
	- Hay 2 métodos:
		- Content based deduplication: hará un hash SHA-256 al cuerpo del mensaje.
		- Explícitamente provee un Messade Deduplication ID.
- Message Grouping
	- Si especificamos el mismo valor de MessageGroupID en una cola SQS FIFO, podemos solo tener un consumer, y todos los mensajes en orden.
	- Para tener orden a nidel de subconjunto de mensajes, especificar diferentes valores de MessageGroupID.
		- Los mensajes que comparten un Message Group ID en común serán ordenados dentro del grupo.
		- Cada ID de grupo puede tener un consumer diferente (procesamiento paralelo).
		- Ordenamiento entre grupos no garantizado.


## Amazon SNS

- ¿Qué pasaría si queremos enviar un mensaje para muchos destinatarios?
- El productor del evento sólo envía el mensaje a un tema SNS.
- Podemos tener muchos "receptores de eventos" (suscripciones) tanto como queremos escuchar las notificaciones de temas de SNS.
- Cada suscriptor al tema recibirá todos los mensajes (nota: nueva característica para filtrar mensajes).
- Hasta 12.500.000 suscripciones por tema.
- Límite de 100.000 temas.

SNS se integra con muchos servicios de AWS a los que podemos enviar los datos para notificaciones SNS, estos servicios son CloudWatch, AWS budgets, Lambda, ASG, buckets S3, DynamoDB, CloudFormation, AWS DMS, eventos RDS, etc.

### ¿Cómo publicar?
- Topic Publish (usando el SDK).
	- Crear un tema.
	- Crear una suscripción (o muchas).
	- Publicar el tema.
- Publicación directa (SDK para aplicaciones móviles).
	- Crear una plataforma de aplicación.
	- Crear un endpoint de plataforma.
	- Publicar el endpoint de la plataforma.
	- Funciona con Google GCM, Apple APSN, Amazon ADM...
- Encriptación:
	- Encriptación in-flight usando API HTTPS.
	- Encriptación at-rest usando llaves KMS.
	- Encriptación client-side si el cliente quiere hacer la encriptación/desencriptación por su cuenta.
- Control de acceso: políticas IAM para regular el acceso al API de SNS.
- Políticas de acceso SNS (similar a las políticas de los bucket S3).


## Kinesis

- Hace fácil recolectar, procesas y analizar data de sreaming en tiempo real.
- Ingesta data en tiempo real como: Logs de aplicación, métricas, clickstreams de sitios web, data telemétrica IoT.
- Kinesis Data Streams captura, procesa y guarda los streams de datos.
	- Retención entre 1 y 365 días.
	- Habilidad de reprocesar los datos.
	- Una vez la data es insertada en Kinesis, no puede ser eliminada (inmutabilidad).
	- La data que comparte la misma partición va al mismo shard (ordenamiento).
	- Producers: AWS SDK, Kinesis Producer Library (KPL), Kinesis Agent.
	- Consumers:
		- Escribirlos nosotros: Kinesis Client Library (KCL), AWS SDK.
		- Administrados: AWS Lambda, Kinesis Data Firehose, Kinesis Data Analytics.
- Kinesis Data Firehouse: cada los streams de los datos en AWS data stores.
- Kinesis Data Analytics: analiza los streams de datos con SQL o Apache Flink.
- Kinesis Video Streams: captura, procesa y guarda streams de videos.


## SQS vs SNS vs Kinesis

- SQS:
	- El consumer baja los datos.
	- Los datos son eliminados después de ser consumidos.
	- Puede tener tantos trabajadores (consumers) como queramos.
	- No necesita provisionamiento de rendimiento.
	- Ordenamiento garantizado sólo en colas FIFO.
	- Capacidad de delay en mensaje individual.
- SNS:
	- Carga los datos a muchs suscriptores.
	- Hasta 12.500.000 suscriptores.
	- La data no es persistente (se pierde si no se entrega).
	- Pub/Sub.
	- Hasta 100.000 temas.
	- No necesita provisionamiento de rendimiento.
	- Integración con SQS para patrón de arquitectura fan-out.
	- Capacidad FIFO para SQS FIFO.
- Kinesis:
	- Standard: bajar datos.
		- 2mb por shard.
	- Enhanced-fan out: carga data.
		- 2mb por shard por consumer.
	- Posibilidad de reenviar la data.
	- Destinado a big data en tiempo real, analíticas y ETL.
	- Ordenamiento a nivel de shard.
	- Los datos expiran después de cierto tiempo.
	- Modo de provisionamiento o  capacidad de modo on-demand.