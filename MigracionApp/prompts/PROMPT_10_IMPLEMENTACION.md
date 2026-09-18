# 10 - IMPLEMENTACIÓN

## OBJETIVO

Implementar la nueva aplicación Android utilizando la arquitectura
definida y aprobada en:

docs/08_ARQUITECTURA_OBJETIVO.md

La implementación debe basarse también en:

docs/01_INVENTARIO_TECNICO.md
docs/02_ARQUITECTURA_ACTUAL.md
docs/03_FLUJOS_FUNCIONALES.md
docs/04_BASE_DATOS.md
docs/05_API_Y_RED.md
docs/06_REGLAS_NEGOCIO.md
docs/07_RIESGOS_Y_ANR.md
docs/09_AUDITORIA_PRE_IMPLEMENTACION.md

---

# TECNOLOGÍAS

- Kotlin
- Clean Architecture
- MVVM
- Room
- Dagger Hilt
- Coroutines
- Flow
- StateFlow
- Retrofit
- OkHttp
- ViewModel
- Navigation Component
- XML
- ViewBinding
- RecyclerView

NO utilizar Jetpack Compose.

---

# REGLA PRINCIPAL

NO realizar una conversión automática:

Java → Kotlin

La nueva aplicación debe ser una implementación nueva basada
en la arquitectura objetivo.

Conservar:

- funcionalidad
- reglas de negocio
- validaciones
- comportamiento
- datos

Rediseñar:

- arquitectura
- clases
- paquetes
- persistencia
- red
- UI
- concurrencia

---

# FASES

Implementar de forma incremental.

## FASE 1 - PROYECTO BASE

Crear:

- proyecto Kotlin
- Gradle
- configuración Android
- Hilt
- Navigation
- estructura de paquetes

Resultado:

Proyecto que compila.

---

## FASE 2 - DOMAIN

Implementar:

- Domain Models
- Repository interfaces
- Use Cases

No depender de:

- Android
- Room
- Retrofit

---

## FASE 3 - ROOM

Implementar:

- Entities
- DAOs
- Database
- relaciones
- índices
- converters
- migrations

Probar cada DAO.

---

## FASE 4 - DATA

Implementar:

- RepositoryImpl
- LocalDataSource
- Mappers
- DataStore si corresponde

---

## FASE 5 - NETWORK

Implementar:

- Retrofit
- OkHttp
- API
- DTO
- RemoteDataSource
- autenticación
- manejo de errores

---

## FASE 6 - HILT

Configurar:

- DatabaseModule
- NetworkModule
- RepositoryModule
- otros módulos necesarios

---

## FASE 7 - PRESENTATION

Implementar pantalla por pantalla:

Fragment
 ↓
ViewModel
 ↓
UiState
 ↓
UseCase

Utilizar:

XML
ViewBinding

NO Compose.

---

## FASE 8 - NAVEGACIÓN

Implementar:

- Navigation Graph
- argumentos
- diálogos
- back stack

---

## FASE 9 - FUNCIONALIDADES

Implementar cada funcionalidad individualmente.

Cada funcionalidad debe:

1. compilar
2. ejecutarse
3. probarse
4. compararse con el comportamiento original

---

## FASE 10 - PRUEBAS

Realizar:

- pruebas unitarias
- pruebas de DAO
- pruebas de Repository
- pruebas de UseCase
- pruebas de ViewModel
- pruebas de navegación
- pruebas funcionales

---

# REGLAS DE CALIDAD

## UI

NO ejecutar:

- SQLite
- HTTP
- operaciones pesadas

en Main Thread.

---

## DOMAIN

No depender de Android.

---

## VIEWMODEL

No acceder directamente a:

- DAO
- Room
- Retrofit

---

## REPOSITORY

Centralizar acceso a datos.

---

## ROOM

Utilizar suspend/Flow según corresponda.

---

## NETWORK

Utilizar Retrofit + OkHttp.

---

## ERRORES

No ocultar excepciones.

Utilizar un mecanismo consistente de resultado/error.

---

# IMPLEMENTACIÓN INCREMENTAL

NO implementar múltiples fases grandes simultáneamente.

Antes de cada fase:

1. revisar dependencias
2. explicar qué se implementará
3. implementar
4. compilar
5. corregir errores
6. probar
7. documentar resultado

---

# CONTROL DE CAMBIOS

No modificar archivos fuera del alcance de la fase actual.

No eliminar código antiguo sin autorización.

No modificar el proyecto original salvo que explícitamente se indique.

---

# CRITERIOS DE ACEPTACIÓN

Cada fase debe cumplir:

- compila
- no introduce errores
- mantiene las reglas de negocio
- respeta arquitectura
- no bloquea Main Thread
- tiene pruebas apropiadas

---

# FINAL

Al terminar cada fase generar:

docs/IMPLEMENTACION_FASE_[N].md

Incluyendo:

- qué se implementó
- archivos creados
- archivos modificados
- decisiones tomadas
- problemas encontrados
- pruebas realizadas
- resultado
- pendientes

NO avanzar automáticamente a la siguiente fase sin validar
el resultado de la fase actual.