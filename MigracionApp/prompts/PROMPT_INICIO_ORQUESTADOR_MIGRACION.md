# ORQUESTADOR DE MIGRACIÓN DE APLICACIÓN ANDROID

## 1. OBJETIVO

Este documento define el proceso completo y controlado para analizar,
diseñar y migrar una aplicación Android legacy hacia una arquitectura
moderna.

La migración debe realizarse de manera incremental, documentada y con
validación humana entre cada etapa.

El objetivo NO es realizar una conversión mecánica del código antiguo.

El objetivo es:

- comprender completamente la aplicación existente;
- identificar sus funcionalidades;
- identificar sus reglas de negocio;
- identificar su modelo de datos;
- identificar sus servicios;
- identificar sus dependencias;
- identificar sus problemas técnicos;
- diseñar una nueva arquitectura;
- validar dicha arquitectura;
- implementar progresivamente la nueva aplicación;
- verificar que la nueva aplicación conserve el comportamiento funcional
  requerido.

--------------------------------------------------
## 2. TECNOLOGÍAS OBJETIVO
--------------------------------------------------

La nueva aplicación deberá utilizar, cuando corresponda:

- Kotlin
- Clean Architecture
- MVVM
- ViewModel
- Coroutines
- Flow
- StateFlow
- Room
- Dagger Hilt
- Repository Pattern
- Retrofit
- OkHttp
- Navigation Component
- XML
- ViewBinding
- RecyclerView

IMPORTANTE:

NO utilizar Jetpack Compose.

La interfaz de usuario deberá desarrollarse mediante:

- XML
- ViewBinding
- Fragment
- Activity cuando corresponda
- RecyclerView
- componentes tradicionales de Android
- vistas dinámicas cuando sean necesarias
- componentes personalizados cuando estén justificados

No introducir una tecnología únicamente porque sea moderna.

Toda tecnología debe tener una justificación técnica.

--------------------------------------------------
## 3. PRINCIPIOS FUNDAMENTALES
--------------------------------------------------

### 3.1 No realizar conversión mecánica

NO convertir automáticamente:

Java → Kotlin

El código nuevo debe diseñarse de acuerdo con la arquitectura objetivo.

El código antiguo debe utilizarse como fuente para comprender:

- comportamiento;
- reglas de negocio;
- validaciones;
- datos;
- procesos;
- servicios;
- navegación.

Pero la implementación nueva puede ser completamente diferente.

--------------------------------------------------

### 3.2 Conservar funcionalidad

La nueva aplicación debe conservar cuando corresponda:

- funcionalidades;
- reglas de negocio;
- validaciones;
- cálculos;
- estados;
- datos;
- procesos;
- sincronización;
- comportamiento funcional.

--------------------------------------------------

### 3.3 Se puede rediseñar

Se permite rediseñar:

- arquitectura;
- clases;
- paquetes;
- nombres;
- persistencia;
- acceso a datos;
- cliente HTTP;
- inyección de dependencias;
- concurrencia;
- navegación;
- interfaz de usuario.

--------------------------------------------------

### 3.4 Reglas obligatorias de generación de documentación

Estas reglas aplican a TODOS los archivos `.md` generados o
modificados durante la migración.

#### 3.4.1 Codificación

- Todos los archivos `.md` DEBEN escribirse en UTF-8.
- NO utilizar UTF-16.
- NO utilizar UTF-32.
- NO utilizar otras codificaciones.
- Evitar BOM salvo que sea estrictamente necesario.

#### 3.4.2 Caracteres inválidos o corruptos

- NO insertar caracteres de control ASCII.
- NO insertar el carácter BEL (ASCII 0x07).
- NO insertar caracteres ESC u otros caracteres de control.
- NO insertar caracteres Unicode extraños o corruptos.
- NO insertar caracteres que no correspondan al contenido original.

Si se detecta cualquier carácter extraño o corrupto:

1. identificar su origen;
2. eliminarlo;
3. regenerar o corregir el contenido;
4. verificar nuevamente el archivo antes de finalizar.

#### 3.4.3 Identificadores provenientes del código fuente

Los nombres obtenidos del código fuente DEBEN conservarse
EXACTAMENTE como aparecen en el código.

Esto aplica a:

- clases;
- interfaces;
- métodos;
- variables;
- atributos;
- constantes;
- paquetes;
- servicios;
- entidades;
- tablas;
- campos;
- endpoints.

NO traducir.

NO corregir ortográficamente.

NO reinterpretar.

NO reemplazar.

NO agregar caracteres.

Ejemplo:

Correcto:

`preguntaController`

Incorrecto:

`preguntaController痞子`

Correcto:

`activity`

Incorrecto:

`ctivity`

#### 3.4.4 Markdown

Los documentos DEBEN utilizar Markdown válido y estándar.

Las tablas deben utilizar:

| Clase | Líneas | Campos | Función |
|---|---:|---|---|

NO utilizar:

\| Clase \| Líneas \| Campos \|

Los encabezados deben utilizar:

# Título

## Subtítulo

NO utilizar:

**## Título**

No escapar innecesariamente caracteres Markdown.

#### 3.4.5 No modificar el contenido técnico

Al documentar código:

- conservar literalmente los nombres técnicos;
- conservar nombres de clases y métodos;
- conservar nombres de variables;
- conservar nombres de archivos;
- conservar rutas;
- conservar endpoints;
- conservar nombres de tablas y columnas.

La documentación puede explicar el significado del código,
pero NO debe alterar los identificadores originales.

#### 3.4.6 Validación obligatoria antes de finalizar un `.md`

Antes de considerar terminado cualquier archivo `.md`:

1. Verificar que el archivo esté en UTF-8.

2. Verificar que NO contenga caracteres de control.

3. Verificar que NO contenga BEL (ASCII 0x07).

4. Verificar que NO contenga caracteres Unicode corruptos.

5. Verificar que los identificadores técnicos coincidan
   exactamente con el código fuente.

6. Verificar que las tablas sean Markdown válido.

7. Verificar que los encabezados sean Markdown válido.

8. Verificar que no existan caracteres introducidos
   accidentalmente durante la generación.

9. Si se detecta cualquier problema, corregirlo antes de
   entregar el documento.

#### 3.4.7 No inventar información

La documentación DEBE basarse en el código fuente analizado
y en la información proporcionada por el usuario.

NO inventar:

- clases;
- métodos;
- variables;
- relaciones;
- funcionalidades;
- dependencias;
- reglas de negocio;
- cantidades;
- nombres técnicos.

Si una información no puede determinarse:

`NO DETERMINADO EN EL CÓDIGO`

--------------------------------------------------
## 4. REGLAS DE ANÁLISIS
--------------------------------------------------

Durante las etapas de análisis:

- NO modificar código fuente.
- NO eliminar archivos.
- NO refactorizar.
- NO actualizar dependencias.
- NO cambiar configuraciones.
- NO convertir código Java a Kotlin.
- NO implementar funcionalidades.
- NO crear código de producción.

Las conclusiones deben basarse en el código existente.

Cuando una información no pueda determinarse:

"NO DETERMINADO EN EL CÓDIGO"

Distinguir entre:

HECHO
Información encontrada directamente en el código.

INFERENCIA
Conclusión razonable derivada del código.

PROPUESTA
Recomendación para la nueva arquitectura.

Nunca presentar una inferencia o propuesta como si fuera un hecho.

--------------------------------------------------
## 5. ALCANCE DEL ANÁLISIS
--------------------------------------------------

Si el usuario indica un módulo, modo o funcionalidad específica:

analizar solamente dicho alcance.

Sin embargo, realizar análisis recursivo de dependencias para detectar
componentes compartidos.

Clasificar componentes como:

[S] Exclusivo del módulo solicitado

[C] Compartido con otros módulos

[O] Exclusivo de otros módulos

Si existe una dependencia con otro módulo:

documentarla explícitamente.

NO eliminar una dependencia solamente porque pertenece a otro módulo.

--------------------------------------------------
# 6. FLUJO GENERAL DE TRABAJO
--------------------------------------------------

El proceso completo será:

ETAPA 1
Inventario técnico

↓

ETAPA 2
Arquitectura actual

↓

ETAPA 3
Flujos funcionales

↓

ETAPA 4
Base de datos

↓

ETAPA 5
API y red

↓

ETAPA 6
Reglas de negocio

↓

ETAPA 7
Riesgos y ANR

↓

CONOCIMIENTO DE LA APLICACIÓN

↓

ETAPA 8
Arquitectura objetivo

↓

ETAPA 9
Auditoría pre-implementación

↓

APROBACIÓN HUMANA

↓

BUILD

↓

FASE 1 DE IMPLEMENTACIÓN

↓

VALIDACIÓN

↓

FASE 2

↓

VALIDACIÓN

↓

FASE 3

↓

...

--------------------------------------------------
# 7. CONTROL DE CALIDAD DE CADA ETAPA
--------------------------------------------------

Cada etapa tiene dos estados:

EN REVISIÓN

APROBADA

Nunca considerar automáticamente una etapa como aprobada.

--------------------------------------------------
## 7.1 Al finalizar una etapa
--------------------------------------------------

Al terminar cualquier etapa:

1. Ejecutar el análisis correspondiente.
2. Generar o actualizar la documentación.
3. Verificar internamente los resultados.
4. Identificar información faltante.
5. Identificar contradicciones.
6. Identificar dependencias no documentadas.
7. Informar los resultados.
8. Indicar problemas encontrados.
9. Indicar decisiones pendientes.
10. Marcar la etapa como:

EN REVISIÓN

11. DETENERSE.

NO avanzar automáticamente.

--------------------------------------------------
## 7.2 Revisión del usuario
--------------------------------------------------

El usuario revisará la documentación generada.

Puede:

- aprobar;
- agregar observaciones;
- solicitar correcciones;
- agregar información adicional;
- cuestionar una conclusión;
- solicitar una nueva investigación.

Mientras existan observaciones pendientes:

NO avanzar a la siguiente etapa.

--------------------------------------------------
## 7.3 Corrección de una etapa
--------------------------------------------------

Si el usuario escribe:

"CORREGIR ETAPA N"

se debe:

1. Leer las observaciones.
2. Revisar nuevamente el código fuente.
3. Verificar cada observación.
4. Determinar si la observación es:
   - confirmada;
   - parcialmente confirmada;
   - no encontrada;
   - contradictoria.
5. Corregir la documentación.
6. Realizar investigación adicional si es necesario.
7. Actualizar los documentos afectados.
8. Actualizar el estado de continuidad.
9. Marcar nuevamente la etapa como:

EN REVISIÓN

10. DETENERSE.

NO avanzar a la siguiente etapa.

--------------------------------------------------
## 7.4 Aprobación de una etapa
--------------------------------------------------

Solo se podrá avanzar cuando el usuario indique explícitamente:

"ETAPA N APROBADA"

Ejemplos:

ETAPA 1 APROBADA

ETAPA 2 APROBADA

ETAPA 3 APROBADA

La aprobación explícita permite iniciar la siguiente etapa.

NO interpretar automáticamente como aprobación:

- OK
- está bien
- correcto
- continúa
- adelante
- parece correcto

si todavía existen observaciones pendientes.

--------------------------------------------------
## 7.5 Nueva información proporcionada por el usuario
--------------------------------------------------

Si el usuario proporciona información adicional:

1. Analizarla.
2. Verificarla contra el código cuando sea posible.
3. Determinar si confirma, complementa o contradice la documentación.
4. Actualizar los documentos correspondientes.
5. Registrar la decisión.
6. Mantener la etapa en:

EN REVISIÓN

7. DETENERSE.

--------------------------------------------------
## 7.6 Regla absoluta
--------------------------------------------------

NUNCA avanzar automáticamente de una etapa a otra.

La transición entre etapas requiere aprobación explícita del usuario.

--------------------------------------------------
# 8. ETAPA 1 - INVENTARIO TÉCNICO
--------------------------------------------------

Ejecutar:

prompts/PROMPT_01_INVENTARIO_TECNICO.md

Resultado:

docs/01_INVENTARIO_TECNICO.md

Objetivo:

Conocer:

- estructura;
- módulos;
- paquetes;
- clases;
- Activities;
- Fragments;
- Services;
- componentes;
- librerías;
- persistencia;
- red;
- configuración;
- concurrencia;
- UI.

Al finalizar:

Estado = EN REVISIÓN

DETENERSE.

Esperar:

ETAPA 1 APROBADA

--------------------------------------------------
# 9. ETAPA 2 - ARQUITECTURA ACTUAL
--------------------------------------------------

Ejecutar:

prompts/PROMPT_02_ARQUITECTURA_ACTUAL.md

Utilizar:

docs/01_INVENTARIO_TECNICO.md

Resultado:

docs/02_ARQUITECTURA_ACTUAL.md

Determinar la arquitectura REAL.

No asumir MVC, MVP, MVVM o Clean Architecture.

Al finalizar:

Estado = EN REVISIÓN

DETENERSE.

Esperar:

ETAPA 2 APROBADA

--------------------------------------------------
# 10. ETAPA 3 - FLUJOS FUNCIONALES
--------------------------------------------------

Ejecutar:

prompts/PROMPT_03_FLUJOS_FUNCIONALES.md

Utilizar:

docs/01_INVENTARIO_TECNICO.md
docs/02_ARQUITECTURA_ACTUAL.md

Resultado:

docs/03_FLUJOS_FUNCIONALES.md

Reconstruir los principales flujos.

Al finalizar:

Estado = EN REVISIÓN

DETENERSE.

Esperar:

ETAPA 3 APROBADA

--------------------------------------------------
# 11. ETAPA 4 - BASE DE DATOS
--------------------------------------------------

Ejecutar:

prompts/PROMPT_04_BASE_DATOS.md

Utilizar:

docs/01_INVENTARIO_TECNICO.md
docs/02_ARQUITECTURA_ACTUAL.md
docs/03_FLUJOS_FUNCIONALES.md

Resultado:

docs/04_BASE_DATOS.md

Analizar:

- tablas;
- columnas;
- PK;
- FK;
- índices;
- relaciones;
- consultas;
- transacciones;
- operaciones.

Al finalizar:

Estado = EN REVISIÓN

DETENERSE.

Esperar:

ETAPA 4 APROBADA

--------------------------------------------------
# 12. ETAPA 5 - API Y RED
--------------------------------------------------

Ejecutar:

prompts/PROMPT_05_API_RED.md

Resultado:

docs/05_API_Y_RED.md

Analizar:

- endpoints;
- requests;
- responses;
- autenticación;
- errores;
- timeout;
- SSL/TLS;
- serialización.

No asumir XML o JSON.

Determinar el formato real.

Al finalizar:

Estado = EN REVISIÓN

DETENERSE.

Esperar:

ETAPA 5 APROBADA

--------------------------------------------------
# 13. ETAPA 6 - REGLAS DE NEGOCIO
--------------------------------------------------

Ejecutar:

prompts/PROMPT_06_REGLAS_NEGOCIO.md

Resultado:

docs/06_REGLAS_NEGOCIO.md

Identificar las reglas reales.

Especial atención a:

- validaciones;
- cálculos;
- estados;
- condiciones;
- procesos;
- sincronización;
- recuperaciones;
- restricciones.

Al finalizar:

Estado = EN REVISIÓN

DETENERSE.

Esperar:

ETAPA 6 APROBADA

--------------------------------------------------
# 14. ETAPA 7 - RIESGOS Y ANR
--------------------------------------------------

Ejecutar:

prompts/PROMPT_07_RIESGOS_ANR.md

Resultado:

docs/07_RIESGOS_Y_ANR.md

Analizar:

- ANR;
- Main Thread;
- SQLite;
- HTTP;
- archivos;
- GPS;
- concurrencia;
- memoria;
- NullPointerException;
- deadlocks;
- APIs deprecated;
- compatibilidad Android moderno.

Clasificar:

CRÍTICO
ALTO
MEDIO
BAJO

Al finalizar:

Estado = EN REVISIÓN

DETENERSE.

Esperar:

ETAPA 7 APROBADA

--------------------------------------------------
# 15. FINALIZACIÓN DE ETAPAS 1-7
--------------------------------------------------

Cuando las etapas 1-7 hayan sido aprobadas:

considerar que se ha completado:

"CONOCIMIENTO DE LA APLICACIÓN"

NO comenzar la arquitectura objetivo automáticamente.

Esperar:

"CONTINUAR CON ETAPA 8"

--------------------------------------------------
# 16. ETAPA 8 - ARQUITECTURA OBJETIVO
--------------------------------------------------

Ejecutar:

prompts/PROMPT_08_ARQUITECTURA_OBJETIVO.md

Utilizar toda la documentación aprobada:

docs/01_INVENTARIO_TECNICO.md
docs/02_ARQUITECTURA_ACTUAL.md
docs/03_FLUJOS_FUNCIONALES.md
docs/04_BASE_DATOS.md
docs/05_API_Y_RED.md
docs/06_REGLAS_NEGOCIO.md
docs/07_RIESGOS_Y_ANR.md

Diseñar:

- Clean Architecture;
- MVVM;
- Domain;
- Data;
- Presentation;
- Room;
- Hilt;
- Coroutines;
- Flow;
- Retrofit;
- OkHttp;
- XML;
- ViewBinding;
- Navigation.

NO implementar código.

Resultado:

docs/08_ARQUITECTURA_OBJETIVO.md

Al finalizar:

Estado = EN REVISIÓN

DETENERSE.

Esperar:

ETAPA 8 APROBADA

--------------------------------------------------
# 17. ETAPA 9 - AUDITORÍA PRE-IMPLEMENTACIÓN
--------------------------------------------------

Ejecutar:

prompts/PROMPT_09_AUDITORIA_PRE_IMPLEMENTACION.md

Utilizar todos los documentos anteriores.

Verificar:

- funcionalidades;
- tablas;
- endpoints;
- reglas;
- navegación;
- dependencias;
- arquitectura;
- riesgos;
- ANR;
- seguridad;
- migración.

Resultado:

docs/09_AUDITORIA_PRE_IMPLEMENTACION.md

Si existen problemas:

NO implementar.

Documentar los problemas.

Estado:

EN REVISIÓN

DETENERSE.

--------------------------------------------------
# 18. APROBACIÓN PARA IMPLEMENTACIÓN
--------------------------------------------------

Después de completar la Etapa 9:

NO comenzar automáticamente la implementación.

Esperar explícitamente:

"APROBADO PARA IMPLEMENTACIÓN"

Esta frase significa que el usuario autoriza pasar de PLAN a BUILD.

Hasta recibir esa autorización:

- NO modificar código;
- NO crear código Kotlin;
- NO crear el nuevo proyecto;
- NO cambiar dependencias;
- NO iniciar ninguna fase de implementación.

--------------------------------------------------
# 19. CAMBIO DE PLAN A BUILD
--------------------------------------------------

Cuando el usuario indique:

"APROBADO PARA IMPLEMENTACIÓN"

cambiar a modo BUILD.

Ejecutar:

prompts/PROMPT_10_IMPLEMENTACION.md

Pero implementar de manera incremental.

NO implementar toda la aplicación de una sola vez.

--------------------------------------------------
# 20. FASES DE IMPLEMENTACIÓN
--------------------------------------------------

Cada fase tendrá:

1. Preparación.
2. Implementación.
3. Compilación.
4. Pruebas.
5. Revisión.
6. Documentación.
7. Validación humana.

--------------------------------------------------
# 21. PREPARACIÓN DE CADA FASE
--------------------------------------------------

Antes de modificar archivos:

informar:

- objetivo de la fase;
- funcionalidades involucradas;
- archivos que se crearán;
- archivos que se modificarán;
- dependencias;
- riesgos;
- criterios de aceptación.

No modificar archivos fuera del alcance de la fase.

--------------------------------------------------
# 22. IMPLEMENTACIÓN DE LA FASE
--------------------------------------------------

Implementar solamente la fase autorizada.

Respetar:

- Kotlin;
- Clean Architecture;
- MVVM;
- Room;
- Hilt;
- Coroutines;
- Flow;
- Retrofit;
- OkHttp;
- XML;
- ViewBinding.

NO utilizar Compose.

--------------------------------------------------
# 23. COMPILACIÓN
--------------------------------------------------

Después de implementar:

1. Compilar.
2. Analizar errores.
3. Corregir errores relacionados con la fase.
4. Volver a compilar.

No introducir cambios no relacionados con la fase.

--------------------------------------------------
# 24. PRUEBAS
--------------------------------------------------

Realizar las pruebas correspondientes.

Cuando corresponda:

- Unit Tests;
- DAO Tests;
- Repository Tests;
- UseCase Tests;
- ViewModel Tests;
- UI Tests;
- pruebas de navegación;
- pruebas funcionales.

También verificar:

- Main Thread;
- errores de red;
- errores de BD;
- estados;
- navegación.

--------------------------------------------------
# 25. DOCUMENTACIÓN DE LA FASE
--------------------------------------------------

Al finalizar cada fase generar:

docs/IMPLEMENTACION_FASE_XX.md

Debe contener:

- objetivo;
- implementación;
- archivos creados;
- archivos modificados;
- decisiones;
- problemas;
- pruebas;
- resultado;
- pendientes.

Estado:

EN REVISIÓN

DETENERSE.

--------------------------------------------------
# 26. VALIDACIÓN DE FASE
--------------------------------------------------

Esperar explícitamente:

"FASE X APROBADA"

Ejemplo:

FASE 1 APROBADA

Solo entonces iniciar la siguiente fase.

--------------------------------------------------
# 27. CORRECCIÓN DE UNA FASE
--------------------------------------------------

Si el usuario indica:

"CORREGIR FASE X"

o proporciona observaciones:

1. Analizar las observaciones.
2. Revisar el código.
3. Corregir únicamente lo relacionado con la fase.
4. Compilar.
5. Probar.
6. Documentar.
7. Marcar nuevamente:

EN REVISIÓN

8. DETENERSE.

NO avanzar.

--------------------------------------------------
# 28. NUEVOS DESCUBRIMIENTOS DURANTE BUILD
--------------------------------------------------

Si durante la implementación aparece:

- una tabla no documentada;
- endpoint no documentado;
- regla de negocio no documentada;
- dependencia no documentada;
- funcionalidad no documentada;
- problema arquitectónico;
- incompatibilidad;
- información contradictoria;

DETENER LA IMPLEMENTACIÓN DE LA FASE.

Documentar:

- qué se encontró;
- dónde;
- por qué afecta;
- impacto;
- opciones;
- recomendación.

Esperar decisión del usuario.

--------------------------------------------------
# 29. REGLA SOBRE EL CÓDIGO ANTIGUO
--------------------------------------------------

El código antiguo es una fuente de conocimiento.

NO modificarlo durante el análisis.

Durante la implementación:

NO eliminar código antiguo sin autorización explícita.

NO realizar refactorizaciones del código antiguo que no sean necesarias
para la migración autorizada.

--------------------------------------------------
# 30. ESTADO DE LA MIGRACIÓN
--------------------------------------------------

Mantener actualizado:

docs/ESTADO_MIGRACION.md

Este archivo es el tablero de control del proyecto y debe reflejar
siempre el estado real de la migración.

Debe indicar:

## Aplicación

Nombre de la aplicación.

## Módulo

Módulo o funcionalidad que se está migrando.

## Etapa actual

Etapa en la que se encuentra el proyecto.

## Estado de la etapa

Uno de:

- EN REVISIÓN
- APROBADA
- BLOQUEADA

## Modo OpenCode

Uno de:

- PLAN
- BUILD

## Etapas de análisis

Registrar:

| Etapa | Descripción | Estado |
|---|---|---|
| 1 | Inventario Técnico | |
| 2 | Arquitectura Actual | |
| 3 | Flujos Funcionales | |
| 4 | Base de Datos | |
| 5 | API y Red | |
| 6 | Reglas de Negocio | |
| 7 | Riesgos y ANR | |
| 8 | Arquitectura Objetivo | |
| 9 | Auditoría Pre-Implementación | |

## Observaciones pendientes

Registrar todas las observaciones que el usuario haya indicado y
que todavía no hayan sido resueltas.

## Decisiones tomadas

Registrar las decisiones técnicas y funcionales aprobadas.

## Decisiones pendientes

Registrar las decisiones que requieren confirmación del usuario.

## Problemas encontrados

Registrar problemas técnicos encontrados durante el análisis
o implementación.

## Implementación

Indicar:

- estado de implementación;
- fase actual;
- fases completadas;
- fases pendientes.

## Archivos creados

Registrar los archivos creados durante la implementación.

## Archivos modificados

Registrar los archivos modificados durante la implementación.

## Próxima acción

Indicar exactamente cuál es la siguiente acción autorizada.

## Bloqueos

Registrar cualquier situación que impida continuar.

## Regla de continuidad

NUNCA utilizar este archivo como autorización para avanzar.

La autorización siempre requiere una instrucción explícita del usuario.

Ejemplos:

"ETAPA 1 APROBADA"

"ETAPA 2 APROBADA"

"APROBADO PARA IMPLEMENTACIÓN"

"FASE 1 APROBADA"

El archivo solamente refleja el estado actual del proyecto.

Este archivo debe permitir que el trabajo continúe después de cerrar
la sesión sin perder el contexto.

--------------------------------------------------
# 31. COMANDOS DE CONTROL
--------------------------------------------------

Los siguientes comandos tienen significado explícito.

### INICIAR ETAPA 1

Ejecutar únicamente la Etapa 1.

### CORREGIR ETAPA N

Corregir la etapa indicada.

NO avanzar.

### ETAPA N APROBADA

Autoriza iniciar la siguiente etapa.

### CONTINUAR CON ETAPA 8

Iniciar Etapa 8 después de haber aprobado las etapas 1-7.

### CONTINUAR CON ETAPA 9

Iniciar Etapa 9.

### APROBADO PARA IMPLEMENTACIÓN

Autoriza pasar de PLAN a BUILD.

### INICIAR FASE N

Preparar la fase de implementación indicada.

### FASE N APROBADA

Autoriza iniciar la siguiente fase.

### CORREGIR FASE N

Corregir solamente la fase indicada.

### DETENER

No realizar modificaciones.

--------------------------------------------------
# 32. REGLA DE SEGURIDAD FINAL
--------------------------------------------------

NUNCA:

- avanzar automáticamente;
- asumir aprobación;
- implementar varias fases simultáneamente;
- modificar código fuera del alcance;
- eliminar código antiguo sin autorización;
- actualizar dependencias sin autorización;
- introducir Compose;
- ignorar reglas de negocio;
- ignorar observaciones del usuario;
- inventar información;
- ocultar errores de compilación;
- ocultar problemas arquitectónicos.

Si existe incertidumbre importante:

DETENERSE.

Informar la incertidumbre.

Solicitar decisión.

--------------------------------------------------
# 33. PRINCIPIO FINAL
--------------------------------------------------

El proceso debe funcionar así:

ANALIZAR
    ↓
DOCUMENTAR
    ↓
REVISAR
    ↓
CORREGIR SI ES NECESARIO
    ↓
REVISAR NUEVAMENTE
    ↓
APROBAR
    ↓
SIGUIENTE ETAPA

Y durante implementación:

IMPLEMENTAR
    ↓
COMPILAR
    ↓
PROBAR
    ↓
DOCUMENTAR
    ↓
REVISAR
    ↓
CORREGIR SI ES NECESARIO
    ↓
VALIDAR
    ↓
SIGUIENTE FASE

La decisión de avanzar siempre corresponde al usuario.

NO avanzar automáticamente.

==================================================
FIN DEL ORQUESTADOR
==================================================