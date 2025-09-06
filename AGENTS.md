# Directrices para Agentes de IA sobre el Lenguaje HP PPL

## Objetivo Principal

Tu propósito es generar código en HP PPL (HP Prime Programming Language) que sea funcional, limpio y consistente con los ejemplos existentes en este repositorio. **No inventes funciones ni sintaxis**. Basa todo tu conocimiento en los programas que se encuentran en el directorio `/programas`.

## Ejemplo de Referencia (Gold Standard)

El siguiente programa, `Interpolate2D`, es el ejemplo canónico de cómo se debe estructurar y escribir el código. Analízalo con atención.

```hpp
// Funció per a interpolació bilineal en una taula
EXPORT Interpolate2D(input_row, input_col, row_headers, col_headers, data_table)
BEGIN
    // Declaración de variables locales al principio
    LOCAL I;
    LOCAL R_LOW_IDX, R_HIGH_IDX, C_LOW_IDX, C_HIGH_IDX;
    LOCAL R_LOW_VAL, R_HIGH_VAL, C_LOW_VAL, C_HIGH_VAL;
    LOCAL Z_LL, Z_LH, Z_HL, Z_HH;
    LOCAL TEMP_RESULT_LOW_ROW, TEMP_RESULT_HIGH_ROW;

    // --- Búsqueda de índices para la fila (Eje Y) ---
    IF input_row <= row_headers(1) THEN
        R_LOW_IDX := 1;
        R_HIGH_IDX := 1;
    ELSE
        IF input_row >= row_headers(SIZE(row_headers)) THEN
            R_LOW_IDX := SIZE(row_headers);
            R_HIGH_IDX := SIZE(row_headers);
        ELSE
            FOR I FROM 1 TO SIZE(row_headers) - 1 DO
                IF input_row >= row_headers(I) AND input_row <= row_headers(I+1) THEN
                    R_LOW_IDX := I;
                    R_HIGH_IDX := I+1;
                    BREAK;
                END;
            END;
        END;
    END;

    // --- Búsqueda de índices para la columna (Eje X) ---
    // (Lógica similar a la búsqueda de filas)
    IF input_col <= col_headers(1) THEN
        C_LOW_IDX := 1;
        C_HIGH_IDX := 1;
    ELSE
        IF input_col >= col_headers(SIZE(col_headers)) THEN
            C_LOW_IDX := SIZE(col_headers);
            C_HIGH_IDX := SIZE(col_headers);
        ELSE
            FOR I FROM 1 TO SIZE(col_headers) - 1 DO
                IF input_col >= col_headers(I) AND input_col <= col_headers(I+1) THEN
                    C_LOW_IDX := I;
                    C_HIGH_IDX := I+1;
                    BREAK;
                END;
            END;
        END;
    END;

    // --- Interpolación Bilineal ---
    R_LOW_VAL := row_headers(R_LOW_IDX);
    R_HIGH_VAL := row_headers(R_HIGH_IDX);
    C_LOW_VAL := col_headers(C_LOW_IDX);
    C_HIGH_VAL := col_headers(C_HIGH_IDX);

    Z_LL := data_table(R_LOW_IDX, C_LOW_IDX);
    Z_LH := data_table(R_LOW_IDX, C_HIGH_IDX);
    Z_HL := data_table(R_HIGH_IDX, C_LOW_IDX);
    Z_HH := data_table(R_HIGH_IDX, C_HIGH_IDX);

    IF C_HIGH_VAL == C_LOW_VAL THEN
        TEMP_RESULT_LOW_ROW := Z_LL;
        TEMP_RESULT_HIGH_ROW := Z_HL;
    ELSE
        TEMP_RESULT_LOW_ROW := Z_LL + (Z_LH - Z_LL) * (input_col - C_LOW_VAL) / (C_HIGH_VAL - C_LOW_VAL);
        TEMP_RESULT_HIGH_ROW := Z_HL + (Z_HH - Z_HL) * (input_col - C_LOW_VAL) / (C_HIGH_VAL - C_LOW_VAL);
    END;

    IF R_HIGH_VAL == R_LOW_VAL THEN
        RETURN TEMP_RESULT_LOW_ROW;
    ELSE
        RETURN TEMP_RESULT_LOW_ROW + (TEMP_RESULT_HIGH_ROW - TEMP_RESULT_LOW_ROW) * (input_row - R_LOW_VAL) / (R_HIGH_VAL - R_LOW_VAL);
    END;
END;
```

## Reglas de Sintaxis y Estilo Obligatorias

1.  **Definición de Funciones:** Las funciones que deben ser accesibles desde fuera se declaran con `EXPORT nombre_funcion(parametros)`.
2.  **Cuerpo del Programa:** El código principal de una función se encuentra entre `BEGIN` y `END;`. **No olvides el punto y coma (;) después de `END`**.
3.  **Declaración de Variables:** Todas las variables locales **deben** declararse al inicio del bloque `BEGIN` usando la palabra clave `LOCAL`. Ejemplo: `LOCAL mi_var, otra_var;`.
4.  **Asignación:** Utiliza el operador `:=` para asignar valores a las variables. NO uses `=`.
5.  **Bloques Condicionales:** La estructura es `IF condicion THEN ... END;` o `IF condicion THEN ... ELSE ... END;`. Todos los bloques (`IF`, `FOR`, etc.) deben terminar con `END;`.
6.  **Bucles `FOR`:** La sintaxis es `FOR variable FROM inicio TO fin DO ... END;`.
7.  **Comentarios:** Usa `//` para comentarios de una sola línea.
8.  **Listas y Matrices:** El acceso a los elementos se hace con paréntesis. Ejemplo: `mi_lista(3)`.
9.  **Palabras Clave:** Escribe todas las palabras clave del lenguaje (`EXPORT`, `BEGIN`, `END`, `LOCAL`, `IF`, `THEN`, `ELSE`, `FOR`, `DO`, `RETURN`, `BREAK`) en **MAYÚSCULAS** por consistencia.
10. **Funciones Nativas:** Utiliza únicamente funciones que sepas que existen en el entorno de la HP Prime, como `SIZE()`, `INPUT()`, `CHOOSE()`, `PRINT()`. Consulta los programas de ejemplo si tienes dudas.
