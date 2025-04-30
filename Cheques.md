# 📋 Manual de Uso - Reporte de Cheques 🏦

## 🌟 Descripción General

Este reporte permite visualizar el **histórico completo de cheques** organizados en 3 grupos principales:

- 📥 **En Cartera** (Disponibles)
- ✉️ **Entregados** (a clientes/proveedores)
- 🏛️ **Depositados** (en bancos)

👉 **Funcionalidad clave**: Seguimiento del ciclo de vida completo de cada cheque con alertas visuales para vencimientos.

---

## 🚀 Cómo acceder al reporte

1. Ingresar al módulo de **📊 Reportes Contables**
2. Seleccionar la categoría **🧾 Cheques y Valores**
3. Hacer clic en **"Histórico de Cheques por Grupos"**
4. Configurar filtros según necesidad
5. Generar reporte (botón **🔽 Descargar** para exportar)

---

## 🔍 Filtros Avanzados (Búsqueda específica)

### 📅 Filtros de Fecha

| Parámetro                 | Descripción                                                          | Ejemplo    |
| ------------------------- | -------------------------------------------------------------------- | ---------- |
| `Fecha Desde`             | Fecha inicial del período                                            | 01/01/2024 |
| `Fecha Hasta`             | Fecha final del período                                              | 31/12/2024 |
| _Comportamiento especial_ | Si solo ingresa una fecha, muestra exclusivamente cheques de ese día |            |

[Ver Filtro Fechas](./ComportamientoFiltroFechas.md)

### 🔢 Búsqueda por Cheque

- Busca por número completo o parcial
- No distingue mayúsculas/minúsculas
- Ejemplo: Buscar "258" encontrará "00258" y "1258X"

### 👥 Filtros de Clientes/Proveedores

- Modos de búsqueda:

  - 🏷️ ORIGEN: Solo quien entregó el cheque
  - 🎯 DESTINO: Solo quien lo recibió
  - 🔄 AMBOS: Busca en ambos campos

## 🎨 Interpretación Visual

### 🎨 Guía Visual de Estados de Cheques

Para ayudarte a identificar rápidamente el estado de cada cheque, hemos creado este sistema de codificación por colores:

| Estado         | Código Color | Visualización                                  | Significado                  | Acción Requerida         |
| -------------- | ------------ | ---------------------------------------------- | ---------------------------- | ------------------------ |
| **EN CARTERA** | `#607D8B`    | <span style="color:#607D8B">■</span> Azul/gris | Disponible para uso          | Programar pago o entrega |
| **ENTREGADO**  | `#4CAF50`    | <span style="color:#4CAF50">■</span> Verde     | Transferido a cliente        | Verificar comprobante    |
| **DEPOSITADO** | `#FF9800`    | <span style="color:#FF9800">■</span> Naranja   | Ingresado en cuenta bancaria | Conciliar con extracto   |

### ⚠️ Alertas importantes

- 🔴 **Fondo rojo**: Cheque vencido o por vencer (<5 días)
- 🟢 **Fondo verde**: Cheque con margen seguro (>5 días)

## 📊 Estructura del Reporte

### 🔢 Columnas principales

1. **N° Cheque**  
   Identificador único del cheque

2. **Banco**  
   Entidad emisora del cheque

3. **Fechas**:

   - 📅 Ingreso: Cuando se registró el cheque
   - 💰 Cobro: Fecha de vencimiento
   - 🚚 Entrega: Fecha de movimiento

4. **Partes**:
   - 👤 Cliente origen: Quien entregó el cheque
   - 🏢 Proveedor destino: Quien recibió el cheque

## ❓ Preguntas Frecuentes (FAQ)

### ❌ No veo todos los cheques

✅ **Verificar**:

1. Filtros activos (especialmente fechas)
2. Estado de visualización (3 checkboxes superiores)
3. Permisos de usuario

### 🔍 ¿Cómo buscar si no recuerdo el número exacto?

- Usar **comodines implícitos**:
- Ejemplo: Buscar "23" encontrará "123", "230", etc.
- **No diferencia** mayúsculas/minúsculas ("abc" = "ABC")

### 💾 Formatos de exportación

| Formato | Ideal para                 | Limitaciones   |
| ------- | -------------------------- | -------------- |
| Excel   | Edición avanzada           | Máx. 65k filas |
| PDF     | Impresión profesional      | No editable    |
| CSV     | Análisis en otros sistemas | Sin formato    |

### 💰 Estados Financieros

- ¿Los cheques "Depositados" ya se consideran cobrados?
  Sí, se consideran fondos disponibles una vez depositados

- ¿Por qué algunos cheques entregados aparecen sin nombre de destino?
  Puede ser error de registro. Reportar a Contabilidad con el ID del cheque

### ⚠️ Casos Especiales

- ¿Qué hacer si veo un cheque duplicado?
  Reportar inmediatamente a Tesorería con el ID de ambos registros

- ¿Cómo marco un cheque como anulado?
  No se puede hacer desde el reporte. Solicitar anulación en módulo "Cheques/Anulaciones"

### 📅 Manejo de Fechas

- ¿Por qué no aparecen cheques de hoy si los acabo de registrar?
  El sistema actualiza datos cada 2 horas. Usa "Forzar actualización" si es urgente

- ¿Cómo veo cheques vencidos hace más de 30 días?
  Ajusta "Fecha Desde" a un rango mayor y busca fondos rojos

## 📋 Manual de Buenas Prácticas para Usuarios del Reporte de Cheques

### 🔍 Búsquedas Eficientes

### 📅 Búsqueda por fechas múltiples

- **Selección rápida**: Mantén presionada la tecla `CTRL` mientras haces clic en fechas no consecutivas
- **Rangos personalizados**: Escribe manualmente formato `"dd/mm/aaaa al dd/mm/aaaa"`  
  Ejemplo: `"01/01/2024 al 31/12/2024"`

### 🔎 Búsqueda combinada (ejemplo práctico)

1. **Primer filtro**: Banco → `"Galicia"`
2. **Segundo filtro**: Estado → `"En Cartera"`
3. **Ordenar**: Haz clic en columna `"Fecha de cobro"`

## 📊 Análisis Rápidos

### 🚦 Identificación visual

| Color    | Significado       | Acción Recomendada |
| -------- | ----------------- | ------------------ |
| 🔴 Rojo  | Vence en <5 días  | Revisar urgente    |
| 🟢 Verde | Vence en >15 días | Programar pago     |

### 📈 Comparación mes a mes

1. Generar reporte de Enero
2. Exportar a Excel (botón `CTRL+E`)
3. Repetir para Febrero
4. Comparar usando:
   ```excel
   =SUMAR.SI(rango_enero, "En Cartera", rango_importes)
   ```

## 🚀 Funciones Especiales

### ⌨️ Atajos de teclado imprescindibles

| Combinación | Función            | Icono |
| ----------- | ------------------ | ----- |
| `F5`        | Actualizar datos   | 🔄    |
| `CTRL+E`    | Exportar a Excel   | 📊    |
| `CTRL+P`    | Vista de impresión | 🖨️    |
| `CTRL+F`    | Buscar en reporte  | 🔍    |
| `ALT+V`     | Alternar vistas    | 👁️    |

### ⚙️ Vistas preconfiguradas útiles

Guarda estos filtros frecuentes:

1. **"Cheques urgentes"**  
   `Estado = "En Cartera" + Días < 5`

2. **"Pendientes Cliente X"**  
   `Cliente = "Nombre Cliente" + Estado ≠ "Depositado"`

3. **"Revisión mensual"**  
   `Fecha cobro = Mes actual`

## 💾 Gestión de Exportaciones Profesional

### 🖨️ Exportar a PDF

1. Click en [Exportar] → [PDF]
2. Configurar:
   - ☑️ Incluir logos
   - ☑️ Encabezados en cada página
   - Formato: "2 páginas/hoja"
3. Guardar como: "Cheques\_[Fecha].pdf"

### 📥 Exportación selectiva avanzada

1. Seleccionar múltiples filas:
   - Rango: `SHIFT+clic`
   - Dispersas: `CTRL+clic`
2. Botón derecho → "Exportar selección"
3. Elegir formato: Excel/PDF/CSV

### ❓ Solución rápida de problemas

| Problema                  | Pasos a Seguir                         | Icono |
| ------------------------- | -------------------------------------- | ----- |
| **Cheque no aparece**     | 1. Resetear todos los filtros (🚫)     | 🔍    |
|                           | 2. Verificar rango de fechas (📅)      |       |
|                           | 3. Comprobar permisos de visualización | 👁️    |
| **Datos desactualizados** | 1. Usar botón de actualización (🔄)    | ⏱️    |
|                           | 2. Forzar actualización con F5         | 💻    |
| **Error en exportación**  | 1. Reducir cantidad de filas (✂️)      | 📊    |
|                           | 2. Probar otro formato de exportación  | 🔄    |
|                           | 3. Verificar espacio en disco          | 💾    |

💡 **Consejo adicional**: Para problemas persistentes, tomar captura de pantalla que muestre:

1. Los filtros aplicados
2. El mensaje de error
3. La fecha/hora del sistema

## 🆘 Soporte Técnico

### Tips útiles para pedir ayuda:

- 📸 Capturas de pantalla de errores o filtros usados
- 📅 Especificar rangos de fecha y tipo de reporte
- 📥 Exportar y compartir el Excel si es posible

📩 **Contacto de soporte**:  
[`Ingresa al formulario`](https://surl.li/kmiuwb)

```

```
