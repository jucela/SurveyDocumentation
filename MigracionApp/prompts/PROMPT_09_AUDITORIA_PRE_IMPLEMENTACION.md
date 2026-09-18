# 09 - AUDITORÍA PRE-IMPLEMENTACIÓN

## OBJETIVO

Verificar que la documentación, el conocimiento del sistema actual
y la arquitectura objetivo estén suficientemente completos,
consistentes y trazables antes de comenzar la implementación.

Esta etapa es una AUDITORÍA.

NO es una etapa de implementación.

NO modificar código fuente.

NO iniciar la migración.

NO crear componentes de producción.

NO modificar Gradle.

NO agregar dependencias.

La auditoría debe determinar si existe información suficiente para
comenzar la implementación de manera controlada.

--------------------------------------------------
# 1. FUENTES OBLIGATORIAS
--------------------------------------------------

Utilizar como fuentes principales:

- docs/01_INVENTARIO_TECNICO.md
- docs/02_ARQUITECTURA_ACTUAL.md
- docs/03_FLUJOS_FUNCIONALES.md
- docs/04_BASE_DATOS.md
- docs/05_API_Y_RED.md
- docs/06_REGLAS_NEGOCIO.md
- docs/07_RIESGOS_Y_ANR.md
- docs/08_ARQUITECTURA_OBJETIVO.md

Verificar nuevamente la información contra el código fuente.

NO asumir que la documentación anterior es completa únicamente
porque existe.

Cuando sea necesario:

- localizar clases;
- localizar métodos;
- localizar consultas;
- localizar endpoints;
- localizar reglas;
- localizar condiciones;
- localizar navegación;
- localizar operaciones de limpieza;
- localizar dependencias.

Toda conclusión importante debe poder relacionarse con evidencia
del código o de la documentación disponible.

--------------------------------------------------
# 2. PRINCIPIO FUNDAMENTAL DE LA AUDITORÍA
--------------------------------------------------

La existencia de documentación NO significa que el sistema esté
listo para implementar.

Debe existir trazabilidad suficiente entre:

CÓDIGO ACTUAL
      ↓
FUNCIONALIDAD / REGLA
      ↓
ARQUITECTURA OBJETIVO
      ↓
COMPONENTE NUEVO
      ↓
ESTRATEGIA DE MIGRACIÓN
      ↓
VALIDACIÓN

La auditoría debe detectar cualquier ruptura de esta cadena.

Si una funcionalidad, regla o componente importante no tiene
correspondencia clara con la arquitectura objetivo:

marcarlo como:

PENDIENTE DE ANÁLISIS

No inventar una correspondencia.

--------------------------------------------------
# 3. CRITERIOS DE ESTADO
--------------------------------------------------

Utilizar los siguientes estados:

- COMPLETO
- COMPLETO CON OBSERVACIONES
- PENDIENTE
- INCONSISTENTE
- FALTANTE
- BLOQUEANTE

Definir como BLOQUEANTE cualquier problema que pueda provocar
pérdida de funcionalidad, datos o reglas de negocio durante la
migración.

--------------------------------------------------
# 4. FUNCIONALIDADES FALTANTES
--------------------------------------------------

Buscar funcionalidades presentes en la aplicación actual pero
ausentes de:

- documentación;
- arquitectura objetivo;
- estrategia de migración.

Comparar:

Aplicación actual
vs
Documentación
vs
Arquitectura objetivo

Verificar especialmente:

- inicio de encuesta;
- selección de vivienda;
- selección de hogar;
- selección de informante;
- capítulos;
- preguntas;
- validaciones;
- cálculos;
- navegación;
- saltos;
- filtros;
- condiciones;
- GPS;
- sincronización;
- envío de información;
- recuperación;
- manejo de errores;
- trabajo offline;
- operaciones auxiliares.

Crear una matriz:

| Funcionalidad | Actual | Documentada | Arquitectura nueva | Migración | Estado |
|---|---|---|---|---|---|

--------------------------------------------------
# 5. TRAZABILIDAD DE REGLAS DE NEGOCIO
--------------------------------------------------

Este es un punto crítico.

Verificar que las reglas de negocio identificadas en:

docs/06_REGLAS_NEGOCIO.md

tengan una correspondencia clara en:

docs/08_ARQUITECTURA_OBJETIVO.md

y posteriormente en un componente de la arquitectura nueva.

Especialmente verificar:

- condiciones;
- validaciones;
- cálculos;
- dependencias;
- filtros;
- reglas de visibilidad;
- reglas de navegación;
- reglas de salto;
- reglas de limpieza;
- reglas cross-chapter.

Crear una matriz:

| Regla | Ubicación actual | Evidencia | Nuevo componente | Acción | Estado |
|---|---|---|---|---|---|

Ninguna regla crítica debe quedar sin destino.

--------------------------------------------------
# 6. CONDICIONES Y SALTOS
--------------------------------------------------

Auditar específicamente la lógica de flujo de la encuesta.

La aplicación contiene:

- capítulos;
- preguntas condicionadas;
- saltos;
- filtros;
- preguntas dependientes;
- preguntas ocultas;
- Fragment ocultos;
- navegación condicional.

Verificar nuevamente contra el código:

1. Condiciones de preguntas.

2. Saltos entre preguntas.

3. Saltos entre capítulos.

4. Preguntas que se ocultan.

5. Fragment que se ocultan.

6. Capítulos que pueden omitirse.

7. Respuestas que modifican preguntas posteriores.

8. Dependencias entre capítulos.

9. Reglas que producen navegación diferente.

10. Reglas que provocan limpieza de datos.

Determinar para cada caso:

Actual
↓
Nueva arquitectura
↓
Componente responsable

--------------------------------------------------
# 7. AUDITORÍA DE SURVEY FLOW ENGINE
--------------------------------------------------

Verificar que la arquitectura definida en:

docs/08_ARQUITECTURA_OBJETIVO.md

sea suficiente para representar la complejidad encontrada.

Auditar como mínimo:

- SurveyFlowEngine;
- ConditionEvaluator;
- NavigationResolver;
- VisibilityResolver;
- CleanupManager;
- ChapterResolver;
- SurveyGraph.

Para cada componente verificar:

- responsabilidad;
- entradas;
- salidas;
- dependencias;
- ubicación arquitectónica;
- relación con otros componentes;
- evidencia en código actual.

Verificar que no exista una responsabilidad actual importante
sin destino en estos componentes o en otra parte de Domain.

--------------------------------------------------
# 8. INDEPENDENCIA DEL SURVEY FLOW ENGINE
--------------------------------------------------

Verificar que la arquitectura propuesta mantenga el
SurveyFlowEngine independiente de Android.

Detectar si alguna responsabilidad del Engine requiere:

- Activity;
- Fragment;
- View;
- Context;
- NavController;
- FragmentManager;
- ViewPager;
- Android Framework;
- Room;
- Retrofit.

Si se encuentra alguna dependencia:

determinar si:

- es realmente necesaria;
- debe trasladarse a otra capa;
- debe introducirse una abstracción;
- constituye una inconsistencia arquitectónica.

El Engine debe representar decisiones lógicas de la encuesta,
no acciones específicas de UI o persistencia.

--------------------------------------------------
# 9. AUDITORÍA DE CLEANUP
--------------------------------------------------

Auditar especialmente las operaciones de limpieza existentes.

Buscar nuevamente:

- métodos limpiar*();
- DELETE;
- UPDATE relacionados con limpieza;
- limpieza en cascada;
- limpieza condicionada;
- limpieza cross-chapter;
- limpieza provocada por cambios de respuesta.

Verificar que cada operación tenga una correspondencia clara.

Distinguir:

A. Qué datos deben limpiarse.

B. Quién determina la limpieza.

C. Cómo se representa la decisión de limpieza.

D. Quién ejecuta la limpieza.

E. Quién persiste la limpieza.

La arquitectura debe mantener la separación:

SurveyFlowEngine
    ↓
CleanupPlan / CleanupDecision
    ↓
UseCase
    ↓
Repository
    ↓
Room

Si la documentación contradice esta separación:

marcar como INCONSISTENTE.

--------------------------------------------------
# 10. AUDITORÍA DE SURVEY STATE
--------------------------------------------------

Verificar que la arquitectura defina claramente:

- capítulo actual;
- pregunta actual;
- respuestas;
- visibilidad;
- progreso;
- navegación;
- estado de sincronización;
- estado persistido;
- estado temporal.

Revisar especialmente la representación:

answers: Map<String, String>

Verificar que esté documentado:

- tipos de respuesta;
- conversión;
- null;
- comparación;
- validación;
- interpretación por ConditionEvaluator.

Determinar si existen tipos de respuesta actuales que no puedan
representarse correctamente.

--------------------------------------------------
# 11. AUDITORÍA DE PRECEDENCIA
--------------------------------------------------

Verificar las reglas de:

SurveyGraph
y
TransitionRule

Determinar si está claramente definido:

- qué ocurre cuando ninguna regla coincide;
- qué ocurre cuando una coincide;
- qué ocurre cuando varias coinciden;
- cómo funciona priority;
- qué ocurre ante empate;
- qué ocurre ante contradicción;
- existencia de regla default;
- destinos inválidos;
- reglas duplicadas;
- reglas inalcanzables.

La resolución debe ser determinista.

Si existe alguna ambigüedad:

marcarla como PENDIENTE o BLOQUEANTE según su impacto.

--------------------------------------------------
# 12. AUDITORÍA DE PERSISTENCIA
--------------------------------------------------

Comparar:

SQLite actual
vs
Room propuesto

Verificar:

- tablas;
- columnas;
- tipos;
- claves primarias;
- claves foráneas;
- relaciones;
- índices;
- constraints;
- valores NULL;
- defaults;
- consultas;
- transacciones;
- triggers si existen;
- migraciones;
- datos existentes.

Crear matriz:

| Tabla actual | Room Entity | DAO | Relaciones | Migración | Estado |
|---|---|---|---|---|---|

Detectar cualquier tabla o estructura utilizada por el código
pero ausente del diseño Room.

--------------------------------------------------
# 13. AUDITORÍA DE CONSULTAS
--------------------------------------------------

No limitar la auditoría a las tablas.

Buscar las consultas realmente utilizadas por la aplicación.

Verificar:

- SELECT;
- INSERT;
- UPDATE;
- DELETE;
- JOIN;
- filtros;
- ordenamientos;
- transacciones;
- consultas especiales.

Determinar dónde quedará cada consulta:

- DAO;
- Repository;
- UseCase;
- otra ubicación justificada.

No asumir que una tabla documentada significa que todas sus
operaciones también están documentadas.

--------------------------------------------------
# 14. ENDPOINTS FALTANTES
--------------------------------------------------

Comparar:

API actual
vs
API propuesta

Buscar en el código:

- URLs;
- endpoints;
- métodos HTTP;
- parámetros;
- headers;
- autenticación;
- respuestas;
- errores;
- subida de archivos;
- descarga de archivos.

Crear matriz:

| Endpoint actual | Método | Uso | Nuevo API | DTO | Repository | Estado |
|---|---|---|---|---|---|---|

Detectar:

- endpoints no documentados;
- endpoints sin destino;
- parámetros faltantes;
- respuestas no modeladas;
- errores no contemplados.

--------------------------------------------------
# 15. AUDITORÍA DE RED
--------------------------------------------------

Verificar que la arquitectura nueva contemple:

- autenticación;
- SSL/TLS;
- hostname;
- timeouts;
- retries;
- errores HTTP;
- errores de conectividad;
- conectividad intermitente;
- subida de archivos;
- descarga;
- sincronización;
- ejecución fuera de Main Thread.

Comparar con los problemas identificados en:

docs/07_RIESGOS_Y_ANR.md

Toda problemática crítica de red debe tener una estrategia.

--------------------------------------------------
# 16. PANTALLAS Y PRESENTATION
--------------------------------------------------

Comparar:

UI actual
vs
Presentation propuesta.

NO asumir que:

cada Fragment actual
=
cada Fragment nuevo.

Determinar para cada grupo funcional:

- qué existe actualmente;
- qué se conserva;
- qué se puede combinar;
- qué se puede dividir;
- qué queda pendiente;
- qué componente nuevo lo reemplazará.

Crear matriz:

| Actual | Función | Nuevo | Acción | Estado |
|---|---|---|---|---|

--------------------------------------------------
# 17. AUDITORÍA DE UX
--------------------------------------------------

Verificar que la arquitectura no obligue a reproducir
mecánicamente la UI actual.

La aplicación contiene:

- capítulos;
- preguntas;
- condiciones;
- saltos;
- preguntas ocultas;
- Fragment ocultos;
- navegación condicional.

Determinar si existen limitaciones arquitectónicas que impidan
una futura mejora de visualización.

No es obligatorio definir una nueva UI completa en esta etapa.

Pero debe quedar claro que la arquitectura permite posteriormente:

- combinar preguntas;
- dividir preguntas;
- cambiar navegación;
- utilizar grupos;
- utilizar pasos;
- utilizar indicadores de progreso;
- cambiar la relación entre capítulo y pantalla.

La decisión de UX podrá validarse durante el piloto.

--------------------------------------------------
# 18. DEPENDENCIAS OCULTAS
--------------------------------------------------

Realizar análisis recursivo de dependencias.

Buscar:

- llamadas indirectas;
- callbacks;
- listeners;
- servicios globales;
- variables estáticas;
- SharedPreferences;
- acceso directo a SQLite;
- acceso directo a red;
- referencias a Activity;
- referencias a Fragment;
- dependencias entre Controllers;
- dependencias entre capítulos;
- dependencias cross-chapter;
- dependencias implícitas.

Especialmente identificar lógica que actualmente parezca pertenecer
a UI pero que realmente implemente reglas de negocio.

--------------------------------------------------
# 19. DEPENDENCIAS CROSS-CHAPTER
--------------------------------------------------

Auditar específicamente dependencias entre capítulos.

Buscar casos donde:

- una respuesta de un capítulo afecta otro;
- una condición depende de información anterior;
- una limpieza afecta otro capítulo;
- una pregunta modifica el flujo futuro;
- un capítulo determina si otro se muestra.

Crear matriz:

| Origen | Destino | Tipo de dependencia | Regla | Nuevo componente | Estado |
|---|---|---|---|---|---|

Ninguna dependencia crítica debe quedar implícita.

--------------------------------------------------
# 20. RIESGOS
--------------------------------------------------

Revisar:

docs/07_RIESGOS_Y_ANR.md

Verificar que cada riesgo crítico tenga:

- causa conocida;
- impacto;
- estrategia de mitigación;
- responsable arquitectónico;
- forma de validación.

Especialmente:

- ANR;
- operaciones en Main Thread;
- SQLite;
- red;
- GPS;
- memoria;
- ciclos de vida;
- pérdida de estado;
- sincronización;
- concurrencia.

--------------------------------------------------
# 21. CICLO DE VIDA Y RECUPERACIÓN
--------------------------------------------------

Verificar cómo la arquitectura manejará:

- rotación;
- recreación de Activity;
- recreación de Fragment;
- process death;
- cierre inesperado;
- recuperación de encuesta;
- recuperación de respuestas;
- recuperación de navegación.

Determinar claramente:

qué se mantiene en memoria
vs
qué se persiste.

Verificar que:

estado de encuesta
+
respuestas
+
progreso
+
navegación

puedan recuperarse de forma consistente.

--------------------------------------------------
# 22. HILT
--------------------------------------------------

Si Hilt fue seleccionado:

verificar que las dependencias principales tengan una estrategia
de inyección.

No implementar.

Solamente comprobar que la arquitectura tenga definidos:

- DatabaseModule;
- NetworkModule;
- RepositoryModule;
- otros módulos necesarios.

No crear módulos innecesarios.

--------------------------------------------------
# 23. COROUTINES / FLOW / STATEFLOW
--------------------------------------------------

Verificar que la arquitectura defina correctamente:

- operaciones suspend;
- Dispatchers.IO;
- Dispatchers.Default;
- Main;
- Flow;
- StateFlow;
- cancelación;
- ciclo de vida;
- errores.

Verificar especialmente:

- SQLite;
- red;
- GPS;
- sincronización;
- tareas largas.

Detectar cualquier operación potencialmente bloqueante que no
tenga estrategia.

--------------------------------------------------
# 24. MAPA COMPLETO ACTUAL → NUEVO
--------------------------------------------------

Crear una matriz global de migración.

Como mínimo incluir:

- Activities;
- Fragments;
- Controllers;
- Services;
- DAOs;
- Models;
- SQLite;
- HTTP clients;
- endpoints;
- validaciones;
- condiciones;
- saltos;
- visibilidad;
- limpieza;
- navegación;
- preferencias;
- GPS;
- sincronización;
- utilitarios críticos.

Formato:

| Componente actual | Responsabilidad | Nuevo componente | Capa | Acción | Validación | Estado |
|---|---|---|---|---|---|---|

Ningún componente crítico debe quedar sin una acción.

Acciones permitidas:

- MIGRAR
- REEMPLAZAR
- CONSOLIDAR
- DIVIDIR
- ELIMINAR
- MANTENER TEMPORALMENTE
- PENDIENTE DE ANÁLISIS

--------------------------------------------------
# 25. TRAZABILIDAD COMPLETA
--------------------------------------------------

Para los componentes críticos demostrar la cadena:

Código actual
↓
Regla / funcionalidad
↓
Arquitectura objetivo
↓
Nuevo componente
↓
Validación

Crear una tabla:

| Elemento actual | Regla/función | Arquitectura nueva | Validación | Estado |
|---|---|---|---|---|

Considerar especialmente:

- condiciones;
- saltos;
- visibilidad;
- limpieza;
- navegación;
- cálculos;
- persistencia;
- sincronización.

--------------------------------------------------
# 26. DECISIONES PENDIENTES
--------------------------------------------------

Crear:

| # | Decisión | Impacto | Evidencia requerida | Responsable | Estado |
|---|---|---|---|---|---|

Diferenciar:

- decisión pendiente pero no bloqueante;
- decisión pendiente bloqueante.

No considerar que todas las decisiones pendientes impiden
comenzar la implementación.

--------------------------------------------------
# 27. PILOTO Y VALIDACIÓN
--------------------------------------------------

Verificar que la arquitectura contemple una estrategia de
validación antes de migrar masivamente.

El piloto definido en la arquitectura debe permitir validar:

- condiciones;
- saltos;
- visibilidad;
- limpieza;
- navegación;
- estado;
- persistencia;
- sincronización;
- comportamiento funcional;
- experiencia de usuario.

Verificar especialmente la estrategia:

Legacy
vs
New Engine

y, cuando sea viable:

Shadow Engine / Dual Execution.

Determinar qué comportamientos deben compararse.

No implementar esta estrategia durante la auditoría.

--------------------------------------------------
# 28. CRITERIO DE PREPARACIÓN PARA IMPLEMENTAR
--------------------------------------------------

NO considerar que el proyecto está listo únicamente porque
existan los documentos.

Para determinar:

LISTO PARA IMPLEMENTAR = SÍ

debe cumplirse como mínimo:

1. Las funcionalidades críticas están identificadas.

2. Las reglas críticas están trazadas.

3. Las condiciones están trazadas.

4. Los saltos están trazados.

5. La visibilidad dinámica está trazada.

6. La limpieza está trazada.

7. Las dependencias cross-chapter están identificadas.

8. Las tablas están trazadas.

9. Las consultas críticas están trazadas.

10. Los endpoints están trazados.

11. Las pantallas críticas tienen estrategia.

12. El estado de la encuesta está definido.

13. La navegación lógica está definida.

14. La separación Engine / Android está validada.

15. Los riesgos críticos tienen mitigación.

16. Los componentes importantes tienen mapa Actual → Nuevo.

17. No existen contradicciones críticas entre documentos.

18. Las decisiones bloqueantes están resueltas.

19. Existe una estrategia de validación para el piloto.

--------------------------------------------------
# 29. BLOQUEADORES
--------------------------------------------------

Identificar explícitamente cualquier:

## BLOQUEADOR CRÍTICO

que impida comenzar la implementación.

Ejemplos:

- regla crítica sin destino;
- tabla crítica sin migración;
- endpoint crítico sin diseño;
- flujo crítico no comprendido;
- pérdida potencial de datos;
- comportamiento no determinista;
- dependencia arquitectónica incompatible;
- operación crítica sin estrategia de concurrencia;
- navegación crítica no definida.

No marcar como bloqueador algo que pueda resolverse durante
la implementación sin riesgo significativo.

--------------------------------------------------
# 30. RESULTADO FINAL
--------------------------------------------------

Generar:

docs/09_AUDITORIA_PRE_IMPLEMENTACION.md

El documento debe contener:

1. Resumen ejecutivo.

2. Estado general de preparación.

3. Funcionalidades faltantes.

4. Reglas faltantes.

5. Condiciones y saltos.

6. Auditoría del SurveyFlowEngine.

7. Auditoría de Cleanup.

8. Auditoría de SurveyState.

9. Reglas de precedencia.

10. Persistencia.

11. Consultas.

12. Endpoints.

13. Red.

14. Presentation.

15. UX.

16. Dependencias ocultas.

17. Dependencias cross-chapter.

18. Riesgos.

19. Ciclo de vida y recuperación.

20. Coroutines / Flow / StateFlow.

21. Mapa Actual → Nuevo.

22. Trazabilidad.

23. Decisiones pendientes.

24. Estrategia de validación.

25. Bloqueadores.

26. Recomendación final.

--------------------------------------------------
# 31. RESPUESTA OBLIGATORIA
--------------------------------------------------

Al finalizar responder explícitamente:

### A. ¿Está listo para implementar?

SI / NO

### B. ¿Qué falta?

Enumerar.

### C. ¿Qué riesgos críticos existen?

Enumerar.

### D. ¿Qué información falta?

Enumerar.

### E. ¿Qué decisiones deben aprobarse?

Enumerar.

### F. ¿Qué bloqueadores existen?

Enumerar.

### G. ¿Qué puede dejarse para el piloto?

Enumerar.

--------------------------------------------------
# 32. REGLA DE HONESTIDAD
--------------------------------------------------

NO declarar:

"LISTO PARA IMPLEMENTAR"

simplemente porque la documentación parezca completa.

La conclusión debe basarse en la evidencia encontrada en:

- documentación;
- código fuente;
- trazabilidad.

Si no existe evidencia suficiente:

indicar:

`NO DETERMINADO`

No inventar.

Si existen contradicciones:

reportarlas.

Si existen reglas cuyo comportamiento no puede determinarse:

reportarlas.

--------------------------------------------------
# 33. REGLA DE NO IMPLEMENTACIÓN
--------------------------------------------------

Esta etapa es exclusivamente de auditoría.

NO:

- modificar código;
- crear código Kotlin;
- migrar Java;
- modificar Gradle;
- agregar dependencias;
- crear Room;
- crear Retrofit;
- crear Hilt;
- crear ViewModels;
- crear Fragments nuevos;
- modificar la base de datos;
- modificar endpoints.

Únicamente analizar y documentar.

--------------------------------------------------
# 34. REGLA DE NO AVANZAR
--------------------------------------------------

NO iniciar la implementación.

NO iniciar la migración de Cap500.

NO crear el piloto.

NO modificar componentes existentes.

La auditoría debe terminar primero.

Si:

LISTO PARA IMPLEMENTAR = NO

detenerse y reportar los bloqueadores.

Si:

LISTO PARA IMPLEMENTAR = SI

detenerse igualmente y esperar aprobación explícita.

--------------------------------------------------
# 35. RESULTADO
--------------------------------------------------

Generar:

docs/09_AUDITORIA_PRE_IMPLEMENTACION.md

No generar documentos alternativos.

No crear:

- *_REVISION.md
- *_FINAL.md
- *_V2.md

El archivo:

docs/09_AUDITORIA_PRE_IMPLEMENTACION.md

debe ser la única versión oficial de la auditoría.

--------------------------------------------------
FIN DEL PROMPT 09
--------------------------------------------------