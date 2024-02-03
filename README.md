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


## EBS vs EFS
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