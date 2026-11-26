## ¿Qué es un ataque SSRF?
Un ataque **SSRF** (*Server-Side Request Forgery* o falsificación de petición del lado del servidor) ocurre cuando una aplicación web permite que el usuario ingrese una URL o dirección (por ejemplo, para importar una foto de perfil desde un enlace o generar un PDF) y el **servidor hace esa petición HTTP en su nombre** sin validar a dónde apunta.

El peligro es que el atacante puede obligar al servidor a conectarse a direcciones internas que no están expuestas a internet, usando al propio servidor como puente.

---

## ¿Qué es la dirección `169.254.169.254` y qué pasa al consultarla?

En proveedores de nube como **Amazon Web Services (AWS)**, Google Cloud (GCP) o Azure, la dirección IP **`169.254.169.254`** corresponde al **Servicio de Metadatos de la Instancia** (*Instance Metadata Service* o **IMDS**).

* **Es una IP interna especial (*link-local*):** Solo es accesible desde adentro de la propia máquina virtual o servidor alojado en la nube; nadie desde afuera en internet puede llegar a ella directamente.
* **No pide contraseña por defecto (IMDSv1):** En su versión clásica, este servicio asume que cualquier petición que venga desde adentro del propio servidor es legítima y devuelve información crítica sin pedir autenticación.

Cuando el atacante engaña a la aplicación para que haga una petición `GET http://169.254.169.254/latest/meta-data/`, el servidor lee sus propios metadatos internos y se los muestra al atacante.

---

## ¿Qué información altamente sensible busca extraer el atacante?

El objetivo principal del atacante al consultar esa IP en AWS es robar **credenciales de acceso a la nube** para tomar el control de toda la infraestructura:

1. **Credenciales temporales de IAM (El premio mayor):**
   * Al navegar hasta la ruta `/latest/meta-data/iam/security-credentials/<nombre-del-rol>`, el servicio entrega en texto plano el **`AccessKeyId`**, el **`SecretAccessKey`** y el **`SessionToken`** asignados al servidor.
   * Con esas tres claves, el atacante puede configurar AWS CLI en su propia computadora y actuar con todos los permisos que tenga ese servidor (leer buckets privados de S3, descargar bases de datos, borrar servidores o crear máquinas nuevas para minar criptomonedas).
2. **Datos de configuración e inicialización (`user-data`):**
   * En la ruta `/latest/user-data`, suelen quedar guardados los scripts que se ejecutaron al crear el servidor, donde muchos desarrolladores dejan escritas contraseñas de bases de datos, claves de APIs externas o tokens privados.
3. **Información de red y arquitectura interna:**
   * IPs privadas, subredes, grupos de seguridad (*Security Groups*), claves públicas SSH y el identificador de la cuenta de AWS, datos útiles para seguir atacando otros servidores dentro de la red interna.

## ¿Cómo se soluciona?* **En la aplicación (Backend):** Validar estrictamente las URLs ingresadas por el usuario mediante una **lista blanca** de dominios permitidos y bloquear cualquier petición dirigida a rangos de IP privadas o reservadas (`127.0.0.1`, `10.0.0.0/8`, `192.168.0.0/16` y especialmente `169.254.169.254`).
* **En la infraestructura de AWS:** Desactivar IMDSv1 y exigir obligatoriamente **IMDSv2**, que obliga a generar un token de sesión mediante una petición `PUT` previa con cabeceras especiales antes de poder leer cualquier metadato, frenando la gran mayoría de ataques SSRF simples.
