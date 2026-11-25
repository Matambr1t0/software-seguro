# Objetivo 1: Estandarización de Vulnerabilidades (CVE y CWE)

En la industria de la ciberseguridad, estandarizar la forma en que se nombran, clasifican y documentan los fallos de seguridad permite que investigadores, fabricantes, equipos de defensa y herramientas automatizadas compartan información precisa sin ambigüedades.

---

## 1. ¿Qué es CVE (Common Vulnerabilities and Exposures)?
**CVE** es un diccionario o catálogo público que asigna un identificador único a **vulnerabilidades y exposiciones específicas** descubiertas en productos de software o hardware reales (por ejemplo, una falla concreta en una versión determinada de Apache, Windows o WordPress).

* **Formato del identificador:** `CVE-AÑO-NÚMERO` (por ejemplo, `CVE-2021-44228` para la vulnerabilidad crítica *Log4Shell* en Apache Log4j).
* **Objetivo:** Garantizar que cualquier especialista o herramienta que mencione un código CVE se refiera exactamente al mismo fallo técnico en un producto concreto.

---

## 2. ¿Qué es CWE (Common Weakness Enumeration)?
**CWE** es un catálogo comunitario que clasifica los **tipos de debilidades o errores de diseño/programación** que dan origen a las vulnerabilidades. A diferencia de CVE, no señala un producto específico, sino la **causa raíz** del fallo a nivel de arquitectura o código.

* **Formato del identificador:** `CWE-NÚMERO` (por ejemplo, `CWE-89` para *Inyección SQL* o `CWE-79` para *Cross-Site Scripting - XSS*).
* **Objetivo:** Servir como lenguaje común para describir patrones de fallos de seguridad, ayudar a los desarrolladores a prevenir errores recurrentes y categorizar vulnerabilidades.

---

## 3. Diferencia clave entre CVE y CWE

| Característica | CVE (*Vulnerabilidades y Exposiciones*) | CWE (*Enumeración de Debilidades*) |
| :--- | :--- | :--- |
| **¿Qué describe?** | Una instancia específica de un fallo en un producto y versión concretos. | El tipo o categoría general del error de código o diseño (causa raíz). |
| **Analogía** | Es el **diagnóstico puntual** de un paciente concreto en una fecha dada. | Es la **enfermedad o patología** general descrita en un manual médico. |
| **Ejemplo práctico** | `CVE-2024-XXXX` (fallo de inyección en el formulario de login de *App X v1.2*). | `CWE-89` (*Improper Neutralization of Special Elements used in an SQL Command* / SQL Injection). |
| **Relación** | Uno o muchos CVEs específicos suelen mapearse bajo un mismo CWE. | Un CWE agrupa múltiples CVEs distintos que comparten el mismo origen técnico. |

---

## 4. Autoridades de Asignación: ¿Quiénes son las CNAs?
Los identificadores CVE no los asigna una única entidad centralizada de forma manual, sino una red global llamada **CNAs (*CVE Numbering Authorities*)** bajo la coordinación del Programa CVE (gestionado por MITRE y patrocinado por CISA).

* **¿Qué son las CNAs?** Son organizaciones autorizadas oficialmente para asignar identificadores CVE y publicar registros dentro de un alcance (*scope*) definido.
* **Quiénes integran esta red:**
  * **Fabricantes y proveedores de tecnología:** Microsoft, Google, Apple, Cisco, Red Hat, Adobe, etc. (asignan CVEs para sus propios productos).
  * **Equipos de respuesta a incidentes (CERTs / CSIRTs):** Organismos nacionales o sectoriales como INCIBE (España) o JPCERT/CC (Japón).
  * **Organizaciones de investigación y plataformas de Bug Bounty:** GitHub, HackerOne, Bugcrowd, VulnCheck, entre otras.

---

## 5. ¿Dónde se consultan públicamente los CVE y CWE?
Existen bases de datos oficiales y gratuitas abiertas a toda la comunidad para consultar detalles técnicos, severidad y parches:

* **CVE.org (`https://www.cve.org`):** Portal oficial del Programa CVE donde se publican los registros originales emitidos por las CNAs.
* **NVD – National Vulnerability Database (`https://nvd.nist.gov`):** Base de datos mantenida por el NIST (EE. UU.) que sincroniza todos los registros CVE y les añade análisis técnico, puntuación de severidad **CVSS**, productos afectados (**CPE**) y su clasificación **CWE** correspondiente.
* **CWE MITRE (`https://cwe.mitre.org`):** Sitio oficial donde se aloja el catálogo completo de debilidades CWE, con ejemplos de código vulnerable, mitigaciones y relaciones entre categorías.

---

## 6. Herramientas automatizadas que detectan vulnerabilidades con CVE
En la industria se utilizan escáneres de vulnerabilidades que inspeccionan sistemas, redes o aplicaciones, identifican versiones de software y cruzan esa información contra bases de datos como la NVD para reportar los **CVEs asociados**:

1. **Tenable Nessus:** Uno de los escáneres de vulnerabilidades más utilizados a nivel empresarial. Analiza servidores, dispositivos de red y estaciones de trabajo mediante complementos (*plugins*) actualizados diariamente para detectar configuraciones inseguras y software desactualizado con CVEs conocidos.
2. **OpenVAS (Greenbone Vulnerability Management):** Escáner de vulnerabilidades de código abierto ampliamente adoptado en auditorías de seguridad. Realiza pruebas exhaustivas sobre servicios de red y aplicaciones para identificar fallos catalogados con sus respectivos códigos CVE y puntuaciones CVSS.
3. **Nuclei (ProjectDiscovery):** Escáner moderno basado en plantillas YAML impulsado por la comunidad, muy utilizado en *Bug Bounty* y pentesting para detectar rápidamente CVEs recién publicados en aplicaciones web e infraestructura expuesta.
