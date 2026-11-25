# Objetivo 2: Análisis de la Tríada CIA

La seguridad de la información se apoya en tres pilares fundamentales conocidos como la **Tríada CIA** (*Confidentiality, Integrity, Availability*). Proteger un sistema o aplicación web implica garantizar que estos tres principios se mantengan intactos frente a fallos o ataques.

---

## 1. Confidencialidad (*Confidentiality*)

* **Explicación:** Consiste en asegurar que la información sensible sea accesible únicamente para las personas, cuentas o sistemas que tienen autorización explícita para verla. En una aplicación web, significa que los datos privados de un usuario (contraseñas, documentos, historial médico o datos bancarios) no queden expuestos a terceros ni a otros usuarios de la misma plataforma.
* **Ejemplo de ataque / vulnerabilidad web:** **IDOR (*Insecure Direct Object Reference* / Referencia Directa Insegura a Objetos).**
  * **Cómo compromete el pilar:** Ocurre cuando una aplicación web expone un identificador interno en la URL o en una petición (por ejemplo, `https://app.com/api/facturas?id=1054`) y el servidor no verifica si el usuario autenticado es realmente el dueño de ese recurso. Un atacante simplemente cambia el número a `1055` y lee la factura confidencial de otro cliente, violando directamente la privacidad y confidencialidad de los datos sin modificar ni tirar el sistema.

---

## 2. Integridad (*Integrity*)

* **Explicación:** Garantiza que los datos y el código de la aplicación se mantengan exactos, completos y auténticos, evitando que sean alterados, borrados o falsificados de manera no autorizada, ya sea mientras están almacenados en una base de datos o mientras viajan por la red.
* **Ejemplo de ataque / vulnerabilidad web:** **Inyección SQL (*SQL Injection* – sentencia `UPDATE` / `INSERT`) o CSRF (*Cross-Site Request Forgery*).**
  * **Cómo compromete el pilar:** Si un formulario o parámetro vulnerable permite ejecutar una consulta SQL no sanitizada (como `UPDATE usuarios SET saldo = 999999 WHERE id = 12`), el atacante altera directamente los registros de la base de datos. De forma similar, mediante un ataque **CSRF**, un atacante engaña al navegador de una víctima con sesión iniciada para que envíe una petición oculta que cambie el correo electrónico o la contraseña de su cuenta sin su consentimiento, corrompiendo la autenticidad de la información almacenada.

---

## 3. Disponibilidad (*Availability*)

* **Explicación:** Asegura que los servicios, sistemas y datos estén operativos y accesibles en tiempo y forma cada vez que un usuario legítimo necesite utilizarlos. De nada sirve que una web sea confidencial e íntegra si los usuarios no pueden entrar o el servidor no responde.
* **Ejemplo de ataque / vulnerabilidad web:** **DDoS (*Distributed Denial of Service* / Denegación de Servicio Distribuida) o ReDoS (*Regular Expression Denial of Service*).**
  * **Cómo compromete el pilar:** En un ataque **DDoS** a nivel de aplicación (Capa 7), miles de equipos comprometidos inundan simultáneamente un endpoint pesado (como un buscador interno sin paginación o una ruta de generación de reportes) con millones de peticiones HTTP. El servidor agota su memoria RAM y capacidad de CPU intentando responder, dejando el sitio web completamente caído e inaccesible para los clientes reales.

---

## Resumen Comparativo

| Pilar | ¿Qué protege? | Vulnerabilidad / Ataque Web | Impacto Directo |
| :--- | :--- | :--- | :--- |
| **Confidencialidad** | El secreto y privacidad de los datos. | **IDOR** (*Broken Access Control*) | Lectura o filtrado de información privada a usuarios no autorizados. |
| **Integridad** | La exactitud y autenticidad de la información. | **SQL Injection** (manipulación) / **CSRF** | Alteración, corrupción o falsificación de registros y configuraciones. |
| **Disponibilidad** | El acceso continuo y funcionamiento del servicio. | **DDoS** (Capa 7) / **ReDoS** | Saturación de recursos y caída del servidor para los usuarios legítimos. |
