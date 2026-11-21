# Guía del equipo – Ficha automática de mercado y entorno (Venture One)

## 1. Qué hay en el repositorio

```
config/parametros.yaml              ← reglas oficiales (radio, estados, confianza). Ningún número va fijo en el código
datos/originales/                   ← 3 CSV de Venture One (nunca se modifican)
datos/originales/geo/               ← transporte.geojson, equipamientos.geojson (y pot_tratamientos.geojson cuando llegue)
docs/                               ← diccionario de datos y plantilla de la ficha (de Venture One)
ejemplos/ficha_ejemplo.json         ← formato oficial de salida
validacion/                         ← cálculo manual, esperado.json y validar.py (de Venture One)
plantilla/captura_inmuebles.xlsx    ← propuesta de captura con validaciones (del equipo)
ficha_mercado_entorno.ipynb         ← EL cuaderno principal: genera fichas, valida y exporta para Power BI
salidas/fichas/                     ← una ficha .json y una vista .txt por inmueble (las genera el cuaderno)
datos/limpios/powerbi/              ← modelo_powerbi.xlsx para el tablero (lo genera el cuaderno)
```

## 2. Cómo ejecutar

1. Activa el entorno (una sola vez por terminal) o elige el kernel **reto** en VS Code.
2. Instala dependencias si es la primera vez:
   `& "$HOME\venvs\reto\Scripts\python.exe" -m pip install -r requirements.txt`
3. Abre `ficha_mercado_entorno.ipynb` → **Run All**.
4. En el Paso 17 debe salir:
   ```
   ✓ INM-04
   ✓ INM-06
   ✓ INM-09
   ✅ Coincide con el cálculo manual
   ```

**Si Venture One cambia una regla** (por ejemplo el radio a 1.500 m): se edita `config/parametros.yaml` y se vuelve a correr.
Ojo: si cambian los parámetros, el validador marcará diferencias, porque el cálculo manual se hizo con la versión 1.0.

**Cuando llegue la capa del POT:** copiar `pot_tratamientos.geojson` en `datos/originales/geo/` y volver a correr.

## 3. Reglas aplicadas (resumen para la presentación)

| Paso | Regla | Fuente |
|---|---|---|
| Estado | Texto normalizado → `remodelado` / `habitable` / `por_remodelar`; lo demás `sin_clasificar` | `estados` |
| Duplicados | Mismo `id_anuncio` **o** misma (`zona`, `area_m2`, `precio`); se conserva la primera fila | `deduplicacion` |
| Inválidos | Sin área, sin precio, precio/m² fuera de [1 M; 20 M], estado sin clasificar | `comparables` |
| Comparables | Misma zona y estado, área ±30 %, excluye el propio inmueble | `comparables.tolerancia_area` |
| Estadísticas | Mediana, promedio, desviación muestral, CV | plantilla |
| Confianza | Alta: n ≥ 5 y CV ≤ 15 %. Media: n ≥ 3 y CV ≤ 25 %. Si no, baja | `confianza` |
| Entorno | Haversine; estaciones metro/tranvía/metrocable; conteos a ≤ 800 m | `entorno` |
| Norma | POT solo Medellín; sin capa → `pendiente_capa` | `norma` |

Resultado: 117 filas → 84 anuncios únicos → 63 válidos. 11 fichas generadas. Validación ✅.

## 4. Tablero en Power BI

### 4.1 Cargar
**Obtener datos → Libro de Excel →** `datos/limpios/powerbi/modelo_powerbi.xlsx` → marcar las 8 hojas → **Transformar datos**
→ revisar que `lat`, `lon`, `coef_variacion` y `diferencia_vs_mediana` sean **Número decimal** → **Cerrar y aplicar**.

### 4.2 Relaciones (Vista de modelo)
Borrar las automáticas y crear estas (todas **uno a varios**, filtro **único**):

| Desde (1) | Hacia (varios) | Columna |
|---|---|---|
| `dim_zona` | `inmuebles` | `zona` |
| `dim_zona` | `anuncios` | `zona` |
| `dim_estado` | `inmuebles` | `estado` |
| `dim_estado` | `anuncios` | `estado` |
| `inmuebles` | `comparables` | `id_inmueble` |
| `inmuebles` | `entorno_inmueble` | `id_inmueble` |
| `puntos_entorno` | `entorno_inmueble` | `id_punto` |

`calidad` queda sin relaciones. **No** relacionar `anuncios` con `comparables` (crearía dos caminos de filtro).

En la vista de tabla, marcar `lat` → Categoría de datos **Latitud** y `lon` → **Longitud** (en `inmuebles` y `puntos_entorno`).

### 4.3 Columnas calculadas (tabla `inmuebles` → Nueva columna)

```DAX
Posicion frente al mercado =
SWITCH ( TRUE (),
    ISBLANK ( inmuebles[diferencia_vs_mediana] ), "Sin dato",
    inmuebles[confianza] = "baja", "Referencia poco confiable",
    inmuebles[diferencia_vs_mediana] <= -0.10, "Por debajo de la mediana",
    inmuebles[diferencia_vs_mediana] <= 0.10, "En línea con la mediana",
    "Por encima de la mediana" )

Perfil de demanda (hipotesis) =
SWITCH ( TRUE (),
    inmuebles[area_m2] >= 75 && inmuebles[n_educacion] >= 10 && inmuebles[n_parques] >= 5, "Familias",
    inmuebles[area_m2] < 75 && inmuebles[distancia_estacion_m] <= 1000, "Renta: estudiantes / profesionales",
    inmuebles[area_m2] < 75, "Pareja o persona sola",
    "Perfil mixto" )
```

> La plantilla oficial dice que la ficha **no recomienda comprar**. Por eso el tablero habla de "posición frente al mercado"
> y de "perfil de demanda (hipótesis)", no de "comprar / no comprar". Los umbrales son ejemplos para discutir con la empresa.

### 4.4 Medidas

```DAX
Comparables usados = COUNTROWS ( comparables )
Mediana comparables = MEDIAN ( comparables[precio_m2] )
Anuncios validos = CALCULATE ( COUNTROWS ( anuncios ), anuncios[es_valido] = TRUE () )
Mediana zona-estado = CALCULATE ( MEDIAN ( anuncios[precio_m2] ), anuncios[es_valido] = TRUE () )
Puntos en radio = CALCULATE ( COUNTROWS ( entorno_inmueble ), entorno_inmueble[dentro_radio] = TRUE () )
Problemas de calidad = COUNTROWS ( calidad )
```
(Si da error de sintaxis, cambiar las comas por punto y coma.)

### 4.5 Páginas

| Página | Visuales | Pregunta |
|---|---|---|
| **1. Resumen** | Tabla con `id_inmueble`, `barrio`, `estado`, `mediana_m2`, `confianza`, `Posicion frente al mercado`, `Perfil de demanda (hipotesis)`, `datos_faltantes`. Formato condicional en `confianza` | ¿Qué inmuebles tienen buena referencia de mercado? |
| **2. Ficha** | Segmentador `id_inmueble`; tarjetas `area_m2`, `mediana_m2`, `confianza`, `motivo_confianza`, `estacion_cercana`, `distancia_estacion_m`, `norma_estado`; tabla de `comparables` (origen, fuente, área, precio, precio_m2); barras de equipamientos (`n_educacion`…`n_cultura`); tarjeta de alerta si `entorno_aproximado` | Réplica visual de la vista de lectura oficial |
| **3. Mercado** | Columnas: `nombre_zona` × `nombre_estado` con `Mediana zona-estado`; dispersión `area_m2` vs `precio` de `anuncios` con leyenda `estado` | ¿Cuánto vale el m² por zona y estado? |
| **4. Entorno** | Mapa (o Azure Maps) con `puntos_entorno` (leyenda `categoria`) filtrado por `entorno_inmueble[dentro_radio] = Verdadero` y segmentador `id_inmueble` | ¿Qué hay a 800 m? |
| **5. Calidad** | Matriz `archivo` × `problema` con `Problemas de calidad`; tabla de detalle; tarjetas de 117 → 84 → 63 | ¿Podemos confiar en los datos? |

## 5. Propuesta de proceso (para la presentación)

1. **Captura única** en `plantilla/captura_inmuebles.xlsx` (SharePoint): listas desplegables, validaciones, duplicados en rojo,
   **matrícula inmobiliaria** como llave de inmuebles, `fecha_publicacion` y `tipo_precio = cierre` obligatorios.
2. **Cálculo reproducible:** el cuaderno lee los parámetros y genera fichas + validación cada vez que entra una oportunidad.
3. **Vigilancia:** página de calidad en Power BI, revisada cada semana por el dueño de datos.
4. **Por qué importa (con evidencia del reto):** sin fecha ni precios de cierre no se puede medir liquidez; los duplicados
   ambiguos (casos 1 y 2 del cálculo manual) desaparecen con una llave única.
