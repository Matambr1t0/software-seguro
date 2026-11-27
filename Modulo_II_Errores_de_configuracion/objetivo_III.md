Cuando un cliente solicita una ruta correspondiente a un directorio (por ejemplo, `https://ejemplo.com/uploads/` o `https://ejemplo.com/assets/`), el servidor web busca primero un archivo predeterminado definido por la directiva de índice (`DirectoryIndex` en Apache o `index` en Nginx, habitualmente `index.html` o `index.php`).

Si dicho archivo no existe dentro de la carpeta solicitada:
1. **Con Directory Listing deshabilitado (comportamiento seguro):** El servidor deniega la enumeración del contenido y devuelve un código de estado HTTP `403 Forbidden` (o `404 Not Found`).
2. **Con Directory Listing habilitado:** El servidor genera dinámicamente una página HTML (`Index of /directorio`) que lista todos los archivos y subdirectorios presentes en esa ruta junto con enlaces directos para descargarlos o visualizarlos, además de metadatos como el tamaño y la fecha de última modificación.

---

### Archivos Sensibles Expuestos

Permitir la exploración libre de directorios facilita la fase de reconocimiento activo de un atacante, pudiendo exponer:

* **Copias de seguridad y volcados:** Archivos comprimidos o respaldos temporales (`.zip`, `.tar.gz`, `.bak`, `.old`, `.swp`) que contienen código fuente sin interpretar o estructuras completas del sitio.
* **Volcados de bases de datos:** Exportaciones en texto plano (`.sql`, `.db`, `.sqlite`) con registros de usuarios, hashes de contraseñas o datos personales.
* **Archivos de configuración y credenciales:** Ficheros ocultos o de entorno (`.env`, `config.php.bak`, `web.config`, `.git/config`) con claves de API, tokens y credenciales de conexión a bases de datos.
* **Logs del sistema y registros de errores:** Archivos `.log` que revelan rutas internas del servidor, versiones de software, consultas SQL fallidas o tokens de sesión.
* **Documentos internos o privados:** Facturas, reportes, archivos subidos por otros usuarios o scripts administrativos no enlazados públicamente.

---

### Mecanismos de Mitigación

La solución principal consiste en deshabilitar la generación automática de índices a nivel de configuración del servidor web:

1. **En Apache:**
   Eliminar la opción `Indexes` de la directiva `Options` en el archivo de configuración global (`apache2.conf` / `httpd.conf`), en el *VirtualHost* correspondiente o mediante un archivo `.htaccess`:
   ```apache
   <Directory /var/www/html>
       Options -Indexes
   </Directory>
