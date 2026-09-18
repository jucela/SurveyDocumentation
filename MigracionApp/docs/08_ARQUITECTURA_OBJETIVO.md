# 08 - ARQUITECTURA OBJETIVO

> **Fecha:** 2026-08-30
> **Estado:** EN REVISION
> **Clasificacion:** HECHO (analisis tecnico del proyecto real)

---

# 1. RESUMEN EJECUTIVO

## Problema

La aplicacion ENAHO2026 es una encuesta estructurada con 149 fragments, 39 fragments con navegacion condicional, 110+ toggles de visibilidad, y 40+ metodos de limpieza de datos. La logica de encuesta (condiciones, saltos, filtros) esta dispersa en 39+ archivos Java, acoplada directamente a los Fragments.

## Solucion

**MVVM + Survey Flow Engine** - Un patron de presentacion MVVM combinado con un motor de reglas de encuesta que centraliza la logica de navegacion, visibilidad y limpieza.

## Componentes Clave

| Componente | Responsabilidad |
|------------|-----------------|
| **MVVM** | Patron de presentacion (Fragment + ViewModel + UiState) |
| **SurveyFlowEngine** | Fachada/orquestador que evalua reglas de navegacion, visibilidad, limpieza |
| **ConditionEvaluator** | Evalua condiciones booleanas puras |
| **NavigationResolver** | Determina siguiente destino logico |
| **VisibilityResolver** | Determina preguntas visibles |
| **CleanupManager** | Determina QUE datos deben limpiarse (retorna plan, NO ejecuta) |
| **ChapterResolver** | Determina capitulos aplicables |
| **SurveyGraph** | Grafo de navegacion declarativo (estructura de datos) |

## Decisiones Principales

| Decision | Eleccion | Justificacion |
|----------|----------|---------------|
| Patron de presentacion | MVVM | Estado predecible de encuesta, conocido, testeable |
| Logica de encuesta | SurveyFlowEngine | Centraliza 100+ condiciones, testeable sin Android |
| Persistencia | Room | 137 tablas, 29 DAOs |
| Red | Retrofit + OkHttp | Estandar, type-safe |
| DI | Hilt | 29 DAOs, compile-time |
| Concurrency | Coroutines + StateFlow | Evita ANR, lifecycle-aware |
| UI | XML + ViewBinding | Restriccion del proyecto |

---

# 2. CARACTERISTICAS DE LA APLICACION

## 2.1 Metricas del Proyecto

| Metrica | Valor |
|---------|-------|
| Fragments totales | 382 archivos, ~149 en encuesta |
| getPaginaSiguiente() overrides | 39 fragments |
| getPaginaAnterior() overrides | 31 fragments |
| setVisibility() calls | 110+ locations |
| setRelevancia() calls | 100+ locations |
| Metodos limpiar*() | 40+ |
| Dependencias cross-chapter | 40% de fragments ENAHO01A |
| Patrones recurrentes identificados | 5 patrones |
| Condiciones unicas | 100+ regex + equals |
| DAOs | 29 |
| Entities | 185 |
| Services | 21 |
| Tablas SQLite | 137 |

## 2.2 Complejidad de la Encuesta

### Condiciones

Las condiciones son **diversas**:
- **Value-matching** (~60%): .matches("[regex]"), .equals("value")
- **Age-based** (~15%): edad >= 3, edad >= 14
- **Multi-variable** (~15%): Combinaciones de 5+ campos
- **Subsystem mode** (~10%): aplicarSubsistemaReentrevista

### Saltos

**39 fragments** override getPaginaSiguiente() con navegacion condicional:
- Saltos hacia adelante: ~25
- Saltos hacia atras: ~15
- Llamadas nextFragment(): ~40
- Avance relativo: ~20
- Retroceso relativo: ~20

### Grafo de Navegacion

Cap500 solo tiene **30+ edges** en el grafo de navegacion:
- P537 puede saltar a: CAP500_9, CAP500_10, CAP500_11, CAP500_13
- P544 puede saltar a: CAP500_9, CAP500_8, CAP500_7, CAP500_6, CAP500_5
- P545 puede saltar a: CAP500_1, CAP500_10, CAP500_9

### Limpieza de Datos

**40+ metodos** limpiar*() con composicion:
- limpiar530_555 = limpiar530_536 + limpiar537_543 + limpiar544 + limpiar545_555
- Rangos jerarquicos
- Cascada entre tablas

### Dependencias Cross-Chapter

- Cap200 (Personas) a Cap300 (Educacion): P212
- Cap200 (Personas) a Cap400 (Salud): P213
- Cap200 (Personas) a Cap500 (Empleo): P214
- COVID mode a Todos los capitulos
- Reentrevista mode a Skip fragments

## 2.3 Problemas Identificados

| Problema | Impacto |
|----------|---------|
| 149 fragments creados al inicio | Memoria innecesaria |
| Navegacion condicional dispersa | 39+ archivos |
| Logica acoplada a Fragments | Dificil de mantener |
| Sin indicador de progreso | Usuario no sabe cuanto falta |
| State no se persiste en rotation | Perdida de estado |
| Cross-chapter via Activity fields | Perdida en process restart |

---

# 3. EVALUACION DE TECNOLOGIAS

## 3.1 Kotlin

| Aspecto | Evaluacion |
|---------|------------|
| Funcionalidad | Coroutines, null safety, data classes |
| Mantenibilidad | Sintaxis concisa, type inference |
| Compatibilidad | 100% interop con Java |
| **Decision** | **ADOPTAR** |

## 3.2 Room

| Aspecto | Evaluacion |
|---------|------------|
| Funcionalidad | ORM con anotaciones, migrations |
| Mantenibilidad | Menos SQL manual |
| Migracion | Requiere Entity/DAO/Migration |
| **Decision** | **ADOPTAR** |

## 3.3 Retrofit + OkHttp

| Aspecto | Evaluacion |
|---------|------------|
| Funcionalidad | Type-safe HTTP, interceptors |
| Mantenibilidad | API interface declarativa |
| **Decision** | **ADOPTAR** |

## 3.4 Dagger Hilt

| Aspecto | Evaluacion |
|---------|------------|
| Funcionalidad | DI compile-time |
| Mantenibilidad | Boilerplate reducido |
| **Decision** | **ADOPTAR** |

## 3.5 Coroutines + Flow + StateFlow

| Aspecto | Evaluacion |
|---------|------------|
| Funcionalidad | Async, reactive, lifecycle-aware |
| Mantenibilidad | Structured concurrency |
| **Decision** | **ADOPTAR** |

## 3.6 Navigation Component

| Aspecto | Evaluacion |
|---------|------------|
| Funcionalidad | Graph-based navigation |
| Limitacion | ViewPager-based navigation actual |
| **Decision** | **PENDIENTE DE VALIDACION** en piloto |

## 3.7 XML + ViewBinding

| Aspecto | Evaluacion |
|---------|------------|
| Funcionalidad | Layouts declarativos |
| Restriccion | NO usar Compose |
| **Decision** | **ADOPTAR** |

## 3.8 RecyclerView

| Aspecto | Evaluacion |
|---------|------------|
| Funcionalidad | Lista eficiente, diffing |
| **Decision** | **ADOPTAR** |

---

# 4. EVALUACION DE PATRONES

## 4.1 Comparativa

| Criterio | MVVM | MVI | MVP |
|----------|------|-----|-----|
| Complejidad | Baja-Media | Media-Alta | Media |
| Mantenibilidad | Alta | Alta | Media |
| Testabilidad | Alta | Alta | Alta |
| Manejo de estado | Bueno | Excelente | Manual |
| Migracion incremental | Si | Dificil | Si |
| Curva aprendizaje | Baja | Media | Baja |
| **Total** | **26/30** | **22/30** | **18/30** |

## 4.2 Veredicto

**MVVM es el patron mas adecuado para esta aplicacion.**

---

# 5. EVALUACION DE MVVM

## 5.1 Analisis por Componente

| Componente | MVVM Maneja? | Notas |
|------------|--------------|-------|
| Capitulos | Si | ViewModel contiene estado |
| Preguntas | Si | UiState contiene respuestas |
| Condiciones | No | Requiere SurveyFlowEngine |
| Saltos | No | Requiere SurveyFlowEngine |
| Visibilidad | Si | Via UiState.visibleQuestions |
| Navegacion | Si | ViewModel consulta Engine |
| Estado | Si | StateFlow |
| Recuperacion | Si | Room DB |
| Rotacion | Si | ViewModel + StateFlow |
| Offline | Si | Room DB local |

## 5.2 Veredicto

**MVVM es SUFICIENTE con complemento de SurveyFlowEngine.**

---

# 6. EVALUACION DE MVI

## 6.1 Analisis

MVI proporciona flujo unidireccional: View a Intent a Model a State a View

### Ventajas
- Estado predecible
- Time-travel debugging
- Testable con funciones puras

### Desventajas
- Mas boilerplate que MVVM
- Complejidad adicional para encuestas con estado predecible
- Curva de aprendizaje para el equipo

## 6.2 Por que NO MVI para ENAHO

El estado de la encuesta es **predecible**: un campo tiene un valor. No hay estados impredecibles como en chat o redes sociales. MVVM + StateFlow ya proporciona:
- Flujo unidireccional ViewModel a StateFlow a Fragment
- Estado reactivo via StateFlow
- Testeabilidad via ViewModel

MVI agrega Intents sealed class, Reducer functions, y State unificado que no se justifican para 39 fragments con forms simples.

## 6.3 Veredicto

**MVI no aporta beneficios suficientes para justificar su complejidad adicional.**

---

# 7. EVALUACION DE OTRAS ALTERNATIVAS

| Alternativa | Ventaja | Contra | Veredicto |
|-------------|---------|--------|-----------|
| MVVM + UseCases | Simple | Reglas dispersas | No optimal |
| MVVM + Reglas Distribuidas | Simple | Sin centralizacion | No optimal |
| MVVM + State Machine | Formal | Transiciones complejas | Overkill |
| MVVM + Declarative | Mantenible | Riesgo DSL | Prometedor |

## 7.5 Veredicto

**La mejor alternativa es MVVM + Survey Flow Engine (data-driven).**

---

# 8. DECISION DEL PATRON DE PRESENTACION

## 8.1 Decision

**MVVM + Survey Flow Engine**

## 8.2 Justificacion

1. Resuelve el problema real: 39 fragments con navegacion condicional
2. Separacion correcta: UI NO contiene logica de encuesta
3. Migracion incremental: Capitulo por capitulo
4. Reutilizacion: 5 patrones recurrentes se generalizan
5. No es over-engineering: Complejidad justificada

## 8.3 Alternativas Descartadas

| Alternativa | Por que se descarto |
|-------------|---------------------|
| MVI | No aporta beneficios suficientes para estado predecible |
| MVP | Obsoleto |
| MVVM + UseCases | Reglas dispersas |
| MVVM + SM | Transiciones muy complejas |

---

# 9. EVALUACION DE CLEAN ARCHITECTURE

## 9.1 Capas Necesarias

| Capa | Necesaria | Justificacion |
|------|-----------|---------------|
| Presentation | Si | UI separada de logica |
| Domain | Si | SurveyFlowEngine, Models |
| Data | Si | Room, Retrofit, Repository |

## 9.2 Decision

**ADOPTAR Clean Architecture simplificada: 3 capas.**

---

# 10. ARQUITECTURA OBJETIVO

## 10.1 Diagrama de Capas

`
PRESENTATION: Fragment a ViewModel a UiState a StateFlow
     |
DOMAIN: SurveyFlowEngine, Models, Repository interfaces
     |
DATA: Room, Retrofit, RepositoryImpl
`

## 10.2 Flujo de Datos

`
Usuario interactua con Fragment
    a Fragment envia evento a ViewModel
    a ViewModel consulta SurveyFlowEngine
    a SurveyFlowEngine evalua reglas
    a SurveyFlowEngine retorna Decision
    a ViewModel actualiza UiState
    a StateFlow notifica a Fragment
    a Fragment renderiza nuevo estado
`

---

# 11. SURVEY FLOW ENGINE

## 11.1 Responsabilidad Principal

El Survey Flow Engine es una **fachada/orquestador** que evalua reglas de navegacion de encuesta. Responde: **Dado el estado actual de la encuesta, que debe ocurrir a continuacion?**

El Engine NO contiene logica propia. Orquesta los 6 componentes y combina sus resultados.

## 11.2 Composicion

`
SurveyFlowEngine (fachada/orquestador)
    ConditionEvaluator      # Evalua condiciones booleanas puras
    NavigationResolver      # Determina siguiente destino logico
    VisibilityResolver      # Determina preguntas visibles
    CleanupManager          # Determina QUE datos limpiarse (retorna plan)
    ChapterResolver         # Determina capitulos aplicables
    SurveyGraph             # Estructura de datos del grafo
`

## 11.3 Entradas y Salidas del Engine

`
ENTRADA:
    currentState: SurveyState
    action: SurveyAction (NEXT, BACK, ANSWER, JUMP)

SALIDA:
    navigation: NavigationDecision (target logico, cleanup plan)
    visibility: VisibilityDecision (preguntas visibles)
    state: SurveyState (nuevo estado)
`

## 11.4 Responsabilidades

| RESPONSABILIDADES DEL ENGINE | NO RESPONSABILIDADES |
|------------------------------|----------------------|
| Evaluar condiciones de navegacion | Renderizar UI |
| Determinar siguiente destino logico | Gestionar Fragments |
| Determinar preguntas visibles | Persistir datos |
| Determinar capitulos aplicables | Ejecutar red |
| Retornar plan de limpieza | Ejecutar limpieza de datos |
| Gestionar saltos | Calcular incentivos |
| Gestionar retroceso | Generar reportes |
| Gestionar cambio de capitulo | Manejar GPS |
| Mantener grafo de navegacion | Validar formularios |
| Evaluar reglas de relevancia | Manejar lifecycle Android |

## 11.5 Independencia de Android

**El Survey Flow Engine NO conoce Android.** Ninguno de sus 6 componentes conoce Fragment, Activity, View, Context, Navigation Component, ni ViewPager.

| Componente | Conoce Android? | Justificacion |
|------------|-----------------|---------------|
| ConditionEvaluator | NO | Condiciones booleanas puras |
| NavigationResolver | NO | Destino basado en reglas |
| VisibilityResolver | NO | Visibilidad basado en reglas |
| CleanupManager | NO | Determina que limpiar, NO ejecuta |
| ChapterResolver | NO | Capitulos aplicables |
| SurveyGraph | NO | Estructura de datos |

## 11.6 Pruebas

El Engine es testeable SIN Android. Los 6 componentes se testean individualmente con unit tests.

---

# 12. GESTION DEL ESTADO DE LA ENCUESTA

## 12.1 Representacion

`kotlin
data class SurveyState(
    val currentChapter: Int,
    val currentFragment: String,
    val answers: Map<String, String>,
    val currentMember: MemberInfo?,
    val subsystem: Subsystem
)
`

**NOTA:** Se usa Map<String, String> para compatibilidad con la DB actual donde todos los campos son VARCHAR. La conversion a tipos se hace en el ViewModel/UI, NO en SurveyState.

## 12.2 Tipos de Estado

| Tipo | Ejemplo | En memoria? | Persistir? | Calcular? | Quien |
|------|---------|-------------|------------|-----------|-------|
| UI temporal | Scroll position | Si | No | No | Fragment |
| Estado encuesta | currentChapter, currentFragment | Si | Si | No | ViewModel + SharedPreferences |
| Respuestas | P101="1", P537="01,07" | Si | Si | No | ViewModel + Room DB |
| Progreso | 45% completado | No | No | Si | Calculado desde answers |
| Navegacion | Pila de fragments | Si | No | No | ViewPager |
| Sincronizacion | lastSync | No | Si | No | Repository + Room |
| Derivado | visibleQuestions, applicableChapters | No | No | Si | SurveyFlowEngine |

## 12.3 Responsabilidades

| Componente | Responsabilidad |
|------------|-----------------|
| Fragment | Estado UI temporal (scroll, focus) |
| ViewModel | Estado encuesta en memory via StateFlow |
| SharedPreferences | Posicion actual (chapter, fragment, member) |
| Room DB | Todas las respuestas |
| SurveyFlowEngine | Estado derivado (visible, applicable) |
| Repository | Orquestar carga/guardado |

## 12.4 Flujo de Recuperacion

`
1. App se destruye (rotacion, process death)
2. ViewModel se destruye (in-memory state lost)
3. Room DB mantiene todas las respuestas
4. SharedPreferences mantiene currentFragment, currentMember
5. Al recrear:
    a. Cargar answers desde Room
    b. Cargar currentFragment desde SharedPreferences
    c. Reconstruir SurveyState
    d. Engine recalcula visibleQuestions, applicableChapters
    e. Fragment renderiza estado
`

---

# 13. GESTION DE CONDICIONES

## 13.1 Tipos de Condiciones

| Tipo | Ejemplo | Frecuencia |
|------|---------|------------|
| Value-matching | P537.contains("07") | 60% |
| Age-based | edad >= 3 | 15% |
| Multi-variable | P507 in ["1","2"] && P517 != null | 15% |
| Subsystem mode | aplicarSubsistemaReentrevista | 10% |

## 13.2 Evaluacion

Las condiciones se evaluan de forma declarativa via ConditionEvaluator. Cada condicion es un objeto de dominio (data class), NO codigo imperativo.

---

# 14. GESTION DE SALTOS

## 14.1 Transition Rules

`kotlin
data class TransitionRule(
    val from: String,
    val conditions: List<Condition>,
    val cleanup: List<CleanupAction>? = null,
    val target: String,
    val priority: Int = 0,
    val isDefault: Boolean = false
)
`

## 14.2 Reglas de Precedencia y Resolucion de Conflictos

| Regla | Descripcion |
|-------|-------------|
| Primera coincidencia | La primera regla cuyas condiciones se cumplen es la ganadora |
| Prioridad | Mayor prioridad se evalua primero (sortedByDescending) |
| Empate | Se usa la regla de mayor prioridad |
| Default | Si ninguna condicion coincide, se usa la regla con isDefault=true |
| Sin default | Si no hay default, se navega secuencialmente |
| Destino invalido | Error de validacion en inicializacion del grafo |
| Regla duplicada | Error de validacion en inicializacion del grafo |

## 14.3 Evaluacion Determinista

`kotlin
fun evaluate(from: String, answers: Map<String, String>): TransitionResult {
    val rules = graph.getRules(from).sortedByDescending { it.priority }
    
    // 1. Evaluar reglas con condiciones
    for (rule in rules.filter { !it.isDefault }) {
        if (rule.conditions.all { conditionEvaluator.evaluate(it, context) }) {
            return TransitionResult.Jump(rule.target, rule.cleanup)
        }
    }
    
    // 2. Si ninguna coincide, usar default
    val default = rules.firstOrNull { it.isDefault }
    if (default != null) {
        return TransitionResult.Sequential(default.cleanup)
    }
    
    // 3. Si no hay default, navegar secuencialmente
    return TransitionResult.Sequential(null)
}
`

## 14.4 Validacion del Grafo

`kotlin
fun validateGraph(graph: SurveyGraph): List<GraphError> {
    val errors = mutableListOf<GraphError>()
    // Detectar destinos inexistentes
    // Detectar ciclos infinitos
    // Detectar reglas duplicadas (mismo from + mismas condiciones)
    // Detectar multiples defaults para el mismo nodo
    // Detectar reglas inalcanzables
    return errors
}
`

## 14.5 Grafo de Navegacion

El grafo se define como lista de TransitionRules en SurveyGraph. Se valida en inicializacion.

---

# 15. GESTION DE PREGUNTAS DINAMICAS

## 15.1 Visibility Rules

`kotlin
data class VisibilityRule(
    val questionId: String,
    val condition: Condition,
    val visible: Boolean = true
)
`

## 15.2 Fragment Ocultos

Los fragments se ocultan via SurveyFlowEngine.shouldSkipFragment().

---

# 16. GESTION DE CAPITULOS

## 16.1 Chapter Rules

`kotlin
data class ChapterRule(
    val chapter: Int,
    val condition: Condition,
    val applicable: Boolean = true
)
`

## 16.2 Ejemplo

- Cap300: solo si person.p212 == "1" (age >= 3)
- Cap500: solo si person.p214 == "1" (age >= 14)

---

# 17. GESTION DE NAVEGACION

## 17.1 Separacion Logica vs Android

| Capa | Responsabilidad | Conoce Android? |
|------|-----------------|-----------------|
| SurveyFlowEngine | Determinar destino LOGICO ("CAP500_9") | NO |
| NavigationMapper | Convertir destino logico a ordinal ViewPager | SI |
| ViewPager | Mostrar Fragment correspondiente | SI |

## 17.2 Flujo

`
1. Fragment llama a viewModel.onNext()
2. ViewModel consulta engine.getNextDestination(state)
3. Engine retorna: "El destino es CAP500_9"
4. ViewModel consulta navigationMapper.toOrdinal("CAP500_9")
5. Mapper retorna: ordinal 52
6. ViewModel ejecuta: viewPager.setCurrentItem(52, false)
`

## 17.3 Tipos de Navegacion

| Tipo | Descripcion | Ejemplo |
|------|-------------|---------|
| SEQUENTIAL | Siguiente secuencial | P101 a P102 |
| SKIP | Saltar a especifico | P537 a CAP500_9 |
| BLOCK | Bloquear | Validacion fallida |
| CHAPTER_CHANGE | Cambiar capitulo | Fin Cap500 a Inicio Cap01B |

---

# 18. EVALUACION DEL FLUJO DE ENCUESTA

## 18.1 Flujo Actual vs Propuesto

| Aspecto | Actual | Propuesto |
|---------|--------|-----------|
| Logica | Dispersa en 39+ archivos | Centralizada en Engine |
| Mantenibilidad | Dificil | Facil |
| Testabilidad | Dificil (Android) | Facil (unit tests) |
| Trazabilidad | Dificil | Facil |

---

# 19. ESTRATEGIA DE VISUALIZACION

## 19.1 Decision

**Mantener la UI actual durante el piloto Cap500 y evaluar posteriormente.**

La UI NO esta obligada a reproducir la estructura actual de Fragment. Tampoco se impone un rediseño completo ahora. La decision final se basara en datos del piloto.

## 19.2 Que Evaluar en el Piloto

| Aspecto | Metrica |
|---------|---------|
| Velocidad de captura | Tiempo promedio por capitulo |
| Errores de navegacion | Cantidad de saltos incorrectos |
| Correccion de respuestas | Tiempo para corregir una respuesta |
| Satisfaccion del encuestador | Feedback cualitativo |
| Fragment ocultos | Se navega correctamente? |
| Preguntas condicionadas | Se muestran/ocultan correctamente? |

## 19.3 Opcion A: Mantener Estructura Actual

**Pros:** Menos riesgo, migracion mas rapida
**Contras:** Puede no ser la mejor UX

## 19.4 Opcion B: Rediseñar UI

**Pros:** Mejor UX potencialmente
**Contras:** Alto riesgo, mas tiempo

## 19.5 Recomendacion

**Evaluar en piloto.** Si la UI actual funciona bien, mantener. Si hay problemas significativos, rediseñar incrementalmente.

---

# 20. PERSISTENCIA

## 20.1 Room

- 137 tablas a Room @Entity
- 29 DAOs a Room @Dao
- 0 indices a Room @Index
- 100+ inyeccion SQL a Room compile-time
- Sin migraciones a Room Migration API

---

# 21. RED

## 21.1 Retrofit + OkHttp

| Aspecto | Actual | Propuesto |
|---------|--------|-----------|
| HTTP Client | EnahoHttpClient | Retrofit + OkHttp |
| Auth | Basic Auth hardcoded | AuthInterceptor |
| SSL | MySSLSocketFactory | Certificado valido |

---

# 22. INYECCION DE DEPENDENCIAS

## 22.1 Hilt

| Modulo | Responsabilidad |
|--------|-----------------|
| DatabaseModule | Room database, DAOs |
| NetworkModule | Retrofit, OkHttp |
| RepositoryModule | Repository implementations |
| SurveyModule | SurveyFlowEngine + 6 componentes |

---

# 23. CONCURRENCIA

## 23.1 Coroutines

| Uso | Dispatcher |
|-----|------------|
| Room queries | Dispatchers.IO |
| Retrofit calls | Dispatchers.IO |
| Calculos | Dispatchers.Default |
| UI updates | Dispatchers.Main |

---

# 24. MODELOS

| Tipo | Uso | Ejemplo |
|------|-----|---------|
| Domain Model | Logica de negocio | Vivienda, Hogar |
| Data Entity | Room persistence | ViviendaEntity |
| DTO | Network transfer | GpsData |
| UI Model | Presentation | EncuestaUiState |

---

# 25. MAPA ACTUAL A NUEVO

| Actual | Nuevo | Accion |
|--------|-------|--------|
| DAO | Room @Dao | Reemplazar |
| Service | UseCase + Repository | Reemplazar |
| HTTP Client | Retrofit + OkHttp | Reemplazar |
| Model | Domain / Entity / DTO | Separar |
| Activity | Fragment + ViewModel | Migrar |
| Controller | ViewModel + Engine | Reemplazar |
| Logica saltos | SurveyFlowEngine.NavigationResolver | Centralizar |
| Logica visibilidad | SurveyFlowEngine.VisibilityResolver | Centralizar |
| Logica limpieza | CleanupManager (retorna plan) | Centralizar |
| AsyncTask | Coroutines | Reemplazar |
| ProgressDialog | ProgressBar + StateFlow | Reemplazar |
| AppContext | Dagger Hilt | Reemplazar |
| MySSLSocketFactory | Certificado valido | Reemplazar |
| SQLite v56 | Room | Reemplazar |
| EnahoHttpClient | Retrofit | Reemplazar |

---

# 26. ESTRUCTURA DE PAQUETES

`
gob.inei.enaho2026/
    app/
        EnahoApplication.kt
        di/
            DatabaseModule.kt
            NetworkModule.kt
            RepositoryModule.kt
            SurveyModule.kt
    presentation/
        login/
        encuesta/
        export/
        import/
    domain/
        survey/
            SurveyFlowEngine.kt
            ConditionEvaluator.kt
            NavigationResolver.kt
            VisibilityResolver.kt
            CleanupManager.kt
            ChapterResolver.kt
            SurveyGraph.kt
            model/
        model/
        repository/
        usecase/
            ExecuteCleanupUseCase.kt
    data/
        local/
            EnahoDatabase.kt
            entity/
            dao/
            migration/
        remote/
            api/
            interceptor/
        repository/
        mapper/
`

---

# 27. DIAGRAMAS

## 27.1 Arquitectura General

`
PRESENTATION: Fragment a ViewModel a UiState a StateFlow
     |
DOMAIN: SurveyFlowEngine (6 componentes) + Models + Repositories
     |
DATA: Room + Retrofit + RepositoryImpl
`

## 27.2 SurveyFlowEngine

`
SurveyFlowEngine (fachada/orquestador)
    ConditionEvaluator  <-  evalua condiciones booleanas
    NavigationResolver  <-  determina destino logico
    VisibilityResolver  <-  determina visibilidad
    CleanupManager      <-  determina que limpiar (retorna plan)
    ChapterResolver     <-  determina capitulos
    SurveyGraph         <-  estructura de datos del grafo
`

## 27.3 Separacion Navegacion Logica vs Android

`
SurveyFlowEngine (Domain)
    "El destino logico es CAP500_9"
            |
            v
NavigationMapper (Presentation)
    Convierte "CAP500_9" a ordinal 52
            |
            v
ViewPager (Android)
    Muestra el Fragment en posicion 52
`

---

# 28. MATRIZ DE DECISIONES

| Tecnologia | Decision | Justificacion |
|------------|----------|---------------|
| Kotlin | ADOPTAR | Null safety, coroutines |
| Clean Architecture | ADOPTAR | Separacion clara |
| MVVM | ADOPTAR | Estado predecible de encuesta |
| Survey Flow Engine | ADOPTAR | Centraliza 100+ condiciones |
| Room | ADOPTAR | 137 tablas, 29 DAOs |
| Hilt | ADOPTAR | DI compile-time |
| Coroutines | ADOPTAR | Evita ANR |
| StateFlow | ADOPTAR | Lifecycle-aware |
| Retrofit | ADOPTAR | Type-safe HTTP |
| XML + ViewBinding | ADOPTAR | Restriccion proyecto |
| RecyclerView | ADOPTAR | Lista eficiente |
| Navigation Component | PENDIENTE | Evaluar en piloto |

---

# 29. RIESGOS ARQUITECTONICOS

| Riesgo | Probabilidad | Impacto | Mitigacion |
|--------|--------------|---------|------------|
| Engine God Object | Media | Alto | Dividir en 6 componentes |
| Complejidad Engine | Media | Medio | Tests unitarios |
| Migracion larga | Alta | Medio | Incremental, piloto |
| Room migration falla | Media | Alto | Tests, backup |
| Retrofit incompatible | Baja | Medio | Validar contra API |
| CleanupActions incompletas | Media | Alto | Mapeo exhaustivo de 40+ metodos |

---

# 30. ESTRATEGIA DE MIGRACION

## 30.1 Fases

### Fase 1: Crear Engine (Sin Modificar Fragments)
Crear SurveyFlowEngine + 6 componentes + tests. NO modificar fragments existentes.

### Fase 2: Piloto Cap500
Migrar Cap500 como piloto. Validar navegacion, limpieza, visibilidad.

### Fase 3: Refinar
Ajustar reglas, optimizar, corregir edge cases.

### Fase 4: Migrar Resto
Migrar progresivamente: Cap300, Cap400, Cap01B, Cap02, Cap04.

### Fase 5: Room + Retrofit
Migrar paralelamente: Entities, DAOs, Migrations, API.

### Fase 6: UI Refinamiento (Opcional)
Evaluar si se necesita rediseño de UI basado en datos del piloto.

## 30.2 Estrategia de Validacion

### Comparacion Engine vs Codigo Actual

`
1. Extraer reglas de 39 fragments con getPaginaSiguiente()
2. Convertir a TransitionRules
3. Ejecutar Engine con mismas entradas
4. Comparar salidas con codigo actual
5. Si difieren -> ajustar reglas del Engine
`

### Shadow Engine

`kotlin
class ShadowEngine(private val legacyFragments: List<FragmentForm>) {
    fun getNextDestination(current: Int): Int {
        val fragment = legacyFragments[current]
        return fragment.getPaginaSiguiente() ?: (current + 1)
    }
}
`

### Piloto Cap500 con Dual Execution

`
1. Migrar solo Cap500 (fragments mas complejos)
2. Mantener fragments actuales como fallback
3. Ejecutar ambos en paralelo
4. Comparar resultados
5. Si Engine falla -> usar codigo actual
`

---

# 31. DECISIONES PENDIENTES

| # | Decision | Pendiente de |
|---|----------|-------------|
| 1 | Navigation Component con ViewPager? | Compatibilidad en piloto |
| 2 | UI actual vs rediseño? | Feedback del encuestador en piloto |
| 3 | Fragment-per-question vs agrupacion? | Analisis de UX en piloto |
| 4 | Indicador de progreso? | Implementacion y validacion |
| 5 | Tiempo estimado de migracion? | Datos del piloto |

---

# HALLAZGOS PRINCIPALES

1. **MVVM es suficiente** con SurveyFlowEngine (estado predecible)
2. **SurveyFlowEngine resuelve** 100+ condiciones dispersas
3. **Engine data-driven** = reglas como objetos de dominio (data classes), NO como configuracion en DB
4. **Engine dividido en 6 componentes** (sin God Object)
5. **Engine independiente de Android** (domain/, testeable sin Android)
6. **CleanupManager retorna plan, NO ejecuta** (UseCase ejecuta)
7. **Room para persistencia** (137 tablas)
8. **Retrofit + OkHttp** para red
9. **Coroutines + StateFlow** para concurrencia
10. **Migracion incremental** (6 fases, piloto Cap500)
11. **UI se evalua en piloto** (no se impone rediseño completo)

---

*Documento generado como parte de la Etapa 8 - Arquitectura Objetivo*
*Proximo paso: Etapa 9 - Fase Piloto*
