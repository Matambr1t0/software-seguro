# Objetivo 3: Anatomía de una Condición de Carrera (Race Condition)

Una **Condición de Carrera** (*Race Condition* o *TOCTOU*) ocurre cuando un servidor recibe varias peticiones al mismo tiempo y existe una pequeña ventana de milisegundos entre el momento en que verifica si una acción es válida y el momento en que guarda el resultado en la base de datos.

## ¿Cómo realiza el ataque?

1. **Captura de la petición:** El atacante aplica el cupón de descuento una vez de forma normal y, con un proxy como Burp Suite, intercepta la petición HTTP que envía ese código al servidor.
2. **Preparación del envío paralelo:** Carga esa petición en una extensión de alta velocidad como **Turbo Intruder**, que permite preparar decenas de copias de la misma solicitud y sincronizarlas para que lleguen al servidor exactamente en el mismo instante.
3. **Disparo simultáneo:** Envía las 50 peticiones al mismo tiempo. Como los servidores web procesan solicitudes en paralelo mediante múltiples hilos, varias de esas peticiones entran a ejecutarse a la vez.
4. **Explotación antes de la actualización:** Los primeros hilos consultan la base de datos casi al mismo milisegundo y todos leen que el cupón sigue disponible, porque el primer hilo todavía no terminó de marcarlo como usado. De esta forma, el servidor aprueba y aplica el descuento múltiples veces antes de alcanzar a actualizar el estado del cupón en la base de datos.

## ¿Cómo se evita?

* **Bloqueos en la base de datos:** Utilizar transacciones con bloqueo de registros (`SELECT ... FOR UPDATE`) para que las peticiones se procesen en fila y ninguna pueda leer el estado del cupón mientras otra lo está modificando.
* **Restricciones únicas:** Configurar una clave única en la base de datos que impida registrar más de un canje del mismo cupón para el mismo usuario.
