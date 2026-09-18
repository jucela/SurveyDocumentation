# 09 - AUDITORIA PRE-IMPLEMENTACION

> **Fecha:** 2026-08-30
> **Estado:** EN REVISION
> **Tipo:** Auditoria, no implementacion
> **Resultado:** LISTO PARA IMPLEMENTACIÓN GENERAL = NO | LISTO PARA PILOTO = REQUIERE 22 CRITERIOS

---

# 1. RESUMEN EJECUTIVO

La auditoria pre-implementacion reviso la trazabilidad entre el codigo actual, la documentacion generada en `docs/01` a `docs/08`, y la arquitectura objetivo definida para la migracion.

La conclusion es que **NO esta listo para iniciar implementacion general**. La arquitectura objetivo es tecnicamente adecuada, pero todavia existen brechas de trazabilidad que pueden provocar perdida de reglas de negocio, perdida de datos o comportamiento funcional distinto durante la migracion.

La implementacion solo debe comenzar despues de resolver los bloqueadores de trazabilidad minima para el piloto, especialmente en Cap500, visibilidad dinamica, limpieza cross-chapter, estado recuperable y validacion Legacy vs New Engine.

## 1.1 Fuentes Revisadas

| Fuente | Uso en auditoria | Estado |
|---|---|---|
| `docs/01_INVENTARIO_TECNICO.md` | Inventario tecnico y plataforma actual | COMPLETO CON OBSERVACIONES |
| `docs/02_ARQUITECTURA_ACTUAL.md` | Arquitectura real y acoplamientos | COMPLETO CON OBSERVACIONES |
| `docs/03_FLUJOS_FUNCIONALES.md` | Flujos funcionales principales | COMPLETO CON OBSERVACIONES |
| `docs/04_BASE_DATOS.md` | Persistencia, tablas, consultas, riesgos DB | COMPLETO CON OBSERVACIONES |
| `docs/05_API_Y_RED.md` | Endpoints, clientes HTTP, seguridad de red | COMPLETO CON OBSERVACIONES |
| `docs/06_REGLAS_NEGOCIO.md` | Reglas criticas y trazabilidad inicial | COMPLETO CON OBSERVACIONES |
| `docs/07_RIESGOS_Y_ANR.md` | Riesgos tecnicos, ANR, seguridad | COMPLETO |
| `docs/08_ARQUITECTURA_OBJETIVO.md` | MVVM + SurveyFlowEngine | COMPLETO CON OBSERVACIONES |
| Codigo fuente Java/XML | Verificacion directa | COMPLETO CON OBSERVACIONES |

## 1.2 Verificaciones Contra Codigo

| Evidencia | Resultado |
|---|---|
| `getPaginaSiguiente()` | 42 referencias encontradas, con 39+ casos funcionales relevantes |
| `getPaginaAnterior()` | 33 referencias encontradas |
| `setRelevancia` / visibilidad | 100+ referencias encontradas |
| `limpiar*()` | 100+ referencias encontradas incluyendo metodos UI, service y DAO |
| `AppContext.getServices()` | 100+ referencias encontradas |
| `SyncHttpClient` | 11 instancias encontradas en `EnahoWebService.java` |
| Room annotations | 0 ocurrencias de `@Entity`, `@Dao`, `@Database`, `RoomDatabase` |
| Endpoints activos | 10 endpoints confirmados en `EnahoWebService.java` |
| SSL legacy | `MySSLSocketFactory` con `TrustManager` sin validacion confirmado |
| Concurrencia legacy | `AsyncTask`, `ProgressDialog`, `Thread`, `Handler` confirmados |
| Lifecycle | `configChanges`, `allowBackup=true`, `InfoGPS exported=true` confirmados en Manifest |

---

# 2. ESTADO GENERAL DE PREPARACION

| Area | Estado | Motivo |
|---|---|---|
| Arquitectura objetivo | COMPLETO CON OBSERVACIONES | MVVM + SurveyFlowEngine esta definido, pero faltan catalogos exhaustivos de reglas |
| SurveyFlowEngine | COMPLETO CON OBSERVACIONES | Componentes correctos, falta inventario declarativo completo de reglas |
| Funcionalidades criticas | COMPLETO CON OBSERVACIONES | Flujos principales identificados, faltan reglas secundarias y modulos auxiliares |
| Reglas de negocio | PENDIENTE | Hay reglas en codigo no documentadas individualmente |
| Condiciones y saltos | PENDIENTE | 42 referencias requieren mapeo a `TransitionRule` |
| Visibilidad dinamica | BLOQUEANTE | 100+ reglas deben convertirse a `VisibilityRule` antes de migracion segura |
| Limpieza | BLOQUEANTE | Las cascadas cross-chapter no tienen catalogo completo de `CleanupAction` |
| Persistencia Room | BLOQUEANTE | No existen entidades Room, DAOs, indices ni migraciones |
| Consultas | BLOQUEANTE | 40+ consultas complejas y 100+ SQL dinamicos requieren mapeo |
| Endpoints | COMPLETO CON OBSERVACIONES | Endpoints conocidos, falta `ApiResponse<T>` y manejo centralizado |
| Red y seguridad | BLOQUEANTE | SSL legacy inseguro y credenciales hardcoded |
| Presentation | PENDIENTE | Estrategia general definida, falta mapa pantalla actual a pantalla nueva |
| UX | PENDIENTE NO BLOQUEANTE | Decision aplazada al piloto, aceptable si se mantiene UI actual inicialmente |
| Dependencias ocultas | BLOQUEANTE | Service Locator global y referencias Activity/Fragment deben aislarse |
| Ciclo de vida | BLOQUEANTE | Estado actual no se recupera completamente en process death |
| Concurrencia | BLOQUEANTE | Riesgos ANR confirmados en red, DB, archivos y GPS |
| Piloto | PENDIENTE | Estrategia existe, falta definir datos de comparacion y criterios de aceptacion |

---

# 3. FUNCIONALIDADES FALTANTES

| Funcionalidad | Actual | Documentada | Arquitectura nueva | Migracion | Estado |
|---|---|---|---|---|---|
| Login offline | `LoginActivity`, `Inicializacion`, `PersonalService` | Si | `presentation/login` + Repository | Reemplazar AsyncTask por coroutine | COMPLETO CON OBSERVACIONES |
| Seleccion vivienda | `MarcoFragment`, `HogarService.findReporteHome` | Si | Presentation + Repository | Migrar incrementalmente | COMPLETO |
| Apertura vivienda/hogar | `MarcoFragment`, `ViviendaService`, `HogarService` | Si | UseCases + Room | Requiere mapa de validaciones | COMPLETO CON OBSERVACIONES |
| Seleccion informante/persona | Cap200/Cap200_B + Activity state | Parcial | `SurveyState.currentMember` | Persistir miembro actual | PENDIENTE |
| Captura capitulos | 149 fragments aprox. | Si, alto nivel | Fragment/ViewModel/UiState | Piloto Cap500 primero | COMPLETO CON OBSERVACIONES |
| Validaciones de campo | `FragmentForm`, fragments y controllers | Parcial | ViewModel + Domain validators | Catalogar validaciones inline | PENDIENTE |
| Calculos | `EnahoUtil`, consistencias, reportes | Si, principales | Domain UseCases | Migrar fuera de UI | COMPLETO CON OBSERVACIONES |
| Navegacion condicional | `getPaginaSiguiente`, `getPaginaAnterior`, `nextFragment` | Parcial | `NavigationResolver` + `SurveyGraph` | Convertir a `TransitionRule` | BLOQUEANTE |
| Filtros y relevancia | `setRelevancia`, `setRelevanciaFija` | Parcial | `VisibilityResolver` | Convertir a `VisibilityRule` | BLOQUEANTE |
| Limpieza de datos | `limpiar*`, DELETE/UPDATE | Parcial | `CleanupManager` + `ExecuteCleanupUseCase` | Catalogar `CleanupAction` | BLOQUEANTE |
| GPS tracking | `InfoGPS`, `EnahoWebService`, `ViviendaService` | Si | Repository/DataSource/Worker o service moderno | Redisenar lifecycle | BLOQUEANTE |
| GPS offline queue | `UBICACIONUSUARIO`, `invokeGpsUploadListPoints` | Si | Repository + Room + WorkManager/worker equivalente | Batch/retry pendiente | PENDIENTE |
| Exportacion | `ExportacionFragment`, `Exportacion`, XML/ZIP | Si | Presentation + UseCase + Repository | Migrar con transacciones y IO dispatcher | PENDIENTE |
| Importacion | `Importacion`, `XMLReader`, transacciones | Si | UseCase + Repository + Room migrations/import | Mantener exactitud XML | PENDIENTE |
| Envio servidor | `uploadMultiple_v2` | Si | Retrofit + Repository | Definir DTO/wrapper | COMPLETO CON OBSERVACIONES |
| Descarga backup | `descargaBackup_v2` | Si | Retrofit streaming + Repository | Definir manejo binario | COMPLETO CON OBSERVACIONES |
| Mensajes/rutas | `MensajesFragment`, `PlanRutaFragment`, endpoints legacy | Parcial | Feature modules + Repository | Catalogar prioridad | PENDIENTE |
| Manejo errores | Toast/Dialog disperso | Si, flujo general | Error model centralizado | Definir `Result`/errores | PENDIENTE |
| Trabajo offline | DB local + GPS queue | Parcial | Room + Repository | Explicitar limites offline | PENDIENTE |
| Operaciones auxiliares | Copia DB, admin, config, verificar apps | Parcial | Utilitarios/UseCases | Definir alcance de migracion | PENDIENTE |

---

# 4. REGLAS FALTANTES

Las reglas criticas de `docs/06_REGLAS_NEGOCIO.md` tienen trazabilidad inicial, pero la auditoria encontro reglas en codigo que no estan documentadas con granularidad suficiente para implementacion segura.

| Regla | Ubicacion actual | Evidencia | Nuevo componente | Accion | Estado |
|---|---|---|---|---|---|
| Campo obligatorio | `FragmentForm` y fragments | Validaciones base de formulario | ViewModel/Domain validator | Migrar validaciones base | COMPLETO CON OBSERVACIONES |
| GPS activo / SIM / datos | `EnahoUtil`, `AbstractVisitaController` | Validaciones previas de visita/login | PreConditionValidator o UseCase | Crear destino arquitectonico explicito | PENDIENTE |
| Auto-close 30 min | `Configuracion`, `EnahoDao`, `HogarDao` | `MINUTOS_PARA_CIERRE_VISITA` | Domain UseCase + Repository | Mantener exactitud | COMPLETO |
| RESFIN | `HogarDao` | Precedencia 5/7 sobre 1-4 | Domain UseCase | Unit tests obligatorios | COMPLETO |
| Capitulo por edad | `Cap200_BFragment.actualizar212_213_214` | P212/P213/P214 | `ChapterResolver` | Convertir a `ChapterRule` | COMPLETO |
| Cap200_B COVID visibility | `Cap200_BFragment:1789-1797` | Oculta P210/P211A/P211C/P211D | `VisibilityResolver` | Agregar a catalogo | FALTANTE |
| Cap300 relevancia cascada | `Cap300Fragment_*` | 50+ relevancias reportadas | `VisibilityResolver` | Catalogar reglas | FALTANTE |
| Cap400/Agua relevancia | `CapAguaFragment`, Cap400 fragments | Relevancia condicional | `VisibilityResolver` | Catalogar reglas | FALTANTE |
| Cap500 P537 saltos | `Cap500Fragment_007_P537` | Saltos a CAP500_9/10/11/13 y limpiezas | `NavigationResolver` + `CleanupManager` | Prioridad piloto | COMPLETO CON OBSERVACIONES |
| Cap500 P541 navegacion | `Cap500Fragment_009_P541` | `getPaginaSiguiente` propio | `NavigationResolver` | Documentar regla | FALTANTE |
| ENAHO02 navegacion | `Cap2300`, `Cap2500`, `Cap2800B` | `getPaginaSiguiente` propios | `NavigationResolver` | Mapear luego del piloto | PENDIENTE |
| Cap400 a Cap200_B reverse | Cap400 fragment reportado | Navegacion inversa cross-chapter | `SurveyGraph` | Incluir regla inversa | FALTANTE |
| Limpieza Cap500 -> ENAHO02 | `Cap500Fragment_002_P505`, servicios ENAHO02 | Limpia 2000-2700, otros, observacion | `CleanupManager` | Catalogo obligatorio | FALTANTE |
| Limpieza Cap500 -> ENAHO04 | `Cap500Fragment_003A_P516`, `Cap200Controller` | `deleteCalculosModulo`, Cap10-40 | `CleanupManager` | Catalogo obligatorio | FALTANTE |
| Export validacion previa | `ExportacionFragment`, `Exportacion` | Visitas, hogares, informante | ExportValidationUseCase | Migrar fuera de UI | COMPLETO CON OBSERVACIONES |
| GPS offline fallback | `EnahoWebService.invokeGpsUpload` | Guarda local si no hay red | GpsRepository | Validar cola y retries | COMPLETO CON OBSERVACIONES |

---

# 5. CONDICIONES Y SALTOS

## 5.1 Estado Actual

| Tipo | Evidencia | Estado |
|---|---|---|
| Saltos adelante | `getPaginaSiguiente()` en fragments y supervisor/cobertura | PENDIENTE |
| Saltos atras | `getPaginaAnterior()` en fragments | PENDIENTE |
| Saltos por ordinal | `FragmentoEnaho.*.ordinal()` | PENDIENTE |
| Saltos por Activity | `nextFragment(int)` en Activities | PENDIENTE |
| Condiciones por valor | `equals`, `matches`, `StringUtil.contieneOR` | PENDIENTE |
| Condiciones por edad | P212/P213/P214 | COMPLETO CON OBSERVACIONES |
| Condiciones por subsistema | `aplicarSubsistemaReentrevista`, `aplicarCuestionarioCovidReducido` | PENDIENTE |
| Condiciones cross-chapter | Cap200 afecta Cap300/400/500/02/04 | PENDIENTE |

## 5.2 Caso Verificado: Cap500 P537

`Cap500Fragment_007_P537.java` contiene reglas que combinan respuestas multiples, condiciones sobre P507/P517/P513T, navegacion condicional y limpieza inmediata. Esta regla requiere representacion compuesta:

| Caso | Actual | Nuevo componente | Estado |
|---|---|---|---|
| P537 contiene 01/02/03/04/05/06/10/11 | `return super.getPaginaSiguiente()` | Default/sequential rule | COMPLETO CON OBSERVACIONES |
| P537 contiene 07/08 | Limpia 538-540 y va a CAP500_9 | `TransitionRule` + `CleanupAction` | COMPLETO CON OBSERVACIONES |
| P537 contiene 09/12 + condiciones empleo | Limpia 538-543, 544, 545-555 segun caso | `TransitionRule` con prioridad | PENDIENTE |
| Ninguna condicion | `super.getPaginaSiguiente()` | Default sequential | COMPLETO |

## 5.3 Riesgo

Si los saltos se migran directamente a Navigation Component sin separar destino logico y ordinal, se reproducira el acoplamiento actual. La arquitectura objetivo corrige esto con `NavigationMapper`, pero el catalogo de reglas aun no existe.

---

# 6. VISIBILIDAD DINAMICA

La visibilidad dinamica es un bloqueador para implementacion general. La evidencia muestra mas de 100 usos de `setRelevancia` y `setRelevanciaFija`, pero solo una parte esta documentada individualmente.

| Grupo | Evidencia | Nuevo componente | Estado |
|---|---|---|---|
| Cap100 subsistema | `Cap100_1Fragment_P101` documentado | `VisibilityResolver` | COMPLETO CON OBSERVACIONES |
| Cap200_B reentrevista | `Cap200_BFragment:1800-1806` | `VisibilityResolver` | COMPLETO |
| Cap200_B COVID | `Cap200_BFragment:1789-1797` | `VisibilityResolver` | FALTANTE |
| Cap300 relevancia | `Cap300Fragment_*`, 50+ reglas reportadas | `VisibilityResolver` | FALTANTE |
| Cap400/Agua | `CapAguaFragment:177-351` | `VisibilityResolver` | FALTANTE |
| ENAHO04 | `Cap10Fragment_*`, relevancias multiples | `VisibilityResolver` | PENDIENTE |
| Visitas | `AbstractVisitaController`, subresultado/otro | Presentation + Domain validator | PENDIENTE |

**Conclusion:** Antes de migrar cualquier capitulo fuera del piloto debe existir un catalogo `VisibilityRule` por capitulo. Para el piloto Cap500, el catalogo puede limitarse a Cap500, pero debe ser exhaustivo dentro de ese capitulo.

---

# 7. LIMPIEZA

## 7.1 Separacion Requerida

La arquitectura objetivo define correctamente la separacion:

```text
SurveyFlowEngine
    -> CleanupPlan / CleanupDecision
    -> ExecuteCleanupUseCase
    -> Repository
    -> Room
```

Esta separacion es consistente y debe mantenerse. `CleanupManager` no debe ejecutar persistencia.

## 7.2 Hallazgos

| Limpieza | Ubicacion actual | Tipo | Nuevo componente | Estado |
|---|---|---|---|---|
| `limpiar538_540` | Cap500 P537 | Rango local Cap500 | `CleanupAction` | COMPLETO CON OBSERVACIONES |
| `limpiar538_543` | Cap500 P537 | Rango local Cap500 | `CleanupAction` | COMPLETO CON OBSERVACIONES |
| `limpiar544` | Cap500 P537 | Local Cap500 | `CleanupAction` | COMPLETO CON OBSERVACIONES |
| `limpiar545_555` | Cap500 P537 | Rango local Cap500 | `CleanupAction` | COMPLETO CON OBSERVACIONES |
| Eliminar miembro | `Cap200Controller.eliminar` | Cascada cross-chapter | `CleanupPlan` compuesto | BLOQUEANTE |
| ENAHO02 2000-2700 | `Enaho02Service.limpiar*` | Cross-chapter | `CleanupAction.DeleteRows` | BLOQUEANTE |
| ENAHO04 Cap10-40/calculos | `Enaho04Service`, `Cap200Controller` | Cross-chapter | `CleanupAction.DeleteRows` | BLOQUEANTE |
| Observaciones/Otros | ENAHO01A/02/04 services | Datos auxiliares | `CleanupAction.DeleteRows` | PENDIENTE |
| Soft delete visita | `HogarDao` | UPDATE activo=0 + reordenar | UseCase especifico | PENDIENTE |

## 7.3 Bloqueador

Las cascadas de limpieza pueden borrar datos de capitulos completos. Si se omite una accion o se ejecuta de mas, se producira perdida de datos. Por eso la limpieza es **BLOQUEANTE** antes de implementacion general.

---

# 8. AUDITORIA DEL SURVEYFLOWENGINE

| Componente | Responsabilidad definida | Evidencia actual que cubre | Entradas | Salidas | Estado |
|---|---|---|---|---|---|
| `SurveyFlowEngine` | Fachada/orquestador sin logica propia | Reglas dispersas en fragments | `SurveyState`, `SurveyAction` | `NavigationDecision`, `VisibilityDecision`, nuevo state | COMPLETO CON OBSERVACIONES |
| `ConditionEvaluator` | Evaluar condiciones puras | `equals`, `matches`, `contieneOR`, edad, subsistema | `answers`, `MemberInfo`, `Subsystem` | Boolean | COMPLETO CON OBSERVACIONES |
| `NavigationResolver` | Resolver destino logico | `getPaginaSiguiente`, `getPaginaAnterior` | Nodo actual + reglas | Destino logico | PENDIENTE |
| `VisibilityResolver` | Resolver preguntas/fragments visibles | `setRelevancia`, `setRelevanciaFija` | State + reglas | Lista/flags visibles | BLOQUEANTE |
| `CleanupManager` | Determinar plan de limpieza | `limpiar*`, DELETE/UPDATE | State + transicion | `CleanupPlan` | BLOQUEANTE |
| `ChapterResolver` | Capitulos aplicables | P212/P213/P214, subsistema | Miembro + hogar + edad | Capitulos aplicables | COMPLETO CON OBSERVACIONES |
| `SurveyGraph` | Grafo declarativo | Ordinales `FragmentoEnaho` + saltos | Reglas | Edges validados | PENDIENTE |

## 8.1 Independencia Android

La arquitectura objetivo mantiene al Engine independiente de Android. Esto es correcto. Las dependencias a `Fragment`, `Activity`, `ViewPager`, `Context`, Room o Retrofit deben vivir fuera del Engine.

| Responsabilidad | Requiere Android? | Donde debe vivir |
|---|---|---|
| Evaluar condicion P537 | No | Domain |
| Calcular destino logico CAP500_9 | No | Domain |
| Convertir CAP500_9 a ordinal | Si, por ViewPager actual | Presentation `NavigationMapper` |
| Ejecutar `setCurrentItem` | Si | Fragment/Activity |
| Borrar filas en DB | Si, persistencia | UseCase + Repository + Room |
| Mostrar/ocultar widgets | Si | Fragment renderizando UiState |

---

# 9. AUDITORIA DE SURVEYSTATE

La representacion `answers: Map<String, String>` es consistente con la base actual, donde las respuestas se almacenan principalmente como `VARCHAR`/texto. La conversion de tipos debe ser responsabilidad de UI/ViewModel/ConditionEvaluator segun corresponda.

| Estado | Actual | Arquitectura nueva | Estado |
|---|---|---|---|
| Capitulo actual | Activity/ViewPager/ordinal | `SurveyState.currentChapter` | COMPLETO CON OBSERVACIONES |
| Fragment actual | ViewPager current item | `SurveyState.currentFragment` logico | COMPLETO CON OBSERVACIONES |
| Miembro actual | Campos en Activity (`setMiembro`) | `SurveyState.currentMember` | PENDIENTE |
| Respuestas | SQLite + UI widgets | `answers: Map<String, String>` + Room | COMPLETO CON OBSERVACIONES |
| Visibilidad | Estado de widgets | Derivado por `VisibilityResolver` | PENDIENTE |
| Progreso | No consolidado | Derivado desde grafo/respuestas | PENDIENTE NO BLOQUEANTE |
| Navegacion | ViewPager ordinal + metodos | Destino logico + mapper | PENDIENTE |
| Sincronizacion | Flags DB/preferencias | Repository state | PENDIENTE |
| Estado temporal UI | Fragment/widgets | Fragment/UiState temporal | COMPLETO |

## 9.1 Riesgo

El estado actual depende de Activity fields, SharedPreferences y DB. Ante process death, las respuestas sobreviven en DB, pero el miembro actual, pagina logica y estado derivado pueden quedar inconsistentes. La migracion debe implementar reconstruccion deterministica desde Room + preferencias minimas.

---

# 10. REGLAS DE PRECEDENCIA

La arquitectura objetivo define precedencia deterministica: mayor prioridad primero, primera coincidencia gana, default si no coincide ninguna, y navegacion secuencial si no existe default.

| Caso | Definicion objetivo | Estado |
|---|---|---|
| Ninguna regla coincide | Usar `isDefault=true`; si no existe, secuencial | COMPLETO |
| Una regla coincide | Gana esa regla | COMPLETO |
| Varias reglas coinciden | Gana mayor `priority`; primera tras ordenar | COMPLETO CON OBSERVACIONES |
| Empate | Requiere orden estable/documentado por lista | PENDIENTE |
| Contradiccion | Error de validacion del grafo | COMPLETO CON OBSERVACIONES |
| Destino invalido | Error al inicializar `SurveyGraph` | COMPLETO |
| Reglas duplicadas | Error al inicializar `SurveyGraph` | COMPLETO |
| Regla inalcanzable | Warning/error de validacion | PENDIENTE |

**Observacion:** El empate por misma prioridad debe definirse con mas precision antes de implementar el motor. Recomendacion: prohibir reglas ambiguas con igual `from`, igual prioridad y condiciones solapadas salvo que una sea explicitamente default.

---

# 11. PERSISTENCIA

| Area | Actual | Room propuesto | Estado |
|---|---|---|---|
| Motor | SQLite nativo | Room sobre SQLite | PENDIENTE |
| ORM | `SQLiteDAO` por reflexion | `@Entity`, `@Dao`, `@Database` | BLOQUEANTE |
| Tablas | 137 documentadas, diferencias con upgrades | 137 entidades objetivo | BLOQUEANTE |
| Migraciones | XML raw `upgrade02` a `upgrade93` | `Migration` Room | BLOQUEANTE |
| Indices | 0 `CREATE INDEX` confirmados | `@Index` | BLOQUEANTE |
| Foreign keys | Parciales y `PRAGMA foreign_keys` no global | `@ForeignKey` | BLOQUEANTE |
| Transacciones | Inconsistentes | `@Transaction` / Room transaction | BLOQUEANTE |
| Consultas dinamicas | `rawQuery`, `execSQL`, concatenacion | `@Query` parametrizado | BLOQUEANTE |

## 11.1 Tablas con Observacion

| Tabla | Situacion | Estado |
|---|---|---|
| `MASCOTA` | Encontrada en upgrades/codigo, no suficientemente documentada | PENDIENTE |
| `ANEXO_MASCOTA` | Encontrada en upgrades/codigo | PENDIENTE |
| `UBICACIONUSUARIO` | Usada para GPS offline | PENDIENTE |
| `SUPERVISION_PARAMETROS_EVALUACION` | Upgrade/supervision | PENDIENTE |
| `SUPERVISION_REVISION_ITEM_HOGAR` | Upgrade/supervision | PENDIENTE |
| `CAMPO_LOG_TIEMPOS` | Referenciada, requiere trazabilidad de creacion | PENDIENTE |

---

# 12. CONSULTAS

| Consulta / Patron | Ubicacion | Nuevo destino | Estado |
|---|---|---|---|
| CRUD generico | `SQLiteDAO` | DAOs Room tipados | BLOQUEANTE |
| `ingresos02_10()` | `MarcoDAO` | DAO/UseCase de reportes | BLOQUEANTE |
| `findReporteHome()` | `HogarDao` | DAO Room + Repository | BLOQUEANTE |
| `indIngresosHogar()` | `MarcoDAO` | DAO/UseCase | PENDIENTE |
| `indConsumoPerCapita()` | `MarcoDAO` | DAO/UseCase | PENDIENTE |
| `indPreciosExtremos()` | `MarcoDAO` | DAO/UseCase | PENDIENTE |
| `indBalanceHogar()` | `MarcoDAO` | DAO/UseCase | PENDIENTE |
| `indIngresosTotales()` | `MarcoDAO` | DAO/UseCase | PENDIENTE |
| `UNION ALL` de observaciones/otros | `Enaho01Dao`, `Enaho02Dao`, `ViviendaDao` | DAO Room | PENDIENTE |
| DELETE cascada modulo | DAOs/Services | `CleanupRepository` | BLOQUEANTE |
| UPDATE masivos por vivienda/persona | `ViviendaDao`, `HogarDao` | Transacciones Room | BLOQUEANTE |

**Conclusion:** No basta con mapear tablas. Las consultas criticas son parte del comportamiento funcional y deben tener destino especifico antes de declarar Room listo.

---

# 13. ENDPOINTS

| Endpoint actual | Metodo | Uso | Nuevo API | DTO | Repository | Estado |
|---|---|---|---|---|---|---|
| `/ubicacionGpsUsuario` | POST | GPS actual/offline flush | `@FormUrlEncoded @POST` | `GpsUploadRequest` o `FieldMap` | `GpsRepository` | COMPLETO CON OBSERVACIONES |
| `/consultaInfo` | POST | Consulta ciudadano/usuario | `@POST` | `ConsultaInfo` existente + wrapper | `InfoRepository` | COMPLETO CON OBSERVACIONES |
| `/consultaRuta` | POST | Plan ruta | `@POST` | Ruta DTO | `RutaRepository` | PENDIENTE |
| `/grabarRuta` | POST | Crear ruta | `@POST` | Ruta request | `RutaRepository` | PENDIENTE |
| `/actualizarRuta` | POST | Actualizar ruta | `@POST` | Ruta request | `RutaRepository` | PENDIENTE |
| `/consultaHora` | GET | Hora servidor | `@GET` | `ConsultaHoraResponse` | `TimeRepository` | COMPLETO CON OBSERVACIONES |
| `/consultaSubidos_v2` | POST | Archivos subidos | `@POST` | `Archivo` list + wrapper | `ExportRepository` | COMPLETO CON OBSERVACIONES |
| `/uploadMultiple_v2` | POST multipart | Exportar data | `@Multipart @POST` | Multipart parts | `ExportRepository` | COMPLETO CON OBSERVACIONES |
| `/descargaBackup_v2` | POST binary | Descargar backup | `@Streaming @POST` | Binary response | `ImportRepository` | COMPLETO CON OBSERVACIONES |
| `/aguaService` | POST | Datos agua | `@POST` | Agua request | `AguaRepository` | PENDIENTE |
| `/descargaDataInicialReentrevista` | POST | Sin uso/no probado | Evaluar | DTO pendiente | Repository pendiente | PENDIENTE |
| `/consultaMensajes` | POST | Mensajes legacy | Evaluar | DTO pendiente | `MensajesRepository` | PENDIENTE |
| `/mensajes` | POST | Mensajes legacy | Evaluar | DTO pendiente | `MensajesRepository` | PENDIENTE |

No se encontraron endpoints activos adicionales fuera de los documentados, pero existen URLs hardcoded auxiliares en `NetworkUtil`, `InfoActivity` y FTP.

---

# 14. RED

| Aspecto | Actual | Arquitectura nueva | Estado |
|---|---|---|---|
| Cliente HTTP | `EnahoHttpClient` + `AsyncHttpClient` | Retrofit + OkHttp | COMPLETO CON OBSERVACIONES |
| SSL | `MySSLSocketFactory` trust-all legacy | Certificados validos | BLOQUEANTE |
| Auth | Basic hardcoded | `AuthInterceptor`/config segura | BLOQUEANTE |
| Timeouts | 20s connect, 30s read | OkHttp config | COMPLETO CON OBSERVACIONES |
| Retry | Legacy 2 retries, nuevo 0 | Politica centralizada | PENDIENTE |
| Error handling | Duplicado por callback | `Result`/error mapper | PENDIENTE |
| Offline | GPS solamente | Repository + cola | PENDIENTE |
| Upload | Multipart manual | Retrofit multipart | COMPLETO CON OBSERVACIONES |
| Download | Binary manual | Retrofit streaming | COMPLETO CON OBSERVACIONES |
| Main thread | Riesgos SyncHttpClient | Dispatchers.IO | BLOQUEANTE |

---

# 15. PRESENTATION

| Actual | Funcion | Nuevo | Accion | Estado |
|---|---|---|---|---|
| `LoginActivity` | Login offline | `LoginFragment`/Activity + ViewModel | MIGRAR | PENDIENTE |
| `CuestionarioFragmentActivity` | Contenedor encuesta, ViewPager, drawer | Survey host + ViewModel | DIVIDIR | BLOQUEANTE |
| `AbstractCuestionarioFragmentActivity` | Navegacion, grabado, estado | Host + navigator + shared ViewModel | DIVIDIR | BLOQUEANTE |
| `FragmentForm` | UI, validacion, mapeo, persistencia | Fragment + ViewBinding + ViewModel | DIVIDIR | BLOQUEANTE |
| Fragments Cap500 | Captura empleo | Pilot fragments/ViewModels | MIGRAR PILOTO | PENDIENTE |
| Fragments ENAHO01/01A/01B/02/04 | Captura capitulos | Feature modules | MIGRAR INCREMENTAL | PENDIENTE |
| DialogFragments | Captura auxiliar | Dialogs/ViewModels | MIGRAR | PENDIENTE |
| Navigation Drawer ListView | Menu principal | Navigation UI tradicional XML | REEMPLAZAR | PENDIENTE |
| ProgressDialog | Progreso operaciones largas | ProgressBar/UiState | REEMPLAZAR | PENDIENTE |
| ListView/Custom table | Listas/tablas | RecyclerView cuando aplique | REEMPLAZAR/MANTENER TEMPORAL | PENDIENTE |

---

# 16. UX

La arquitectura objetivo no obliga a reproducir mecanicamente la UI actual. La decision aprobada para Etapa 8 es mantener la UI actual durante el piloto Cap500 y evaluar posteriormente.

| Capacidad futura | Permitida por arquitectura | Estado |
|---|---|---|
| Combinar preguntas | Si, si el Engine usa destinos logicos | COMPLETO |
| Dividir preguntas | Si | COMPLETO |
| Cambiar relacion capitulo/pantalla | Si, via NavigationMapper | COMPLETO |
| Indicador de progreso | Si, derivado de `SurveyGraph` | PENDIENTE |
| Navegacion por pasos | Si | PENDIENTE |
| Reordenar pantallas sin cambiar reglas | Si, si destino es logico | COMPLETO CON OBSERVACIONES |

**Conclusion:** UX no bloquea el inicio del piloto si se conserva estructura actual, pero si bloquea un redisenio completo porque todavia no existe evidencia de campo ni mapa pantalla-pregunta definitivo.

---

# 17. DEPENDENCIAS OCULTAS

| Dependencia | Evidencia | Riesgo | Nuevo destino | Estado |
|---|---|---|---|---|
| Service Locator global | `AppContext.getServices()` 100+ referencias | Alto | Hilt modules | BLOQUEANTE |
| Estado global | `AppContext.personalEntity`, PIN, usuario | Alto | SessionRepository/StateHolder | BLOQUEANTE |
| Activity en Services | `EnahoWebService`, `HogarService`, `ViviendaService` | Alto | Repository sin Android | BLOQUEANTE |
| Fragment en Services | `EnahoWebService` con fragments | Alto | Callbacks/ViewModel | BLOQUEANTE |
| Activity en `InfoGPS` | Referencia directa a `CuestionarioFragmentActivity` | Critico | Service/Worker desacoplado | BLOQUEANTE |
| Reflection UI-Entity | `FragmentForm.entityToUI/uiToEntity` | Alto | Mappers tipados | PENDIENTE |
| Reflection ORM | `SQLiteDAO`, `Entity` | Alto | Room codegen | BLOQUEANTE |
| SharedPreferences disperso | `Preferencias` + Activities | Medio | DataStore/Repository o prefs centralizadas | PENDIENTE |
| Ordinales ViewPager | `FragmentoEnaho.*.ordinal()` | Alto | `NavigationMapper` | BLOQUEANTE |
| SQL dinamico | `rawQuery/execSQL` | Alto | DAO parametrizado | BLOQUEANTE |

---

# 18. DEPENDENCIAS CROSS-CHAPTER

| Origen | Destino | Tipo de dependencia | Regla | Nuevo componente | Estado |
|---|---|---|---|---|---|
| Cap200 | Cap300 | Elegibilidad | P212 por edad/vive hogar | `ChapterResolver` | COMPLETO CON OBSERVACIONES |
| Cap200 | Cap400 | Elegibilidad | P213 por edad/vive hogar | `ChapterResolver` | COMPLETO CON OBSERVACIONES |
| Cap200 | Cap500 | Elegibilidad | P214 por edad/vive hogar | `ChapterResolver` | COMPLETO CON OBSERVACIONES |
| Cap200 eliminar miembro | Cap300/400/500 | Limpieza cascada | Elimina modulos persona | `CleanupManager` | BLOQUEANTE |
| Cap200 eliminar miembro | ENAHO02 | Limpieza cascada | Limpia 2000-2700 | `CleanupManager` | BLOQUEANTE |
| Cap200 eliminar miembro | ENAHO04 | Limpieza cascada | Limpia Cap10-40/calculos | `CleanupManager` | BLOQUEANTE |
| Cap500 | ENAHO02 | Habilitacion/limpieza actividad economica | P505/P516 y afines | `ChapterResolver` + `CleanupManager` | BLOQUEANTE |
| Cap500 | ENAHO04 | Actividades/gastos | Calculos por actividad | `CleanupManager` + UseCases | BLOQUEANTE |
| Subsistema reentrevista | Varios capitulos | Ocultar preguntas/flujo | `aplicarSubsistemaReentrevista` | `ConditionEvaluator` + `VisibilityResolver` | PENDIENTE |
| COVID/reducido | Varios capitulos | Ocultar preguntas/flujo | `aplicarCuestionarioCovidReducido` | `ConditionEvaluator` + `VisibilityResolver` | PENDIENTE |

---

# 19. RIESGOS

| Riesgo | Causa conocida | Impacto | Mitigacion propuesta | Validacion | Estado |
|---|---|---|---|---|---|
| ANR por red sincronica | `SyncHttpClient`, `NetworkUtil.isInternetAvailable` | Congelamiento app | Retrofit/coroutines IO | StrictMode/tests/manual | BLOQUEANTE |
| ANR por DB/XML | `ConfigActivity`, queries pesadas | Congelamiento | Dispatchers.IO/Room | pruebas carga | BLOQUEANTE |
| ANR por archivo | Copia DB en main thread | Congelamiento | IO dispatcher/Worker | prueba DB grande | PENDIENTE |
| SSL trust-all | `MySSLSocketFactory` | MITM | OkHttp SSL valido | prueba certificado invalido | BLOQUEANTE |
| Credenciales hardcoded | `HttpConfig`, FTP, SIS, ADMIN | Exposicion | Config segura | revision binario/codigo | BLOQUEANTE |
| SQL injection | SQL concatenado | Seguridad/integridad | Room queries parametrizadas | pruebas parametros | BLOQUEANTE |
| 0 indices | No `CREATE INDEX` | Performance | `@Index` por consultas | EXPLAIN/query timing | BLOQUEANTE |
| Memory leak Activity | `InfoGPS`, AsyncTask, static context | Fugas/crash | Lifecycle-aware components | LeakCanary/manual | BLOQUEANTE |
| NPE GPS legacy | `invokeGpsUpload1` reportado | Crash | Null handling | unit/manual | PENDIENTE |
| Android 12+ | service/flags | Crash moderno | actualizar target y manifest | pruebas API 31+ | BLOQUEANTE |
| Perdida de estado | Activity fields/global state | Encuesta inconsistente | `SurveyState` recuperable | process death test | BLOQUEANTE |
| Limpieza incompleta | Cascadas dispersas | Datos huerfanos | `CleanupPlan` exhaustivo | dual execution | BLOQUEANTE |

---

# 20. CICLO DE VIDA Y RECUPERACION

| Escenario | Actual | Arquitectura objetivo | Estado |
|---|---|---|---|
| Rotacion | `configChanges` evita recreacion normal | ViewModel + StateFlow | PENDIENTE |
| Process death | Respuestas en DB, estado UI/global no garantizado | Reconstruir desde Room + prefs minimas | BLOQUEANTE |
| Recreacion Fragment | Fragment carga desde services/DB | UiState desde ViewModel | PENDIENTE |
| App kill durante captura | DB conserva ultimo guardado, pagina/miembro incierto | SurveyState persistido | BLOQUEANTE |
| GPS service vivo | `InfoGPS` mantiene Activity | Worker/service desacoplado | BLOQUEANTE |
| Async callback despues de destroy | Posible ProgressDialog/Activity leak | Lifecycle-aware collection | BLOQUEANTE |
| Recuperacion respuestas | SQLite | Room | COMPLETO CON OBSERVACIONES |
| Recuperacion navegacion | ViewPager ordinal/preferencias parciales | destino logico persistible | PENDIENTE |

---

# 21. HILT

La arquitectura objetivo define modulos suficientes para comenzar diseno, pero no implementacion:

| Modulo | Dependencias | Estado |
|---|---|---|
| `DatabaseModule` | Room database y DAOs | PENDIENTE |
| `NetworkModule` | Retrofit, OkHttp, interceptors | PENDIENTE |
| `RepositoryModule` | Interfaces/implementaciones | PENDIENTE |
| `SurveyModule` | SurveyFlowEngine y 6 componentes | PENDIENTE |
| Session/config module | Usuario, modo, subsistema | FALTANTE |

---

# 22. COROUTINES / FLOW / STATEFLOW

| Operacion | Actual | Objetivo | Estado |
|---|---|---|---|
| SQLite | Main thread + raw queries + AsyncTask | `suspend` Room en `Dispatchers.IO` | BLOQUEANTE |
| Red | Sync/Async callbacks + Thread | Retrofit suspend en IO | BLOQUEANTE |
| GPS | Service + Timer + Thread | Worker/service lifecycle-aware | BLOQUEANTE |
| Export/import | AsyncTask + ProgressDialog | Coroutine/Flow de progreso | PENDIENTE |
| UI state | Widgets/Activity fields | `StateFlow<UiState>` | PENDIENTE |
| Cancelacion | Manual/insuficiente | Structured concurrency | PENDIENTE |
| Errores | Toast/Dialog disperso | Error state centralizado | PENDIENTE |

---

# 23. MAPA ACTUAL A NUEVO

| Componente actual | Responsabilidad | Nuevo componente | Capa | Accion | Validacion | Estado |
|---|---|---|---|---|---|---|
| `Aplicacion` | Inicializacion global | `EnahoApplication` + Hilt | App | REEMPLAZAR | Startup test | PENDIENTE |
| `AppContext` | Service Locator/estado global | Hilt + repositories/session | App/Data | REEMPLAZAR | Sin refs globales nuevas | BLOQUEANTE |
| `ModelContext` | Construccion manual services/DAOs | Hilt modules | App | REEMPLAZAR | DI graph | BLOQUEANTE |
| `FragmentForm` | UI+validacion+mapeo+persistencia | Fragment + ViewModel + mappers | Presentation | DIVIDIR | UI tests/pilot | BLOQUEANTE |
| `CuestionarioFragmentActivity` | Host encuesta/navegacion | Survey host | Presentation | DIVIDIR | Navegacion piloto | BLOQUEANTE |
| `FragmentoEnaho` ordinal | Indice ViewPager | destino logico + mapper | Presentation | REEMPLAZAR | map tests | BLOQUEANTE |
| `getPaginaSiguiente/Anterior` | Navegacion condicional | `NavigationResolver` | Domain | CONSOLIDAR | Legacy vs Engine | BLOQUEANTE |
| `setRelevancia` | Visibilidad dinamica | `VisibilityResolver` | Domain/Presentation | CONSOLIDAR | snapshot UI | BLOQUEANTE |
| `limpiar*` | Limpieza datos | `CleanupManager` + UseCase | Domain/Data | CONSOLIDAR | DB diff | BLOQUEANTE |
| Controllers | Validacion/coordinacion | ViewModel + UseCases | Presentation/Domain | DIVIDIR | unit tests | PENDIENTE |
| Services Java | Orquestacion sobre DAOs/red | Repositories + UseCases | Domain/Data | REEMPLAZAR | integration tests | BLOQUEANTE |
| DAOs raw | SQL persistence | Room DAOs | Data | REEMPLAZAR | migration tests | BLOQUEANTE |
| Entities custom | ORM/reflection | Room entities + domain models | Data/Domain | DIVIDIR | schema tests | BLOQUEANTE |
| `SQLiteDAO` | ORM generico | Room | Data | ELIMINAR | no usage | BLOQUEANTE |
| `MyDatabaseHelper` | schema/upgrades | Room database + migrations | Data | REEMPLAZAR | migration tests | BLOQUEANTE |
| `EnahoWebService` | Red + UI callbacks | Retrofit repositories | Data | DIVIDIR | API tests | BLOQUEANTE |
| `EnahoHttpClient` | HTTP custom | OkHttp/Retrofit | Data | REEMPLAZAR | endpoint tests | PENDIENTE |
| `MySSLSocketFactory` | SSL inseguro | SSL sistema/OkHttp config | Data | ELIMINAR | security test | BLOQUEANTE |
| `InfoGPS` | GPS tracking | GPS repository/worker | Data/Infra | REEMPLAZAR | lifecycle/offline tests | BLOQUEANTE |
| `Preferencias` | SharedPreferences | prefs repository/DataStore opcional | Data | CONSOLIDAR | state recovery | PENDIENTE |
| AsyncTask classes | Background work | Coroutines/Worker | Presentation/Data | REEMPLAZAR | ANR tests | BLOQUEANTE |
| ProgressDialog | Progreso blocking UI | UiState progress | Presentation | REEMPLAZAR | lifecycle test | PENDIENTE |

---

# 24. TRAZABILIDAD COMPLETA

| Elemento actual | Regla/funcion | Arquitectura nueva | Validacion | Estado |
|---|---|---|---|---|
| `Cap200_B.actualizar212_213_214` | Capitulo por edad | `ChapterResolver` | Unit tests edad/vive hogar | COMPLETO CON OBSERVACIONES |
| `Cap500Fragment_007_P537` | Saltos/limpieza P537 | `NavigationResolver` + `CleanupManager` | Dual execution Cap500 | PENDIENTE |
| `Cap200Controller.eliminar` | Limpieza cross-chapter | `CleanupPlan` compuesto | DB diff antes/despues | BLOQUEANTE |
| `EnahoDao.onAntesInsertarRegistro` | Visita activa/auto-close | SaveAnswerUseCase/VisitUseCase | Unit/integration | PENDIENTE |
| `HogarDao.getResultadoFinalHogarCalculado` | RESFIN | Domain calculation | Unit tests | COMPLETO CON OBSERVACIONES |
| `EnahoWebService.invokeGpsUpload` | GPS online/offline | `GpsRepository` | fake network tests | PENDIENTE |
| `Exportacion` | XML/ZIP export | ExportUseCase | file diff | PENDIENTE |
| `Importacion` | XML import transaccional | ImportUseCase + Room tx | rollback tests | PENDIENTE |
| `SQLiteDAO` | CRUD generico | Room DAOs | schema/query tests | BLOQUEANTE |
| `EnahoWebService` endpoints | API | Retrofit service | mock server tests | PENDIENTE |
| `FragmentForm.entityToUI/uiToEntity` | Mapeo UI-datos | typed mappers/ViewBinding | UI tests | PENDIENTE |
| `setRelevancia` | Visibilidad | `VisibilityResolver` | rules tests | BLOQUEANTE |

---

# 25. DECISIONES PENDIENTES

| # | Decision | Impacto | Evidencia requerida | Responsable | Estado |
|---|---|---|---|---|---|
| 1 | Navigation Component con ViewPager o reemplazo gradual | Alto | Prueba piloto de navegacion | Arquitectura/Usuario | PENDIENTE NO BLOQUEANTE |
| 2 | Mantener UI actual despues del piloto o redisenar | Medio | Feedback encuestador + metricas | Usuario/UX | PENDIENTE NO BLOQUEANTE |
| 3 | Fragment-per-question vs agrupacion | Medio | Datos piloto | Usuario/Arquitectura | PENDIENTE NO BLOQUEANTE |
| 4 | Indicador de progreso | Bajo-Medio | Definir metrica de progreso | Usuario/UX | PENDIENTE NO BLOQUEANTE |
| 5 | Alcance exacto del piloto Cap500 | Alto | Lista fragments/reglas Cap500 | Usuario/Arquitectura | PENDIENTE BLOQUEANTE PARA PILOTO |
| 6 | Catalogo minimo de `CleanupAction` para piloto | Alto | Mapa limpiar Cap500 + cross-chapter | Arquitectura | PENDIENTE BLOQUEANTE |
| 7 | Criterios de comparacion Legacy vs Engine | Alto | Casos de prueba y entradas | Arquitectura/QA | PENDIENTE BLOQUEANTE |
| 8 | Estrategia Room inicial: todo schema o slice piloto | Alto | Riesgo migracion DB | Arquitectura/Usuario | PENDIENTE BLOQUEANTE |
| 9 | Tratamiento de credenciales | Alto | Politica seguridad | Usuario/Seguridad | PENDIENTE BLOQUEANTE |
| 10 | GPS: Service moderno vs Worker | Alto | Requisito operativo background | Arquitectura/Usuario | PENDIENTE BLOQUEANTE |

---

# 26. ESTRATEGIA DE VALIDACION

## 26.1 Piloto Cap500

La estrategia de piloto es valida, pero debe formalizarse antes de implementar.

| Comportamiento | Validacion requerida | Estado |
|---|---|---|
| Navegacion P501-P560 | Legacy vs Engine destino logico | PENDIENTE |
| Saltos P537/P541/P544/P545 | Casos por combinacion respuestas | PENDIENTE |
| Limpieza local Cap500 | DB diff por `CleanupPlan` | PENDIENTE |
| Limpieza cross-chapter | DB diff ENAHO02/04 | BLOQUEANTE |
| Visibilidad | Snapshot de preguntas visibles | PENDIENTE |
| Estado/recuperacion | Rotacion/process death | PENDIENTE |
| Persistencia respuestas | Comparar filas legacy vs nuevo | PENDIENTE |
| Red/sync | No migrar en piloto salvo necesario | PENDIENTE |
| UX | Tiempo, errores, feedback | PENDIENTE |

## 26.2 Dual Execution / Shadow Engine

Se debe ejecutar el Engine en paralelo al codigo legacy sin afectar datos al inicio. La comparacion minima debe registrar:

| Entrada | Legacy | Engine | Resultado |
|---|---|---|---|
| Fragment actual | Ordinal/destino actual | Destino logico | Match/mismatch |
| Respuestas | Valores UI/DB | `SurveyState.answers` | Match/mismatch |
| Limpieza esperada | Metodos `limpiar*` invocados | `CleanupPlan` | Match/mismatch |
| Visibilidad | Widgets relevantes | `VisibilityDecision` | Match/mismatch |

---

# 27. BLOQUEADORES CLASIFICADOS

## 27.1 Clasificación por Alcance

| Bloqueador | ¿Bloquea PILOTO Cap500? | ¿Bloquea IMPLEMENTACIÓN GENERAL? |
|------------|-------------------------|----------------------------------|
| Catálogo incompleto visibilidad dinamica | SI (solo Cap500) | SI (toda la app) |
| Catálogo incompleto limpieza | SI (Cap500 + cascadas ENAHO02/04) | SI (toda la app) |
| Navegación condicional no mapeada | SI (Cap500 completo) | SI (toda la app) |
| Room no implementado | NO (usar DB legacy encapsulada) | SI |
| Consultas críticas sin destino | SI (solo piloto) | SI (reportes/export/import) |
| Estado recuperable no implementado | SI (definir mínimo para piloto) | SI |
| Seguridad red crítica | NO (legacy funciona, pero anotar) | SI |
| Concurrencia/ANR | SI (si piloto toca flujos largos) | SI |
| Dependencias globales | NO (aislar temporalmente) | SI |

## 27.2 Justificación de Decisiones Clave

### Room (Punto 2)
- **Para piloto:** DB legacy encapsulada temporalmente (Room parcial o encapsulación pending)
- **Para implementación general:** Room completo con migraciones, indices, foreign keys
- **Razón:** No bloquear piloto por implementación Room completa

### Seguridad (Punto 7)
- **Para piloto:** SSL legacy funciona; credenciales hardcoded son riesgo conocido pero no bloqueante si se mantiene patrón actual
- **Para implementación general:** Reemplazar SSL, mover credenciales a config segura

### Ciclo vida/estado (Punto 8)
- **Para piloto:** Definir mínimo: currentFragment, currentMember, respuestas persistidas, reconstrucción desde Room
- **Para implementación general:** Estado completo incluyendo derivation, progreso, sincronización

---

# 28. CRITERIOS VERIFICABLES

## 28.1 LISTO PARA PILOTO CAP500 (22 condiciones)

**Cada criterio debe verificarse objetivamente antes de iniciar el piloto.**

| # | Criterio | Evidencia requerida | ¿Bloquea? |
|---|----------|---------------------|-----------|
| 1 | **Alcance del piloto Cap500 documentado completamente:** fragments, reglas de negocio, consultas SQL, limpiezas (locales y cross-chapter), endpoints necesarios, tablas afectadas, y dependencias con otros capítulos | Documento de alcance Cap500 que incluya: lista fragments, reglas de negocio, consultas SQL, limpiezas, endpoints, tablas, dependencias cross-chapter | SI |
| 2 | **Navegación Cap500 completa:** catálogo TransitionRule con todas las reglas (from, conditions, priority, destination, isDefault) más validación de prioridades, destinos inválidos, reglas inalcanzables, reglas duplicadas, y empate de prioridades | Catálogo TransitionRule Cap500 con: from, conditions, priority, destination, isDefault; más validación de: prioridades, destinos inválidos, reglas inalcanzables, reglas duplicadas, empate de prioridades | SI |
| 3 | **Visibilidad Cap500 completa:** catálogo VisibilityRule con todas las reglas de visibilidad Cap500 (setRelevancia, setRelevanciaFija, condiciones, preguntas/fragmentos ocultos) verificando que todas las reglas de visibilidad Cap500 están catalogadas | Catálogo VisibilityRule Cap500 con: pregunta/fragmento, condición, tipo (show/hide), fuente (setRelevancia/setRelevanciaFija), y verificación de que todas las reglas de visibilidad Cap500 están catalogadas | SI |
| 4 | **Limpieza local Cap500 completa:** catálogo CleanupAction con todas las limpiezas locales Cap500 (limpiar538_540, limpiar538_543, limpiar544, limpiar545_555, etc.) con DB diff antes/después verificando que solo se afectan datos Cap500 | Catálogo CleanupAction Cap500 con: método limpiar*, tablas afectadas, tipo (DELETE/UPDATE), condición, y verificación de DB diff antes/después | SI |
| 5 | **Limpieza cross-chapter Cap500 completa:** catálogo CleanupAction con todas las limpiezas cross-chapter que afectan datos fuera de Cap500 (ENAHO02, ENAHO04, eliminación de miembro, etc.) con verificación de que la limpieza no afecta datos del piloto | Catálogo CleanupAction cross-chapter Cap500 con: origen (Cap500), destino (ENAHO02/04), tablas afectadas, tipo (DELETE/UPDATE), condición, y verificación de que la limpieza no afecta datos del piloto | SI |
| 6 | **Estado mínimo del piloto definido completamente:** currentChapter, currentFragment, currentMember, respuestas, capítulo actual, destino lógico, estado derivado necesario, con persistencia y recuperación definidas para rotación, recreación de Fragment, process death, cierre inesperado de aplicación, y recuperación desde DB | Definición completa del estado mínimo del piloto: currentChapter, currentFragment, currentMember, respuestas, capítulo actual, destino lógico, estado derivado necesario, con persistencia y recuperación definidas | SI |
| 7 | **Persistencia del piloto definida completamente:** tablas necesarias para Cap500, consultas necesarias, operaciones INSERT/UPDATE/DELETE, transacciones, datos de otros capítulos afectados, abstracción de DB legacy, y verificación de seguridad | Decisión de persistencia documentada con: tablas necesarias para Cap500, consultas necesarias, operaciones INSERT/UPDATE/DELETE, transacciones, datos de otros capítulos afectados, abstracción de DB legacy, y verificación de seguridad | SI |
| 8 | **Estrategia Legacy vs Engine documentada completamente:** evidencia, logs, identificador del caso, entrada utilizada, resultado Legacy, resultado Engine, diferencia, severidad, reproducción | Estrategia de comparación Legacy vs Engine documentada con: evidencia, logs, identificador del caso, entrada utilizada, resultado Legacy, resultado Engine, diferencia, severidad, reproducción | SI |
| 9 | **Casos de prueba documentados completamente:** al menos 1 escenario completo por regla Cap500 con datos de entrada, datos de salida esperados, y verificación | Casos de prueba documentados con: escenario, datos de entrada, datos de salida esperados, verificación | SI |
| 10 | **Rollback mínimo documentado completamente:** detectar fallo, evitar datos productivos, regresar legacy, preservar información, evitar corrupción, evitar duplicidad, conservar respuestas, detectar inconsistencia, registrar fallo | Definición de rollback documentada con: detectar fallo, evitar datos productivos, regresar legacy, preservar información, evitar corrupción, evitar duplicidad, conservar respuestas, detectar inconsistencia, registrar fallo | SI |
| 11 | **Consultas usadas por Cap500 documentadas completamente:** consulta, tablas, operaciones, transacciones | Inventario de consultas Cap500 documentado con: consulta, tablas, operaciones, transacciones | SI |
| 12 | **GPS para piloto:** decisión documentada con estrategia, justificación, y riesgos | Decisión GPS documentada con: estrategia, justificación, riesgos | NO |
| 13 | **ANR para piloto:** plan documentado con flujos largos, operaciones bloqueantes, y mitigation | Plan ANR documentado con: flujos largos, operaciones bloqueantes, mitigation | SI |
| 14 | **Dependencias del piloto aisladas completamente:** dependencias, abstracción, verificación | Dependencias aisladas documentadas con: dependencias, abstracción, verificación | SI |
| 15 | **Integridad de datos verificada:** plan de verificación de integridad documentado | Plan de verificación de integridad documentado | SI |
| 16 | **Transacciones definidas:** cómo se manejan las transacciones en el piloto | Definición de transacciones documentada | SI |
| 17 | **Cobertura de reglas verificada:** matriz de cobertura que demuestre que todas las reglas Cap500 están cubiertas | Matriz de cobertura documentada | SI |
| 18 | **Errores manejados:** plan de manejo de errores documentado | Plan de manejo de errores documentado | SI |
| 19 | **Observabilidad/logging:** plan de logging documentado | Plan de logging documentado | SI |
| 20 | **Seguridad de datos:** plan de seguridad documentado | Plan de seguridad documentado | SI |
| 21 | **Compatibilidad con aplicación actual:** plan de compatibilidad documentado | Plan de compatibilidad documentado | SI |
| 22 | **Dual execution o shadow engine:** mecanismo para comparar resultados sin afectar producción | Mecanismo de comparación documentado | SI |

**Nota importante:** Cada criterio debe verificarse objetivamente. La pregunta clave para cada criterio es: "¿Cómo sabemos objetivamente que está cumplido?" La respuesta debe incluir evidencia concreta, no asunciones.

## 28.2 LISTO PARA IMPLEMENTACIÓN GENERAL (requisitos más estrictos)

| # | Condición | Verificación |
|---|-----------|--------------|
| 1 | Catálogo completo reglas negocio | 100% reglas documentadas individualmente |
| 2 | Persistencia Room completa | Entities, DAOs, indices, foreign keys, migraciones |
| 3 | Seguridad | SSL válido, credenciales seguras, sin hardcoded |
| 4 | Red completa Retrofit | Endpoints, DTOs, repositories, error handling |
| 5 | Concurrencia/ANR resueltos | IO dispatcher, workers, lifecycle-aware |
| 6 | Ciclo vida completo | Process death recovery, recreación, rotación |
| 7 | Dependencias globales eliminadas | Service locator, Activity/Fragment en services |
| 8 | Navegación completa | Todos los capítulos, cross-chapter, precedencia |
| 9 | Visibilidad completa | Todos los capítulos, reglas dinámicas |
| 10 | Limpieza completa | Todas las cascadas, soft delete |
| 11 | Consultas completas | Todas las 40+ consultas y 100+ SQL dinámicos |
| 12 | Trazabilidad todos capítulos | Legacy vs Engine para todos |
| 13 | Casos de prueba completos | Todos los flujos, edge cases |
| 14 | Rollback completo | Estrategia completa de recuperación |
| 15 | UX evaluada | Feedback encuestador, métricas |

## 28.3 ESTRATEGIA LEGACY VS NEW ENGINE

**Nota:** La estrategia de comparación Legacy vs New Engine está integrada en el **Criterio 8** de la sección 28.1. Cada elemento debe documentarse con: evidencia, logs, identificador del caso, entrada utilizada, resultado Legacy, resultado Engine, diferencia, severidad, y reproducción.

| Elemento | Comparación | Estado |
|----------|-------------|--------|
| Respuestas | Valores UI/DB vs SurveyState.answers | Criterio 8 |
| Destino lógico | Ordinal actual vs TransitionRule | Criterio 8 |
| Visibilidad | Widgets relevantes vs VisibilityDecision | Criterio 8 |
| Limpieza | Métodos limpiar* vs CleanupPlan | Criterio 8 |
| Estado | Activity fields vs SurveyState persistido | Criterio 8 |
| Capítulos | FragmentoEnaho vs ChapterResolver | Criterio 8 |

**Cada elemento debe clasificarse como:** MATCH (coincide), MISMATCH (no coincide), o NO DETERMINADO (requiere más análisis). Para cada MISMATCH, debe documentarse: evidencia, severidad, y plan de corrección.

---

# 29. CONCLUSIÓN

## Estado de la Auditoría

| Alcance | Estado | Detalle |
|---------|--------|---------|
| **Implementación General** | NO LISTO | Faltan catálogos, Room, seguridad, ciclo vida completo |
| **Piloto Cap500** | REQUIERE 22 CRITERIOS | 22 criterios verificables definidos en sección 28.1 |
| **Bloqueadores Piloto** | 21 identificados | Alcance, navegación, visibilidad, limpieza, estado, persistencia, Legacy vs Engine, casos prueba, rollback, consultas, ANR, dependencias, integridad, transacciones, cobertura, errores, logging, seguridad, compatibilidad, dual execution |
| **Bloqueadores Generales** | 12+ identificados | Room completo, seguridad completa, dependencias globales, ciclo vida completo |
| **Decisiones Pendientes** | 6 críticas | Alcance piloto, Room, Legacy vs Engine, credenciales, GPS, UX |
| **Siguiente Paso** | Verificar 22 condiciones | Cada criterio debe verificarse objetivamente con evidencia concreta |

## Hallazgos Importantes (mantenidos)

1. **Reglas no catalogadas:** 100+ setRelevancia son ocurrencias, no reglas funcionales distintas
2. **Visibilidad dinámica:** 100+ usos deben convertirse a VisibilityRule
3. **Limpieza cross-chapter:** Cascadas pueden borrar datos completos
4. **Navegación:** 42+ reglas getPaginaSiguiente requieren mapeo a TransitionRule
5. **Dependencias globales:** Service Locator y Activity/Fragment en services
6. **Estado:** Process death puede perder contexto crítico
7. **ANR:** SyncHttpClient y operaciones bloqueantes
8. **Seguridad:** SSL trust-all y credenciales hardcoded
9. **Room:** 0 anotaciones, schema complejo (137 tablas)
10. **Consultas:** 40+ complejas y 100+ SQL dinámicos
11. **Lifecycle:** configChanges, allowBackup, InfoGPS exported

## Próximos Pasos

1. Verificar los 22 criterios de la sección 28.1 con evidencia concreta
2. Para cada criterio, responder: "¿Cómo sabemos objetivamente que está cumplido?"
3. Crear documentos de soporte para cada criterio (alcance, catálogos, planes, definiciones)
4. Validar que cada criterio tiene evidencia verificable
5. Esperar aprobación del usuario de los 22 criterios
6. Solo después de aprobación: comenzar preparación del piloto

**Estado final:** REQUIERE 22 CRITERIOS VERIFICABLES.

**Acción siguiente:** Verificar cada uno de los 22 criterios con evidencia concreta. No avanzar a implementación.

---

*Documento generado como parte de la Etapa 09 - Auditoria Pre-Implementacion.*
