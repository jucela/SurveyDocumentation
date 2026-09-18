# 01 - INVENTARIO TÉCNICO

## OBJETIVO

Realizar un inventario técnico completo de la aplicación Android
existente.

Este análisis corresponde a una aplicación Android legacy que
posteriormente podría ser migrada a una arquitectura moderna.

## REGLAS

IMPORTANTE:

- NO modificar ningún archivo.
- NO crear código.
- NO refactorizar.
- NO actualizar dependencias.
- NO eliminar archivos.
- NO realizar migración todavía.
- NO asumir la arquitectura.
- NO inventar información.

Todo dato debe estar sustentado por el código existente.

Si una información no puede determinarse:

"NO DETERMINADO EN EL CÓDIGO"

Distinguir:

HECHO:
Información encontrada directamente en el proyecto.

INFERENCIA:
Conclusión obtenida a partir del código.

NO DETERMINADO:
Información que no puede comprobarse.

---

# 1. INFORMACIÓN GENERAL

Identificar:

- nombre del proyecto
- applicationId/package
- lenguaje(s)
- versión de Java
- versión de Kotlin si existe
- compileSdk
- minSdk
- targetSdk
- Gradle
- Android Gradle Plugin
- módulos
- variantes/build types
- IDE/configuración relevante

---

# 2. ESTRUCTURA DEL PROYECTO

Analizar:

- módulos
- carpetas
- paquetes
- recursos
- librerías locales
- archivos de configuración

Generar una estructura jerárquica.

---

# 3. COMPONENTES ANDROID

Identificar:

- Activities
- Fragments
- Services
- BroadcastReceivers
- ContentProviders
- Application
- Workers
- adapters
- dialogs
- custom views

Para cada uno:

| Clase | Tipo | Paquete | Responsabilidad | Clasificación |

---

# 4. ANDROIDMANIFEST

Analizar:

- Activities
- Services
- Receivers
- Providers
- permisos
- intent-filters
- configuración de aplicación

---

# 5. PERSISTENCIA

Identificar:

- SQLite
- Room
- SharedPreferences
- DataStore
- archivos
- otras tecnologías

---

# 6. RED

Identificar:

- HTTP clients
- Retrofit
- OkHttp
- AsyncHttpClient
- SyncHttpClient
- WebServices
- REST
- SOAP
- autenticación
- SSL/TLS

---

# 7. LIBRERÍAS

Inventariar todas las dependencias.

| Librería | Versión | Uso | Ubicación | Estado |

Clasificar:

- Android oficial
- tercera parte
- propia
- obsoleta
- deprecated
- desconocida

---

# 8. CONCURRENCIA

Identificar:

- Thread
- Runnable
- Handler
- AsyncTask
- Executor
- ExecutorService
- Coroutine
- Flow
- synchronized
- wait
- notify
- join
- sleep
- WorkManager

---

# 9. UI

Identificar:

- XML
- ViewBinding
- DataBinding
- Compose
- RecyclerView
- ListView
- GridView
- componentes personalizados
- diálogos
- tablas
- vistas dinámicas

---

# 10. CONFIGURACIÓN

Identificar:

- constantes
- URLs
- endpoints
- timeouts
- preferencias
- claves de configuración
- variables globales

No mostrar secretos o credenciales reales.

---

# 11. RESUMEN

Generar:

- tecnologías
- arquitectura aparente
- persistencia
- red
- UI
- concurrencia
- principales componentes
- principales dependencias

---

# RESULTADO

Generar:

docs/01_INVENTARIO_TECNICO.md

NO modificar código fuente.