# ============================================================
# PROMPT V4 — CREAR DOCUMENTACIÓN HISTÓRICA DE UN CAPÍTULO
# ============================================================


# ============================================================
# 0. PARÁMETROS DE EJECUCIÓN
# ============================================================

NOMBRE DEL PDF:
CAPITULO_500_2026.pdf

CAPÍTULO:
500

AÑO:
2026


# ============================================================
# REGLA FUNDAMENTAL DE LOS PARÁMETROS
# ============================================================

Los tres valores anteriores son los ÚNICOS valores que deben
modificarse para ejecutar este prompt.

NO solicitar al usuario ningún otro dato.

NO solicitar modificaciones adicionales del prompt.

Utilizar automáticamente estos valores en TODO el proceso:

- NOMBRE DEL PDF = documento fuente exacto.
- CAPÍTULO = capítulo a documentar.
- AÑO = versión anual del cuestionario.


# ============================================================
# 1. DOCUMENTO FUENTE
# ============================================================

Trabajar EXCLUSIVAMENTE con:

[NOMBRE DEL PDF]

La carpeta puede contener múltiples archivos PDF.

NO seleccionar automáticamente otro PDF.

NO utilizar archivos cuyo nombre sea parecido.

NO combinar información de diferentes PDFs.

NO utilizar otro cuestionario para completar información.

Si el documento indicado no existe, no puede abrirse o no puede
identificarse de manera inequívoca:

DETENER EL PROCESAMIENTO.

Informar el problema.

NO sustituir automáticamente el documento por otro PDF.


# ============================================================
# 2. CAPÍTULO OBJETIVO
# ============================================================

Analizar EXCLUSIVAMENTE:

CAPÍTULO [CAPÍTULO]

del documento:

[NOMBRE DEL PDF]

Ignorar completamente otros capítulos.


# ============================================================
# 3. AÑO
# ============================================================

La documentación corresponde al año:

[AÑO]


# ============================================================
# 4. OBJETIVO
# ============================================================

Construir una documentación estructurada, histórica y trazable
del capítulo [CAPÍTULO].

La documentación debe representar correctamente:

- preguntas;
- subpreguntas;
- títulos;
- subtítulos;
- secciones;
- subtítulos de sección;
- alternativas;
- matrices;
- ítems de matrices;
- matrices anidadas;
- instrucciones al encuestador;
- filtros;
- condiciones;
- saltos;
- saltos por alternativa;
- dependencias;
- campos dependientes;
- páginas;
- orden;
- año;
- documento fuente.


# ============================================================
# 5. PRINCIPIO FUNDAMENTAL
# ============================================================

El PDF es un cuestionario diseñado visualmente.

La posición, proximidad, alineación, separación, recuadros, flechas,
columnas y jerarquía visual forman parte de la información.

NO tratar el PDF como una simple secuencia de texto.

NO confiar exclusivamente en OCR o extracción textual.

La extracción textual puede mezclar columnas, matrices, instrucciones
y alternativas. Si existe conflicto entre texto extraído y estructura
visual, prevalece la estructura visual.

El proceso obligatorio es:

PDF
↓
ANÁLISIS VISUAL
↓
MAPA DE PÁGINAS
↓
COLUMNAS Y REGIONES
↓
BLOQUES
↓
RECONSTRUCCIÓN VISUAL
↓
PREGUNTAS Y SUBPREGUNTAS
↓
MATRICES Y MATRICES ANIDADAS
↓
ALTERNATIVAS
↓
TIPOS Y COMPONENTES
↓
FLUJO Y DEPENDENCIAS
↓
VALIDACIÓN DE COBERTURA
↓
DOCUMENTACIÓN


# ============================================================
# 6. ANÁLISIS VISUAL OBLIGATORIO
# ============================================================

Antes de generar cualquier documento definitivo:

analizar visualmente las páginas correspondientes al capítulo.

Para cada página identificar:

- número de página;
- columnas;
- regiones;
- cuadros;
- bloques;
- encabezados;
- títulos;
- subtítulos;
- preguntas;
- matrices;
- ítems de matrices;
- alternativas;
- instrucciones;
- filtros;
- saltos;
- flechas;
- relaciones visuales.

La estructura visual tiene prioridad sobre el orden producido
por OCR o extracción de texto.


# ============================================================
# 7. CAP[CAPÍTULO]_ANALISIS_PAGINAS.md
# ============================================================

Crear OBLIGATORIAMENTE:

CAP[CAPÍTULO]_ANALISIS_PAGINAS.md

Este archivo es un documento AUXILIAR DE ANÁLISIS.

NO forma parte del historial de preguntas.

NO debe utilizarse como sustituto de los otros documentos.

Su finalidad es registrar cómo fue interpretada visualmente
cada página antes de generar la documentación definitiva.


# ------------------------------------------------------------
# 7.1 ESTRUCTURA DEL ANÁLISIS DE PÁGINAS
# ------------------------------------------------------------

Para cada página registrar:

## Página X

### Estructura visual

Indicar:

- una columna;
- dos columnas;
- múltiples columnas;
- cuadros;
- regiones principales.

### Columna izquierda

Enumerar los bloques detectados.

### Columna derecha

Enumerar los bloques detectados.

Si existen más columnas:

registrarlas individualmente.

### Bloques detectados

Clasificar cada bloque como:

- ENCABEZADO_DOCUMENTO
- ENCABEZADO_SECCION
- SUBTITULO_SECCION
- PREGUNTA
- SUBTITULO_PREGUNTA
- MATRIZ
- ITEM_MATRIZ
- ALTERNATIVA
- INSTRUCCION_ENCUESTADOR
- FILTRO
- SALTO
- CAMPO_DOCUMENTO
- OBSERVACION

### Preguntas identificadas

Lista de códigos oficiales.

### Elementos que NO son preguntas

Lista de elementos detectados que podrían confundirse
con preguntas.

### Matrices

Identificar:

- pregunta padre;
- ítems;
- subpreguntas;
- matrices hijas.

### Instrucciones del encuestador

Registrar separadamente.

### Saltos y lógica

Registrar cualquier relación visible.


# ============================================================
# 8. COLUMNAS
# ============================================================

Determinar visualmente si cada página tiene:

- una columna;
- dos columnas;
- múltiples columnas.

Si existen varias columnas:

1. identificar cada columna;
2. analizar cada columna como bloque independiente;
3. determinar sus límites;
4. identificar preguntas dentro de cada bloque;
5. identificar matrices dentro de cada bloque;
6. identificar instrucciones dentro de cada bloque;
7. establecer posteriormente las relaciones entre bloques.

NO mezclar texto de columnas diferentes.


# ============================================================
# 9. REGIONES Y CUADROS
# ============================================================

Identificar visualmente:

- cuadros;
- recuadros;
- encabezados;
- instrucciones;
- preguntas;
- matrices;
- alternativas;
- flechas;
- saltos;
- filtros.

El contenido de un cuadro debe mantenerse dentro de su
propio bloque lógico.


# ============================================================
# 10. CLASIFICACIÓN PREVIA
# ============================================================

Antes de crear preguntas:

clasificar cada bloque como:

ENCABEZADO_DOCUMENTO
ENCABEZADO_SECCION
SUBTITULO_SECCION
PREGUNTA
SUBTITULO_PREGUNTA
MATRIZ
ITEM_MATRIZ
ALTERNATIVA
INSTRUCCION_ENCUESTADOR
FILTRO
SALTO
CAMPO_DOCUMENTO
OBSERVACION

NO crear preguntas antes de realizar esta clasificación.


# ============================================================
# 11. IDENTIFICACIÓN DE CÓDIGOS
# ============================================================

Un número o código solamente puede convertirse en pregunta
si la estructura visual demuestra que corresponde a una pregunta.

NO convertir automáticamente cualquier número encontrado
en el PDF en pregunta.

Un número puede representar:

- código de pregunta;
- código de alternativa;
- número de ítem;
- código de respuesta;
- número de persona;
- número de página;
- número dentro de una instrucción.


# ============================================================
# 12. REGLA CRÍTICA PARA MATRICES
# ============================================================

Si un bloque contiene:
- múltiples filas o ítems;
- una estructura común;
- columnas comunes de respuesta;
- numeración interna;

determinar si es una MATRIZ.

Ejemplo:

504

1. ...
2. ...
3. ...
...
11. ...

Si esos números son filas o ítems de una misma estructura:

Pregunta padre:
504

Tipo:
MATRIZ

Los números 1...11 son:

ITEM_MATRIZ

NO crear automáticamente preguntas independientes para esos números.

PERO existe una excepción CRÍTICA:

Si un ítem de matriz funciona como una pregunta/subpregunta real,
es decir, posee título o enunciado propio Y tiene comportamiento de
respuesta propio (por ejemplo RadioGroup, EditText, CheckBox, Spinner
explícitamente definido o una matriz hija), DEBE aparecer también en
CAP[CAPÍTULO]_CATALOGO.md como registro hijo.

En ese caso:
- NO tratarlo como una alternativa;
- NO tratarlo como una pregunta de nivel superior;
- conservar la relación con su matriz/padre;
- registrar su tipo;
- registrar su componente UI;
- registrar sus alternativas si las tiene;
- registrar su lógica si la tiene;
- registrar su orden visual;
- registrar su título literalmente.

Por tanto, distinguir:

ITEM_MATRIZ_SIMPLE
→ solo es una fila/ítem de una matriz y no tiene comportamiento
   independiente de pregunta.

SUBPREGUNTA_MATRIZ
→ tiene estructura propia de pregunta y DEBE estar en el catálogo.

Las subpreguntas de matriz son preguntas hijas a efectos de
documentación estructural, aunque no sean preguntas de nivel superior.

# 13. IDENTIFICADORES INTERNOS DE MATRIZ
# ============================================================

Los ítems pueden recibir identificadores internos:

P[CAPÍTULO]-[CÓDIGO]/ITEM-01
P[CAPÍTULO]-[CÓDIGO]/ITEM-02

Estos NO son códigos oficiales.

NO inventar códigos oficiales.


# ============================================================
# 14. CAMPOS DEL DOCUMENTO
# ============================================================

Elementos como:

Persona N°
Nombre
Informante N°
Fecha
Número de orden

son:

CAMPO_DOCUMENTO

NO son preguntas.


# ============================================================
# 15. INSTRUCCIONES DEL ENCUESTADOR
# ============================================================

Los cuadros que contengan:

ENCUESTADOR(A)

o instrucciones similares:

clasificar como:

INSTRUCCION_ENCUESTADOR

NO incorporarlos dentro del título de una pregunta.

Pueden contener:

- verificaciones;
- filtros;
- condiciones;
- saltos;
- instrucciones de navegación.


# ============================================================
# 16. TÍTULO DE PREGUNTA
# ============================================================

El texto principal se registra como:

TÍTULO

REGLA CRÍTICA:

El título debe copiarse LITERALMENTE del PDF.

NO:
- parafrasear;
- resumir;
- corregir;
- reinterpretar;
- traducir;
- cambiar mayúsculas/minúsculas;
- eliminar tildes;
- cambiar puntuación;
- reemplazar abreviaturas;
- truncar el texto;
- completar con información de otra pregunta.

Conservar exactamente la escritura visual del PDF.

Si el título continúa en otra página, reconstruir el texto completo
respetando el contenido literal y registrar todas las páginas
involucradas.

Si el OCR devuelve texto incompleto, mezclado o en minúsculas pero
la representación visual permite recuperar el texto correcto,
utilizar la evidencia visual.

Si un fragmento no puede leerse con seguridad, NO inventarlo:
registrarlo como REVISAR_MANUALMENTE en el análisis de páginas.

El título debe pertenecer exclusivamente al bloque visual de esa
pregunta.

NO incluir:
- texto de otra columna;
- instrucciones;
- saltos;
- alternativas;
- títulos de sección;
- texto de otra pregunta.

# 17. SUBTÍTULO DE PREGUNTA
# ============================================================

Separar instrucciones complementarias.

Ejemplos:

(Acepte una o más alternativas)

(Acepte una o más opciones)

Registrar como:

SUBTÍTULO

No incorporarlo automáticamente al título.


# ============================================================
# 18. TÍTULOS DE SECCIÓN
# ============================================================

Identificar títulos que introducen bloques de preguntas.

Registrar:

- código de sección si existe;
- título;
- subtítulo;
- preguntas pertenecientes.


# ============================================================
# 19. SUBTÍTULOS DE SECCIÓN
# ============================================================

Separar:

TÍTULO DE SECCIÓN

de:

SUBTÍTULO DE SECCIÓN


# ============================================================
# 20. DETERMINACIÓN DEL TIPO
# ============================================================

Determinar el tipo DESPUÉS de reconstruir correctamente
la estructura visual.

Tipos permitidos:

ABIERTA
CERRADA_UNICA
CERRADA_MULTIPLE
MATRIZ


# ============================================================
# 21. ABIERTA
# ============================================================

Si requiere ingresar información:

ABIERTA

Componente:

EditText


# ============================================================
# 22. CERRADA_UNICA
# ============================================================

Si solo puede seleccionarse una alternativa:

CERRADA_UNICA

Componente predeterminado:

RadioGroup


# ============================================================
# 23. CERRADA_MULTIPLE
# ============================================================

Si puede seleccionarse más de una alternativa:

CERRADA_MULTIPLE

Componente:

CheckBox

Detectar especialmente:

(Acepte una o más alternativas)

(Acepte una o más opciones)

o equivalentes.

IMPORTANTE:

La existencia de varias alternativas NO significa
automáticamente CERRADA_MULTIPLE.

Debe existir evidencia de selección múltiple.


# ============================================================
# 24. SPINNER
# ============================================================

NO asignar Spinner automáticamente.

CERRADA_UNICA:

→ RadioGroup por defecto.

Spinner solamente cuando exista una definición explícita.


# ============================================================
# 25. OTRO / ESPECIFIQUE
# ============================================================

Si una alternativa contiene:

Otro (especifique)
Otro, especifique
Otra (especifique)
Otros (especifique)

o equivalente:

clasificar como:

OPCION_CON_CAMPO_DEPENDIENTE

Campo:

EditText

La pregunta mantiene su tipo principal.


# ============================================================
# 26. ALTERNATIVAS
# ============================================================

Cada alternativa debe conservar:

- código;
- texto;
- orden;
- pregunta padre.

Puede contener:

- salto;
- condición;
- campo dependiente.


# ============================================================
# 27. SALTOS POR ALTERNATIVA
# ============================================================

Cada alternativa puede tener un salto diferente.

Ejemplo:

A1 → D
A2 → E
A3 → C

Registrar cada relación individualmente.

Los destinos utilizan códigos oficiales.


# ============================================================
# 28. MATRICES ANIDADAS
# ============================================================

Una pregunta o subpregunta dentro de una matriz puede contener otra
matriz.

Ejemplo:

MATRIZ A
│
├── A1 [RadioGroup]
│   └── SI → MATRIZ A1
│       ├── A1.1 [EditText]
│       ├── A1.2 [RadioGroup]
│       └── A1.3 [CheckBox]
│
├── A2 [CheckBox]
│   └── Opción 1 → MATRIZ A2
│       ├── A2.1
│       └── A2.2
│
└── A3
    └── Sin matriz

REGLA:

A1, A1.1, A1.2, A1.3, A2, A2.1, etc. deben aparecer en
CAP[CAPÍTULO]_CATALOGO.md cuando tengan comportamiento propio de
pregunta.

Cada registro debe conservar:
- ID;
- Código oficial, si existe;
- Padre;
- Nivel;
- Orden;
- Tipo;
- Componente UI;
- Título literal;
- Subtítulo;
- Página;
- relación con matriz hija, si existe.

NO limitar la profundidad.

NO inventar códigos oficiales para subpreguntas que no los tengan.


# ============================================================
# 29. VISIBILIDAD DE MATRICES
# ============================================================

Una matriz hija puede depender de una respuesta.

Ejemplo:

SI → MOSTRAR
NO → NO MOSTRAR

En CheckBox:

puede depender de una o varias opciones.

NO asumir selección única.


# ============================================================
# 30. RELACIÓN PADRE-HIJO
# ============================================================

Registrar:

ID
Código
Padre
Nivel

Ejemplo:

P500-504
P500-504/ITEM-01
P500-504/ITEM-01/SUBITEM-01


# ============================================================
# 31. IDENTIFICADOR HISTÓRICO
# ============================================================

Crear:

P[CAPÍTULO]-[CÓDIGO]

Ejemplo:

P500-501
P500-501A
P500-502
P500-504


# ============================================================
# 32. ORDEN
# ============================================================

Registrar el orden de presentación de cada pregunta dentro del año.

IMPORTANTE:

CÓDIGO ≠ ORDEN.

El código oficial identifica la pregunta.

El orden indica dónde aparece visualmente dentro del cuestionario
para ese año.

NO ordenar las preguntas por su código.

Es válido que visualmente aparezca:

12
11
13

En ese caso:
- Código de la primera = 12
- Orden de la primera = posición visual correspondiente
- Código de la segunda = 11
- Orden de la segunda = posición visual correspondiente

NO modificar códigos para hacerlos consecutivos.

Si una secuencia histórica fue:

502
503
504

y en otro año aparece:

502
502A
503
504

el ID histórico de 503 permanece igual.

Si en un año desaparece una pregunta, NO renumerar las demás.

El mismo principio se aplica a las alternativas:

CÓDIGO_ALTERNATIVA ≠ ORDEN_ALTERNATIVA.

Si las alternativas tienen códigos:

1
2
3
5

y visualmente se presentan en ese orden, conservar esos códigos.

Si falta el código 4:
- NO crear el código 4;
- NO cambiar 5 por 4;
- registrar el orden de presentación por separado.

# 33. ORDEN VISUAL Y ORDEN LÓGICO
# ============================================================

Diferenciar:

ORDEN_VISUAL

de:

ORDEN_LOGICO

El orden visual corresponde a la posición en el documento.

El orden lógico corresponde a la navegación del cuestionario.

Los saltos y condiciones determinan el orden lógico.

NO asumir que ambos son iguales.


# ============================================================
# 34. PREGUNTAS CONTINUADAS
# ============================================================

Si una pregunta continúa en otra página:

NO duplicarla.

Reconstruir el contenido completo.

Registrar las páginas involucradas.


# ============================================================
# 35. DOCUMENTOS DEFINITIVOS
# ============================================================

Crear OBLIGATORIAMENTE estos SIETE archivos:

1. CAP[CAPÍTULO]_ANALISIS_PAGINAS.md
2. CAP[CAPÍTULO]_CATALOGO.md
3. CAP[CAPÍTULO]_VERSIONES.md
4. CAP[CAPÍTULO]_ALTERNATIVAS.md
5. CAP[CAPÍTULO]_FLUJO.md
6. CAP[CAPÍTULO]_CAMBIOS.md
7. CAP[CAPÍTULO]_DICCIONARIO.md

NINGUNO es opcional.


# ============================================================
# 36. CAP[CAPÍTULO]_CATALOGO.md
# ============================================================

El catálogo debe representar preguntas de nivel superior Y
subpreguntas/matriz con comportamiento propio.

Estructura obligatoria:

| ID | Año | Capítulo | Sección | Código | Padre | Nivel | Orden | Tipo | Componente UI | Título | Subtítulo | Página |
|---|---:|---:|---|---|---|---:|---:|---|---|---|---|---|

REGLAS:

1. ID = identificador histórico estable.
2. Código = código oficial de la pregunta, si existe.
3. Padre = ID del elemento superior.
4. Nivel = profundidad jerárquica.
5. Orden = posición visual/presentación dentro del año.
6. Tipo = ABIERTA, CERRADA_UNICA, CERRADA_MULTIPLE o MATRIZ.
7. Componente UI = EditText, RadioGroup, CheckBox, Matriz o Spinner
   solo cuando corresponda según las reglas de este prompt.
8. Título = transcripción literal del PDF.
9. Subtítulo = texto complementario literal, separado del título.
10. Página = página o páginas donde aparece.

NO incluir como registros de pregunta:
- Persona N°;
- Nombre;
- Informante N°;
- campos administrativos del documento;
- instrucciones del encuestador;
- códigos de alternativas;
- códigos de respuesta;
- números de página.

IMPORTANTE SOBRE MATRICES:

Una MATRIZ contenedora sí aparece como registro del catálogo.

Un ITEM_MATRIZ_SIMPLE no aparece como pregunta independiente.

Una SUBPREGUNTA_MATRIZ con comportamiento propio SÍ aparece como
registro hijo del catálogo.

Ejemplo:

| ID | Código | Padre | Nivel | Orden | Tipo | Componente UI | Título |
|---|---|---|---:|---:|---|---|---|
| P500-504 | 504 | — | 0 | 10 | MATRIZ | Matriz | ... |
| P500-504/ITEM-01 | — | P500-504 | 1 | 1 | CERRADA_UNICA | RadioGroup | ... |
| P500-504/ITEM-02 | — | P500-504 | 1 | 2 | ABIERTA | EditText | ... |

El código "—" significa que el PDF no presenta un código oficial
para esa subpregunta. NO inventar uno.

Si una subpregunta tiene código oficial, registrarlo.

Si una subpregunta controla una matriz hija, conservarla en el
catálogo y documentar la condición en FLUJO.md.

NO ordenar el catálogo por Código.

El orden principal debe permitir reconstruir el orden visual y la
jerarquía del documento.

# 37. CAP[CAPÍTULO]_VERSIONES.md
# ============================================================

Registrar:

| Año | ID | Código | Orden | Estado |
|---:|---|---|---:|---|

Estados:

PRESENTE
NO PRESENTE
REINCORPORADA
PRESENTE CON CAMBIOS
PRESENTE SIN CAMBIOS


# ============================================================
# 38. CAP[CAPÍTULO]_ALTERNATIVAS.md
# ============================================================

Registrar las alternativas de preguntas de nivel superior y de
subpreguntas que tengan alternativas.

Estructura:

| Año | Pregunta | Ítem | Código | Orden | Texto | Tipo opción | Campo dependiente |
|---:|---|---|---|---:|---|---|---|

REGLAS:

- Pregunta = ID de la pregunta/subpregunta padre.
- Ítem = identificador del ítem de alternativa si existe.
- Código = código oficial de la alternativa.
- Orden = posición visual de la alternativa dentro de esa pregunta.
- Texto = texto literal del PDF.
- Tipo opción = clasificación de la alternativa.
- Campo dependiente = componente activado por esa alternativa, cuando
  corresponda.

IMPORTANTE:

CÓDIGO ≠ ORDEN.

NO renumerar alternativas porque desaparezca una alternativa.

Ejemplo:

Código | Orden
1      | 1
2      | 2
3      | 3
5      | 4

El código 5 permanece como 5.

NO convertir subpreguntas de una matriz en alternativas.

Si A1, A1.1 y A1.2 son preguntas/subpreguntas con comportamiento
propio, se registran en CATALOGO.md. Sus alternativas, si existen,
se registran aquí vinculadas mediante el ID de Pregunta.

CAMPO DEPENDIENTE:

Si una alternativa contiene:
- Otro (especifique)
- Otro, especifique
- Otra (especifique)
- Otros (especifique)
- o una expresión equivalente claramente asociada a la necesidad de
  escribir una respuesta,

registrar:

Tipo opción = OPCION_CON_CAMPO_DEPENDIENTE
Campo dependiente = EditText

NO dejar Campo dependiente vacío cuando el PDF muestre claramente
esa relación.

# 39. CAP[CAPÍTULO]_FLUJO.md
# ============================================================

Registrar:

- saltos;
- saltos por alternativa;
- filtros;
- condiciones;
- instrucciones;
- dependencias;
- visibilidad de matrices.

Estructura:

| Año | Origen | Condición | Acción | Destino | Fuente |
|---:|---|---|---|---|---|


# ============================================================
# 40. CAP[CAPÍTULO]_CAMBIOS.md
# ============================================================

OBLIGATORIO.

Si es la primera versión:

registrar que no existe una versión histórica anterior.

Clasificar las preguntas como:

NUEVA

respecto al historial documental.

Registrar:

- nuevas;
- presentes sin cambios;
- presentes con cambios;
- no presentes;
- reincorporadas;
- cambios de orden;
- cambios de tipo;
- cambios de componente;
- cambios de alternativas;
- cambios de matrices;
- cambios de flujo.

NO eliminar información histórica.


# ============================================================
# 41. CAP[CAPÍTULO]_DICCIONARIO.md
# ============================================================

Documentar el significado de todos los campos.

Como mínimo:

| Campo | Descripción |
|---|---|
| ID | Identificador histórico |
| Año | Año |
| Capítulo | Capítulo |
| Sección | Sección |
| Título de sección | Título |
| Subtítulo de sección | Subtítulo |
| Código | Código oficial |
| Padre | Elemento superior |
| Nivel | Profundidad |
| Orden | Posición |
| Tipo | Tipo de pregunta |
| Componente UI | Componente |
| Título | Título de pregunta |
| Subtítulo | Subtítulo |
| Alternativa | Opción |
| Tipo de opción | Clasificación |
| Campo dependiente | Campo generado |
| Filtro | Condición |
| Salto | Regla |
| Condición | Regla lógica |
| Destino | Pregunta destino |
| Página | Página del PDF |
| Documento fuente | Nombre exacto del PDF |


# ============================================================
# 42. VALIDACIÓN DE PREGUNTAS
# ============================================================

Verificar que NO se hayan convertido en preguntas:

- Persona N°;
- Nombre;
- Informante N°;
- números de ítems de matrices;
- códigos de alternativas;
- códigos de respuesta;
- instrucciones del encuestador;
- números de página.

Verificar:

- cada pregunta tiene código;
- cada pregunta tiene título;
- cada matriz tiene padre;
- cada ítem tiene padre;
- cada salto tiene origen y destino;
- cada alternativa tiene pregunta padre.


# ============================================================
# 43. VALIDACIÓN DE TIPOS
# ============================================================

Selección única:

CERRADA_UNICA
RadioGroup

Selección múltiple:

CERRADA_MULTIPLE
CheckBox

Respuesta abierta:

ABIERTA
EditText

Matriz:

MATRIZ
Matriz

Spinner:

solo si fue definido explícitamente.


# ============================================================
# 44. VALIDACIÓN CRÍTICA DEL CATÁLOGO
# ============================================================

Antes de finalizar:

comparar el catálogo contra:

CAP[CAPÍTULO]_ANALISIS_PAGINAS.md

VALIDACIÓN BIDIRECCIONAL:

1. Toda pregunta del catálogo debe estar respaldada por un bloque
   identificado en el análisis de páginas.

2. Todo bloque identificado como PREGUNTA en el análisis debe tener
   correspondencia en el catálogo.

3. Toda SUBPREGUNTA_MATRIZ con comportamiento propio identificada en
   el análisis debe tener registro en el catálogo.

4. Un ITEM_MATRIZ_SIMPLE NO puede aparecer como pregunta independiente.

5. Todo CAMPO_DOCUMENTO identificado en el análisis NO puede aparecer
   como pregunta.

6. Toda INSTRUCCION_ENCUESTADOR identificada en el análisis NO puede
   aparecer dentro del título de una pregunta, salvo que visualmente
   forme parte del título.

7. Toda pregunta debe tener título literal.

8. Ningún título puede estar truncado, parafraseado o normalizado a
   minúsculas si el PDF lo presenta de otra forma.

9. Toda pregunta/subpregunta debe tener Orden.

10. Código y Orden deben conservarse como campos independientes.

11. Toda alternativa debe tener Código y Orden independientes.

12. Toda alternativa "Otro/Otra/Otros (especifique)" debe tener
    Campo dependiente = EditText cuando la evidencia visual lo
    confirme.

13. Toda matriz debe conservar su relación Padre-Hijo.

14. Toda matriz hija debe tener una condición de visibilidad o una
    explicación de por qué es siempre visible.

15. Toda pregunta que controle una matriz hija debe aparecer tanto en
    CATALOGO.md como en FLUJO.md cuando exista lógica condicional.

16. Debe existir trazabilidad Página → Bloque → Pregunta/ID y
    Pregunta/ID → Página.

17. Revisar específicamente que no existan códigos de pregunta
    detectados visualmente que hayan desaparecido del catálogo por
    error de extracción, columnas o matrices.

18. No asumir que una secuencia numérica es completa. La ausencia de
    un número no significa que deba inventarse una pregunta.

19. No asumir que una secuencia numérica es continua. Si existen
    preguntas 545, 546, 547, 548, 549, 550 y 551, todas deben quedar
    documentadas si el análisis visual las identifica.

# 45. VALIDACIÓN DE LOS SIETE ARCHIVOS
# ============================================================

Antes de informar finalización:

verificar que existan:

CAP[CAPÍTULO]_ANALISIS_PAGINAS.md
CAP[CAPÍTULO]_CATALOGO.md
CAP[CAPÍTULO]_VERSIONES.md
CAP[CAPÍTULO]_ALTERNATIVAS.md
CAP[CAPÍTULO]_FLUJO.md
CAP[CAPÍTULO]_CAMBIOS.md
CAP[CAPÍTULO]_DICCIONARIO.md

Verificar:

- existencia;
- contenido;
- capítulo;
- año;
- coherencia.

Si falta alguno:

NO finalizar.


# ============================================================
# 46. VALIDACIÓN INICIAL
# ============================================================

ANTES de generar definitivamente los documentos:

analizar visualmente la primera página correspondiente al capítulo
y comprobar que el método de reconstrucción funciona.

Mostrar:

- columnas;
- regiones;
- bloques;
- preguntas;
- títulos literales;
- subtítulos;
- tipos;
- componentes;
- alternativas;
- matrices;
- subpreguntas;
- padre-hijo;
- instrucciones;
- saltos;
- elementos que NO son preguntas.

Mostrar también:

| ID | Código | Padre | Nivel | Orden | Tipo | Componente | Título |
|---|---|---|---:|---:|---|---|---|

IMPORTANTE:

Esta validación NO debe detener el proceso simplemente porque exista
una duda local.

Si un fragmento no es legible o existe ambigüedad:
- continuar con el resto;
- marcar el elemento como REVISAR_MANUALMENTE;
- registrar la duda en CAP[CAPÍTULO]_ANALISIS_PAGINAS.md;
- NO inventar información.

Solo detener el procesamiento si no puede determinarse de forma
inequívoca el documento fuente, el capítulo objetivo o si el PDF
no puede procesarse.

# 47. INFORME FINAL
# ============================================================

Mostrar:

## Documento

[NOMBRE DEL PDF]

## Capítulo

[CAPÍTULO]

## Año

[AÑO]

## Preguntas identificadas

Cantidad de preguntas de nivel superior.

## Subpreguntas de matrices

Cantidad y lista de IDs.

## Matrices

Lista.

## Matrices anidadas

Lista con relación Padre-Hijo.

## Preguntas CheckBox

Lista.

## Campos dependientes

Lista de alternativas que activan EditText.

## Saltos

Cantidad y lista de relaciones principales.

## Rango/cobertura de códigos detectados

Lista de códigos oficiales detectados visualmente y confirmación de
que todos tienen correspondencia documental.

## Elementos NO considerados preguntas

Lista.

## Elementos pendientes de revisión manual

Lista, si existe.

## Archivos generados

Los siete archivos.


# ============================================================
# 48. VALIDACIÓN DE COBERTURA Y TRAZABILIDAD
# ============================================================

Esta validación es OBLIGATORIA antes de finalizar.

Construir internamente una matriz de cobertura:

PÁGINA
→ COLUMNA/REGIÓN
→ BLOQUE
→ CÓDIGO/ID
→ CATÁLOGO
→ ALTERNATIVAS
→ FLUJO

Comprobar:

- cada página del capítulo fue revisada;
- cada columna de cada página fue revisada;
- cada bloque relevante fue clasificado;
- cada pregunta detectada visualmente tiene registro;
- cada subpregunta con comportamiento propio tiene registro;
- cada matriz tiene su estructura documentada;
- cada alternativa tiene padre;
- cada campo dependiente tiene evidencia;
- cada salto tiene origen y destino;
- cada pregunta del catálogo tiene página de respaldo.

ATENCIÓN ESPECIAL:

No declarar que el capítulo está completo basándose únicamente en
la numeración de preguntas.

La cobertura se determina por evidencia visual del documento.

Si el PDF contiene una secuencia como:

545
546
547
548
549
550
551

y todos son bloques de pregunta, los siete deben aparecer.

Si un código no aparece, NO inventarlo.

Si un código aparece pero su estructura es ambigua, registrarlo en
ANALISIS_PAGINAS.md como REVISAR_MANUALMENTE y no ocultarlo.

# ============================================================
# 49. REGLA ABSOLUTA
# ============================================================

NO inventar.

NO mezclar PDFs.

NO mezclar columnas.

NO mezclar cuadros.

NO convertir números arbitrariamente en preguntas.

NO convertir ítems de matrices simples en preguntas.

SÍ registrar en CATALOGO.md las subpreguntas de matrices que tengan
comportamiento propio de pregunta.

NO mezclar instrucciones con títulos.

NO truncar, parafrasear, corregir ni cambiar el uso de mayúsculas
de los títulos.

NO asignar Spinner automáticamente.

NO confundir Código con Orden.

NO renumerar preguntas ni alternativas porque falten números.

NO dejar vacío Campo dependiente cuando el PDF muestre
"Otro/Otra/Otros (especifique)" asociado a un campo de texto.

NO eliminar información.

NO finalizar si falta alguno de los siete archivos.

NO declarar cobertura completa si existe una página, columna,
pregunta o subpregunta relevante sin trazabilidad.

PRIORIDAD:

FIDELIDAD VISUAL
>
TRANSCRIPCIÓN LITERAL
>
ESTRUCTURA
>
COBERTURA
>
IDENTIFICACIÓN
>
JERARQUÍA
>
CÓDIGO ≠ ORDEN
>
TIPO
>
ALTERNATIVAS
>
FLUJO
>
TRAZABILIDAD
>
DOCUMENTACIÓN


PDF = [NOMBRE DEL PDF]
CAPÍTULO = [CAPÍTULO]
AÑO = [AÑO]
VERSIÓN DEL PROMPT = V4