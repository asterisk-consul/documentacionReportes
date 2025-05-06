# Documentación Técnica - Reporte RHSUELDOS

## Descripción General
Este reporte, denominado "RHSUELDOS", está diseñado para mostrar información de sueldos y retiros agrupados por empleado. El reporte presenta datos como fecha, descripción, rol y montos totales con y sin impuestos. Los resultados están agrupados por nombre de empleado, con totales calculados para cada grupo.

## Especificaciones Técnicas

### Información Básica
- **Nombre del Reporte**: RHSUELDOS
- **UUID**: dd8ef93c-27cd-4f2c-9d09-1e898d7f40ab
- **Dimensiones**: 
  - Ancho de página: 595 unidades
  - Alto de página: 842 unidades
  - Ancho de columna: 555 unidades
  - Márgenes: 20 unidades (izquierda, derecha, superior, inferior)
- **Adaptador de Datos**: GM

### Estilos Definidos
1. **ColumnHeader**:
   - Modo: Opaco
   - Color de texto: Blanco (#FFFFFF)
   - Color de fondo: Verde azulado (#486666)
   - Alineación de texto: Centro (horizontal y vertical)
   - Tamaño de fuente: 15 puntos
   - Bordes: línea de 1.0 en todos los lados

2. **RowStyle**: 
   - Alineación de texto: Centro (horizontal y vertical)
   - Estilo condicional: Filas impares con fondo gris claro (#E0E0E0)

### Parámetros
| Parámetro | Tipo | Descripción |
|-----------|------|-------------|
| fechaDesde | java.sql.Date | Fecha de inicio para filtrar registros |
| fechaHasta | java.sql.Date | Fecha de fin para filtrar registros |
| id | java.lang.Integer | ID del cliente para filtrar |
| nombre | java.lang.String | Nombre del cliente para filtrar (búsqueda parcial) |
| rol | java.lang.String | Rol para filtrar (búsqueda parcial) |

### Consulta SQL
La consulta SQL extrae datos de las tablas `test9000.registrocab`, `test9000.perfiles`, `test9000.categoriasperfiles` y `test9000.categorias`. Filtra registros según:
- Categorías con ID 1082 o 1084
- FlowID = 10150
- Fechas dentro del rango especificado o fecha actual si no se proporciona rango
- Filtros opcionales por ID de cliente, nombre y rol
- Ordenación por fecha

### Campos
| Campo | Tipo | Fuente | Descripción |
|-------|------|--------|-------------|
| id | java.lang.Long | registrocab.clientid | ID del cliente |
| fecha | java.sql.Date | registrocab.fecha | Fecha del presupuesto |
| nombre | java.lang.String | registrocab.clientname | Nombre del cliente |
| descripcion | java.lang.String | registrocab.referenciatexto | Descripción del registro |
| totalsinimpuestos | java.math.BigDecimal | registrocab.totalprecio | Total sin impuestos |
| totalconimpuestos | java.math.BigDecimal | registrocab.totalimpuestos | Total con impuestos |
| rol | java.lang.String | categorias.name | Rol del cliente |

### Variables
- **total**: Variable de tipo Double que calcula la suma de `totalconimpuestos` para cada grupo.
  - Restablecimiento: Por grupo (Group1)
  - Cálculo: Sum
  - Expresión: `$F{totalconimpuestos}`

### Grupos
- **Group1**: Agrupa registros por `nombre`.
  - Encabezado: Muestra el nombre del cliente con fondo gris (#9C9C9C)
  - Pie: Muestra el total acumulado para el cliente

## Estructura del Reporte

### Título
- Sección de 30 unidades de altura
- Texto estático "SUELDOS / RETIROS" en color verde azulado (#486666)
- Tamaño de fuente: 15 puntos, negrita
- Bordes en todos los lados

### Encabezado de Columna
- Sección de 25 unidades de altura
- Cuatro columnas con estilo "ColumnHeader":
  1. **Fecha** (100 unidades de ancho)
  2. **Descripcion** (180 unidades de ancho)
  3. **Rol** (100 unidades de ancho)
  4. **Total** (174 unidades de ancho)

### Detalle
- Sección de 24 unidades de altura por registro
- Muestra:
  1. **Fecha** (formato: dd MMMM, yyyy)
  2. **Descripción**
  3. **Rol** 
  4. **Total con impuestos** (precedido por símbolo "$")
- Se aplica estilo "RowStyle" con alternancia de color en filas impares

### Encabezado de Grupo
- Se activa con cada cambio de nombre
- Muestra el nombre del cliente con fondo gris (#9C9C9C)
- Tamaño de fuente: 14 puntos, negrita

### Pie de Grupo
- Muestra:
  - Texto "Total" con fondo gris (#9C9C9C)
  - Suma de todos los valores `totalconimpuestos` para el cliente actual

## Visualización de Datos
- Los datos se presentan en formato tabular
- Los registros están agrupados por cliente
- Cada grupo muestra un encabezado con el nombre del cliente
- Al final de cada grupo, se muestra el total acumulado
- Filas alternas con fondo gris claro para mejor legibilidad

## Filtros Aplicados
El reporte puede filtrarse por:
- Rango de fechas (fechaDesde, fechaHasta)
- ID de cliente específico
- Nombre de cliente (búsqueda parcial)
- Rol (búsqueda parcial)

Si no se proporcionan fechas, el reporte considerará los últimos 6 meses hasta la fecha actual.
