# ETAPA 09 — GENERACIÓN DE EVIDENCIA
# PROMPT_09_01_CAP500_ALCANCE.md

## 1. OBJETIVO

Generar y completar únicamente:

docs/CAP500_ALCANCE.md

Este documento debe proporcionar la evidencia técnica necesaria
para evaluar el:

**Criterio 1 — Alcance Cap500**

definido en:

docs/09_MATRIZ_CUMPLIMIENTO.md

El documento debe construirse mediante investigación real y
trazable del código fuente y de la documentación existente.

NO inventar información.

NO completar información mediante suposiciones.

NO considerar que una afirmación es evidencia solamente porque
aparezca en docs/09_MATRIZ_CUMPLIMIENTO.md.

La matriz es un índice de trabajo; la evidencia debe verificarse
contra las fuentes originales.

---

# 2. REGLA ABSOLUTA DE ALCANCE

Esta ejecución tiene UN ÚNICO ENTREGABLE.

ÚNICO ARCHIVO QUE PUEDE CREARSE O MODIFICARSE:

docs/CAP500_ALCANCE.md

Está PROHIBIDO crear, modificar, regenerar o completar cualquier
otro archivo de documentación durante esta ejecución.

Especialmente NO crear ni modificar:

- docs/CAP500_REGLAS.md
- docs/CAP500_VISIBILIDAD.md
- docs/CAP500_LIMPIEZA.md
- docs/CAP500_ESTADO.md
- docs/CAP500_VALIDACION.md
- docs/CAP500_RIESGOS.md

Tampoco crear documentos adicionales de soporte.

---

# 3. CONDICIÓN DE PARADA OBLIGATORIA

Después de crear o actualizar:

docs/CAP500_ALCANCE.md

DEBES DETENER LA EJECUCIÓN.

NO comenzar ninguna tarea posterior.

NO generar los otros documentos CAP500_*.

NO modificar:

docs/09_MATRIZ_CUMPLIMIENTO.md

NO modificar:

docs/09_AUDITORIA_PRE_IMPLEMENTACION.md

NO modificar código.

NO modificar configuración.

NO preguntar si deseas continuar creando los siguientes documentos.

La ejecución termina después de presentar el resultado de:

docs/CAP500_ALCANCE.md

---

# 4. NO IMPLEMENTAR

Esta tarea es exclusivamente de documentación y obtención de
evidencia.

NO:

- implementar código;
- crear Kotlin;
- crear clases nuevas;
- crear interfaces nuevas;
- crear Repository;
- crear RepositoryImpl;
- crear ViewModel;
- crear UseCase;
- crear SurveyFlowEngine;
- crear ConditionEvaluator;
- crear NavigationResolver;
- crear VisibilityResolver;
- crear CleanupManager;
- crear ChapterResolver;
- crear SurveyGraph;
- crear Room;
- migrar DAO;
- migrar SQLite;
- modificar Java;
- modificar XML;
- modificar Gradle;
- modificar AndroidManifest;
- modificar dependencias;
- modificar configuración de producción.

---

# 5. FUENTES PRINCIPALES

Utilizar primero:

docs/09_MATRIZ_CUMPLIMIENTO.md

docs/09_AUDITORIA_PRE_IMPLEMENTACION.md

Consultar únicamente cuando sea necesario:

docs/03_FLUJOS_FUNCIONALES.md

docs/04_BASE_DATOS.md

docs/05_API_Y_RED.md

docs/06_REGLAS_NEGOCIO.md

docs/07_RIESGOS_Y_ANR.md

docs/08_ARQUITECTURA_OBJETIVO.md

También verificar fundamentalmente contra el código fuente.

No leer nuevamente todos los documentos completos si no es
necesario.

Priorizar:

1. documento directamente relacionado;
2. archivo de código directamente relacionado;
3. métodos relevantes;
4. dependencias;
5. resto del proyecto únicamente si es necesario.

---

# 6. REGLA DE EVIDENCIA

Para cada afirmación importante utilizar evidencia trazable.

La evidencia debe poder relacionarse, cuando corresponda, con:

- archivo;
- clase;
- método;
- línea;
- consulta;
- tabla;
- endpoint;
- documento;
- sección.

Ejemplo:

Clase:
Cap500Fragment_X

Archivo:
ruta/al/archivo.java

Método:
metodoX()

Evidencia:
descripción concreta de lo encontrado.

Conclusión:
qué demuestra exactamente.

NO escribir:

"Cap500 utiliza esta tabla"

sin indicar cómo se determinó.

NO escribir:

"Existe esta regla"

sin indicar dónde se encontró.

NO escribir:

"Este Fragment pertenece a Cap500"

sin indicar la evidencia que demuestra esa pertenencia.

---

# 7. CLASIFICACIÓN DE EVIDENCIA

Utilizar únicamente estas categorías:

### CONFIRMADO

Existe evidencia directa y suficiente.

### PARCIAL

Existe evidencia, pero falta información para cerrar el elemento.

### NO DETERMINADO

No es posible demostrarlo con la información disponible.

### NO ENCONTRADO

Se realizó una búsqueda concreta y no se encontró evidencia.

IMPORTANTE:

NO ENCONTRADO

NO significa:

NO EXISTE.

No convertir una ausencia de resultados en una afirmación de
inexistencia.

Ejemplo:

Correcto:

"NO ENCONTRADO en las búsquedas realizadas."

Incorrecto:

"No existe."

salvo que exista evidencia suficiente para demostrarlo.

---

# 8. NO CONFIAR EN EL NÚMERO "149"

La auditoría menciona aproximadamente:

149 fragments Cap500.

NO asumir que 149 es el número definitivo.

Determinar mediante evidencia qué elementos pertenecen realmente
al alcance de Cap500.

Si el número real es diferente, documentarlo.

Si no es posible determinarlo con certeza:

NO DETERMINADO

No modificar artificialmente el número para coincidir con otros
documentos.

No utilizar el número 149 simplemente porque aparece en una
auditoría anterior.

---

# 9. DEFINIR EL ALCANCE CAP500

Determinar técnicamente qué componentes forman parte del alcance
de Cap500.

Considerar como mínimo:

- Fragments;
- Activities relacionadas;
- Controllers;
- Services;
- Models;
- DAOs;
- consultas;
- tablas;
- métodos de persistencia;
- reglas de navegación;
- reglas de visibilidad;
- limpiezas;
- dependencias con otros capítulos;
- dependencias con hogares;
- dependencias con miembros/personas;
- estado global;
- endpoints;
- Preferences;
- configuración;
- componentes compartidos;
- diálogos;
- componentes dinámicos;
- otros componentes que afecten directamente al flujo Cap500.

No incluir componentes únicamente porque tengan nombres parecidos.

Para cada inclusión importante explicar por qué pertenece al
alcance.

También identificar componentes que inicialmente parezcan formar
parte de Cap500 pero cuya pertenencia no pueda demostrarse.

Clasificarlos como:

NO DETERMINADO

---

# 10. INVENTARIO DE FRAGMENTS

Construir un inventario de los Fragments que realmente formen
parte de Cap500.

Utilizar como mínimo:

| # | Fragment | Clase | Archivo | Preguntas/Función | Dependencias | Navegación | Visibilidad | Persistencia | Evidencia |
|---|---|---|---|---|---|---|---|---|---|

No limitarse a listar nombres de archivos.

Cuando sea posible identificarlo, registrar:

- función;
- capítulo/sección;
- preguntas;
- fragmento anterior;
- fragmento siguiente;
- condiciones;
- reglas de visibilidad;
- métodos de limpieza;
- persistencia;
- dependencias;
- componentes UI relevantes.

Si un Fragment es auxiliar o compartido, indicarlo claramente.

Si un elemento es un diálogo y NO un Fragment, no contarlo como
Fragment.

---

# 11. CONTROL DE CONTEOS

Verificar cuidadosamente todos los conteos.

No asumir que:

24 principales
+
8 auxiliares
+
1 diálogo
=
32

si matemáticamente el resultado es diferente.

Todo conteo debe cuadrar.

Si se contabilizan:

- Fragments principales;
- Fragments auxiliares;
- diálogos;
- Activities;
- Controllers;
- otros componentes;

distinguirlos en categorías separadas.

No mezclar "Fragments" con "componentes".

Al final mostrar claramente, cuando corresponda:

Fragments principales: X

Fragments auxiliares: X

Diálogos: X

Activities relacionadas: X

Controllers relacionados: X

Otros componentes: X

Total de componentes: X

IMPORTANTE:

Verificar que la suma de cada categoría coincida con el total
declarado.

No utilizar un total que contradiga las filas de las tablas.

---

# 12. PREGUNTAS

Determinar cómo están representadas las preguntas de Cap500.

No asumir que una pregunta corresponde a una clase.

Identificar, cuando sea posible:

- identificador;
- código;
- número;
- método;
- campo;
- componente UI;
- variable;
- modelo;
- Fragment donde aparece.

Si existe numeración como:

P537
P538
P539

documentar la relación solamente cuando pueda demostrarse.

No inventar preguntas faltantes.

Si una pregunta se construye dinámicamente, documentar cómo se
determina su existencia.

Si no es posible obtener el número exacto:

NO DETERMINADO

---

# 13. REGLAS DE NEGOCIO IDENTIFICADAS

Identificar las reglas de negocio que forman parte del alcance.

NO realizar todavía el catálogo exhaustivo de reglas.

NO generar:

docs/CAP500_REGLAS.md

NO generar:

docs/CAP500_VISIBILIDAD.md

Aquí solamente identificar:

- tipos de reglas;
- ubicación;
- preguntas afectadas;
- Fragments afectados;
- dependencias.

Considerar:

- condiciones;
- saltos;
- relevancia;
- visibilidad;
- limpieza;
- cálculos;
- dependencias entre respuestas;
- dependencias entre miembros;
- dependencias entre capítulos;
- reglas que determinan el siguiente destino.

IMPORTANTE:

La encuesta tiene capítulos y preguntas condicionales.

Existen flujos donde:

- una condición puede ocultar preguntas dentro del mismo Fragment;
- una condición puede ocultar o saltar un Fragment completo;
- una respuesta puede determinar qué pregunta aparece después;
- una respuesta puede provocar limpieza de datos;
- una condición puede afectar otro capítulo.

Documentar la existencia de estos mecanismos únicamente cuando
puedan demostrarse mediante código o documentación.

NO diseñar todavía la solución futura.

---

# 14. PERSISTENCIA

Identificar las operaciones de persistencia que afectan directamente
a Cap500.

Buscar:

- SELECT;
- INSERT;
- UPDATE;
- DELETE;
- DAO;
- Service;
- SQLiteOpenHelper;
- SQL;
- acceso directo a DB;
- transacciones;
- métodos save;
- métodos update;
- métodos delete;
- métodos find/query.

Construir:

| Método | Clase | Operación | Tabla | Parámetros | Uso Cap500 | Compartida | Evidencia |
|---|---|---|---|---|---|---|---|

NO migrar las operaciones.

NO convertirlas a Room.

Solamente documentar el estado actual.

Si una operación es utilizada por varios capítulos:

COMPARTIDA

y explicar la dependencia.

---

# 15. TABLAS AFECTADAS

Identificar las tablas que Cap500:

- consulta;
- inserta;
- actualiza;
- elimina.

Construir:

| Tabla | Operación | Método | Fragment/Componente | Directa/Indirecta | Compartida | Evidencia |
|---|---|---|---|---|---|---|

Distinguir:

### DIRECTAMENTE UTILIZADA POR CAP500

de:

### AFECTADA INDIRECTAMENTE POR UNA OPERACIÓN CROSS-CHAPTER

No asumir que una tabla pertenece a Cap500 solamente por su
nombre.

La pertenencia debe tener evidencia.

---

# 16. ENDPOINTS

Identificar solamente los endpoints que realmente estén
relacionados con Cap500.

Buscar:

- URLs;
- métodos HTTP;
- Services;
- clientes;
- SyncHttpClient;
- AsyncHttpClient;
- EnahoHttpClient;
- Retrofit si existiera;
- llamadas indirectas;
- servicios compartidos.

Construir:

| Endpoint | HTTP | Clase | Método | Uso Cap500 | Directo/Indirecto | Evidencia |
|---|---|---|---|---|---|---|

Si no existe evidencia suficiente de que un endpoint sea utilizado
por Cap500:

NO incluirlo como endpoint confirmado.

---

# 17. LIMPIEZAS

Identificar las operaciones de limpieza relacionadas con Cap500.

Como mínimo verificar:

- limpiar538_540;
- limpiar538_543;
- limpiar544;
- limpiar545_555;

y cualquier otra limpieza que realmente afecte Cap500.

Construir:

| Método | Condición | Tablas | Operación | Local/Cross-Chapter | Llamado desde | Evidencia |
|---|---|---|---|---|---|---|

IMPORTANTE:

Si una búsqueda no encuentra una limpieza cross-chapter:

registrar:

NO ENCONTRADO EN LA BÚSQUEDA REALIZADA

No escribir:

NO EXISTE

salvo que exista evidencia suficiente para demostrarlo.

NO realizar todavía el catálogo exhaustivo de limpieza.

Ese trabajo corresponde posteriormente a:

docs/CAP500_LIMPIEZA.md

En este documento solamente establecer el alcance y las
dependencias conocidas.

---

# 18. DEPENDENCIAS CROSS-CHAPTER

Identificar componentes externos necesarios para Cap500.

Prestar especial atención a:

- Cap200;
- ENAHO02;
- ENAHO04;
- hogares;
- miembros;
- personas;
- visitas;
- estado global;
- otros capítulos que proporcionen datos o condiciones.

Construir:

| Origen | Destino | Tipo | Condición | Datos afectados | Evidencia |
|---|---|---|---|---|---|

Clasificar:

- lectura;
- escritura;
- navegación;
- limpieza;
- estado.

No inferir dependencias únicamente por nombres.

---

# 19. ESTADO GLOBAL

Identificar cualquier estado externo necesario para ejecutar
Cap500:

- Preferences;
- AppContext;
- variables globales;
- Activity;
- Fragment;
- Services;
- singleton;
- sesión;
- variables compartidas;
- contexto de cuestionario;
- estado del hogar;
- estado de visita;
- estado del informante.

Construir:

| Dependencia | Clase | Método/Uso | Función en Cap500 | Riesgo | Evidencia |
|---|---|---|---|---|---|

NO proponer todavía la solución arquitectónica.

Solamente documentar el estado actual.

---

# 20. MAPA GENERAL DE CAP500

Construir un mapa conceptual:

Cap500
↓
Capítulos / Secciones
↓
Fragments
↓
Preguntas
↓
Condiciones
↓
Saltos
↓
Navegación
↓
Visibilidad
↓
Persistencia
↓
Tablas
↓
Limpiezas
↓
Dependencias cross-chapter
↓
Red / Endpoints
↓
Estado global

El objetivo es demostrar qué elementos forman realmente parte
del alcance.

IMPORTANTE:

No asumir que la representación actual basada en Fragments es
necesariamente la arquitectura futura.

Este documento describe el sistema actual.

No diseñar aquí la nueva UI.

---

# 21. TRAZABILIDAD

Crear una sección de trazabilidad.

Cada elemento relevante debe poder rastrearse hacia su evidencia.

Ejemplo:

CAP500_1

→ Fragment: ...

→ Preguntas: ...

→ Método: ...

→ Tabla: ...

→ Regla: ...

→ Evidencia: ...

No utilizar afirmaciones generales sin origen.

Cuando existan varias fuentes:

Código:
...

Documentación:
...

Conclusión:
...

Si existe contradicción:

DOCUMENTACIÓN:
...

CÓDIGO:
...

DIFERENCIA:
...

VERIFICACIÓN NECESARIA:
...

---

# 22. GAPS

Crear:

## GAPS DEL ALCANCE

Enumerar únicamente los gaps demostrados.

Ejemplos:

- Fragment sin ubicación;
- pregunta sin ubicación;
- tabla sin trazabilidad;
- consulta sin origen;
- endpoint sin uso comprobado;
- dependencia sin origen;
- limpieza sin llamada identificada;
- componente cuyo alcance no puede determinarse;
- navegación cuyo origen no puede determinarse;
- condición cuyo efecto no puede determinarse.

Separar:

### GAP CONFIRMADO

de:

### INFORMACIÓN NO DETERMINADA

No convertir un gap en una afirmación de inexistencia.

---

# 23. CRITERIO DE CIERRE

El documento debe permitir responder:

> ¿Sabemos exactamente qué forma parte de Cap500 y qué no?

y:

> ¿El alcance identificado es suficientemente confiable para servir
> como base de los siguientes documentos de evidencia?

Si la respuesta es NO:

explicar exactamente qué falta.

NO declarar el criterio cumplido artificialmente.

El objetivo es producir evidencia, no conseguir una aprobación.

---

# 24. RELACIÓN CON 09_MATRIZ_CUMPLIMIENTO.md

NO modificar:

docs/09_MATRIZ_CUMPLIMIENTO.md

Al finalizar indicar solamente:

### Criterio 1 — Alcance Cap500

Estado recomendado:

- CUMPLIDO
- PARCIAL
- FALTA EVIDENCIA

Justificar la recomendación con evidencia.

La matriz será actualizada posteriormente y de forma separada.

NO cambiar el estado de la matriz automáticamente.

---

# 25. REGLA DE CONSISTENCIA

Antes de finalizar comprobar:

1. Los conteos de Fragments cuadran.
2. Los conteos de componentes cuadran.
3. El número de preguntas no contradice la evidencia.
4. Las tablas tienen trazabilidad.
5. Las consultas tienen origen.
6. Los endpoints tienen evidencia.
7. Las limpiezas distinguen "no encontrado" de "no existe".
8. Las dependencias cross-chapter tienen origen y destino.
9. El estado global tiene evidencia.
10. Los GAPS coinciden con la información realmente faltante.
11. El estado recomendado del Criterio 1 coincide con la evidencia.
12. No existen contradicciones internas dentro de
    CAP500_ALCANCE.md.
13. Ningún conteo se basa únicamente en una cifra heredada de
    otra auditoría sin verificación.
14. Fragments, diálogos y otros componentes no se contabilizan
    como si fueran la misma categoría.
15. Las afirmaciones sobre Cap500 pueden rastrearse hacia código
    o documentación.

---

# 26. REGLA DE EFICIENCIA

Trabajar de forma incremental.

No leer nuevamente todo el proyecto.

No repetir búsquedas globales ya realizadas.

Utilizar los documentos existentes como índice.

Buscar primero:

- Cap500;
- nombres de Fragments;
- métodos relevantes;
- tablas;
- consultas;
- navegación;
- limpieza;
- persistencia.

Leer solamente los métodos/secciones necesarios.

Ampliar contexto únicamente cuando exista una dependencia real.

Si una búsqueda ya demuestra un hecho, reutilizar ese resultado.

---

# 27. REGLA DE CONSERVACIÓN

Si:

docs/CAP500_ALCANCE.md

ya existe:

ACTUALIZAR ese mismo archivo.

NO crear:

- CAP500_ALCANCE_V2.md
- CAP500_ALCANCE_FINAL.md
- CAP500_ALCANCE_REVISION.md

salvo que el usuario lo solicite explícitamente.

Si el archivo no existe:

CREAR:

docs/CAP500_ALCANCE.md

Si el archivo ya contiene información válida:

conservarla y corregir únicamente lo necesario.

NO eliminar evidencia válida sin justificar la modificación.

---

# 28. ESTRUCTURA DEL DOCUMENTO FINAL

El archivo:

docs/CAP500_ALCANCE.md

debe contener como mínimo:

# CAP500 — ALCANCE

## 1. Objetivo

## 2. Criterio utilizado para determinar el alcance

## 3. Inventario de Fragments

## 4. Inventario de preguntas

## 5. Reglas de negocio identificadas

## 6. Persistencia

## 7. Tablas afectadas

## 8. Endpoints

## 9. Limpiezas

## 10. Dependencias cross-chapter

## 11. Estado global

## 12. Mapa general Cap500

## 13. Trazabilidad

## 14. Gaps

## 15. Conclusión

Puede agregar subsecciones cuando sean necesarias para presentar
correctamente la evidencia.

No agregar contenido que no tenga relación con el alcance.

---

# 29. CONCLUSIÓN FINAL

Al terminar mostrar brevemente:

### Archivo creado/actualizado

docs/CAP500_ALCANCE.md

### Resumen

- Fragments principales: X
- Fragments auxiliares: X
- Diálogos: X
- Otros componentes: X
- Preguntas identificadas: X
- Tablas identificadas: X
- Consultas identificadas: X
- Endpoints identificados: X
- Limpiezas identificadas: X
- Dependencias cross-chapter: X
- Elementos CONFIRMADOS: X
- Elementos PARCIALES: X
- Elementos NO DETERMINADOS: X
- Elementos NO ENCONTRADOS: X

### Criterio 1

CUMPLIDO / PARCIAL / FALTA EVIDENCIA

con una justificación breve y basada en evidencia.

---

# 30. CONFIRMACIÓN DE RESTRICCIONES

Al finalizar confirmar explícitamente:

- Se creó/modificó únicamente docs/CAP500_ALCANCE.md.
- No se modificó código fuente.
- No se modificó Gradle.
- No se modificó AndroidManifest.
- No se modificó docs/09_MATRIZ_CUMPLIMIENTO.md.
- No se modificó docs/09_AUDITORIA_PRE_IMPLEMENTACION.md.
- No se crearon CAP500_REGLAS.md.
- No se crearon CAP500_VISIBILIDAD.md.
- No se crearon CAP500_LIMPIEZA.md.
- No se crearon CAP500_ESTADO.md.
- No se crearon CAP500_VALIDACION.md.
- No se crearon CAP500_RIESGOS.md.
- No se creó ningún documento adicional.
- No se implementó ninguna funcionalidad.
- No se inició el piloto.

Si alguno de estos puntos no se cumple, informarlo explícitamente.

---

# 31. DETENCIÓN OBLIGATORIA

Después de completar:

docs/CAP500_ALCANCE.md

DETENERSE.

No generar ningún otro documento.

No modificar ningún otro archivo.

No iniciar:

CAP500_REGLAS.md

No iniciar:

CAP500_VISIBILIDAD.md

No iniciar:

CAP500_LIMPIEZA.md

No iniciar:

CAP500_ESTADO.md

No iniciar:

CAP500_VALIDACION.md

No iniciar:

CAP500_RIESGOS.md

No actualizar:

docs/09_MATRIZ_CUMPLIMIENTO.md

No realizar tareas adicionales.

No realizar una nueva auditoría general.

No preguntar si debe continuar con los siguientes documentos.

Esperar una nueva instrucción del usuario.

FIN.