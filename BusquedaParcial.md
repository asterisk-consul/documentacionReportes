# Nota Técnica: "Búsqueda Parcial" en Filtros de Reportes

## 📌 Definición

El término **(búsqueda parcial)** indica que el sistema buscará coincidencias con _cualquier parte_ del texto ingresado, sin requerir una coincidencia exacta.

## 🔍 Cómo Funciona

1. **No distingue mayúsculas/minúsculas**  
   Ejemplo: Buscar "mate" encontrará "Material", "MATERIALES" y "Herramientas-Mate"

2. **Coincide con fragmentos**

   - `"log"` encontrará:  
     ✓ "Logística S.A."  
     ✓ "Analógica"  
     ✓ "Catalogo-2023"

3. **No necesita comodines** (a diferencia de SQL)  
   El sistema automáticamente trata el texto como `%TEXTO%`

## 💡 Ejemplos Prácticos

| Búsqueda | Encontrará                                   |
| -------- | -------------------------------------------- |
| `"2023"` | "INS-2023", "PED2023-001", "2023-Materiales" |
| `"cam"`  | "CAM-001", "Cámara", "Empaque-Camion"        |
| `"sa"`   | "S.A.", "Mesas", "Resaltado"                 |

## ⚠️ Limitaciones

- **No soporta operadores avanzados** (AND/OR)
- **Sensibilidad a espacios**:  
  `"log sa"` ≠ `"logsa"`
- **Máximo rendimiento** con 3+ caracteres

## 🛠 Recomendaciones

1. Para mayor precisión usar **términos únicos**  
   Ej: `"MAT-2023"` en lugar de solo `"2023"`

2. Combinar con otros filtros para **reducir resultados**  
   (Fecha + Proveedor + Búsqueda parcial)

3. **Pruebas simples** para verificar comportamiento  
   Ej: Buscar `"a"` mostrará todo lo que contenga esa letra

_🔍 El sistema indexa automáticamente los campos filtrables para optimizar estas búsquedas._
