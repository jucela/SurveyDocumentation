# 03 - FLUJOS FUNCIONALES

## OBJETIVO

Reconstruir los principales flujos funcionales de la aplicación.

Utilizar:

docs/01_INVENTARIO_TECNICO.md
docs/02_ARQUITECTURA_ACTUAL.md

Verificar siempre contra el código.

---

# 1. PUNTOS DE ENTRADA

Identificar:

- Application
- Activity inicial
- Login
- Menús
- navegación

---

# 2. FLUJOS

Para cada funcionalidad:

1. Usuario
2. Pantalla
3. Evento
4. Activity/Fragment
5. Servicio
6. Regla de negocio
7. BD
8. Red
9. Resultado

---

# 3. NAVEGACIÓN

Crear árbol:

Inicio
 ↓
Pantalla
 ↓
Pantalla
 ↓
Detalle

Identificar:

- Activities
- Fragments
- Dialogs
- intents
- argumentos

---

# 4. FLUJOS DE DATOS

Documentar:

Entrada
 ↓
Validación
 ↓
Procesamiento
 ↓
Persistencia
 ↓
Sincronización
 ↓
Resultado

---

# 5. ERRORES

Identificar qué ocurre cuando:

- BD falla
- red falla
- datos inválidos
- servidor devuelve error
- usuario cancela
- ocurre excepción

---

# 6. FLUJOS CRÍTICOS

Identificar las funcionalidades más importantes
para el funcionamiento de la aplicación.

---

# RESULTADO

Generar:

docs/03_FLUJOS_FUNCIONALES.md

NO modificar código.