# **Guía de Uso del Reporte COMPRAS**

El reporte **COMPRAS** es una herramienta diseñada para ayudarte a visualizar y analizar información relacionada con las compras realizadas en el sistema. Este documento te guiará paso a paso sobre cómo usarlo, entenderlo y sacarle el máximo provecho.

## **Tabla de Contenidos**

1. [Descripción General](#descripción-general)
2. [Parámetros del Reporte](#parámetros-del-reporte)
3. [Estructura del Reporte](#estructura-del-reporte)\*\*\*\*
4. [Cómo Interpretar los Datos](#cómo-interpretar-los-datos)
5. [Casos de Uso Comunes](#casos-de-uso-comunes)
6. [Consejos y Mejores Prácticas](#consejos-y-mejores-prácticas)
7. [Pregunstas frecuentes (FAQ)](#preguntas-frecuentes-faq)

---

## **Descripción General**

El reporte **COMPRAS** proporciona un desglose detallado de todas las compras realizadas dentro de un rango de fechas específico. Incluye información sobre:

- Los artículos comprados.
- Las cantidades y precios unitarios.
- Los ajustes aplicados y los impuestos correspondientes.
- El total de cada compra agrupado por fecha y referencia.
- Proveedores

Este reporte es ideal para análisis financieros, seguimiento de inventario y auditorías.

---

## **Parámetros del Reporte**

Antes de ejecutar el reporte, puedes configurar los siguientes parámetros para personalizar los resultados:

| Parámetro       | Descripción                                                                                                                                                              | Ejemplo de Valor  |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------------- |
| `nro_secuencia` | Filtra las compras por número de secuencia. Deja en blanco para incluir todas las secuencias.                                                                            | `123`             |
| `flowid`        | Identificador del flujo de trabajo asociado a las compras.                                                                                                               | `11013` (Compras) |
| `tipo_articulo` | Filtra las compras por categoría de artículo. Deja en blanco para incluir todas las categorías.                                                                          | `"Electrónica"`   |
| `fechaDesde`    | Fecha inicial del rango de búsqueda. Deja en blanco si no quieres filtrar por fechas.                                                                                    | `2023-01-01`      |
| `fechaHasta`    | Fecha final del rango de búsqueda. Deja en blanco si no quieres filtrar por fechas.                                                                                      | `2023-12-31`      |
| `proveedor`     | Filtra las compras por nombre del proveedor. Usa coincidencias parciales(No discrimina por mayusculas o minusculas). Deja en blanco si no quieres filtrar por proveedor. | `"Proveedor ABC"` |
| `internalcode`  | Filtra las compras por código interno del artículo, traera toda la compra donde esta este articulo. Usa coincidencias parciales.                                         | `"INT123"`        |
| `externalcode`  | Filtra las compras por código externo del artículo, traera toda la compra donde esta este articulo.. Usa coincidencias parciales.                                        | `"EXT456"`        |

> flowid: ID que representa un flujo de trabajo. En este contexto, se refiere al proceso de compras.
>
> Imp. Alic: Porcentaje de impuesto aplicado (por ejemplo, IVA).

### **Cómo Configurar los Parámetros**

1. Abre el reporte.
2. Ingresa los valores deseados en los campos de parámetros.
   - Para buscar por **código interno**, usa el parámetro `internalcode`.
   - Para buscar por **código externo**, usa el parámetro `externalcode`.
3. Haz clic en **"APLICAR"** para ver los resultados.

> **Nota**: Si no deseas filtrar por `internalcode` o `externalcode`, deja estos campos en blanco.

---

## **Estructura del Reporte**

El reporte está organizado en varias secciones para facilitar la interpretación de los datos:

### **1. Encabezado**

- Muestra el título del reporte: **"COMPRAS"**.
- Proporciona una vista general del contenido.

### **2. Agrupaciones**

El reporte organiza los datos en dos niveles de agrupación:

- **Por Referencia**: Cada grupo muestra el número de referencia y el número de secuencia.
- **Por Fecha**: Dentro de cada referencia, los datos se agrupan por fecha de compra.

### **3. Columnas de Datos**

Las columnas disponibles son:

| Columna                       | Descripción                                    |
| ----------------------------- | ---------------------------------------------- |
| **Internalcode/Externalcode** | Codigos de los articulos.                      |
| **Descripción**               | Nombre del artículo comprado.                  |
| **Cantidad**                  | Cantidad de unidades compradas.                |
| **Precio Unitario**           | Precio unitario del artículo.                  |
| **Precio Total**              | Total antes de ajustes e impuestos.            |
| **Importe Ajuste**            | Porcentaje de ajuste aplicado al precio total. |
| **Imp. Alic**                 | Porcentaje de impuesto aplicado (IVA).         |
| **Importe Final**             | Total final después de ajustes e impuestos.    |

### **4. Totales**

- Al final de cada grupo de fecha, se muestra el **Total de la compra** para ese día.
- Estos totales son útiles para realizar análisis rápidos.

---

## **Cómo Interpretar los Datos**

### **1. Identificar Compras por Proveedor**

- Usa el filtro `proveedor` para encontrar todas las compras realizadas con un proveedor específico.
- Observa los totales diarios para identificar patrones de gasto.

### **2. Identificar Compras por Código Interno o Externo**

- Usa el filtro `internalcode` para encontrar compras relacionadas con un código interno específico.
- Usa el filtro `externalcode` para encontrar compras relacionadas con un código externo específico.
- Observa los totales diarios para identificar patrones de gasto.

  > 💡 **TIP**: Si estás buscando un artículo específico, usar el código interno suele ser más preciso que el nombre o descripción.

### **3. Analizar Impuestos y Ajustes**

- La columna **Imp. Alic** muestra el porcentaje de impuesto aplicado.
- La columna **Importe Ajuste** indica si se aplicó un descuento o recargo adicional.

### **4. Seguimiento de Inventarios**

- La columna **Cantidad** te permite verificar cuántas unidades de un artículo se han comprado.
- Combina esto con la columna **Descripción** para identificar tendencias en la demanda de productos.

---

## **Casos de Uso Comunes**

### **1. Auditoría de Gastos**

- Usa los filtros `fechaDesde` y `fechaHasta` para revisar las compras realizadas en un período específico.
- Verifica los totales diarios para asegurarte de que coincidan con los registros contables.

### **2. Análisis de Proveedores**

- Aplica el filtro `proveedor` para evaluar el desempeño de un proveedor específico.
- Compara los totales de compra entre diferentes proveedores.

### **3. Control de Inventarios**

- Utiliza la columna **Cantidad** para monitorear el volumen de compras de cada artículo.
- Identifica artículos con altos volúmenes de compra para optimizar el inventario.

---

## **Consejos y Mejores Prácticas**

1. **Usa Filtros Específicos**: Si trabajas con grandes volúmenes de datos, utiliza todos los filtros disponibles para reducir el número de resultados y mejorar el rendimiento.
2. **Exporta los Resultados**: Guarda el reporte en formato PDF o Excel para compartirlo con otros miembros del equipo o para realizar análisis adicionales.

3. **Revisa Regularmente**: Usa este reporte periódicamente para mantener un control constante sobre las compras y detectar posibles irregularidades.

---

## Preguntas frecuentes (FAQ)

### **¿Qué pasa si no aparece ningún resultado?**

- **Respuesta**: Si no aparecen resultados después de ejecutar el reporte, verifica lo siguiente:
  1. Asegúrate de que los filtros aplicados (como `fechaDesde`, `fechaHasta`, `proveedor`, `internalcode`, `externalcode`, etc.) sean correctos.
  2. Comprueba que existan datos en el rango de fechas seleccionado.
  3. Si usas filtros opcionales como `internalcode` o `externalcode`, asegúrate de que los valores ingresados coincidan exactamente con los registros en la base de datos.
  4. Si el problema persiste, contacta al equipo de soporte para verificar la conexión a la base de datos o la consulta SQL.

> **👉 Solución:** Probá dejar algunos filtros en blanco y ejecutá el reporte nuevamente para confirmar si hay datos. Después podés ir refinando la búsqueda.

---

### **¿Por qué una compra no muestra ajustes?**

- **Respuesta**: Una compra puede no mostrar ajustes (`Importe Ajuste`) por las siguientes razones:
  1. No se aplicó ningún ajuste durante la creación de la compra.
  2. El campo `porcentajuste` en la base de datos tiene un valor de `0` o está vacío.
  3. El ajuste podría haber sido aplicado manualmente fuera del sistema.

> **Tip**: Para verificar si hay ajustes, revisa la columna `porcentajuste`. Si el valor es `0`, significa que no se aplicó ningún ajuste.

---

### **¿Puedo usar varios filtros a la vez?**

- **Respuesta**: ¡Sí! Puedes combinar varios filtros para refinar los resultados. Por ejemplo:
  - Filtra por `fechaDesde` y `fechaHasta` para un rango específico.
  - Agrega `proveedor` para buscar compras de un proveedor en particular.
  - Usa `internalcode` o `externalcode` para filtrar por artículos específicos.
  - Combina `tipo_articulo` con otros filtros para obtener resultados aún más precisos.

> **📌 Importante:** Cuantos más filtros uses, más específicos serán los resultados. Sin embargo, asegúrate de no excluir demasiados datos accidentalmente.<br><br> **💡 Tip**: Si no estás seguro de los resultados, prueba aplicando un filtro a la vez para ver cómo afecta la salida.

---

### **¿Cómo interpreto los totales diarios?**

- **Respuesta**: Los totales diarios muestran el monto total de las compras realizadas en un día específico. Estos totales incluyen:
  1. El precio total antes de ajustes e impuestos.
  2. Los ajustes aplicados (`Importe Ajuste`).
  3. Los impuestos (`Imp. Alic`).

> **💡 Tip**: Si deseas comparar los gastos diarios, exporta el reporte a Excel y realiza un análisis adicional.

---

### **¿Puedo buscar parte de un nombre o código?**

Sí. Tanto los campos de **proveedor**, **internalcode** y **externalcode** admiten **coincidencias parciales**. Es decir, si escribís `"ABC"` como proveedor, el sistema buscará proveedores que contengan esas letras, como `"Proveedor ABC S.A."`, `"Distribuidora ABC"` o `"ABC Importaciones"`.

---

### **¿Qué significan los colores alternados en las filas?**

- **Respuesta**: Los colores alternados en las filas (gris claro y blanco) están diseñados para mejorar la legibilidad del reporte. Esto facilita la identificación de cada registro individual.

> **Nota**: Esta funcionalidad está configurada automáticamente y no requiere ninguna acción por parte del usuario.

---

### **¿Por qué algunos campos están vacíos?**

- **Respuesta**: Algunos campos pueden estar vacíos debido a las siguientes razones:
  1. El dato no fue registrado en la base de datos (por ejemplo, `clientid` para prospectos nuevos).
  2. El artículo no tiene un código interno (`internalcode`) o externo (`externalcode`) asignado.
  3. No se aplicaron ajustes o impuestos a la compra.

> **💡 Tip**: Si encuentras muchos campos vacíos, verifica con el equipo de administración si los datos están completos en el sistema.

---

Esperamos que esta guía te ayude a aprovechar al máximo el reporte **COMPRAS**. Si tienes preguntas adicionales o necesitas asistencia técnica, no dudes en contactar al equipo de soporte.
