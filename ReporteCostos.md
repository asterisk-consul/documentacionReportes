# Documentación Técnica: Consulta Recursiva para Árbol de Dependencias de Artículos

## Descripción General

```sql
WITH RECURSIVE ARTICULOSRECUR AS (

	/* ****************************************************************************************************************** */
	/* Recorrer la estructura de arbol de la dependencias entre articulos.                                                */
	/* A medida que se hace el recorrido se arma el path de relacion y se calculan las cantidades a usar de cada articulo */
	/* ****************************************************************************************************************** */

	SELECT 0::bigint artcompid, DA.articuloid, 0::bigint parentarticuloid,
	       ARRAY[DA.articuloid] AS articuloidpath,
	       --ARRAY[nextval('global_unique_id_seq')] uniquepath, /* requiere tener creada una secuencia llamada global_unique_id_seq */
	       ARRAY[0::bigint] idpath,
	       ARRAY[0::bigint] variable,
		   RC.cantidad cantidad,
		   A.internalcode, A.externalcode, A.nombre articulo_nombre,
		   null::text unidad_medida
	  FROM test9000.REGISTROCAB R
	 INNER JOIN test9000.REGISTROCUERPO RC ON R.id = RC.presupcabid
	 INNER JOIN test9000.DEPOSITOSARTICULOS DA ON RC.articulodepositoid = DA.id
	 INNER JOIN test9000.ARTICULOS A ON DA.articuloid = A.id
     WHERE R.id = $P{param_cab_id}  /* ++++++++++++++++++++ID REGISTROCAB+++++++++++++++++ */

	UNION

    SELECT AC.id artcompid, AC.articuloid, AC.parentarticuloid,
	      AR.articuloidpath || AC.articuloid articuloidpath,
	      --AR.uniquepath || nextval('global_unique_id_seq') uniquepath,
	      AR.idpath || AC.id idpath,
	      AR.variable || AC.compvariableid variable,
		  /* En el calculo de la cantidad se calcula la cantidad que le pertenece al nodo y se la multiplica por la cantidad (AR.cantidad) del nodo padre */
		   CASE
			    WHEN (AC.funcion IS NOT NULL) THEN  AR.cantidad * (SELECT test9000.flowscalcularpresupuesto('cantidad'::text, AC.funcion::text, CAB.varcn0::numeric, CAB.varcn1::numeric, CAB.varcn2::numeric))
				WHEN (CAT.valor = '0' ) THEN  AR.cantidad * AC.cantidad
				WHEN (CAT.valor = '1' ) THEN  AR.cantidad * AC.cantidad * CAB.varcn0 * CAB.varcn1
				WHEN (CAT.valor = '2' ) THEN  AR.cantidad * AC.cantidad * CAB.varcn0
				WHEN (CAT.valor = '3' ) THEN  AR.cantidad * AC.cantidad * CAB.varcn1 * CAB.varcn2
				WHEN (CAT.valor = '4' ) THEN  AR.cantidad * AC.cantidad * CAB.varcn0 * CAB.varcn2
				WHEN (CAT.valor = '5' ) THEN  AR.cantidad * AC.cantidad * CAB.varcn0 * CAB.varcn2
				ELSE 0
			END cantidad,
			A.internalcode, A.externalcode, A.nombre articulo_nombre,
			A.um unidad_medida
	 FROM test9000.ARTICULOSCOMPUESTOS AC
	INNER JOIN ARTICULOSRECUR AR ON AR.articuloid = AC.parentarticuloid
	INNER JOIN test9000.ARTICULOS A ON AC.articuloid = A.id
  	 LEFT JOIN test9000.CATEGORIAS CAT ON  AC.compvariableid = CAT.id
  	CROSS JOIN test9000.REGISTROCAB CAB
	WHERE CAB.id = $P{param_cab_id}  /* ++++++++++++++++++++ID REGISTROCAB+++++++++++++++++ */
	  AND NOT AC.articuloid = ANY(AR.articuloidpath)
)
```

La consulta SQL recursiva `WITH RECURSIVE ARTICULOSRECUR` está diseñada para recorrer una estructura jerárquica (árbol) que representa las dependencias entre artículos en un sistema. Durante el recorrido, se calculan las cantidades necesarias de cada artículo y se construye un **path** que describe la relación jerárquica entre los artículos.

Esta consulta es especialmente útil en sistemas donde los artículos pueden estar compuestos por otros artículos (estructura de árbol), como en la gestión de inventarios o presupuestos.

---

## Estructura de la Consulta

### 1. **Parte Base (No Recursiva)**

La parte base de la consulta define el punto inicial del recorrido recursivo. Aquí se seleccionan los artículos principales a partir de los cuales se comenzará el análisis.

#### Campos Seleccionados:

- **artcompid**: ID del artículo compuesto. Inicializado en `0` porque no hay un artículo compuesto en este nivel.
- **articuloid**: ID del artículo principal.
- **parentarticuloid**: ID del artículo padre. Inicializado en `0` porque no hay un padre en este nivel.
- **articuloidpath**: Arreglo que almacena el camino de IDs de artículos hasta el nodo actual.
- **idpath**: Arreglo que almacena los IDs de los nodos recorridos.
- **variable**: Arreglo que almacena las variables asociadas a cada nodo.
- **cantidad**: Cantidad inicial del artículo.
- **internalcode**, **externalcode**, **articulo_nombre**: Datos descriptivos del artículo.
- **unidad_medida**: Unidad de medida del artículo.

#### Relaciones:

- Se une la tabla `REGISTROCAB` con `REGISTROCUERPO` y `DEPOSITOSARTICULOS` para obtener los artículos relacionados con un registro específico (`R.id = $P{param_cab_id}`).

---

### 2. **Parte Recursiva**

La parte recursiva extiende el recorrido hacia los artículos hijos (dependientes) del artículo actual.

#### Campos Seleccionados:

- **artcompid**: ID del artículo compuesto.
- **articuloid**: ID del artículo hijo.
- **parentarticuloid**: ID del artículo padre.
- **articuloidpath**: Se actualiza agregando el ID del artículo hijo al arreglo existente.
- **idpath**: Se actualiza agregando el ID del nodo actual al arreglo existente.
- **variable**: Se actualiza agregando la variable asociada al nodo actual.
- **cantidad**: Cálculo de la cantidad requerida para el artículo hijo, basado en reglas específicas.
- **internalcode**, **externalcode**, **articulo_nombre**: Datos descriptivos del artículo hijo.
- **unidad_medida**: Unidad de medida del artículo hijo.

#### Lógica de Cálculo de Cantidades:

El cálculo de la cantidad depende de varias condiciones:

1. Si el artículo tiene una función definida (`AC.funcion`), se utiliza una función externa (`test9000.flowscalcularpresupuesto`) para calcular la cantidad.
2. Si no hay función, se utiliza el valor de la categoría (`CAT.valor`) para determinar cómo calcular la cantidad:
   - **Valor 0**: Multiplicación directa de la cantidad del nodo padre por la cantidad del nodo hijo.
   - **Valor 1**: Multiplicación adicional por variables específicas (`CAB.varcn0`, `CAB.varcn1`).
   - **Valor 2**: Multiplicación por una variable específica (`CAB.varcn0`).
   - **Valor 3**: Multiplicación por otras combinaciones de variables.
   - **Valor 4 o 5**: Multiplicación por combinaciones de variables específicas.
   - **Default**: Retorna `0` si no se cumple ninguna condición.

#### Condiciones de Parada:

- El recorrido se detiene cuando un artículo ya ha sido visitado (`NOT AC.articuloid = ANY(AR.articuloidpath)`), evitando ciclos infinitos en la estructura jerárquica.

---

## Tablas Involucradas

### 1. **REGISTROCAB**

- Contiene información general sobre el registro (cabecera).
- **Campo clave**: `id` (identificador único del registro).

### 2. **REGISTROCUERPO**

- Contiene detalles específicos de cada artículo relacionado con un registro.
- **Relación**: `presupcabid` → `REGISTROCAB.id`.

### 3. **DEPOSITOSARTICULOS**

- Asocia artículos con depósitos.
- **Relación**: `id` → `REGISTROCUERPO.articulodepositoid`.

### 4. **ARTICULOS**

- Información descriptiva de los artículos.
- **Relación**: `id` → `DEPOSITOSARTICULOS.articuloid`.

### 5. **ARTICULOSCOMPUESTOS**

- Define las relaciones jerárquicas entre artículos (padre-hijo).
- **Campos relevantes**:
  - `id`: Identificador único del artículo compuesto.
  - `articuloid`: ID del artículo hijo.
  - `parentarticuloid`: ID del artículo padre.
  - `funcion`: Función opcional para cálculos personalizados.
  - `compvariableid`: ID de la categoría asociada.

### 6. **CATEGORIAS**

- Define categorías utilizadas en los cálculos de cantidades.
- **Relación**: `id` → `ARTICULOSCOMPUESTOS.compvariableid`.

---

## Parámetros de Entrada

- **$P{param_cab_id}**: Identificador del registro cabecera (`REGISTROCAB.id`) desde el cual se inicia el análisis.

---

## Consideraciones Técnicas

1. **Evitar Ciclos Infinitos**:

   - La condición `NOT AC.articuloid = ANY(AR.articuloidpath)` asegura que no se visiten artículos que ya forman parte del camino actual, previniendo ciclos infinitos.

2. **Cálculo Dinámico de Cantidades**:

   - El uso de funciones externas (`test9000.flowscalcularpresupuesto`) permite flexibilidad en los cálculos personalizados.

3. **Estructura Jerárquica**:

   - La consulta utiliza arreglos (`ARRAY`) para almacenar caminos jerárquicos (`articuloidpath`, `idpath`, `variable`), lo que facilita el seguimiento de la estructura.

4. **Escalabilidad**:
   - La consulta está diseñada para manejar estructuras jerárquicas complejas, pero su rendimiento puede verse afectado por la profundidad del árbol y la cantidad de nodos.

---

## Ejemplo de Uso

Supongamos que tenemos la siguiente estructura jerárquica de artículos:

- **Artículo Principal (ID: 1)**
  - **Artículo Hijo 1 (ID: 2)**
    - Artículo Nieto 1 (ID: 3)
    - Artículo Nieto 2 (ID: 4)
  - **Artículo Hijo 2 (ID: 5)**

La consulta generará un resultado similar al siguiente:

| articuloid | parentarticuloid | articuloidpath | cantidad | articulo_nombre    |
| ---------- | ---------------- | -------------- | -------- | ------------------ |
| 1          | 0                | [1]            | 10       | Artículo Principal |
| 2          | 1                | [1, 2]         | 5        | Artículo Hijo 1    |
| 3          | 2                | [1, 2, 3]      | 2        | Artículo Nieto 1   |
| 4          | 2                | [1, 2, 4]      | 3        | Artículo Nieto 2   |
| 5          | 1                | [1, 5]         | 7        | Artículo Hijo 2    |

---

## Conclusión

Esta consulta es una herramienta poderosa para analizar estructuras jerárquicas de artículos y calcular cantidades necesarias en sistemas de inventario o presupuestos. Su diseño modular y flexible permite adaptarse a diferentes escenarios y reglas de negocio.

---

## LISTADOFINAL

## Descripción General

Esta sección de la consulta SQL, denominada **LISTADOFINAL**, tiene como objetivo agregar detalles adicionales a los resultados obtenidos en la consulta recursiva `ARTICULOSRECUR`. Además, calcula el costo total por nodo (artículo) considerando factores como tipo de cálculo, moneda, y coeficientes específicos. A continuación se detalla cada componente.

---

## Propósito

La consulta **LISTADOFINAL** extiende los datos generados en la consulta recursiva para incluir:

1. **Detalles adicionales**: Información sobre el tipo de cálculo, moneda, y otros metadatos.
2. **Cálculos de costos**:
   - Precio unitario en pesos y dólares.
   - Costo total calculado en pesos y dólares.
3. **Estructura jerárquica**: Representación del camino jerárquico de artículos en formato de texto (`pathString`).

---

## Estructura de la Consulta

```slq
 LISTADOFINAL AS (
SELECT
	   CAB.id cab_id, CAB.varcn0, CAB.varcn1, CAB.varcn2, CAB.varcn3 cotizacion_dolar,
	   AC.cantidad cantidad_compuesto, AR.cantidad cantidad_calculada,
       CASE
			WHEN (CAT.valor = '0' ) THEN 'FIJO'
			WHEN (CAT.valor = '1' ) THEN 'VAR L/A'
			WHEN (CAT.valor = '2' ) THEN 'VAR L'
			WHEN (CAT.valor = '3' ) THEN 'VAR A/H'
			WHEN (CAT.valor = '4' ) THEN 'VAR L/H'
			WHEN (CAT.valor = '5' ) THEN 'HORAS'
			ELSE 'NULL'
	   END tipoUB,
	   CASE
			WHEN (CAT.valor = '0' ) THEN 1
			WHEN (CAT.valor = '1' ) THEN CAB.varcn0 * CAB.varcn1
			WHEN (CAT.valor = '2' ) THEN CAB.varcn0
			WHEN (CAT.valor = '3' ) THEN CAB.varcn1 * CAB.varcn2
			WHEN (CAT.valor = '4' ) THEN CAB.varcn0 * CAB.varcn2
			WHEN (CAT.valor = '5' ) THEN 1
			ELSE 0
	   END coeficiente,
	   /*Por ahora 1=PESOS; -1=DOLAR; CAB.varcn3=cotizacion del dolar */
	   CASE WHEN CATPRE.valor = '1' THEN AP.precio ELSE (AP.precio * CAB.varcn3) END precio_unitario_pesos,
	   CASE WHEN CATPRE.valor = '-1' THEN (AP.precio) ELSE (AP.precio / CAB.varcn3) END precio_unitario_dolar,
	   /*Por ahora 1=PESOS; -1=DOLAR; CAB.varcn3=cotizacion del dolar */
	   CASE WHEN CATPRE.valor = '1' THEN (AR.cantidad * AP.precio) ELSE (AR.cantidad * AP.precio * CAB.varcn3) END costo_calculado_pesos,
	   CASE WHEN CATPRE.valor = '-1' THEN (AR.cantidad * AP.precio) ELSE (AR.cantidad * AP.precio / CAB.varcn3) END costo_calculado_dolar,
		array_to_string(AR.articuloidpath, ',') pathString,
		--uniquepath[array_length(AR.uniquepath, 1)] uniqueid,
		--uniquepath[array_length(AR.uniquepath, 1) - 1] uniqueidpadre,
       AR.*,
       AC.*,
	   AP.*,
	   CATPRE.*,
	   CATPRE.name moneda

  FROM ARTICULOSRECUR AR
  LEFT JOIN test9000.ARTICULOSCOMPUESTOS AC ON AR.artcompid = AC.id
  LEFT JOIN test9000.ARTICULOPRECIO AP ON AR.articuloid = AP.articuloid
  LEFT JOIN test9000.CATEGORIAS CAT ON  AC.compvariableid = CAT.id /* Tipo de calculo FIJO, HORAS, etc */
  LEFT JOIN test9000.CATEGORIAS CATPRE ON  AP.categid = CATPRE.id /* Moneda en que esta puesto el precio del articulo */
  CROSS JOIN test9000.REGISTROCAB CAB

 WHERE CAB.id = $P{param_cab_id}  /* ++++++++++++++++++++ID REGISTROCAB+++++++++++++++++ */
 --ORDER BY pathstring
)
```

### 1. **Campos Calculados**

#### a. **Tipo de Cálculo (`tipoUB`)**

El campo `tipoUB` describe el tipo de cálculo asociado al artículo basado en el valor de la categoría (`CAT.valor`):

| Valor de `CAT.valor` | Tipo de Cálculo |
| -------------------- | --------------- |
| `'0'`                | FIJO            |
| `'1'`                | VAR L/A         |
| `'2'`                | VAR L           |
| `'3'`                | VAR A/H         |
| `'4'`                | VAR L/H         |
| `'5'`                | HORAS           |
| Otro                 | NULL            |

#### b. **Coeficiente**

El campo `coeficiente` calcula un multiplicador basado en el tipo de cálculo:

| Valor de `CAT.valor` | Fórmula del Coeficiente   |
| -------------------- | ------------------------- |
| `'0'`                | `1`                       |
| `'1'`                | `CAB.varcn0 * CAB.varcn1` |
| `'2'`                | `CAB.varcn0`              |
| `'3'`                | `CAB.varcn1 * CAB.varcn2` |
| `'4'`                | `CAB.varcn0 * CAB.varcn2` |
| `'5'`                | `1`                       |
| Otro                 | `0`                       |

Este coeficiente se utiliza posteriormente en los cálculos de costos.

---

### 2. **Cálculo de Precios y Costos**

#### a. **Precio Unitario**

Se calculan dos versiones del precio unitario dependiendo de la moneda:

- **En Pesos**:

  ```sql
  CASE WHEN CATPRE.valor = '1' THEN AP.precio ELSE (AP.precio * CAB.varcn3) END
  ```

  - Si la moneda es pesos (CATPRE.valor = '1'), se toma directamente el precio del artículo (AP.precio).
  - Si la moneda es dólares (CATPRE.valor = '-1'), se convierte el precio a pesos multiplicándolo por la cotización del dólar (CAB.varcn3).

- **En Dólares :**
  ```sql
  CASE WHEN CATPRE.valor = '-1' THEN (AP.precio) ELSE (AP.precio / CAB.varcn3) END
  ```
  - Si la moneda es dólares (CATPRE.valor = '-1'), se toma directamente el precio del artículo (AP.precio).
- Si la moneda es pesos (CATPRE.valor = '1'), se convierte el precio a dólares dividiéndolo por la cotización del dólar (CAB.varcn3).

#### b. **Costo Total**

El costo total se calcula multiplicando la cantidad calculada (AR.cantidad) por el precio unitario, considerando la moneda:

- En Pesos :

```sql
CASE WHEN CATPRE.valor = '1' THEN (AR.cantidad * AP.precio) ELSE (AR.cantidad * AP.precio * CAB.varcn3) END
```

- En Dólares :

```sql
CASE WHEN CATPRE.valor = '-1' THEN (AR.cantidad * AP.precio) ELSE (AR.cantidad * AP.precio / CAB.varcn3) END
```

### 3. **Path Jerárquico**

El campo pathString representa el camino jerárquico de artículos en formato de texto. Se genera utilizando la función `array_to_string`:

```sql
array_to_string(AR.articuloidpath, ',')
```

> Por ejemplo, si AR.articuloidpath es [1, 2, 3], el resultado será "1,2,3".

## 4. Unión de Tablas

### a. **ARTICULOSCOMPUESTOS**

Se une con la tabla `ARTICULOSCOMPUESTOS` para obtener información adicional sobre los artículos compuestos.

### b. **ARTICULOPRECIO**

Se une con la tabla `ARTICULOPRECIO` para obtener los precios de los artículos.

### c. **CATEGORIAS**

Se utilizan dos uniones con la tabla `CATEGORIAS`:

- **Una para determinar el tipo de cálculo (`CAT`)**.
- **Otra para determinar la moneda del precio (`CATPRE`)**.

### d. **REGISTROCAB**

Se realiza una unión cruzada (`CROSS JOIN`) con la tabla `REGISTROCAB` para acceder a variables globales como `varcn0`, `varcn1`, `varcn2`, y la cotización del dólar (`varcn3`).

---

## 5. Condiciones de Filtro

La consulta filtra los resultados utilizando el parámetro `$P{param_cab_id}`, que corresponde al ID del registro cabecera (`CAB.id`).

---

## Ejemplo de Resultado

Supongamos los siguientes datos de entrada:

| articuloid | cantidad_calculada | precio_unitario_pesos | precio_unitario_dolar | tipoUB | coeficiente | pathString |
| ---------- | ------------------ | --------------------- | --------------------- | ------ | ----------- | ---------- |
| 1          | 10                 | 100                   | 5                     | FIJO   | 1           | 1          |
| 2          | 5                  | 200                   | 10                    | VAR L  | 2           | 1,2        |
| 3          | 3                  | 300                   | 15                    | HORAS  | 1           | 1,2,3      |

El resultado final incluiría:

| articuloid | cantidad_calculada | precio_unitario_pesos | costo_calculado_pesos | moneda  | pathString |
| ---------- | ------------------ | --------------------- | --------------------- | ------- | ---------- |
| 1          | 10                 | 100                   | 1000                  | Pesos   | 1          |
| 2          | 5                  | 200                   | 1000                  | Pesos   | 1,2        |
| 3          | 3                  | 300                   | 900                   | Dólares | 1,2,3      |

---

## Consideraciones Técnicas

### Moneda

La moneda se determina mediante el campo `CATPRE.valor`:

- `'1'`: Pesos.
- `'-1'`: Dólares.

### Cotización del Dólar

La cotización del dólar (`CAB.varcn3`) se utiliza para convertir precios entre monedas.

### Escalabilidad

La consulta está diseñada para manejar múltiples niveles de jerarquía y diferentes tipos de cálculo.

### Rendimiento

El uso de funciones como `array_to_string` y múltiples uniones puede afectar el rendimiento en estructuras muy grandes.

---

## Conclusión

La consulta **LISTADOFINAL** amplía los resultados de la consulta recursiva `ARTICULOSRECUR` para incluir detalles adicionales, como el tipo de cálculo, moneda, y costos totales en pesos y dólares. Este enfoque permite una gestión detallada y precisa de los artículos en sistemas de inventario o presupuestos.

---

## COSTOSFINAL

La consulta **COSTOSFINAL** tiene como objetivo calcular el **costo total** (en pesos y dólares) para cada nodo en la estructura jerárquica de artículos. Este cálculo incluye los costos acumulados de todos los nodos descendientes (hijos, nietos, etc.) de un nodo dado, además de su propio costo.

---

## Propósito

El propósito de esta sección es:

1. **Acumular costos**: Sumar los costos calculados (`costo_calculado_pesos` y `costo_calculado_dolar`) de todos los nodos descendientes de un nodo específico.
2. **Incluir nodos hoja**: Asegurar que los nodos hoja (que no tienen descendientes) tengan un costo total igual a su costo calculado.
3. **Proporcionar una vista consolidada**: Generar una vista consolidada de costos totales para cada nodo en la jerarquía.

---

## Estructura de la Consulta

```sql
, COSTOSFINAL AS (
SELECT LF01.pathstring, SUM(LF02.costo_calculado_pesos) costo_total_pesos, SUM(LF02.costo_calculado_dolar) costo_total_dolar
  FROM LISTADOFINAL LF01
 /* En este JOIN un nodo se une a todos sus nodos hijos, nietos, etc.
    En este punto, solo los nodos hojas tienen costos. Si uno nodo tiene hijos, entonces el nodo tiene costo 0.
	Los nodos hojas se unen solo consigo mismo, por lo tanto el costo_total es igual al costo_calculado
  */
 INNER JOIN LISTADOFINAL LF02 ON LF01.pathstring = LF02.pathstring             /* un nodo se une consigo mismo */
 							   OR LF02.pathstring like LF01.pathstring || ',%' /* un nodo se une con todos sus hijos, nietos, etc. */
 GROUP BY LF01.pathstring
 --ORDER BY pathstring
)
```

### 1. **Campos Seleccionados**

- **pathstring**: Representa el camino jerárquico del nodo en formato de texto.
- **costo_total_pesos**: Suma acumulada de los costos en pesos de todos los nodos descendientes, incluyendo el nodo actual.
- **costo_total_dolar**: Suma acumulada de los costos en dólares de todos los nodos descendientes, incluyendo el nodo actual.

---

### 2. **Unión de Nodos**

La clave de esta consulta está en la unión (`INNER JOIN`) entre dos instancias de la tabla `LISTADOFINAL` (`LF01` y `LF02`). Esta unión permite relacionar un nodo con todos sus descendientes:

#### a. **Relación Consigo Mismo**

```sql
LF01.pathstring = LF02.pathstring
```

Un nodo siempre se une consigo mismo. Esto asegura que el costo calculado del nodo se incluya en el costo total.

#### b. **Relación con Descendientes**

```sql
LF02.pathstring LIKE LF01.pathstring || ',%'
```

- Un nodo se une con todos sus descendientes (hijos, nietos, etc.). Esto se logra utilizando el operador LIKE y concatenando una coma (',') al final del pathstring del nodo padre.

  > Por ejemplo:
  >
  > - Si LF01.pathstring = '1,2', entonces LF02.pathstring puede ser '1,2,3' o '1,2,4', pero no '1,3'.

### 3 Agrupación Cálculo

- Agrupacion por `pathstring`
  ```slq
  GROUP BY LF01.pathstring
  ```
  - Los resultados se agrupan por el pathstring del nodo principal (LF01).
- **Suma de Costos :**
  ```sql
  SUM(LF02.costo_calculado_pesos) costo_total_pesos,
  SUM(LF02.costo_calculado_dolar) costo_total_dolar
  ```
  - Se suman los costos calculados (costo_calculado_pesos y costo_calculado_dolar) de todos los nodos relacionados (LF02) para cada nodo principal (LF01).

# Consideraciones Importantes

1. **Nodos Hoja**

   - Los nodos hoja (que no tienen descendientes) solo se unen consigo mismos. Por lo tanto, su `costo_total` será igual a su `costo_calculado`.

2. **Nodos Intermedios**

   - Los nodos intermedios (que tienen descendientes) acumulan los costos de todos sus descendientes. Esto permite calcular el costo total de un artículo compuesto que depende de otros artículos.

3. **Costos Acumulativos**
   - El costo total de un nodo incluye:
     - Su propio costo calculado (si existe).
     - Los costos calculados de todos sus descendientes.

---

# Ejemplo de Resultado

Supongamos los siguientes datos en `LISTADOFINAL`:

| pathstring | costo_calculado_pesos | costo_calculado_dolar |
| ---------- | --------------------- | --------------------- |
| 1          | 1000                  | 50                    |
| 1,2        | 500                   | 25                    |
| 1,2,3      | 300                   | 15                    |
| 1,2,4      | 200                   | 10                    |

El resultado final sería:

| pathstring | costo_total_pesos | costo_total_dolar |
| ---------- | ----------------- | ----------------- |
| 1          | 2000              | 100               |
| 1,2        | 1000              | 50                |
| 1,2,3      | 300               | 15                |
| 1,2,4      | 200               | 10                |

### Explicación:

- **Nodo `1`**:
  - Incluye los costos de sí mismo (`1000`) y de sus descendientes (`500 + 300 + 200`).
  - Total: `2000` pesos y `100` dólares.
- **Nodo `1,2`**:
  - Incluye los costos de sí mismo (`500`) y de sus descendientes (`300 + 200`).
  - Total: `1000` pesos y `50` dólares.
- **Nodos `1,2,3` y `1,2,4`**:
  - Son nodos hoja, por lo que su costo total es igual a su costo calculado.

---

### Consideraciones Técnicas

1. **Rendimiento**

   - La consulta utiliza una unión recursiva (`LIKE`) para relacionar nodos con sus descendientes. Esto puede afectar el rendimiento en estructuras jerárquicas muy grandes.
   - Se recomienda indexar el campo `pathstring` para optimizar las búsquedas.

2. **Escalabilidad**

   - La consulta está diseñada para manejar múltiples niveles de jerarquía, pero su rendimiento dependerá de la profundidad y el número de nodos en la estructura.

3. **Ordenamiento**
   - Aunque se incluye un comentario para ordenar por `pathstring`, el ordenamiento no es necesario para el cálculo de costos. Sin embargo, puede ser útil para visualizar los resultados de manera jerárquica.

---

### Conclusión

La consulta **COSTOSFINAL** calcula el costo total acumulado para cada nodo en la estructura jerárquica de artículos. Este enfoque permite consolidar los costos de todos los nodos descendientes, proporcionando una visión clara y detallada de los costos totales en pesos y dólares. Es especialmente útil en sistemas de inventario o presupuestos donde los artículos compuestos dependen de otros artículos.

## Descripción General

Esta consulta final integra y consolida toda la información generada en las etapas previas (`LISTADOFINAL` y `COSTOSFINAL`) para proporcionar una vista detallada y jerárquica de los artículos, incluyendo detalles adicionales como costos totales, secuencias, y metadatos del registro cabecera. A continuación se detalla cada componente.

---

## Propósito

El objetivo de esta consulta es:

1. **Unificar datos**: Combinar información de múltiples tablas para generar un informe completo.
2. **Proporcionar detalles jerárquicos**: Incluir campos que describen la estructura jerárquica de los artículos.
3. **Calcular y mostrar costos consolidados**: Mostrar tanto los costos calculados como los costos totales acumulados por nodo.
4. **Facilitar la visualización**: Ordenar los resultados jerárquicamente mediante el campo `pathstring`.

---

## Estructura de la Consulta

### 1. **Campos Seleccionados**

#### a. **Datos del Registro Cabecera**

- **cab_id**: ID del registro cabecera.
- **referenciatexto**: Referencia textual asociada al registro.
- **fecha**: Fecha del registro.
- **fechacompromiso**: Fecha comprometida para el registro.
- **articulocab**: Nombre del artículo principal asociado al registro cabecera.

#### b. **Secuencia y Metadatos**

- **secnum**: Valor de la secuencia asociada al registro.
- **codigoregistro**: Código del registro.
- **nametxt**: Nombre del registro.
- **NombreRegistro**: Descripción del registro.

#### c. **Detalles del Artículo**

- **internalcode**, **externalcode**: Códigos interno y externo del artículo.
- **articulo_nombre**: Nombre del artículo.
- **pathstring**: Representación jerárquica del camino del artículo.
- **varcn0**, **varcn1**, **varcn2**: Variables globales utilizadas en los cálculos.
- **tipoUB**: Tipo de cálculo asociado al artículo (FIJO, VAR L/A, etc.).
- **cantidad_compuesto**, **cantidad_calculada**: Cantidades inicial y calculada del artículo.
- **coeficiente**: Coeficiente utilizado en los cálculos.
- **moneda**: Moneda en la que está expresado el precio del artículo.
- **cotizacion_dolar**: Cotización del dólar utilizada para conversiones.
- **precio_unitario_pesos**, **precio_unitario_dolar**: Precios unitarios en pesos y dólares.
- **costo_calculado_pesos**, **costo_calculado_dolar**: Costos calculados en pesos y dólares.

#### d. **Costos Totales**

- **costo_total_pesos**, **costo_total_dolar**: Costos totales acumulados en pesos y dólares.

#### e. **Unidad de Medida**

- **unidad_medida**: Unidad de medida del artículo.

#### f. **Grupos Jerárquicos**

Se extraen hasta 15 niveles jerárquicos del campo `pathstring` utilizando la función `SPLIT_PART`:

```sql
SPLIT_PART(LF.pathstring, ',', 1) grupo01,
SPLIT_PART(LF.pathstring, ',', 2) grupo02,
...
SPLIT_PART(LF.pathstring, ',', 15) grupo15
```

Estos campos permiten identificar rápidamente los niveles jerárquicos de un artículo.

---

## 2. Unión de Tablas

### a. COSTOSFINAL

Se une con la tabla `COSTOSFINAL` mediante el campo `pathstring` para agregar los costos totales acumulados (`costo_total_pesos` y `costo_total_dolar`).

### b. REGISTROCAB

Se une con la tabla `REGISTROCAB` para obtener detalles del registro cabecera, como fecha, referencia, y variables globales.

### c. REGISTROCABSEQ y SECUENCIAS

Se unen con las tablas `REGISTROCABSEQ` y `SECUENCIAS` para identificar la secuencia asociada al registro cabecera.

### d. DEPOSITOSARTICULOS y ARTICULOS

Se unen con las tablas `DEPOSITOSARTICULOS` y `ARTICULOS` para obtener el artículo principal asociado al registro cabecera.

## 3. Ordenamiento

Los resultados se ordenan jerárquicamente utilizando el campo `pathstring`.

```sql
ORDER BY LF.pathstring
```

Esto asegura que los nodos se muestren en el orden correcto según su estructura jerárquica.

---

# Ejemplo de Resultado

Supongamos los siguientes datos:

| pathstring | costo_calculado_pesos | costo_calculado_dolar | costo_total_pesos | costo_total_dolar |
| ---------- | --------------------- | --------------------- | ----------------- | ----------------- |
| 1          | 1000                  | 50                    | 2000              | 100               |
| 1,2        | 500                   | 25                    | 1000              | 50                |
| 1,2,3      | 300                   | 15                    | 300               | 15                |
| 1,2,4      | 200                   | 10                    | 200               | 10                |

El resultado final podría ser:

| cab_id | articulocab  | pathstring | costo_total_pesos | costo_total_dolar | grupo01 | grupo02 | grupo03 |
| ------ | ------------ | ---------- | ----------------- | ----------------- | ------- | ------- | ------- |
| 1      | ArtPrincipal | 1          | 2000              | 100               | 1       |         |         |
| 1      | ArtPrincipal | 1,2        | 1000              | 50                | 1       | 2       |         |
| 1      | ArtPrincipal | 1,2,3      | 300               | 15                | 1       | 2       | 3       |
| 1      | ArtPrincipal | 1,2,4      | 200               | 10                | 1       | 2       | 4       |

---

### Consideraciones Técnicas

1. **Jerarquía y Niveles**

   - La función `SPLIT_PART` permite descomponer el campo `pathstring` en niveles jerárquicos individuales. Esto facilita el análisis de la estructura jerárquica.
   - Se incluyen hasta 15 niveles jerárquicos (`grupo01` a `grupo15`), lo que cubre la mayoría de los casos prácticos.

2. **Rendimiento**

   - La consulta involucra múltiples uniones y funciones de cadena (`SPLIT_PART`), lo que puede afectar el rendimiento en estructuras jerárquicas muy grandes.
   - Se recomienda indexar los campos clave (como `pathstring`, `cab_id`, y `registrocabid`) para optimizar las búsquedas.

3. **Escalabilidad**
   - La consulta está diseñada para manejar múltiples niveles de jerarquía, pero su rendimiento dependerá de la profundidad y el número de nodos en la estructura.

---

### Conclusión

Esta consulta final proporciona una vista consolidada y jerárquica de los artículos, incluyendo detalles adicionales como costos totales, secuencias, y metadatos del registro cabecera. Es especialmente útil en sistemas de inventario o presupuestos donde se requiere una representación clara y detallada de los costos y la estructura jerárquica de los artículos.

## CONSULTA COMPLETA

```sql

WITH RECURSIVE ARTICULOSRECUR AS (

	/* ****************************************************************************************************************** */
	/* Recorrer la estructura de arbol de la dependencias entre articulos.                                                */
	/* A medida que se hace el recorrido se arma el path de relacion y se calculan las cantidades a usar de cada articulo */
	/* ****************************************************************************************************************** */

	SELECT 0::bigint artcompid, DA.articuloid, 0::bigint parentarticuloid,
	       ARRAY[DA.articuloid] AS articuloidpath,
	       --ARRAY[nextval('global_unique_id_seq')] uniquepath, /* requiere tener creada una secuencia llamada global_unique_id_seq */
	       ARRAY[0::bigint] idpath,
	       ARRAY[0::bigint] variable,
		   RC.cantidad cantidad,
		   A.internalcode, A.externalcode, A.nombre articulo_nombre,
		   null::text unidad_medida
	  FROM test9000.REGISTROCAB R
	 INNER JOIN test9000.REGISTROCUERPO RC ON R.id = RC.presupcabid
	 INNER JOIN test9000.DEPOSITOSARTICULOS DA ON RC.articulodepositoid = DA.id
	 INNER JOIN test9000.ARTICULOS A ON DA.articuloid = A.id
     WHERE R.id = $P{param_cab_id}  /* ++++++++++++++++++++ID REGISTROCAB+++++++++++++++++ */

	UNION

    SELECT AC.id artcompid, AC.articuloid, AC.parentarticuloid,
	      AR.articuloidpath || AC.articuloid articuloidpath,
	      --AR.uniquepath || nextval('global_unique_id_seq') uniquepath,
	      AR.idpath || AC.id idpath,
	      AR.variable || AC.compvariableid variable,
		  /* En el calculo de la cantidad se calcula la cantidad que le pertenece al nodo y se la multiplica por la cantidad (AR.cantidad) del nodo padre */
		   CASE
			    WHEN (AC.funcion IS NOT NULL) THEN  AR.cantidad * (SELECT test9000.flowscalcularpresupuesto('cantidad'::text, AC.funcion::text, CAB.varcn0::numeric, CAB.varcn1::numeric, CAB.varcn2::numeric))
				WHEN (CAT.valor = '0' ) THEN  AR.cantidad * AC.cantidad
				WHEN (CAT.valor = '1' ) THEN  AR.cantidad * AC.cantidad * CAB.varcn0 * CAB.varcn1
				WHEN (CAT.valor = '2' ) THEN  AR.cantidad * AC.cantidad * CAB.varcn0
				WHEN (CAT.valor = '3' ) THEN  AR.cantidad * AC.cantidad * CAB.varcn1 * CAB.varcn2
				WHEN (CAT.valor = '4' ) THEN  AR.cantidad * AC.cantidad * CAB.varcn0 * CAB.varcn2
				WHEN (CAT.valor = '5' ) THEN  AR.cantidad * AC.cantidad * CAB.varcn0 * CAB.varcn2
				ELSE 0
			END cantidad,
			A.internalcode, A.externalcode, A.nombre articulo_nombre,
			A.um unidad_medida
	 FROM test9000.ARTICULOSCOMPUESTOS AC
	INNER JOIN ARTICULOSRECUR AR ON AR.articuloid = AC.parentarticuloid
	INNER JOIN test9000.ARTICULOS A ON AC.articuloid = A.id
  	 LEFT JOIN test9000.CATEGORIAS CAT ON  AC.compvariableid = CAT.id
  	CROSS JOIN test9000.REGISTROCAB CAB
	WHERE CAB.id = $P{param_cab_id}  /* ++++++++++++++++++++ID REGISTROCAB+++++++++++++++++ */
	  AND NOT AC.articuloid = ANY(AR.articuloidpath)
)
/* *********************************************************************************************************************** */
/* Agregamos algunas columnas de detalles, etc. que sirvan para controlar la consulta y calculamos el costo total por nodo */
/* *********************************************************************************************************************** */
, LISTADOFINAL AS (
SELECT
	   CAB.id cab_id, CAB.varcn0, CAB.varcn1, CAB.varcn2, CAB.varcn3 cotizacion_dolar,
	   AC.cantidad cantidad_compuesto, AR.cantidad cantidad_calculada,
       CASE
			WHEN (CAT.valor = '0' ) THEN 'FIJO'
			WHEN (CAT.valor = '1' ) THEN 'VAR L/A'
			WHEN (CAT.valor = '2' ) THEN 'VAR L'
			WHEN (CAT.valor = '3' ) THEN 'VAR A/H'
			WHEN (CAT.valor = '4' ) THEN 'VAR L/H'
			WHEN (CAT.valor = '5' ) THEN 'HORAS'
			ELSE 'NULL'
	   END tipoUB,
	   CASE
			WHEN (CAT.valor = '0' ) THEN 1
			WHEN (CAT.valor = '1' ) THEN CAB.varcn0 * CAB.varcn1
			WHEN (CAT.valor = '2' ) THEN CAB.varcn0
			WHEN (CAT.valor = '3' ) THEN CAB.varcn1 * CAB.varcn2
			WHEN (CAT.valor = '4' ) THEN CAB.varcn0 * CAB.varcn2
			WHEN (CAT.valor = '5' ) THEN 1
			ELSE 0
	   END coeficiente,
	   /*Por ahora 1=PESOS; -1=DOLAR; CAB.varcn3=cotizacion del dolar */
	   CASE WHEN CATPRE.valor = '1' THEN AP.precio ELSE (AP.precio * CAB.varcn3) END precio_unitario_pesos,
	   CASE WHEN CATPRE.valor = '-1' THEN (AP.precio) ELSE (AP.precio / CAB.varcn3) END precio_unitario_dolar,
	   /*Por ahora 1=PESOS; -1=DOLAR; CAB.varcn3=cotizacion del dolar */
	   CASE WHEN CATPRE.valor = '1' THEN (AR.cantidad * AP.precio) ELSE (AR.cantidad * AP.precio * CAB.varcn3) END costo_calculado_pesos,
	   CASE WHEN CATPRE.valor = '-1' THEN (AR.cantidad * AP.precio) ELSE (AR.cantidad * AP.precio / CAB.varcn3) END costo_calculado_dolar,
		array_to_string(AR.articuloidpath, ',') pathString,
		--uniquepath[array_length(AR.uniquepath, 1)] uniqueid,
		--uniquepath[array_length(AR.uniquepath, 1) - 1] uniqueidpadre,
       AR.*,
       AC.*,
	   AP.*,
	   CATPRE.*,
	   CATPRE.name moneda

  FROM ARTICULOSRECUR AR
  LEFT JOIN test9000.ARTICULOSCOMPUESTOS AC ON AR.artcompid = AC.id
  LEFT JOIN test9000.ARTICULOPRECIO AP ON AR.articuloid = AP.articuloid
  LEFT JOIN test9000.CATEGORIAS CAT ON  AC.compvariableid = CAT.id /* Tipo de calculo FIJO, HORAS, etc */
  LEFT JOIN test9000.CATEGORIAS CATPRE ON  AP.categid = CATPRE.id /* Moneda en que esta puesto el precio del articulo */
  CROSS JOIN test9000.REGISTROCAB CAB

 WHERE CAB.id = $P{param_cab_id}  /* ++++++++++++++++++++ID REGISTROCAB+++++++++++++++++ */
 --ORDER BY pathstring
)
/* ******************************** */
/* Calcular el costo total por nodo */
/* ******************************** */
, COSTOSFINAL AS (
SELECT LF01.pathstring, SUM(LF02.costo_calculado_pesos) costo_total_pesos, SUM(LF02.costo_calculado_dolar) costo_total_dolar
  FROM LISTADOFINAL LF01
 /* En este JOIN un nodo se une a todos sus nodos hijos, nietos, etc.
    En este punto, solo los nodos hojas tienen costos. Si uno nodo tiene hijos, entonces el nodo tiene costo 0.
	Los nodos hojas se unen solo consigo mismo, por lo tanto el costo_total es igual al costo_calculado
  */
 INNER JOIN LISTADOFINAL LF02 ON LF01.pathstring = LF02.pathstring             /* un nodo se une consigo mismo */
 							   OR LF02.pathstring like LF01.pathstring || ',%' /* un nodo se une con todos sus hijos, nietos, etc. */
 GROUP BY LF01.pathstring
 --ORDER BY pathstring
)
/* ************************************************************************* */
SELECT
       CAB.id cab_id, CAB.referenciatexto, CAB.fecha, CAB.fechacompromiso ,
	   ARTCAB.nombre as articulocab,
       SEC.valor AS secnum, S.codigoregistro, S.name as nametxt,  S.descrip as NombreRegistro,
       LF.internalcode, LF.externalcode, LF.articulo_nombre,
       LF.pathstring, LF.varcn0, LF.varcn1, LF.varcn2, LF.tipoUB,
	   LF.cantidad_compuesto, LF.cantidad_calculada, LF.coeficiente,
	   LF.internalcode, LF.externalcode, LF.articulo_nombre, LF.pathstring, LF.moneda,
	   LF.cotizacion_dolar, LF.precio_unitario_pesos, LF.precio_unitario_dolar, LF.costo_calculado_pesos, LF.costo_calculado_dolar,
	   CF.costo_total_pesos, CF.costo_total_dolar, LF.unidad_medida,
	   SPLIT_PART(LF.pathstring, ',', 1) grupo01, SPLIT_PART(LF.pathstring, ',', 2) grupo02, SPLIT_PART(LF.pathstring, ',', 3) grupo03,
	   SPLIT_PART(LF.pathstring, ',', 4) grupo04, SPLIT_PART(LF.pathstring, ',', 5) grupo05, SPLIT_PART(LF.pathstring, ',', 6) grupo06,
	   SPLIT_PART(LF.pathstring, ',', 7) grupo07, SPLIT_PART(LF.pathstring, ',', 8) grupo08, SPLIT_PART(LF.pathstring, ',', 9) grupo09,
	   SPLIT_PART(LF.pathstring, ',', 10) grupo10, SPLIT_PART(LF.pathstring, ',', 11) grupo11, SPLIT_PART(LF.pathstring, ',', 12) grupo12,
	   SPLIT_PART(LF.pathstring, ',', 13) grupo13, SPLIT_PART(LF.pathstring, ',', 14) grupo14, SPLIT_PART(LF.pathstring, ',', 15) grupo15
  FROM LISTADOFINAL LF
 INNER JOIN COSTOSFINAL CF ON LF.pathstring = CF.pathstring
 INNER JOIN  test9000.REGISTROCAB CAB ON LF.cab_id = CAB.id
 /*Identifico la secuencia */
 INNER JOIN  test9000.REGISTROCABSEQ SEC ON  SEC.registrocabid = CAB.id
 INNER JOIN  test9000.SECUENCIAS S ON  SEC.secuenciaid = S.id
 /*Identifico el articulo del cab */
 LEFT JOIN  test9000.DEPOSITOSARTICULOS DACAB ON  CAB.depositoarticuloid = DACAB.id
 LEFT JOIN  test9000.ARTICULOS ARTCAB ON DACAB.articuloid = ARTCAB.id
 ORDER BY LF.pathstring
```
