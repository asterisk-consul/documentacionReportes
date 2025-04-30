# Guía de Uso - Reporte RemitoVSCompras

El reporte **RemitoVSCompras** es una herramienta analítica que permite:

- **Comparar** órdenes de compra con remitos de entrega
- **Identificar discrepancias** entre lo comprado y lo recibido
- **Analizar** precios, cantidades y tiempos de entrega

## **Tabla de Contenidos**

1. [Descripción General](#descripción-general)
2. [Parámetros del Reporte](#parámetros-del-reporte)
3. [Comportamiento de Fechas](#-comportamiento-de-fechas)
4. [Columnas del Reporte](#columnas-del-reporte)
5. [Estilo Visual](#estilo-visual)
6. [Notas Adicionales](#notas-adicionales)
7. [Ejemplos de Uso](#ejemplos-de-uso)
8. [Preguntas Frecuentes (FAQ)](#preguntas-frecuentes-faq)

## Descripción General

El reporte **RemitoVSCompras** es una herramienta clave para el análisis comparativo entre órdenes de compra y remitos de entrega. Permite:

- 🔍 **Rastrear** el flujo completo de compras
- ⚖️ **Comparar** cantidades compradas vs. entregadas
- 📊 **Identificar** discrepancias en precios y cantidades
- 📅 **Analizar** tendencias por períodos específicos

### **Parámetros del Reporte**

| Campo                 | Descripción                                                                                                                    | Ejemplo                     |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------ | --------------------------- |
| **Tipo**              | Selecciona el enfoque del reporte:<br>- `compras`: Solo compras<br>- `remitos`: Solo remitos<br>- `todo`: Comparativo completo | `todo`                      |
| **Fecha Desde/Hasta** | Rango de fechas a analizar. [**(Ver Comportamiento de Fechas)**](#comportamiento-de-fechas)                                    | `01/01/2023` a `31/12/2023` |
| **Código Interno**    | Filtra por código de artículo [**(búsqueda parcial)**](BusquedaParcial.md)                                                     | `INS-2023`                  |
| **Descripcion**       | Filtra por descripción/patente/Factura                                                                                         | `CAM-001`                   |
| **Proveedor**         | Filtra por nombre de proveedor                                                                                                 | `LOGISTICA S.A.`            |

## 📅 Comportamiento de Fechas

El reporte aplica inteligentemente los rangos de fecha según lo especificado:

> [**(Ver diagrama)**](ComportamientoFiltroFechas.md#4-diagrama-de-funcionamiento)

### 1. Filtrado por Día Específico

🔹 **Cuando se ingresa solo una fecha** (inicio o fin)  
 → Muestra exclusivamente registros del **día seleccionado**  
 _Ejemplo útil para:_

- Verificar todas las transacciones de un día particular
- Auditorías diarias rápidas

### 2. Filtrado por Rango Completo

🔹 **Cuando se especifican ambas fechas**  
 → Filtra todos los registros **entre esas fechas inclusive**  
 📌 _Recomendado para:_

- Reportes mensuales/trimestrales
- Análisis de períodos específicos

### 3. Comportamiento por Defecto

🔹 **Sin fechas ingresadas**:
| Tipo Reporte | Período Mostrado | Caso de Uso Típico |
|-------------|------------------|---------------------|
| `"compras"` | **Últimos 30 días** | Control mensual |
| `"remitos"` | **Últimos 30 días** | Control de entregas |
| `"todo"` | **Últimos 30 días** | Análisis operativo |

> #### 💡 Mejores Prácticas
>
> - Para **análisis puntual**: Usar filtro de día único
> - Para **tendencias**: Siempre definir rango completo
> - Para **reportes recurrentes**:
>
>   `Fecha Desde:` Primer día del mes <br> > `Fecha Hasta:` Último día del mes

## Columnas del Reporte

### Columnas Comunes

| Columna         | Descripción                                                  |
| --------------- | ------------------------------------------------------------ |
| Articulo        | Nombre del artículo                                          |
| Descripcion     | Patente/descripción asociada                                 |
| Proveedor       | Nombre del proveedor                                         |
| Notas           | Observaciones adicionales (muestra "-" si está vacío)        |
| Precio unitario | Precio por unidad (formato monetario)                        |
| Precio Total    | Precio total (cantidad × precio unitario, formato monetario) |

### Columnas según Tipo de Reporte

#### Para "compras" o "todo":

| Columna         | Descripción                         |
| --------------- | ----------------------------------- |
| Fecha de Compra | Última fecha de compra del artículo |
| Cantidad Compra | Cantidad total comprada             |

#### Para "remitos" o "todo":

| Columna          | Descripción                         |
| ---------------- | ----------------------------------- |
| Fecha del Remito | Última fecha de remito del artículo |
| Cantidad Remito  | Cantidad total remitida             |

#### Solo para "todo":

| Columna    | Descripción                                                |
| ---------- | ---------------------------------------------------------- |
| Diferencia | Cantidad comprada - Cantidad remitida (puede ser negativa) |

## Estilo Visual

- Filas alternadas con fondo gris claro para mejor legibilidad
- Encabezados en color verde oscuro (#486666) con texto blanco
- Formato monetario para precios (ej: $1,234.56)
- Formato de fecha largo (ej: "January 15, 2023")

## Notas Adicionales

1. El campo "Notas" muestra información visible al cliente (los campos que comienzan con "Obs" no se muestran)
2. Las búsquedas por texto son insensibles a mayúsculas/minúsculas
3. Cuando se filtra por "todo", el reporte muestra las comparaciones lado a lado
4. Los valores nulos o vacíos se muestran como "-" para mejor claridad

## Ejemplos de Uso

1. **Ver todas las compras del último mes**:
   Dejar todos los parámetros por defecto, establecer `tipo` = "compras"

2. **Comparar compras vs remitos de un artículo específico**:
   `tipo` = "todo", `codigoInterno` = "ABC123"

3. **Ver remitos de un proveedor en un rango de fechas**:
   `tipo` = "remitos", `proveedor` = "ProveedorX", `fechaDesde` = "2023-01-01", `fechaHasta` = "2023-01-31"

## Preguntas frecuentes (FAQ)

### 🔍 ¿Por qué no veo datos en el reporte?

Verifica estos puntos:

1. 🚦 **Parámetros muy restrictivos**

   - Prueba ampliar los rangos de fecha
   - Reduce los filtros de texto

2. 📅 **Rango de fechas correcto**

   - Confirma que las fechas ingresadas contienen registros
   - Verifica el formato (DD/MM/AAAA)

3. 🗃️ **Existencia de registros**
   - Consulta con logística si hay datos para esos criterios
   - Revisa permisos de visualización

## 🔎 Análisis de Datos

### ⚖️ ¿Cómo identificar discrepancias?

Sigue este flujo:

1. Establece `Tipo = "todo"`
2. Ordena por columna **"Diferencia"** (click en el encabezado)
3. Enfócate en:
   - 🔴 Valores **negativos** (prioridad alta)
   - 🟡 Diferencias **superiores al 10%**
4. Exporta a Excel para cruzar con otros sistemas

## 💡 Funcionalidades Clave

### 🔄 ¿Qué significa "todo"?

- Muestra **comparación lado a lado**:

  **[Compra] vs [Remito] → [Diferencia]**

- Incluye cálculo automático de:
- Stock pendiente (valores positivos)
- Sobrereposiciones (valores negativos)

## 🛠️ Uso Avanzado

### ✂️ ¿Cómo filtrar por múltiples valores?

Alternativas disponibles:

1. **Búsqueda parcial con patrones**:

- `"MAT-2023%"` (códigos que empiecen con...)
- `"%ENERO%"` (contengan el texto)

2. **Truco para múltiples proveedores**:

`Proveedor:`"%CORP% OR %S.A.%"

3. Generar reportes separados y consolidar en Excel

## ❓ Preguntas Adicionales

### ⏱️ ¿Por qué tarda en generar el reporte?

**Posibles causas:**

- Rango de fechas muy amplio
- Muchos artículos/proveedores seleccionados
- Conexión lenta a la base de datos

💡 **Solución:**  
Filtrar por períodos mensuales y luego consolidar

---

### 📊 ¿Cómo interpretar valores NULL?

**Significados:**

- `Fecha NULL`: No existe transacción del tipo opuesto
- `Diferencia NULL`: Falta un lado de la comparación
- `Precio NULL`: Solo aplica a compras

---

### 📲 ¿Se puede usar en móviles?

✅ Sí, pero con **limitaciones**:

- Mejor experiencia en tablets
- Exportar a PDF para visualización óptima
- Evitar rangos mayores a 3 meses

---

## 🆘 Soporte Técnico

### Tips útiles para pedir ayuda:

- 📸 Capturas de pantalla de errores o filtros usados
- 📅 Especificar rangos de fecha y tipo de reporte
- 📥 Exportar y compartir el Excel si es posible

📩 **Contacto de soporte**:  
[`Ingresa al formulario`](https://surl.li/kmiuwb)
