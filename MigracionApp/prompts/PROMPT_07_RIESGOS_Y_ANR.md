# 07 - RIESGOS TÉCNICOS Y ANR

## OBJETIVO

Realizar una auditoría técnica buscando problemas que puedan afectar
la estabilidad, rendimiento y compatibilidad de la aplicación.

Utilizar los documentos anteriores y verificar directamente contra
el código.

---

# 1. ANR

Buscar:

- red en Main Thread
- SQLite en Main Thread
- archivos en Main Thread
- procesamiento pesado
- loops largos
- loops infinitos
- Thread.sleep
- wait
- join
- synchronized
- locks
- deadlocks

---

# 2. CONCURRENCIA

Analizar:

- Threads
- Executors
- Handlers
- AsyncTask
- callbacks
- sincronización

---

# 3. MEMORY LEAKS

Buscar:

- referencias a Activity
- Context
- listeners
- callbacks
- Threads
- singletons
- adapters

---

# 4. NULL

Buscar:

- NullPointerException
- objetos opcionales mal manejados
- resultados DAO null
- respuestas HTTP null

---

# 5. ANDROID MODERNO

Analizar:

- APIs deprecated
- comportamiento Android moderno
- cambios de permisos
- almacenamiento
- background execution
- Android 12+
- Android 13+
- Android 14+
- Android 15+

---

# 6. RED

Buscar:

- timeouts excesivos
- conexiones síncronas
- SSL inseguro
- retries incorrectos
- ausencia de manejo de errores

---

# 7. BASE DE DATOS

Buscar:

- consultas lentas
- falta de índices
- transacciones
- concurrencia
- acceso desde UI

---

# 8. CLASIFICACIÓN

Para cada problema:

CRÍTICO
ALTO
MEDIO
BAJO

---

# 9. TABLA

| # | Problema | Archivo | Clase | Método | Línea | Severidad | Impacto |

---

# 10. ANR

Para cada candidato:

- operación
- hilo
- escenario
- motivo
- impacto
- solución propuesta

NO corregir todavía.

---

# RESULTADO

Generar:

docs/07_RIESGOS_Y_ANR.md