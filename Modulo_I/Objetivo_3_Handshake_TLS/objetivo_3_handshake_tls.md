# Objetivo 3: TLS Handshake

## ¿Qué es el TLS Handshake?
El **TLS Handshake** es el proceso por el cual el navegador web y el servidor se comunican antes de enviarse la informacion para ponerse de acuerdo de como van a encriptar dicha informacion así si llegara a interceptarla un hacker no podria hacer nada ya que veria solo simbolos sin sentido

---
Los certificados digitales cumplen 2 roles : * **Autenticación:** Garantizan que el servidor es realmente quien dice ser (y no sea un atacante haciendo un ataque *Man-in-the-Middle*). El navegador comprueba que el dominio coincida, que el certificado no esté vencido y que esté firmado por una CA reconocida en su almacén de confianza.
* **Distribución segura de la clave pública:** Entregan al navegador la clave pública del servidor de forma verificable para poder iniciar el intercambio criptográfico sin riesgo de manipulación.

---
Se combinan ambos porque cada uno resuelve una limitación del otro (cifrado híbrido):

* **Cifrado Asimétrico  Se usa **solo al principio** (durante el *Handshake*) para autenticar al servidor y acordar la clave de sesión de forma segura a través de una red insegura sin haber compartido secretos previamente. **Desventaja:** Es matemáticamente muy pesado y lento para cifrar grandes volúmenes de datos.
* **Cifrado Simétrico :** Se usa **una vez terminado el Handshake** para cifrar todo el tráfico HTTP (HTML, imágenes, formularios, cookies). **Ventaja:** Es extremadamente rápido y consume muy pocos recursos de CPU, permitiendo navegar con fluidez sin sacrificar la confidencialidad.
