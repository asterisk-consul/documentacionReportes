## PARAMETROS o FILTROS

Descripción del Reporte
Este reporte está diseñado para mostrar información detallada sobre tareas (tareasexec) en un sistema de gestión de flujos o procesos. Proporciona una vista organizada y agrupada de las actividades realizadas, incluyendo detalles como tiempos estimados, fechas reales, estados y totales acumulativos.

Funcionalidades Principales
Datos de Entrada:
El reporte utiliza parámetros como:
param_tipo_flujo: Filtra por tipo de flujo (producción, contabilidad, etc.).
param_filtro: Permite filtrar por día, semana o mes.
param_fecha: Fecha específica para el filtro.
param_perfilid: Identificador del perfil del usuario responsable de las tareas.
Consulta SQL:
La consulta principal recupera datos de varias tablas relacionadas con tareas (tareasexec), flujos (registrocab), estados (statuses) y usuarios (perfiles, usuarios).
Calcula métricas como horas trabajadas (horas) y estados de las tareas (status).
Agrupaciones:
El reporte organiza los datos en grupos:
Por Instructivo (INSTRUCTIVO) : Agrupa tareas por el ID del instructivo ejecutado.
Por Tarea (TAREASID) : Agrupa detalles específicos de cada tarea.
Por Mes : Agrupa las tareas por el mes de inicio.
Cálculos y Variables:
Calcula totales de horas netas (total_hs_neto) y horas brutas (total_hs_bruto) para cada grupo.
Convierte horas en días cuando corresponde (por ejemplo, $F{hsexec} / 24).
Formato y Diseño:
Muestra encabezados y pies de página con logos, títulos y totales.
Usa estilos condicionales (Style1, Style2) para resaltar estados (sin iniciar, en proceso, finalizado) y fechas críticas.
Secciones del Reporte

1. Encabezado de Página
   Muestra el logo de la empresa (param_logo_imagen).
   Incluye un título dinámico que indica el detalle de actividades, ajustado según el perfil del usuario.
2. Cuerpo del Reporte
   Columnas Principales:
   Descripción de la tarea (tadescripcion).
   Estado (estado): Sin iniciar, En proceso, Finalizado.
   Fechas propuestas y reales (fechaestimadainicio, fechaestimadafin, fechainic, fechafin).
   Horas trabajadas (horas) y tiempo bruto (hsexec).
   Formato Condicional:
   Cambia el color de fondo según el estado de la tarea.
   Resalta fechas estimadas que ya han pasado en rojo.
3. Totales por Grupo
   Muestra totales acumulativos de horas netas y horas brutas al final de cada grupo (INSTRUCTIVO).
4. Resumen Final
   Proporciona un resumen general con totales del período seleccionado (día, semana o mes).
   Incluye un mensaje descriptivo del rango de fechas o período consultado.
   Propósito del Reporte
   El propósito principal de este reporte es proporcionar una visión clara y detallada de las actividades realizadas en un sistema de gestión de flujos o procesos. Es útil para:

Monitorear el Progreso:
Permite a los administradores y responsables de proyectos ver el estado de las tareas (sin iniciar, en proceso, finalizado).
Control de Tiempos:
Calcula y muestra las horas trabajadas y los tiempos brutos, facilitando el análisis de productividad.
Identificar Retrasos:
Resalta tareas con fechas estimadas vencidas, ayudando a priorizar acciones.
Generar Informes Ejecutivos:
Proporciona totales acumulativos y resúmenes mensuales, útiles para informes de alto nivel.
Ejemplo de Uso
Un gerente de proyecto puede usar este reporte para:
Ver qué tareas están pendientes o retrasadas.
Analizar la carga de trabajo de un equipo específico (param_perfilid).
Evaluar el rendimiento mensual o semanal del equipo.
Un analista de datos puede usarlo para:
Extraer métricas clave sobre tiempos y estados.
Generar informes personalizados según filtros específicos.
Conclusión
Este reporte es una herramienta poderosa para gestionar y monitorear tareas en un entorno empresarial. Su diseño flexible, con múltiples parámetros y cálculos automáticos, lo hace adecuado para una amplia variedad de casos de uso, desde seguimiento operativo hasta análisis estratégico.

Documentación del Reporte TA004d
Propósito General
Este reporte Jasper (TA004d) es un detalle de actividades/tareas que muestra información sobre las tareas ejecutadas por usuarios en un sistema de gestión de flujos de trabajo. Proporciona:

Un resumen de horas trabajadas

Estados de las tareas

Fechas planificadas vs reales

Responsables

Comentarios de avance

Parámetros Clave
Parámetro Tipo Descripción Valores comunes
param_perfilid Integer Filtra por ID de perfil de usuario Ej: 123 (si es 0 muestra todos)
param_filtro String Periodo de tiempo a visualizar "dia", "semana", "mes"
param_tipo_flujo String Tipo de flujo de trabajo "PRODUCCION", "CONTABILIDAD", ID específico
param_fecha Date Fecha de referencia Ej: 2023-11-15
Estructura Principal
Secciones del Reporte
Encabezado:

Logo de la organización

Título dinámico ("Detalle de actividades" + nombre del responsable si aplica)

Detalle por Tarea:

Responsable (resptxt)

Descripción de la tarea + comentarios (tadescripcion + comentario)

Estado (estado: "Sin iniciar", "En proceso", "finalizado")

Fechas estimadas (inicio/fin)

Fechas reales (inicio/fin)

Horas netas trabajadas (horas)

Tiempo bruto estimado (hsexec en días)

Totales por Proyecto/Instructivo:

Sumatoria de horas por grupo de tareas

Referencia al proyecto (referenciatexto)

Resumen Final:

Total de horas del periodo seleccionado

Características Especiales
Estilos Condicionales
Colores de fondo según estado:

Azul claro (#C7CDFF): Sin iniciar (status=0)

Amarillo claro (#FFFECF): En proceso (status=1)

Verde claro (#C4FCC0): Finalizado (status=2)

Colores de texto para fechas:

Rojo: Fecha estimada de fin vencida (antes de hoy)

Azul: Fecha estimada de fin pendiente (después de hoy)

Hipervínculos
Enlace a reporte "TA007" (TAREAS PARCIALES) con parámetro param_tareaexecid

Cálculos
Variables de resumen:

total_hs_neto: Suma de horas reales por instructivo

total_hs_bruto: Máximo de horas estimadas por instructivo

Total_horas_netas_mes: Suma total de todas las horas

Consulta SQL Subyacente
La consulta principal:

Une tablas de tareas, instructivos, registros, estados y categorías

Filtra por:

Tipo de flujo (producción, contabilidad o custom)

Perfil de usuario

Rango de fecha (día, semana o mes)

Incluye cálculo de tiempo trabajado mediante función flowsGetTareasexecTiempos

Ordena por fecha de inicio descendente

Uso Típico
Este reporte se utiliza para:

Seguimiento del trabajo diario/semanal de equipos

Identificación de tareas atrasadas (fechas en rojo)

Cálculo de horas invertidas en proyectos

Revisión de productividad individual (cuando se filtra por param_perfilid)

Análisis de cumplimiento de plazos estimados

Personalización
Para adaptar el reporte:

Cambiar test9000 por el schema real de la base de datos

Ajustar los flowIDs en los filtros de tipo de flujo

Modificar la ruta del logo (param_logo_imagen)

Ajustar estilos de colores según paleta corporativa

Documentación Técnica - Reporte TA009e
"Detalle de Actividades por Usuario/Período"

## 1. Objetivo del Reporte

El reporte TA009e proporciona un desglose detallado de las tareas ejecutadas por usuarios en un sistema de gestión de flujos de trabajo (BPM).

Su propósito principal es:

- Monitorear el progreso de las tareas asignadas.
- Medir tiempos reales vs. estimados (horas netas vs. horas brutas).
- Identificar retrasos (fechas vencidas resaltadas).
- Generar métricas de productividad por usuario, proyecto o período.

## 2. Parámetros de Entrada

| Lista de parametros    | Descripcion                                                                                                                                                    | Valor por defecto | Valores |
| ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------- | ------- |
| **`param_perfilid`**   | Valor predeterminado es si `sin usuario` lo traera a todos,si quiere ver uno particular se debe seleccionar `un usuario` y mostrara las tareas asignadas a el. |
| **`param_filtro`**     | Valor predeterminado es **día**, los valores por lo que se puede filtrar son **día, semana, mes.**                                                             |
| **`param_fecha`**      | colocar fecha para realizar el filtro de donde tomara `param_filtro`. Es importante tener en cuenta que el filtro de mes, tomara sin importar el dia.          |
| **`param_tipo_flujo`** | `0` o `"TODOS"`: muestra las tareas que estan en toda la empresa, `1` o `"PRODUCCION"`: muestra las tareas de producción de la empresa                         |

Parámetro Tipo Descripción Valores/Ejemplo
param_perfilid Integer Filtra por usuario (ID de perfil). Si es 0, muestra todos. 123 (ID específico)
param_filtro String Período de tiempo: día, semana o mes. "dia", "semana", "mes"
param_tipo_flujo String Tipo de flujo de trabajo. Puede ser un ID o categoría. "PRODUCCION", "CONTABILIDAD", "11156" (ID personalizado)
param_fecha Date Fecha de referencia para el filtro. 2023-11-15
param_logo_imagen String Ruta del logo (se genera automáticamente). "repo:/images/LOGO"

## 3. Estructura del Reporte

### 3.1. Encabezado

**Título dinámico:** Muestra "Detalle de actividades" + nombre del responsable (si param_perfilid está definido).

### 3.2. Cuerpo Principal

Por cada tarea, se muestra:

- Campo Descripción
- Responsable (resptxt) Usuario asignado a la tarea.
- Descripción (tadescripcion) + Comentario Detalle de la tarea y observaciones.
- Estado (estado) Sin iniciar (🔵), En proceso (🟡), Finalizado (🟢).
- Fechas estimadas (fechaestimadainicio, fechaestimadafin) Plazo planificado (resaltado en rojo si está vencido).
- Fechas reales (fechainic, fechafin) Cuándo se inició/completó realmente.
- Horas netas (horas) Tiempo real invertido (en horas).
- Tiempo bruto (hsexec) Estimación inicial (convertida a días).

### 3.3. Agrupaciones

Por instructivo/proyecto (instructivoexecid):

Subtotal de horas por grupo de tareas.

Referencia del proyecto (referenciatexto).

3.4. Totales
Horas totales del período seleccionado (día, semana o mes).

1. Reglas de Estilo
   Colores de fondo por estado:

🔵 Azul claro (#C7CDFF): Tarea sin iniciar.

🟡 Amarillo claro (#FFFECF): Tarea en proceso.

🟢 Verde claro (#C4FCC0): Tarea finalizada.

Fechas vencidas:

Si fechaestimadafin es antes de hoy → Texto en rojo (#FF081C).

Si fechaestimadafin es después de hoy → Texto en azul (#420AFA).

5. Hipervínculos y Acciones
   Enlace a "Tareas Parciales" (Reporte TA007):

Al hacer clic, redirige a un desglose detallado de la tarea seleccionada (param_tareaexecid).

6. Consulta SQL Base
   Origen de datos:

Tablas principales: tareasexec, instructivoexec, registrocab, usuarios.

Filtros aplicados:

Por tipo de flujo (producción, contabilidad, etc.).

Por usuario (param_perfilid).

Por período de tiempo (param_filtro + param_fecha).

Cálculo de tiempos:

Usa la función flowsGetTareasexecTiempos(TA.id) para obtener horas reales trabajadas.

7. Casos de Uso
   Seguimiento individual:

Un jefe filtra por param_perfilid para evaluar el rendimiento de un colaborador.

Control de proyectos:

Se agrupa por instructivoexecid para medir el avance de un proyecto.

Identificación de cuellos de botella:

Las tareas con fechas en rojo indican retrasos.

Reporte de productividad:

Total de horas invertidas en un día/semana/mes.

8. Personalización
   Cambiar conexión a BD: Modificar el esquema test9000 según el entorno.

Ajustar flujos: Actualizar los IDs en param_tipo_flujo (11156, 11180, etc.).

Estilos: Modificar colores en la sección <style> del JRXML.

Notas Finales
✅ Recomendación: Usar con filtros específicos (param_perfilid, param_filtro) para evitar reportes demasiado extensos.
⚠ Dependencias: Requiere que la función flowsGetTareasexecTiempos() esté disponible en la base de datos.

📌 Versión: 1.0
📅 Última actualización: 2023-11-15
