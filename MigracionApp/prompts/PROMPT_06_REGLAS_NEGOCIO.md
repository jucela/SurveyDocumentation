# 06 - REGLAS DE NEGOCIO

## OBJETIVO

Identificar las reglas de negocio reales existentes en la aplicación.

Este documento es uno de los más importantes para la migración.

---

# REGLA FUNDAMENTAL

No documentar lo que "debería hacer".

Documentar primero lo que REALMENTE hace el código.

---

# 1. VALIDACIONES

Identificar:

- campos obligatorios
- rangos
- formatos
- condiciones
- validaciones cruzadas

---

# 2. ESTADOS

Identificar:

- estados
- valores
- transiciones
- condiciones

---

# 3. CÁLCULOS

Documentar:

- fórmulas
- totales
- subtotales
- conversiones
- redondeos

---

# 4. REGLAS CONDICIONALES

Buscar:

if
else
switch
ternarios

que representen reglas de negocio.

---

# 5. PROCESOS

Documentar procesos completos:

Entrada
 ↓
Validación
 ↓
Regla
 ↓
Resultado

---

# 6. REGLAS DE PERSISTENCIA

Identificar cuándo:

- crea
- actualiza
- elimina
- recupera

información.

---

# 7. REGLAS DE SINCRONIZACIÓN

Documentar:

- qué se envía
- cuándo
- condiciones
- estados
- reintentos

---

# 8. REGLAS CRÍTICAS

Identificar reglas cuya pérdida podría cambiar el comportamiento
funcional de la aplicación.

---

# 9. TRAZABILIDAD

Generar:

| Regla | Clase | Método | Datos | Resultado |

---

# RESULTADO

Generar:

docs/06_REGLAS_NEGOCIO.md

NO modificar código.