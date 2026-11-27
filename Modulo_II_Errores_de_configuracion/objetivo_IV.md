### Mensaje de Error Genérico vs. Stack Trace en Producción

Cuando ocurre una excepción no controlada en el servidor, la aplicación puede responder de dos maneras opuestas según su configuración de entorno:

| Aspecto | Mensaje de Error Genérico | Stack Trace (Traza de la Pila) |
| :--- | :--- | :--- |
| **Información entregada** | Muestra un aviso breve y neutral al usuario final (por ejemplo, *"500 Internal Server Error - Ocurrió un problema inesperado"*), junto con un identificador interno de incidente si es necesario. | Expone la secuencia completa de llamadas a funciones/métodos activos al momento del fallo, rutas absolutas del sistema de archivos, números de línea y detalles de la excepción. |
| **Propósito** | Mantener la operatividad y usabilidad sin filtrar detalles de la arquitectura interna, registrando el error técnico únicamente en los logs privados del servidor. | Facilitar la depuración rápida durante la etapa de desarrollo (`DEBUG=True` o modo desarrollo). |
| **Impacto en seguridad** | Minimiza la fuga de información (*Information Disclosure*), obligando al atacante a trabajar a ciegas (*Black-Box*). | Convierte la auditoría en un escenario de caja semi-blanca (*Grey-Box*), revelando cómo está construida y estructurada la aplicación por dentro. |

---

### ¿Por qué un Stack Trace facilita la explotación?

Exponer una traza detallada en producción acelera el trabajo de un atacante durante el reconocimiento y la explotación por los siguientes motivos:

1. **Identificación exacta de tecnologías y versiones (Fingerprinting):**
   Las clases, paquetes y rutas revelan el lenguaje, el framework y las librerías exactas en uso (por ejemplo, `Express`, `Spring Boot`, `Django`, `PHP/PDO`). Con esto, el atacante puede buscar vulnerabilidades públicas conocidas (CVE) específicas para esa versión.

2. **Exposición de rutas internas y estructura del proyecto:**
   El Stack Trace muestra rutas absolutas del servidor (como `/var/www/app/src/controllers/auth.js:42`), lo que confirma el sistema operativo subyacente (Linux o Windows) y facilita ataques como *Path Traversal* o la inclusión/lectura de archivos locales.

3. **Filtración de lógica de negocio y consultas a bases de datos:**
   Ante errores de base de datos, la traza suele incluir fragmentos de consultas SQL, nombres de tablas, columnas o el motor utilizado (PostgreSQL, MySQL, SQLite). Esto simplifica enormemente el diseño de payloads para **Inyección SQL (SQLi)** al eliminar la necesidad de adivinar la estructura de la base.

4. **Visibilidad sobre validaciones y parámetros:**
   Ver qué función falló y qué tipo de dato esperaba permite al atacante entender cómo se procesan las entradas del usuario y qué filtros o sanitizaciones existen (o faltan) antes de llegar al punto crítico.
