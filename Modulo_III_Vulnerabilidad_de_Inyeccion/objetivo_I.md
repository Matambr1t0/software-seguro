## Diferencia entre Boolean-Based Blind y Time-Based Blind

| Tipo de Inyección Ciega | ¿Cómo funciona? | ¿En qué se fija el atacante para saber la respuesta? |
| :--- | :--- | :--- |
| **Boolean-Based Blind** *(Basada en verdadero/falso)* | Se inyecta una condición lógica en la consulta SQL (por ejemplo, `AND 1=1` frente a `AND 1=2`). | **En los cambios del contenido de la página:** Si la condición es verdadera, la web muestra el contenido normal (ej. "Producto encontrado"). Si es falsa, la página carga vacía, distinta o con un mensaje genérico como "No hay resultados". |
| **Time-Based Blind** *(Basada en tiempo)* | Se inyecta una instrucción que obliga a la base de datos a hacer una pausa (como `SLEEP(5)` o `WAITFOR DELAY`) únicamente si se cumple una condición. | **En cuánto tarda en responder el servidor:** El contenido visual de la página nunca cambia (siempre dice lo mismo, como `"Cargando..."`), pero si la respuesta tarda 5 segundos extra en llegar, el atacante sabe que su condición fue **verdadera**. |

---

## ¿Cómo extrae el nombre de la base de datos letra por letra cuando solo dice "Cargando..."?

Cuando la pantalla siempre muestra una respuesta genérica (como `"Cargando..."`) sin importar si la consulta devuelve datos o no, la única vía disponible es la **Time-Based Blind SQLi** (usando el reloj como canal de respuesta):

1. **Confirmar que la base de datos obedece pausas:** Primero inyecta una orden de espera simple (por ejemplo, `SLEEP(5)`). Si la página tarda 5 segundos más de lo normal en responder, confirma que puede controlar el tiempo de respuesta de la base de datos.
2. **Averiguar el largo del nombre:** Hace preguntas sobre la longitud del nombre de la base de datos actual (`database()`). Por ejemplo: *"Si el largo del nombre es mayor a 5 letras, esperá 5 segundos; si no, respondé rápido"*. Así acota cuántos caracteres tiene exactamente.
3. **Adivinar letra por letra (fuerza bruta condicional):** Usando funciones SQL que toman una letra según su posición (como `SUBSTRING(database(), 1, 1)`), va probando carácter por carácter desde la posición 1 hasta el final:
   * Pregunta: *"¿La primera letra del nombre de la base de datos es `'a'`? Si es sí, hacé `SLEEP(5)`"*.
   * Si el servidor responde en milisegundos, sabe que no es `'a'` y pasa a probar con `'b'`, `'c'`, `'d'`, etc.
   * Cuando llega a la letra correcta (por ejemplo `'p'`), la base de datos ejecuta la pausa de 5 segundos. Al medir ese retraso en la respuesta HTTP, el atacante anota la `'p'` como primera letra confirmada.
4. **Repetir el proceso:** Pasa a la segunda posición (`SUBSTRING(database(), 2, 1)`) y repite el ciclo hasta reconstruir el nombre completo de la base de datos (por ejemplo, `produccion_db`). *(En la práctica, herramientas como `sqlmap` automatizan estas cientos de consultas de tiempo en pocos minutos).*
