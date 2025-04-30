# Manual de Usuario - Reporte ADM007

**Cuenta Corriente de Acreedores**

---

## 📌 **Objetivo**

Este reporte permite visualizar el estado de cuenta de proveedores/empleados, mostrando:

- Movimientos financieros (débitos/créditos)
- Saldos acumulados
- Totales generales

---

## 🖥 **Acceso al Reporte**

1. Navegar al módulo de **Reportes > Cuentas Corrientes > ADM007**
2. Ingresar parámetros requeridos
3. Hacer clic en **Generar**

---

## 🛠 **Parámetros de Entrada**

| Parámetro          | Tipo   | Obligatorio | Descripción                          | Ejemplo        |
| ------------------ | ------ | ----------- | ------------------------------------ | -------------- |
| `ID Cliente`       | Número | ✅ Sí       | Código único del proveedor/empleado  | `205`          |
| `Fecha Desde`      | Fecha  | ❌ No       | Filtra movimientos desde esta fecha  | `01/01/2024`   |
| `Fecha Hasta`      | Fecha  | ❌ No       | Filtra movimientos hasta esta fecha  | `31/12/2024`   |
| `Texto Referencia` | Texto  | ❌ No       | Busca coincidencias en descripciones | `Pago factura` |

> 💡 **Tip**: Dejar fechas vacías para consultar los últimos 6 meses.

---

## 📊 **Interpretación de Columnas**

| Columna     | Descripción                           | Color Dinámico                                                                                                       |
| ----------- | ------------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| **Fecha**   | Fecha del movimiento                  | -                                                                                                                    |
| **Detalle** | Descripción/Referencia del movimiento | -                                                                                                                    |
| **Debe**    | Montos de egresos                     | <span style="color:#FF5252">■</span> `#FFDDDD` (rojo claro)                                                          |
| **Haber**   | Montos de ingresos                    | <span style="color:#4CAF50">■</span> `#DDFFDD` (verde claro)                                                         |
| **Saldo**   | Acumulado actualizado                 | <span style="color:#4CAF50">■</span> Positivo: `#DDFFDD`<br><span style="color:#FF5252">■</span> Negativo: `#FFCDD2` |

## 🎨 **Leyenda de Colores**

### Encabezados

<span style="color:#607D8B">■</span> `#486666` (azul/gris oscuro) + texto blanco

### Filas alternas

<span style="color:#E0E0E0">■</span> `#E0E0E0` (gris claro)

### Totales

| Concepto  | Color                                                                                                                                                  |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Debe**  | <span style="color:#FF5252">■</span> `#FFDDDD` + texto rojo                                                                                            |
| **Haber** | <span style="color:#4CAF50">■</span> `#DDFFDD` + texto verde                                                                                           |
| **Saldo** | <span style="color:#4CAF50">■</span> Positivo<br><span style="color:#FF5252">■</span> Negativo<br><span style="color:#9E9E9E">■</span> Cero: `#BDBDBD` |

---

## 🔍 **Filtros Adicionales**

El reporte aplica automáticamente:

- **Ordenamiento**: Por fecha (más reciente primero)
- **Agrupación**: Por cliente
- **Cálculos**:
  - Sumatoria de débitos/créditos
  - Saldo acumulado progresivo

---

## 📤 **Exportación**

Disponible en formatos:

- PDF (recomendado para impresión)
- Excel (para análisis adicional)
- HTML (visualización rápida)

---

## ❓ **Preguntas Frecuentes (FAQ)**

### ⚠️ **Error: "Sin datos"**

- Verificar que el ID cliente exista
- Ajustar rango de fechas

### ⚠️ **Error en saldos**

- Confirmar que los tipos de movimiento (IDs 735,736,852,853) estén correctamente configurados en BD

### 1. **¿Por qué no aparecen todos los movimientos del cliente?**

- ✅ **Verifica los filtros**:
  - Asegúrate que las fechas seleccionadas cubran el período deseado.
  - Si no se ingresan fechas, el reporte muestra **únicamente los últimos 6 meses**.
- 🔍 **Revisa el ID**: Confirma que el `ID Cliente` ingresado es correcto.

### 2. **¿Cómo interpretar saldos negativos?**

- Un saldo en **rojo** (![#FFCDD2](#)) significa que el cliente tiene:
  - **Deuda pendiente** (si es proveedor).
  - **Anticipo/pago excedente** (si es empleado).

### 3. **¿Por qué algunos movimientos no tienen fecha de vencimiento?**

- Este campo es opcional y depende de cómo se registró el movimiento original.

### 4. **¿Se puede modificar el orden de las columnas?**

- No directamente en el reporte. Si necesitas un orden personalizado:
  1.  Exporta a Excel.
  2.  Reordena las columnas manualmente.

### 5. **¿Qué hago si los totales no coinciden con mis registros?**

- **Pasos para diagnosticar**:
  1.  Verifica que los IDs de categoría (`735, 736, 852, 853`) estén correctamente asignados en la base de datos.
  2.  Confirma que no haya filtros aplicados accidentalmente.
  3.  Compara con el **detalle de movimientos** (no solo los totales).

### 6. **¿Cómo exportar solo movimientos específicos?**

- Usa los filtros:
  - **Texto Referencia**: Para buscar por descripción (ej: `Factura 001`).
  - **Fechas**: Para acotar el período.
- Luego exporta el resultado filtrado.

### 7. **¿Por qué el reporte tarda en generarse?**

- **Causas comunes**:
  - Volumen alto de movimientos (usar filtros de fecha).
  - Conexión lenta a la base de datos.
- **Solución**:
  - Reducir el rango de fechas.
  - Generar en horarios de menor demanda.

### 8. **¿Qué significa "Sin consultorio" en el campo Flows?**

- Indica que el movimiento no está asociado a una categoría específica.
- **Acción**: Revisar el registro en el sistema para completar esta información.

### 9. **¿Se puede agregar más columnas al reporte?**

- Sí, pero requiere modificar el diseño en **JasperSoft Studio**. Contacta al equipo de desarrollo.

### 10. **¿Cómo identificar movimientos duplicados?**

- Busca el **ID de registro** (columna oculta "regcabid") o fechas/montos repetidos.

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
