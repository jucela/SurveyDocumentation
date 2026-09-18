# 08 - ARQUITECTURA OBJETIVO

## OBJETIVO

Diseñar la arquitectura objetivo de la nueva aplicación a partir
del conocimiento obtenido durante las etapas anteriores.

La arquitectura objetivo NO debe ser una conversión mecánica de
la arquitectura actual.

Debe conservar:

- funcionalidad;
- reglas de negocio;
- comportamiento funcional;
- datos;
- validaciones;
- cálculos;
- flujos;
- condiciones;
- saltos;
- dependencias funcionales;
- procesos de sincronización;
- comportamiento requerido de la encuesta.

Sin embargo, puede rediseñar completamente:

- arquitectura;
- clases;
- paquetes;
- persistencia;
- acceso a datos;
- red;
- concurrencia;
- navegación;
- presentación;
- experiencia de usuario;
- forma de visualizar los cuestionarios.

La arquitectura objetivo debe ser el resultado de un análisis
técnico del proyecto real.

NO asumir que las tecnologías o patrones propuestos inicialmente
son necesariamente los mejores.

--------------------------------------------------
# 1. INFORMACIÓN DE ENTRADA
--------------------------------------------------

Utilizar como fuente principal todos los documentos aprobados de
las etapas anteriores:

- docs/01_INVENTARIO_TECNICO.md
- docs/02_ARQUITECTURA_ACTUAL.md
- docs/03_FLUJOS_FUNCIONALES.md
- docs/04_BASE_DATOS.md
- docs/05_API_Y_RED.md
- docs/06_REGLAS_NEGOCIO.md
- docs/07_RIESGOS_Y_ANR.md

También revisar el código fuente cuando sea necesario para
verificar información importante.

NO asumir que la documentación anterior es completa si el código
demuestra lo contrario.

Si existe contradicción entre documentación y código:

1. identificar la contradicción;
2. revisar el código;
3. documentar la diferencia;
4. determinar cuál información es válida;
5. actualizar la propuesta arquitectónica.

No inventar información.

Si una información no puede determinarse:

`NO DETERMINADO EN EL CÓDIGO`

--------------------------------------------------
# 2. PRINCIPIO FUNDAMENTAL DE LA ARQUITECTURA
--------------------------------------------------

La arquitectura objetivo debe responder a las necesidades reales
de esta aplicación de encuesta.

NO diseñar una arquitectura únicamente porque:

- sea moderna;
- sea popular;
- sea recomendada en tutoriales;
- sea utilizada en otros proyectos;
- sea considerada una buena práctica de manera general.

Cada decisión arquitectónica debe tener una justificación
relacionada con las características reales de la aplicación.

Una tecnología o patrón inicialmente propuesto puede:

- mantenerse;
- modificarse;
- reemplazarse;
- descartarse.

También se puede proponer una tecnología o patrón que no estuviera
en la propuesta inicial si el análisis demuestra que es necesario.

--------------------------------------------------
# 3. TECNOLOGÍAS Y PATRONES CANDIDATOS
--------------------------------------------------

Considerar inicialmente como candidatos:

- Kotlin
- Clean Architecture
- MVVM
- MVI
- ViewModel
- Room
- Dagger Hilt
- Coroutines
- Flow
- StateFlow
- Retrofit
- OkHttp
- Navigation Component
- XML
- ViewBinding
- RecyclerView

NO utilizar Jetpack Compose.

La exclusión de Jetpack Compose es una restricción del proyecto.

Las demás tecnologías y patrones son CANDIDATOS y deben ser
evaluados antes de convertirse en decisiones arquitectónicas.

--------------------------------------------------
# 4. CARACTERÍSTICAS PARTICULARES DE LA ENCUESTA
--------------------------------------------------

La aplicación corresponde a una encuesta estructurada en capítulos.

La encuesta NO consiste simplemente en formularios independientes.

Los capítulos contienen preguntas que pueden presentar:

- condiciones;
- saltos;
- filtros;
- dependencias entre preguntas;
- preguntas condicionadas por respuestas anteriores;
- cambios de flujo;
- navegación condicional;
- preguntas que deben ocultarse;
- Fragment que deben ocultarse;
- diferentes recorridos dependiendo de las respuestas.

En la aplicación actual existen situaciones en las que:

1. Algunas preguntas se ocultan dentro del mismo Fragment.

2. Algunos Fragment completos se ocultan dependiendo de las
   condiciones y del flujo de la encuesta.

3. Algunas respuestas pueden afectar preguntas posteriores.

4. El flujo puede variar dependiendo de las respuestas ingresadas.

Por lo tanto, la arquitectura objetivo debe considerar que:

- las preguntas no necesariamente son lineales;
- los capítulos no necesariamente tienen el mismo recorrido;
- no todas las preguntas son siempre visibles;
- no todos los Fragment deben necesariamente mostrarse;
- la navegación puede depender del estado de la encuesta;
- la visibilidad puede depender de respuestas anteriores.

NO asumir que la estructura visual actual representa necesariamente
la mejor forma de implementar la encuesta.

--------------------------------------------------
# 5. EVALUACIÓN DEL FLUJO DE LA ENCUESTA
--------------------------------------------------

Analizar cómo debe representarse el flujo de la encuesta en la
nueva arquitectura.

Considerar:

- capítulo actual;
- pregunta actual;
- respuestas;
- preguntas dependientes;
- condiciones;
- saltos;
- filtros;
- preguntas visibles;
- preguntas ocultas;
- Fragment visibles;
- Fragment ocultos;
- siguiente pregunta;
- pregunta anterior;
- siguiente capítulo;
- capítulo anterior;
- navegación condicional;
- validaciones;
- recuperación del estado;
- progreso de la encuesta.

Determinar dónde deben residir estas responsabilidades.

Evaluar si deben pertenecer a:

- Domain;
- Use Cases;
- ViewModel;
- componente de navegación;
- motor de reglas;
- State Machine;
- componente específico de flujo de encuesta;
- otra abstracción.

NO seleccionar ninguna alternativa automáticamente.

La decisión debe basarse en la complejidad real encontrada en
el código y en los documentos anteriores.

--------------------------------------------------
# 6. EVALUACIÓN DE UN POSIBLE MOTOR DE REGLAS
--------------------------------------------------

Determinar si la aplicación requiere una abstracción específica
para gestionar las condiciones, saltos, filtros y dependencias
de las preguntas.

Evaluar alternativas como:

- reglas distribuidas entre Use Cases;
- reglas dentro de Domain;
- motor de reglas;
- componente de flujo de encuesta;
- State Machine;
- estrategia basada en estados;
- combinación de alternativas;
- otra alternativa técnicamente justificada.

Para cada alternativa analizar:

- complejidad;
- mantenibilidad;
- testabilidad;
- trazabilidad;
- facilidad de migración;
- rendimiento;
- riesgo de sobrearquitectura;
- facilidad para agregar nuevos capítulos;
- facilidad para modificar preguntas;
- facilidad para modificar condiciones;
- reutilización;
- funcionamiento offline.

NO crear un motor de reglas solamente porque parezca una solución
arquitectónicamente elegante.

Debe existir una justificación basada en la complejidad real
de la aplicación.

--------------------------------------------------
# 7. EVALUACIÓN CRÍTICA DEL PATRÓN DE PRESENTACIÓN
--------------------------------------------------

Evaluar como mínimo:

- MVVM;
- MVI;
- MVP;
- otros patrones que resulten técnicamente pertinentes.

NO seleccionar MVVM automáticamente.

Determinar cuál patrón se adapta mejor a las características
reales de la aplicación.

La evaluación debe considerar especialmente:

- formularios extensos;
- cuestionarios;
- capítulos;
- múltiples preguntas;
- preguntas condicionadas;
- saltos;
- filtros;
- preguntas dependientes;
- visibilidad dinámica;
- ocultamiento de preguntas;
- ocultamiento de Fragment;
- navegación condicional;
- estado del cuestionario;
- estado de las respuestas;
- actualización de preguntas cuando cambia una respuesta;
- recuperación del estado;
- recreación de Activity o Fragment;
- rotación del dispositivo;
- funcionamiento offline;
- sincronización;
- GPS;
- validaciones;
- cálculos;
- errores;
- concurrencia;
- pruebas;
- mantenibilidad.

Para cada patrón indicar:

- ventajas;
- desventajas;
- complejidad;
- impacto en la migración;
- facilidad de pruebas;
- manejo del estado;
- manejo de eventos;
- manejo de navegación;
- manejo de condiciones;
- manejo de preguntas dinámicas;
- riesgos.

Si MVVM es seleccionado:

explicar POR QUÉ es adecuado para esta aplicación.

Si MVI es seleccionado:

explicar POR QUÉ es adecuado para esta aplicación.

Si se recomienda una combinación de patrones:

explicar exactamente qué responsabilidad corresponde a cada uno.

--------------------------------------------------
# 8. EVALUACIÓN DE CLEAN ARCHITECTURE
--------------------------------------------------

Evaluar si Clean Architecture es adecuada para este proyecto.

NO aplicarla de manera dogmática.

Determinar:

- qué capas son realmente necesarias;
- responsabilidades de cada capa;
- dependencias;
- nivel de aislamiento;
- testabilidad;
- mantenibilidad;
- complejidad introducida;
- riesgo de sobrearquitectura;
- beneficio para una migración progresiva.

Evaluar si se requiere:

Presentation
↓
Domain
↓
Data

o una variante diferente.

Si se recomienda una arquitectura simplificada:

documentarla y justificarla.

Si se recomienda mantener las tres capas:

justificar técnicamente por qué.

--------------------------------------------------
# 9. EVALUACIÓN DE PERSISTENCIA
--------------------------------------------------

Evaluar las alternativas de persistencia.

Como mínimo considerar:

- Room;
- SQLite directo;
- otras alternativas técnicamente pertinentes.

Analizar:

- estructura actual de SQLite;
- tablas;
- relaciones;
- consultas;
- transacciones;
- índices;
- volumen de datos;
- funcionamiento offline;
- sincronización;
- migraciones;
- compatibilidad;
- complejidad de migración.

Determinar si Room debe:

- adoptarse;
- adoptarse con modificaciones;
- utilizarse parcialmente;
- reemplazarse;
- mantenerse otra estrategia temporal durante la migración.

Justificar la decisión.

--------------------------------------------------
# 10. EVALUACIÓN DE RED
--------------------------------------------------

Evaluar:

- Retrofit;
- OkHttp;
- implementación HTTP actual;
- otras alternativas pertinentes.

Analizar:

- cantidad de endpoints;
- autenticación;
- SSL/TLS;
- timeouts;
- errores;
- reintentos;
- conectividad intermitente;
- subida de archivos;
- descarga de archivos;
- sincronización;
- serialización;
- ejecución fuera del Main Thread.

Determinar si Retrofit + OkHttp es la mejor alternativa.

No asumir que debe utilizarse solamente porque sea una combinación
común en aplicaciones Android modernas.

--------------------------------------------------
# 11. EVALUACIÓN DE INYECCIÓN DE DEPENDENCIAS
--------------------------------------------------

Evaluar:

- Dagger Hilt;
- inyección manual;
- otras alternativas pertinentes.

Considerar:

- tamaño del proyecto;
- cantidad de dependencias;
- cantidad de módulos;
- complejidad;
- testabilidad;
- mantenibilidad;
- impacto de migración.

Determinar si Hilt aporta suficiente beneficio para justificar
su incorporación.

--------------------------------------------------
# 12. EVALUACIÓN DE CONCURRENCIA Y ESTADO
--------------------------------------------------

Evaluar:

- Coroutines;
- Flow;
- StateFlow;
- LiveData cuando resulte pertinente;
- otras alternativas.

Considerar:

- operaciones SQLite;
- HTTP;
- GPS;
- sincronización;
- tareas largas;
- actualización de UI;
- cancelación;
- ciclo de vida;
- recuperación ante errores;
- riesgo de ANR;
- estado de la encuesta.

Determinar cómo se administrará el estado de:

- capítulo;
- pregunta;
- respuestas;
- condiciones;
- navegación;
- progreso;
- errores;
- sincronización.

--------------------------------------------------
# 13. SEPARACIÓN ENTRE REGLAS DE ENCUESTA Y UI
--------------------------------------------------

La arquitectura debe separar claramente:

LÓGICA DE LA ENCUESTA

de

PRESENTACIÓN DE LA ENCUESTA.

Las reglas de negocio y flujo deben poder determinar:

- qué pregunta corresponde;
- si una pregunta debe mostrarse;
- si una pregunta debe ocultarse;
- cuál es la siguiente pregunta;
- cuál es la pregunta anterior;
- si debe realizarse un salto;
- si debe mostrarse un capítulo;
- si un capítulo debe omitirse;
- si un Fragment o pantalla debe mostrarse;
- qué validaciones corresponden;
- qué respuestas afectan preguntas posteriores.

La UI debe encargarse principalmente de:

- presentar el estado;
- recibir respuestas;
- mostrar errores;
- mostrar progreso;
- permitir navegación;
- presentar la información al encuestador.

Evitar que la lógica de condiciones y saltos quede acoplada
directamente a Fragment o Activity.

--------------------------------------------------
# 14. REDISEÑO DE LA EXPERIENCIA DE USUARIO
--------------------------------------------------

La nueva aplicación NO está obligada a conservar la misma forma
de visualización utilizada actualmente.

La migración debe conservar la funcionalidad y las reglas de la
encuesta, pero puede rediseñar completamente la experiencia
de usuario.

NO asumir que:

- cada capítulo debe corresponder a un Fragment;
- cada conjunto de preguntas debe permanecer en una pantalla;
- las preguntas deben mostrarse exactamente como actualmente;
- los saltos deben implementarse mediante ocultamiento de vistas;
- la navegación actual es necesariamente la mejor;
- la estructura actual de Fragment representa la mejor experiencia.

Analizar si existe una forma mejor de visualizar y responder
la encuesta.

--------------------------------------------------
# 15. ALTERNATIVAS DE VISUALIZACIÓN
--------------------------------------------------

Evaluar, cuando sean pertinentes:

- una pregunta por pantalla;
- grupos de preguntas relacionadas;
- secciones dentro de un capítulo;
- navegación por pasos;
- formularios dinámicos;
- indicadores de progreso;
- navegación anterior/siguiente;
- resumen de respuestas;
- presentación dinámica;
- agrupación de preguntas dependientes;
- componentes especializados por tipo de pregunta;
- diferentes estrategias para preguntas simples y complejas;
- otras alternativas justificadas.

NO implementar ninguna alternativa solamente por ser visualmente
más moderna.

La propuesta debe mejorar realmente:

- comprensión;
- velocidad de captura;
- navegación;
- prevención de errores;
- corrección de respuestas;
- visibilidad de información;
- experiencia del encuestador;
- mantenibilidad.

--------------------------------------------------
# 16. EVALUACIÓN DE LA EXPERIENCIA ACTUAL
--------------------------------------------------

Analizar la forma actual de visualización y determinar:

- qué funciona correctamente;
- qué debe conservarse;
- qué genera complejidad;
- qué genera errores;
- qué dificulta la navegación;
- qué dificulta responder preguntas;
- qué genera demasiadas interacciones;
- qué genera confusión;
- qué puede mejorarse.

Si no existe evidencia suficiente para determinar que una parte
de la UI debe cambiar:

indicar:

`NO DETERMINADO`

No inventar problemas de UX.

--------------------------------------------------
# 17. PROPUESTA DE NUEVA EXPERIENCIA
--------------------------------------------------

Si existe una forma significativamente mejor de visualizar
los cuestionarios:

DEBE proponerse.

La propuesta debe explicar:

1. Cómo funciona actualmente.

2. Qué problema presenta la forma actual.

3. Qué alternativa se propone.

4. Cómo se representan los capítulos.

5. Cómo se representan las preguntas.

6. Cómo se representan las respuestas.

7. Cómo funcionan las condiciones.

8. Cómo funcionan los saltos.

9. Cómo se manejan las preguntas ocultas.

10. Cómo se manejan los capítulos o Fragment omitidos.

11. Cómo funciona la navegación.

12. Cómo se muestra el progreso.

13. Cómo se conserva el estado.

14. Cómo se evita perder respuestas.

15. Cómo se corrigen respuestas anteriores.

16. Ventajas frente a la UI actual.

17. Desventajas.

18. Riesgos.

19. Impacto arquitectónico.

20. Impacto en la implementación.

21. Recomendación final.

La nueva experiencia puede ser completamente diferente a la
actual siempre que conserve la funcionalidad y reglas de la
encuesta.

--------------------------------------------------
# 18. EVALUACIÓN DE NAVEGACIÓN
--------------------------------------------------

Diseñar la navegación según la arquitectura y experiencia
seleccionadas.

Definir:

- destinos;
- capítulos;
- preguntas;
- argumentos;
- diálogos;
- back stack;
- navegación condicional;
- recuperación del estado;
- navegación hacia atrás;
- saltos;
- capítulos omitidos;
- preguntas omitidas.

No asumir que cada Fragment actual debe convertirse en un destino
de Navigation Component.

Determinar si la nueva navegación requiere una estructura
diferente.

--------------------------------------------------
# 19. PRESENTATION
--------------------------------------------------

Definir la capa de presentación de acuerdo con el patrón
seleccionado durante la evaluación.

NO asumir automáticamente:

Fragment
↓
ViewModel
↓
UiState
↓
UseCase

Si se selecciona MVVM, definir:

- Fragment;
- ViewModel;
- UiState;
- eventos;
- navegación;
- comunicación con Domain.

Si se selecciona otro patrón:

documentar su estructura.

La presentación debe evitar contener reglas complejas de negocio
o de flujo de encuesta.

--------------------------------------------------
# 20. DOMAIN
--------------------------------------------------

Definir:

- Domain Models;
- Repository interfaces;
- Use Cases;
- reglas de negocio;
- reglas de encuesta;
- condiciones;
- flujo cuando corresponda.

No crear Use Cases innecesarios.

No crear clases solamente para cumplir una estructura teórica.

Cada componente debe tener una responsabilidad clara.

--------------------------------------------------
# 21. DATA
--------------------------------------------------

Definir, cuando corresponda:

- Entity;
- DAO;
- RepositoryImpl;
- LocalDataSource;
- RemoteDataSource;
- DTO;
- Mapper.

Determinar qué componentes son realmente necesarios.

No crear capas o clases sin justificación.

--------------------------------------------------
# 22. ROOM
--------------------------------------------------

Si Room es seleccionado:

definir:

- Database;
- Entities;
- DAOs;
- relaciones;
- índices;
- migraciones;
- estrategia de actualización;
- estrategia de recuperación.

Explicar cómo se realizará la migración desde la persistencia actual.

--------------------------------------------------
# 23. NETWORK
--------------------------------------------------

Definir la arquitectura de red seleccionada.

Si Retrofit + OkHttp son seleccionados:

Retrofit
↓
API
↓
DTO
↓
RemoteDataSource
↓
Repository

Si se selecciona otra alternativa:

documentarla.

Definir:

- autenticación;
- errores;
- timeouts;
- SSL/TLS;
- reintentos;
- serialización;
- conectividad intermitente;
- subida de archivos;
- descarga de archivos;
- sincronización.

--------------------------------------------------
# 24. HILT
--------------------------------------------------

Si Hilt es seleccionado:

definir solamente los módulos necesarios.

Ejemplos:

- DatabaseModule;
- NetworkModule;
- RepositoryModule;
- otros necesarios.

No crear módulos innecesarios.

--------------------------------------------------
# 25. COROUTINES, FLOW Y ESTADO
--------------------------------------------------

Si se seleccionan:

- Coroutines;
- Flow;
- StateFlow;

definir su utilización.

Determinar:

- operaciones suspend;
- Dispatchers.IO;
- Dispatchers.Default;
- Main;
- Flow;
- StateFlow;
- cancelación;
- ciclo de vida;
- manejo de errores.

No bloquear Main Thread.

Definir especialmente cómo se representará el estado de la encuesta.

--------------------------------------------------
# 26. MODELOS
--------------------------------------------------

Separar cuando corresponda:

- Domain Model;
- Data Entity;
- DTO;
- UI Model.

Definir mappers únicamente cuando aporten una separación útil.

No crear modelos duplicados innecesariamente.

--------------------------------------------------
# 27. MAPA ACTUAL → NUEVO
--------------------------------------------------

Crear una matriz de migración.

Ejemplo:

| Actual | Nuevo candidato | Acción | Justificación |
|---|---|---|---|
| DAO | Room DAO | Evaluar | |
| Service | UseCase + Repository | Evaluar | |
| HTTP Client | Retrofit + OkHttp | Evaluar | |
| Model | Domain / Entity / DTO | Evaluar | |
| Activity | Fragment / Activity / otro | Evaluar | |
| Controller | ViewModel / UseCase / Domain / otro | Evaluar | |
| Lógica de saltos | Domain / Rule Engine / State Machine / otro | Evaluar | |
| Lógica de visibilidad | Domain / UI State / otro | Evaluar | |

NO realizar una equivalencia mecánica.

Cada transformación debe tener una justificación.

--------------------------------------------------
# 28. ESTRUCTURA DE PAQUETES
--------------------------------------------------

Definir la estructura completa de paquetes.

La estructura debe reflejar la arquitectura realmente seleccionada.

No asumir que:

presentation/
domain/
data/

es suficiente.

Definir:

- paquetes;
- responsabilidades;
- dependencias permitidas;
- dependencias prohibidas;
- reglas de dirección de dependencias.

--------------------------------------------------
# 29. DIAGRAMAS
--------------------------------------------------

Generar diagramas conceptuales para:

- arquitectura;
- capas;
- flujo de encuesta;
- capítulos;
- preguntas;
- condiciones;
- saltos;
- navegación;
- datos;
- persistencia;
- red;
- dependencias.

Los diagramas deben reflejar la arquitectura propuesta.

No generar diagramas genéricos.

--------------------------------------------------
# 30. MATRIZ FINAL DE DECISIONES
--------------------------------------------------

Generar una matriz final:

| Tecnología / Patrón | Alternativas evaluadas | Decisión | Justificación | Riesgos |
|---|---|---|---|---|

La decisión debe ser una de:

- ADOPTAR
- ADOPTAR CON MODIFICACIONES
- REEMPLAZAR
- NO UTILIZAR
- PENDIENTE DE VALIDACIÓN

Como mínimo evaluar:

- Kotlin;
- Clean Architecture;
- patrón de presentación;
- ViewModel;
- Room;
- Hilt;
- Coroutines;
- Flow;
- StateFlow;
- Retrofit;
- OkHttp;
- Navigation Component;
- XML;
- ViewBinding;
- RecyclerView;
- estrategia de flujo de encuesta;
- estrategia de condiciones;
- estrategia de saltos;
- estrategia de navegación;
- estrategia de visualización.

--------------------------------------------------
# 31. DECISIONES ARQUITECTÓNICAS OBLIGATORIAS
--------------------------------------------------

La documentación final debe responder explícitamente:

1. ¿Cuál es el patrón de presentación seleccionado?

2. ¿Por qué es el mejor para esta aplicación?

3. ¿Qué alternativas fueron evaluadas?

4. ¿Por qué fueron descartadas?

5. ¿Clean Architecture es adecuada?

6. ¿Qué capas son realmente necesarias?

7. ¿Cómo se representará el estado de la encuesta?

8. ¿Dónde estarán las reglas de condiciones?

9. ¿Dónde estarán las reglas de saltos?

10. ¿Dónde estará la lógica de visibilidad?

11. ¿Cómo se determinará la siguiente pregunta?

12. ¿Cómo se determinará el siguiente capítulo?

13. ¿Cómo se manejarán preguntas dependientes?

14. ¿Cómo se manejarán Fragment o pantallas omitidas?

15. ¿Se necesita un motor de reglas?

16. ¿Se necesita una State Machine?

17. ¿Cómo se manejará la navegación?

18. ¿Cómo se conservará el estado?

19. ¿Cómo funcionará offline?

20. ¿Cómo se manejará la sincronización?

21. ¿Cómo se evitarán ANR?

22. ¿Room es la mejor alternativa de persistencia?

23. ¿Retrofit + OkHttp es la mejor alternativa de red?

24. ¿Hilt es necesario?

25. ¿Coroutines + Flow + StateFlow son adecuados?

26. ¿Existe una mejor forma de visualizar los cuestionarios?

27. ¿Existe una mejor forma de responder las preguntas?

28. ¿La nueva UI debería mantener o modificar la estructura actual?

29. ¿Qué partes de la UI actual deben conservarse?

30. ¿Qué partes deberían rediseñarse?

--------------------------------------------------
# 32. REGLA DE NO SOBREARQUITECTURA
--------------------------------------------------

NO crear una arquitectura innecesariamente compleja.

Cada:

- capa;
- clase;
- interfaz;
- Use Case;
- Repository;
- Mapper;
- componente;
- motor;
- abstracción;

debe tener una razón técnica.

La arquitectura debe ser suficientemente robusta para el proyecto,
pero no más compleja de lo necesario.

--------------------------------------------------
# 33. REGLA DE MIGRACIÓN PROGRESIVA
--------------------------------------------------

La arquitectura objetivo debe permitir una migración incremental.

Considerar:

- coexistencia temporal con componentes antiguos;
- migración por módulos;
- migración por capítulos;
- migración por funcionalidades;
- migración de persistencia;
- migración de red;
- migración de UI;
- validación progresiva.

Identificar dependencias que dificulten la migración.

Proponer estrategias para reducir dichas dependencias.

--------------------------------------------------
# 34. REGLA DE NO IMPLEMENTACIÓN
--------------------------------------------------

Esta etapa es exclusivamente de análisis y diseño.

NO:

- modificar código fuente;
- crear código Kotlin de producción;
- crear clases reales del proyecto;
- modificar Gradle;
- modificar dependencias;
- crear Room real;
- crear Retrofit real;
- crear Hilt real;
- migrar código Java;
- eliminar código antiguo.

Únicamente diseñar y documentar.

--------------------------------------------------
# 35. RESULTADO FINAL
--------------------------------------------------

Generar:

docs/08_ARQUITECTURA_OBJETIVO.md

El documento final debe contener como mínimo:

1. Resumen ejecutivo.

2. Características de la aplicación que influyen en la arquitectura.

3. Evaluación de tecnologías.

4. Evaluación de patrones.

5. Evaluación de MVVM.

6. Evaluación de MVI.

7. Evaluación de otras alternativas pertinentes.

8. Decisión del patrón de presentación.

9. Evaluación de Clean Architecture.

10. Arquitectura objetivo.

11. Gestión del estado de la encuesta.

12. Gestión de condiciones.

13. Gestión de saltos.

14. Gestión de preguntas dinámicas.

15. Gestión de capítulos.

16. Gestión de navegación.

17. Evaluación del flujo de encuesta.

18. Evaluación de la estrategia de visualización.

19. Propuesta de UX cuando corresponda.

20. Persistencia.

21. Red.

22. Inyección de dependencias.

23. Concurrencia.

24. Modelos.

25. Mapa Actual → Nuevo.

26. Estructura de paquetes.

27. Diagramas.

28. Matriz de decisiones.

29. Riesgos arquitectónicos.

30. Estrategia de migración progresiva.

31. Decisiones pendientes.

--------------------------------------------------
# 36. REGLA FINAL
--------------------------------------------------

NO asumir que la arquitectura actual debe ser reproducida.

NO asumir que MVVM es necesariamente el mejor patrón.

NO asumir que Clean Architecture debe aplicarse de manera
dogmática.

NO asumir que cada Fragment actual debe convertirse en un
Fragment nuevo.

NO asumir que la UI actual representa la mejor experiencia.

NO asumir que los saltos actuales deben implementarse de la
misma manera.

NO asumir que las tecnologías candidatas deben utilizarse todas.

Analizar.

Comparar.

Cuestionar.

Justificar.

Proponer.

La arquitectura final debe ser la solución que mejor se adapte
a las características reales de la aplicación de encuesta y a
los objetivos de la migración.

Si existe una alternativa arquitectónica o de experiencia de
usuario claramente mejor que la propuesta inicial:

DEBE ser identificada, explicada y justificada.

NO implementar.

--------------------------------------------------
FIN DEL PROMPT 08
--------------------------------------------------