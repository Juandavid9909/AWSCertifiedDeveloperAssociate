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