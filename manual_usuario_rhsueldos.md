# Manual de Usuario - Reporte RHSUELDOS

## Introducción

El reporte **RHSUELDOS** está diseñado para proporcionarle información detallada sobre sueldos y retiros de personal. Este manual le guiará en el uso efectivo del reporte para consultar, filtrar y analizar la información financiera relacionada con pagos a empleados y colaboradores.

## Acceso al Reporte

Para acceder al reporte RHSUELDOS:

1. Inicie sesión en el sistema con sus credenciales
2. Navegue hasta el módulo de Recursos Humanos
3. En la sección "Reportes", seleccione "RHSUELDOS"

## Funcionalidades Principales

### 1. Visualización de Datos

El reporte muestra la siguiente información:

- **Fecha**: Día en que se realizó el pago o retiro
- **Descripción**: Detalle o motivo del pago
- **Rol**: Categoría o función del empleado
- **Total**: Monto total con impuestos incluidos

La información se presenta agrupada por empleado, con totales calculados para cada persona.

### 2. Filtros Disponibles

El reporte permite filtrar la información mediante los siguientes criterios:

| Filtro      | Descripción                            | Ejemplo    |
| ----------- | -------------------------------------- | ---------- |
| Fecha Desde | Fecha inicial del período a consultar  | 01/01/2025 |
| Fecha Hasta | Fecha final del período a consultar    | 30/04/2025 |
| ID          | Identificador numérico del empleado    | 15478      |
| Nombre      | Nombre completo o parcial del empleado | "Martínez" |
| Rol         | Categoría o función del empleado       | "Gerente"  |

**Nota**: Si no se especifican fechas, el sistema mostrará por defecto los datos de los últimos 6 meses hasta la fecha actual.

## Guía Paso a Paso

### Generación de un Reporte Básico

1. Acceda al reporte RHSUELDOS
2. Deje todos los filtros en blanco para ver todos los registros disponibles
3. Haga clic en "Generar Reporte"
4. El sistema mostrará los datos agrupados por empleado

### Filtrado por Período

Para consultar pagos en un período específico:

1. Si ingresa solo una fecha filtrar solo ese dia.
1. En el campo "Fecha Desde", seleccione la fecha inicial
1. En el campo "Fecha Hasta", seleccione la fecha final
1. Haga clic en "Generar Reporte"
1. Visualice los resultados filtrados por el período seleccionado

### Búsqueda por Empleado

#### Por Id

Para localizar pagos a un empleado específico:

1. En el campo "Id", elija el empleado, este colocara el id del mismo.
2. Haga clic en "Generar Reporte"
3. El sistema mostrará solo los registros que coincidan con el nombre especificado

#### Por Nombre

Para localizar pagos a un empleado específico:

1. En el campo "Nombre", escriba el nombre completo o parcial del empleado
2. Haga clic en "Generar Reporte"
3. El sistema mostrará solo los registros que coincidan con el nombre especificado

### Filtrado por Rol

Para ver pagos según la función o categoría:

1. En el campo "Rol", escriba la categoría que desea consultar
2. Haga clic en "Generar Reporte"
3. Visualice los resultados correspondientes a esa categoría

## Interpretación de Resultados

### Agrupación por Empleado

El reporte organiza la información por nombre de empleado:

- Cada grupo comienza con una barra gris que muestra el nombre del empleado
- A continuación se listan todos los pagos o retiros asociados a ese empleado
- Al final de cada grupo se muestra un total acumulado

### Totales y Subtotales

- **Total por empleado**: Suma de todos los pagos realizados al empleado en el período seleccionado
- Los montos se muestran en formato de moneda con el símbolo "$"

## Opciones de Exportación

El reporte puede exportarse en varios formatos para su posterior uso:

1. **PDF**: Ideal para imprimir o compartir el reporte sin modificaciones
2. **Excel**: Permite realizar análisis adicionales y cálculos sobre los datos
3. **CSV**: Formato de texto plano compatible con diversas aplicaciones

Para exportar:

1. Genere el reporte con los filtros deseados
2. Haga clic en el botón "Exportar" ubicado en la parte superior
3. Seleccione el formato deseado
4. Especifique la ubicación donde desea guardar el archivo

## 🔍 Consejos Útiles

- **🧩 Filtros combinados**: Puedes usar varios filtros simultáneamente para obtener resultados más específicos.
- **🔎 Búsquedas parciales**: El sistema reconoce coincidencias parciales en campos de texto (_Nombre_ y _Rol_).
- **📊 Ordenamiento**: Por defecto, los resultados se ordenan por fecha (⏳ los más antiguos primero).
- **🌈 Filas alternadas**: Para facilitar la lectura, las filas se muestran con colores alternados (estilo zebra 🦓).

## Solución de Problemas Comunes

| Problema               | Posible Causa                                         | Solución                                                        |
| ---------------------- | ----------------------------------------------------- | --------------------------------------------------------------- |
| No aparecen resultados | Filtros demasiado restrictivos                        | Amplíe el rango de fechas o elimine algunos filtros             |
| Faltan empleados       | El empleado no tiene pagos en el período seleccionado | Verifique y amplíe el rango de fechas                           |
| Totales incorrectos    | Filtros parciales aplicados                           | Asegúrese de incluir todo el período relevante                  |
| Error al exportar      | Problema de permisos                                  | Verifique los permisos de su cuenta o contacte al administrador |

## 📚 Glosario de Términos

- **📅 Fecha**: Día en que se registró el pago o retiro  
  ⚠️ _Ejemplo: 15/05/2024_

- **📝 Descripción**: Detalle o concepto del pago realizado  
  ⚠️ _Ejemplo: "Compra de materiales de oficina"_

- **👔 Rol**: Categoría o función asignada al empleado en el sistema  
  � _Ejemplo: "Administrador", "Contador"_

- **💰 Total**: Monto final que incluye todos los impuestos aplicables  
  💡 _Se muestra en formato moneda (ej: $105,250.00 ARS)_

## ❓ Preguntas Frecuentes (FAQ)

### 🌐 Generales

**❔ P: ¿Con qué frecuencia se actualizan los datos del reporte?**  
✅ **R:** Los datos se actualizan en tiempo real. Cada vez que se genera el reporte, se obtiene la información más reciente disponible en el sistema.

**❔ P: ¿Puedo guardar mis configuraciones de filtros para uso futuro?**  
⚠️ **R:** Actualmente el sistema no permite guardar configuraciones de filtros. Se recomienda anotar las combinaciones de filtros que utiliza con frecuencia.

**❔ P: ¿El reporte muestra información de años anteriores?**  
✅ **R:** Sí, el reporte puede mostrar datos históricos siempre que estén disponibles en la base de datos. Solo necesita especificar las fechas correspondientes en los filtros.

### 🔍 Filtros y Búsqueda

**❔ P: ¿Puedo buscar por múltiples roles a la vez?**  
⚠️ **R:** El sistema no permite seleccionar múltiples roles simultáneamente. Para comparar diferentes roles, necesitará generar reportes separados.

**❔ P: Si no especifico fechas, ¿qué período de tiempo se muestra por defecto?**  
📅 **R:** Por defecto, el sistema muestra los datos de los últimos 6 meses hasta la fecha actual.

**❔ P: ¿Es posible buscar por monto específico?**  
⚠️ **R:** El reporte actual no incluye filtro por monto. Se recomienda exportar a Excel para realizar este tipo de filtrado.

### 👁️ Visualización y Exportación

**❔ P: ¿Existe un límite en la cantidad de registros que puede mostrar el reporte?**  
📊 **R:** El sistema puede manejar hasta 10,000 registros en una sola visualización. Para conjuntos de datos más grandes, se recomienda aplicar filtros más específicos.

**❔ P: ¿Puedo personalizar las columnas que se muestran en el reporte?**  
⚠️ **R:** La versión actual del reporte tiene un formato fijo. Para análisis personalizados, se recomienda exportar a Excel.

**❔ P: Al exportar a PDF, ¿se mantiene el formato de agrupación por empleado?**  
✅ **R:** Sí, la exportación a PDF conserva exactamente el mismo formato y agrupación que se ve en pantalla.

### 🛠️ Resolución de Problemas

**❔ P: El reporte se carga muy lentamente, ¿qué puedo hacer?**  
⚡ **R:** Pruebe a reducir el rango de fechas o aplicar filtros adicionales para limitar la cantidad de datos procesados.

**❔ P: ¿Por qué algunos empleados aparecen duplicados en el reporte?**  
🔄 **R:** Esto puede ocurrir si el empleado está registrado con variaciones en su nombre (por ejemplo, con o sin segundo apellido). Recomendamos buscar por ID para obtener resultados precisos.

**❔ P: He encontrado discrepancias en los montos totales, ¿a qué se debe?**  
💰 **R:** Verifique si los filtros aplicados están excluyendo algunos registros relevantes. También confirme que está observando el campo "Total con impuestos" y no el "Total sin impuestos".

---

## 📞 **Soporte**

Para asistencia contactar a:  
✉️ **soporte@flowsma.com**  
☎️ +54 9 353 474-6217 <br>
📩 [`Ingresa al formulario`](https://surl.li/kmiuwb)

### Tips útiles para pedir ayuda:

- 📸 Capturas de pantalla de errores o filtros usados
- 📅 Especificar rangos de fecha y tipo de reporte
- 📥 Exportar y compartir el Excel si es posible

---
