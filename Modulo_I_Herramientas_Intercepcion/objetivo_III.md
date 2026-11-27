### Fuerza Bruta Tradicional vs. Ataque de Diccionario

Aunque ambos buscan descubrir credenciales válidas mediante el envío automatizado de múltiples peticiones (por ejemplo, usando herramientas como *Intruder* en Burp Suite), difieren radicalmente en cómo generan las combinaciones:

| Característica | Fuerza Bruta Tradicional | Ataque de Diccionario |
| :--- | :--- | :--- |
| **Método de generación** | Prueba **todas las combinaciones posibles** de caracteres de forma secuencial y exhaustiva (`aaa`, `aab`, `aac`... hasta cubrir todo el espacio de claves). | Utiliza una lista predefinida (*wordlist*) con palabras reales, contraseñas filtradas previamente, nombres comunes o patrones frecuentes. |
| **Tiempo y recursos** | Muy lento y costoso computacionalmente; crece de forma exponencial según la longitud y complejidad de la contraseña. | Mucho más rápido y eficiente en entornos reales, ya que prioriza los patrones que suelen elegir las personas. |
| **Probabilidad de éxito** | Garantiza encontrar la clave tarde o temprano, pero en aplicaciones web suele volverse inviable por los tiempos de red. | Alta efectividad contra contraseñas débiles o reutilizadas, aunque falla si la clave es completamente aleatoria. |

---

### Mecanismos de Defensa y Mitigación

Para evitar que un atacante automatice cientos o miles de intentos sobre un endpoint de autenticación, un desarrollador puede implementar las siguientes medidas:

1. **Rate Limiting (Limitación de tasa):**
   Restringir la cantidad máxima de peticiones que una misma dirección IP o sesión puede realizar en un intervalo de tiempo determinado (por ejemplo, máximo 5 intentos de login por minuto), devolviendo un código HTTP `429 Too Many Requests` al superar el umbral.

2. **Bloqueo temporal de cuentas (Account Lockout):**
   Suspender temporalmente el acceso a un usuario específico tras acumular un número fijo de intentos fallidos consecutivos (por ejemplo, bloquear la cuenta por 15 minutos después de 5 fallos), mitigando ataques distribuidos que roten de IP.

3. **Desafíos CAPTCHA:**
   Incorporar verificación humana (como reCAPTCHA o Turnstile) de forma permanente o activarla dinámicamente después de 2 o 3 intentos fallidos para frenar scripts automatizados.

4. **Multi-Factor Authentication (MFA):**
   Añadir un segundo factor de verificación (como un código TOTP) asegura que, incluso si el atacante logra adivinar la contraseña mediante un diccionario, no pueda completar el inicio de sesión sin el dispositivo del usuario.
