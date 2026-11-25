## ¿Por qué fallan las listas negras y el escape manual de comillas?
Bloquear palabras como `SELECT` o `UNION` (*listas negras*) o reemplazar comillas simples es una mala defensa porque siempre existen formas de evadirlas (usando mayúsculas/minúsculas mezcladas, codificaciones distintas, comentarios en línea o funciones alternativas). Mientras la aplicación siga pegando texto del usuario directamente dentro de la consulta SQL, el riesgo sigue existiendo.

---

## ¿Cómo funciona una Consulta Parametrizada (*Prepared Statement*)?

A nivel de arquitectura entre la aplicación y el motor de base de datos, una consulta parametrizada divide el proceso en **dos etapas completamente separadas**:

1. **Etapa 1: Compilación de la estructura (La plantilla):**
   * La aplicación envía al motor de base de datos únicamente el esqueleto de la consulta con marcadores de posición (`?` o `:usuario`) en lugar de los datos reales. Por ejemplo:
     `SELECT * FROM usuarios WHERE email = ? AND password = ?`
   * El motor de base de datos analiza, compila y define el plan de ejecución de esa consulta **antes** de conocer qué escribió el usuario. En ese momento, la lógica de qué hace la consulta queda cerrada y congelada.

2. **Etapa 2: Envío de los parámetros (Los datos):**
   * Recién después, la aplicación envía los valores ingresados por el usuario por un canal separado para rellenar esos signos `?`.
   * El motor de base de datos inserta esos valores directamente dentro de los espacios reservados, sin volver a interpretar el texto como comandos SQL.

---

## ¿Por qué neutraliza por completo cualquier intento de inyección?

La clave es la **separación estricta entre código (instrucciones) y datos (valores)**:

* **Los datos nunca se ejecutan como código:** Como el motor de base de datos ya compiló la estructura de la consulta en el primer paso, cualquier carácter especial que envíe el atacante (como `' OR 1=1 --` o `UNION SELECT`) es tratado **literalmente como texto plano** para buscar dentro de una columna, nunca como una orden SQL nueva.
* **No importa qué caracteres envíe el usuario:** Si un atacante escribe `admin' OR '1'='1` en el campo de usuario, la base de datos simplemente busca un usuario cuyo nombre literal sea exactamente la cadena `"admin' OR '1'='1"`. Al no encontrarlo, devuelve cero resultados de forma totalmente segura, sin alterar jamás la lógica original de la consulta.
