# 04 - BASE DE DATOS

## OBJETIVO

Documentar completamente la persistencia de la aplicación.

Utilizar:

docs/01_INVENTARIO_TECNICO.md
docs/02_ARQUITECTURA_ACTUAL.md
docs/03_FLUJOS_FUNCIONALES.md

---

# 1. TECNOLOGÍA

Determinar:

- SQLite
- Room
- otra tecnología

---

# 2. TABLAS

Para cada tabla:

| Tabla | Propósito | PK | FK | Índices |

---

# 3. COLUMNAS

Para cada tabla:

| Columna | Tipo | Nullable | Default | PK | FK |

---

# 4. OPERACIONES

Identificar:

- SELECT
- INSERT
- UPDATE
- DELETE
- JOIN
- transacciones

Para cada operación indicar:

- clase
- método
- tabla
- finalidad

---

# 5. RELACIONES

Construir:

Tabla A
 ↓ 1:N
Tabla B

Identificar relaciones reales.

---

# 6. REGLAS DE INTEGRIDAD

Identificar:

- PK
- FK
- UNIQUE
- NOT NULL
- CHECK
- índices
- restricciones

---

# 7. CONSULTAS IMPORTANTES

Identificar consultas complejas o críticas.

---

# 8. RIESGOS

Buscar:

- consultas pesadas
- N+1 queries
- ausencia de índices
- transacciones incorrectas
- acceso a BD desde Main Thread
- corrupción potencial
- problemas de concurrencia

---

# 9. ROOM

Si la aplicación será migrada a Room:

Proponer conceptualmente:

Tabla
 ↓
@Entity
 ↓
DAO
 ↓
Repository

NO implementar código.

---

# RESULTADO

Generar:

docs/04_BASE_DATOS.md