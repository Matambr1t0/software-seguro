## ¿De qué se trata la vulnerabilidad?
Un **JSON Web Token (JWT)** tiene tres partes separadas por puntos (`Header.Payload.Signature`). La tercera parte —la **firma**— es la que garantiza que nadie modificó los datos del usuario en el medio, porque se genera usando una clave secreta que solo conoce el servidor.

El estándar oficial de JWT incluye un valor llamado **`"none"`** en el campo `alg` (*algoritmo*) del *Header*, pensado originalmente para entornos donde el token no necesita firmarse. El problema ocurre cuando una librería o un backend mal configurado acepta tokens con `"alg": "none"` en producción.

---

## ¿Por qué permite falsificar cualquier identidad sin conocer la clave secreta?

Cuando un servidor es vulnerable a esto, la lógica de validación de la librería confía ciegamente en lo que dice el *Header* del token que recibe del cliente:

1. **El atacante toma su propio token válido:** Por ejemplo, inicia sesión como un usuario común y captura su JWT, donde el *Payload* dice `"usuario": "fede"` y `"rol": "user"`.
2. **Modifica el Header y el Payload:**
   * En el **Header**, cambia el algoritmo original (ej. `HS256` o `RS256`) por **`"alg": "none"`**.
   * En el **Payload**, cambia sus datos por los de otra persona o se sube los privilegios a **`"rol": "admin"`**.
3. **Elimina la firma:** Borra la tercera parte del token (la firma), dejando el punto final vacío (`HeaderModificado.PayloadModificado.`).
4. **El servidor lo da por válido:** Al recibir el token, el backend lee el *Header*, ve que dice `"alg": "none"` y **saltea por completo el paso de verificar la firma criptográfica**. Como no intenta comprobar ninguna firma, nunca necesita comparar contra la clave secreta del servidor y acepta los datos modificados como si fueran legítimos.
