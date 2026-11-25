# Objetivo 1: Auditoría de Cookies y Banderas de Seguridad

Utilizando el inspector de almacenamiento del navegador (*DevTools > Storage > Cookies*), se auditaron las cookies de tres sitios web de uso público (**YouTube**, **Mercado Libre** e **Infobae**) para verificar la correcta implementación de las banderas de seguridad `HttpOnly`, `Secure` y `SameSite`.

---

## 1. Resultados de la Auditoría

| Sitio Web Analizado | Cookies Relevantes Inspeccionadas | `HttpOnly` | `Secure` | `SameSite` | Análisis de Seguridad |
| :--- | :--- | :---: | :---: | :---: | :--- |
| **YouTube** (`www.youtube.com`) | `__Secure-1PSID` / `__Secure-3PSID` / `LOGIN_INFO` | `true` | `true` | `None` / Sin definir | **Correcta en cookies críticas:** Protege los identificadores de sesión principales con el prefijo `__Secure-`, `HttpOnly=true` y `Secure=true`. *(Nota: cookies heredadas como `SID` o `HSID` mantienen `Secure=false` u `HttpOnly=false` por compatibilidad, pero la autenticación principal recae en las versiones `__Secure-`)*. |
| **Mercado Libre** (`www.mercadolibre.com.ar`) | `nsa_rotok` / `NSESSI...` / `_csrf` | `true` | `true` | `None` / Sin definir | **Correcta:** Tanto el token de sesión (`nsa_rotok`, que contiene un JWT) como la cookie anti-CSRF (`_csrf`) tienen activadas las banderas `HttpOnly` y `Secure`, evitando su robo vía JavaScript o tráfico sin cifrar. *(Cookies de preferencias como `orgnickp` o `LAST_SEARCH` usan `HttpOnly=false` intencionalmente)*. |
| **Infobae** (`www.infobae.com`) | `AKA_A2` (Akamai) / `_ga` / `_gcl_au` / `sui_1pc` | `true` (en `AKA_A2`) / `false` (en analítica) | `true` / `false` | `Lax` / `None` / Sin definir | **Sin sesión de usuario / Uso de tracking:** Al navegar sin iniciar sesión, no se generan cookies de autenticación de usuario. La cookie técnica de infraestructura (`AKA_A2`) implementa `HttpOnly=true` y `Secure=true`, mientras que las cookies de analítica y publicidad (`_ga`, `_fbp`, `_gcl_au`) tienen ambas banderas en `false` al ser leídas por scripts del lado del cliente. |

---

## 2. Explicación Técnica de las Banderas

### ¿Qué protege exactamente la bandera `HttpOnly`?
La bandera **`HttpOnly`** bloquea el acceso a la cookie desde los scripts del lado del cliente (como JavaScript a través de `document.cookie`), permitiendo que únicamente sea enviada en las cabeceras HTTP/HTTPS hacia el servidor.

* **Protección concreta:** Mitiga el **robo de sesión (*Session Hijacking*) derivado de ataques Cross-Site Scripting (XSS)**. Si un atacante logra inyectar código JavaScript malicioso en el navegador de la víctima (por ejemplo, en Mercado Libre o YouTube), no podrá leer el valor de cookies como `nsa_rotok` o `__Secure-1PSID` para exfiltrarlas y suplantar la identidad del usuario.

### ¿Qué riesgo corre el usuario si la cookie de sesión no tiene la bandera `Secure` activada?
Si una cookie de sesión tiene **`Secure=false`**, el navegador permite que sea transmitida tanto a través de canales cifrados (**HTTPS**) como a través de conexiones en texto plano sin cifrar (**HTTP**).

* **Riesgo concreto:** El usuario queda vulnerable a ataques de **intercepción de tráfico (*Sniffing* o *Man-in-the-Middle*)** en redes no confiables (como redes Wi-Fi públicas). Si el navegador realiza una petición por HTTP al dominio (o un atacante fuerza una redirección HTTP), la cookie viajará legible por la red, permitiendo que un tercero la capture y tome control total de la cuenta del usuario sin conocer su contraseña.
