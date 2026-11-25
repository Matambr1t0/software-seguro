## ¿Por qué ocurre esta falla?
Esta vulnerabilidad es un fallo clásico de **Lógica de Negocio** (*Business Logic Vulnerability*) combinado con **falta de validación en el backend**.

Ocurre porque el desarrollador cometió dos errores principales:

1. **Confiar en la validación del lado del cliente (Frontend):** Probablemente en la página web el input de cantidad tenía un límite visual (como `<input type="number" min="1">` o botones de `+` y `-` que no bajan de 1). Sin embargo, un atacante puede interceptar la petición HTTP con un proxy (como Burp Suite o OWASP ZAP) y cambiar ese `1` por `-5` antes de que llegue al servidor.
2. **Asumir que los datos recibidos son siempre positivos al calcular el total:** El servidor recibe `cantidad = -5`, multiplica directamente `precio_unitario * cantidad` (ej. `$100 * -5 = -$500`) y procesa el pago o descuento sin frenar la operación cuando el monto total o la cantidad es menor o igual a cero.

---

## ¿Cómo debe validarlo el desarrollador en el servidor (Backend)?

Para evitar este ataque, toda validación crítica debe realizarse **estrictamente en el servidor**, sin importar lo que haga el navegador del usuario:

1. **Validar el tipo de dato y el rango permitido:**
   * Verificar que `cantidad` sea un **número entero** (*integer*), descartando decimales, texto o valores nulos.
   * Exigir que sea **estrictamente mayor a cero** (`cantidad >= 1`) y establecer un máximo razonable según el stock disponible (`cantidad <= stock_actual`). Si `cantidad <= 0`, el servidor debe rechazar la petición inmediatamente devolviendo un error `400 Bad Request`.
2. **Validar el total final antes de procesar el pago o saldo:**
   * Antes de confirmar la compra o actualizar la billetera del usuario, el backend debe comprobar que el monto total de la transacción sea positivo (`total > 0`).
3. **No confiar en precios ni totales enviados desde el cliente:**
   * El cliente únicamente debe enviar el `id_producto` y la `cantidad`. El precio unitario se debe consultar siempre desde la base de datos en el servidor y calcular el total internamente.
