# Guía Definitiva de Programación Robusta en HP PPL

## Reglas de Oro (Principios Fundamentales)

1.  **El Codi Funcional Mana (El Código que Funciona Manda):** La regla más importante. La sintaxis teórica o de otros lenguajes no importa. El único modelo a seguir es la estructura de los programas que sabemos que funcionan en la calculadora (ej. `DarcyFactor`, `SuperConversor_V2`, `Conver_MF`, `BernoulliSolver_V2`).

2.  **Simplicitat és Fiabilitat (La Simplicidad es Fiabilidad):** La calculadora HP Prime prefiere estructuras de código simples y directas. Las construcciones complejas o "elegantes" de otros lenguajes (como `CASE` con llamadas externas, funciones lambda) son una fuente de errores. Ante la duda, hazlo de la manera más sencilla posible.

3.  **Un Sol Programa per a Tota l'Aplicació (Un Solo Programa para Toda la Aplicación):** Para aplicaciones con menús y múltiples pantallas, el enfoque más seguro es tener todo el código en un único fichero `EXPORT`. Evita llamar a otros programas `EXPORT` desde tu programa principal, ya que puede generar errores de contexto o de sintaxis difíciles de depurar.

## Estructura y Flujo del Programa

1.  **Bucle Principal `REPEAT...UNTIL 0;`:** Toda aplicación interactiva debe vivir dentro de un bucle principal. La estructura `REPEAT ... UNTIL 0;` con un `BREAK;` para salir es el patrón canónico y más fiable.

2.  **Blocs `IF` en Sèrie per al Menú Principal:** En lugar de un bloque `CASE`, utiliza una serie de `IF opcio_menu == ... THEN ... END;`. Esta estructura es más simple y ha demostrado ser 100% fiable para gestionar la lógica de los menús.

3.  **`CONTINUE` per a Reiniciar el Bucle:** Dentro de un submenú o después d'una operación que no requiere mostrar un resultado final (como "Introduir Dades"), usa `CONTINUE;` para volver inmediatamente al inicio del bucle y mostrar de nuevo el menú.

## Sintaxi i Comandos (Errores Específicos Encontrados)

1.  **Límit de `LOCAL`: Màxim 8 Variables per Declaració:** Una única línea `LOCAL` no puede declarar más de 8 variables. Si necesitas más, utiliza múltiples líneas `LOCAL`.
    *   **Correcto:** `LOCAL a, b, c; LOCAL d, e, f;`
    *   **Incorrecto:** `LOCAL a, b, c, d, e, f, g, h, i;`

2.  **`SIZE(matriu)` Retorna Files:** La función `SIZE()` solo acepta un argumento. Cuando se aplica a una matriz, retorna el número de filas. No existe `SIZE(matriu, dimensio)`.
    *   **Correcto:** `FOR I FROM 1 TO SIZE(tasques) DO ...`
    *   **Incorrecto:** `FOR I FROM 1 TO SIZE(tasques, 1) DO ...`

3.  **Prohibida la Sintaxi `llista(i)(j)`:** No se puede acceder a elementos de listas anidadas encadenando paréntesis. Se debe hacer en dos pasos.
    *   **Correcto:** `LOCAL fila := mi_llista(i); LOCAL valor := fila(j);`
    *   **Incorrecto:** `LOCAL valor := mi_llista(i)(j);`

4.  **No Confiar en `SUM()`: Fes la Suma Manualment:** La función `SUM()` puede no ser fiable en el contexto de programación. El método seguro para sumar los elementos de una lista es con un bucle `FOR` y un acumulador.
    *   **Correcto:** `LOCAL suma := 0; FOR I FROM 1 TO SIZE(llista) DO suma := suma + llista(I); END;`
    *   **Incorrecto:** `LOCAL suma := SUM(llista);`

5.  **Evitar Caràcters Especials:** En los textos para el usuario (`CHOOSE`, `TEXTOUT_P`, `INPUT`), evita apóstrofos ('), acentos y otros caracteres no ASCII. Utiliza `Organizacio` en lugar de `Organització`, `Calcul` en lloc de `Càlcul`, etc.

## Gestió de Dades i Variables

1.  **Declarar `LOCAL` dins de Cada Bloc Lògic:** Para mantener el código limpio y respetar el límite de 8 variables, declara las variables que un módulo necesita justo al inicio de su bloque `IF`. Esto asegura que solo existen cuando se necesitan.

2.  **Ús de "Flags" per a la Gestió d'Estat:** Un patrón muy potente es utilizar una variable "flag" (p. ej., `datos_introducidos := 0;`) para controlar el estado de la aplicación. Esto permite forzar al usuario a introducir datos antes de poder calcular, evitando errores y mejorando la experiencia.

3.  **No Assignar Llistes a Cel·les de Matrius Numèriques:** Una matriz creada con `MAKEMAT` contiene celdas para números. Intentar asignar una lista (`{1,2,3}`) a una de estas celdas (`tasques(I,9) := {1,2,3}`) causa un error de argumento. Los datos estructurales (como las listas de precedencias) se deben mantener en su lista original y consultarlas desde allí.

## Interfície d'Usuari i Experiència (UI/UX)

Dissenyar Pantalles de Resultats Clares (GUI): Per a la sortida de resultats, no s'ha d'utilitzar PRINT. L'objectiu és oferir a l'usuari una pantalla neta, professional i fàcil de llegir. El procediment estàndard és:
a. Netejar la pantalla: Començar sempre amb RECT_P(); per esborrar qualsevol contingut anterior.
b. Estructurar amb títols i etiquetes: Utilitzar TEXTOUT_P("Titol del Resultat", ...) a la part superior i després etiquetes clares per a cada valor (TEXTOUT_P("Lot Optim (Q*): ", ...)).
c. Formatar els números: Fer servir ROUND(valor, decimals) per evitar un nombre excessiu de decimals i concatenar les unitats al final (+ " unitats").
d. Controlar la presentació: Utilitzar les coordenades x, y i la mida_font de TEXTOUT_P per crear una jerarquia visual i alinear correctament les dades.
e. Pausar per a la lectura: Acabar sempre la pantalla de resultats amb WAIT(-1); per donar temps a l'usuari a llegir la informació abans de tornar al menú.
f. Gestionar Errors: Si un càlcul pot fallar, comprovar-ho (p. ex., arrel d'un negatiu) i mostrar un missatge d'error clar amb TEXTOUT_P en lloc de deixar que el programa falli.
