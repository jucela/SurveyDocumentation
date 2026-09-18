# 02 - ARQUITECTURA ACTUAL

## OBJETIVO

Determinar cómo funciona realmente la arquitectura de la aplicación
existente.

Utilizar como referencia:

docs/01_INVENTARIO_TECNICO.md

También verificar directamente contra el código fuente.

---

## REGLAS

NO modificar código.

NO asumir:

- MVC
- MVP
- MVVM
- Clean Architecture

Determinar la arquitectura mediante dependencias y llamadas reales.

---

# 1. CAPAS ACTUALES

Identificar:

- Presentation
- Business
- Data
- Network
- Persistence
- Utilities
- Infrastructure

Explicar qué clases pertenecen a cada grupo.

---

# 2. DEPENDENCIAS

Construir:

UI
 ↓
Lógica
 ↓
Servicios
 ↓
DAO/Repository
 ↓
SQLite/Servidor

Identificar dependencias reales.

---

# 3. CLASES CENTRALES

Identificar:

- clases más utilizadas
- clases con mayor acoplamiento
- clases con muchas dependencias
- clases grandes
- clases con múltiples responsabilidades

---

# 4. DEPENDENCIAS CIRCULARES

Buscar:

A → B → C → A

Documentar cualquier ciclo.

---

# 5. FLUJOS DE DATOS

Explicar cómo circulan los datos:

UI
 ↓
Objeto
 ↓
Servicio
 ↓
DAO
 ↓
BD

y:

UI
 ↓
Servicio
 ↓
HTTP
 ↓
Servidor

---

# 6. PERSISTENCIA

Explicar cómo la aplicación accede a SQLite.

Identificar:

- DAO
- helpers
- servicios
- consultas
- transacciones

---

# 7. RED

Explicar:

UI
 ↓
Service
 ↓
HTTP Client
 ↓
Endpoint

---

# 8. PROBLEMAS ARQUITECTÓNICOS

Identificar:

- alto acoplamiento
- responsabilidades mezcladas
- código duplicado
- clases grandes
- dependencias globales
- estado global
- acceso directo a BD desde UI
- acceso directo a red desde UI

---

# 9. DIAGRAMA GENERAL

Generar un diagrama textual de la arquitectura real.

---

# RESULTADO

Generar:

docs/02_ARQUITECTURA_ACTUAL.md

NO modificar código.