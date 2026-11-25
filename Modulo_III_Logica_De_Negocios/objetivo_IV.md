## ¿Por qué la lógica de negocio es el mayor "punto ciego" de la automatización?

Los escáneres automatizados (como Nessus, Acunetix o Burp Suite) están programados para buscar **patrones técnicos conocidos**: inyectan caracteres especiales para ver si el servidor devuelve un error de base de datos, prueban scripts para detectar XSS o revisan si faltan cabeceras de seguridad.

Estas herramientas **no entienden el contexto ni las reglas de funcionamiento del negocio**.

## ¿Por qué es indispensable la auditoría manual en esta etapa?

La revisión manual realizada por un auditor humano es insustituible porque requiere **comprensión, criterio y creatividad**:

1. **Entender el flujo normal:** Primero hay que usar la aplicación como un usuario común para comprender qué se supone que debe hacer cada paso y qué reglas deben respetarse.
2. **Pensar fuera de lo previsto:** Un auditor puede preguntarse *"¿qué pasa si envío un número negativo?"*, *"¿qué ocurre si mando esta petición dos veces seguidas?"* o *"¿puedo acceder al paso 3 sin pasar por el paso 2?"*, rompiendo las suposiciones que hizo el desarrollador al programar.
3. **Interpretar el impacto real:** Solo una persona puede evaluar si el comportamiento encontrado representa una pérdida económica, un fraude o una ventaja injusta dentro de las reglas de ese negocio en particular.
