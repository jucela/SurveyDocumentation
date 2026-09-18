# 02 - ARQUITECTURA ACTUAL

## REFERENCIA

- docs/01_INVENTARIO_TECNICO.md
- Verificacion directa contra codigo fuente

---

# 1. CAPAS ACTUALES

## 1.1 Presentation Layer

| Subcapa | Clases | Lineas | Funcion |
|---------|--------|--------|---------|
| **Activities** | MasterActivity | ~120 | Contenedor principal con Navigation Drawer |
| | AbstractCuestionarioFragmentActivity | 654 | Base para cuestionarios, gestiona fragments, ViewPager,-sidebar, datos temporales |
| | CuestionarioFragmentActivity | 1200+ | Activity concreta del cuestionario, coordina fragments, datos de captura |
| **Fragment Base** | FragmentForm | 7530 | Base para todos los fragments de cuestionarios, contenedor de preguntas/respuestas |
| | FragmentFormWithoutXML | ~200 | Fragment sin layout XML predefinido |
| | FragmentFormDNCE | ~300 | Base para fragments DNCE |
| | FragmentFormEnaho | ~200 | Base para fragments ENAHO |
| **Fragmentes Concretos** | Cap00Fragment, Cap01Fragment, ... Cap30Fragment | Variable | 100+ fragments de secciones del cuestionario |
| | VisitasFragment, RutaFragment, MensajesFragment, PlanRutaFragment | Variable | Fragments de navegacion y gestion |
| **Componentes UI** | ButtonComponent, QuestionSection, AceptarCancelarDialogEnaho | Variable | Componentes reutilizables de interfaz |

**Total Presentation:** 22 Activities, 100+ Fragments

## 1.2 Business Layer (Controllers)

| Clase | Lineas | Campos | Funcion |
|-------|--------|--------|---------|
| AbstractController | ~200 | 7 (fragmento, cuestionarioContext, preguntaController,痞子, validationHandler, validator, gson) | Base para controllers, maneja validaciones y navegacion |
| Cap200Controller | ~400 | 7+servicios | Controller para Cap 200, gestiona miembros del hogar |
| AbstractVisitaController | ~150 | Variable | Controller base para visitas |
| Controllers especificos | Variable | Variable | 5+ controllers adicionales |

**Total Business:** 7+ controllers

**Problema critico:** Controllers hacen casting directo a CuestionarioFragmentActivity:
- AbstractController.java:94 - ((CuestionarioFragmentActivity) fragmento.getActivity()).setLyexterno(scroll)
- Cap200Controller.java:99 - ctivity = ((CuestionarioFragmentActivity)getFragmento().getActivity())

## 1.3 Service Layer

| Clase | Lineas | Dependencias | Funcion |
|-------|--------|--------------|---------|
| AbstractService<D> | ~100 | DAO generico | Wrapper delgado sobre DAO, stubs de transacciones no-op |
| EnahoWebService | 1950 | EnahoHttpClient, AsyncHttpClient, Fragment types | Servicio HTTP principal, 25+ metodos |
| HogarService | ~800 | HogarDao, Cap200Dao | Gestion de hogares |
| ViviendaService | ~400 | ViviendaDao | Gestion de viviendas |
| MarcoService | ~300 | MarcoDAO | Marco de muestreo |
| SegmentacionService | ~350 | SegmentacionDao | Segmentacion |
| Enaho01Service - Enaho04Service | Variable | DAOs especificos | Servicios por modulo ENAHO |
| CopiaBDService | ~100 | File I/O | Backup de base de datos |
| DiagnosticoService | ~200 | SharedPreferences | Diagnostico ANR |
| InicializacionService | ~150 | - | Login y inicializacion |
| 10+ servicios adicionales | Variable | Variable | Otros modulos |

**Total Service:** 18+ servicios

**Problema critico:** Servicios aceptan Activity/FragmentActivity como parametros:
- EnahoWebService - 13+ metodos con Activity activity
- ViviendaService - 2 metodos con Activity/FragmentActivity
- HogarService - 4 metodos con Activity
- CopiaBDService - 2 metodos con Activity

## 1.4 Data Layer (DAO + Entities)

| Componente | Lineas | Cantidad | Funcion |
|------------|--------|----------|---------|
| SQLiteDAO<T extends Entity> | ~1600 | 1 base | ORM completo por refleccion, CRUD, 3-level savepoint |
| Entity | ~400 | 1 base | Mapeo por refleccion, fillEntity(), getContentValues() |
| MarcoEntity | ~200 | 1 | Entidad de marco muestral |
| ViviendaEntity | ~300 | 1 | Entidad de vivienda |
| Hogar | ~250 | 1 | Entidad de hogar |
| Cap200Entity | ~200 | 1 | Entidad de miembros |
| Cap100Entity - Cap900Entity | Variable | 180+ | Entidades por modulo ENAHO |
| ResultadoEntity, ResultadoCapituloEntity | Variable | 2 | Entidades de resultados |

**Total Data:** 1 base DAO + 17+ DAOs concretos, 185+ Entity derivadas

## 1.5 Network Layer

| Clase | Lineas | Dependencias | Funcion |
|-------|--------|--------------|---------|
| EnahoHttpClient | ~500 | HttpsURLConnection, TrustManager personalizado | Cliente HTTP custom con SSL hardcoded |
| AsyncHttpClient (loopj) | Libreria | - | Cliente HTTP alternativo |

**Configuracion SSL hardcoded (4 ubicaciones):**
- RutasFragment.java:192-199
- LoginActivity.java:65-72
- CuestionarioFragmentActivity.java:845-852
- Conexion.java:43-50

**Total Network:** 2 clientes HTTP, 25+ metodos HTTP en EnahoWebService

## 1.6 Persistence Layer

| Clase | Lineas | Cantidad | Funcion |
|-------|--------|----------|---------|
| MyDatabaseHelper | ~5000+ | 1 | SQLiteOpenHelper, esquema v56, 300+ CREATE TABLE, 30+ CREATE INDEX |
| AbstractSharedPreferences | ~100 | 1 | Wrapper SharedPreferences |
| RutasFiles | ~200 | 1 | I/O de archivos de rutas |
| ModuloFiles | ~200 | 1 | I/O de archivos de modulos |
| ArchivoModulo | ~100 | 1 | Manejo de archivos modulo |

**Total Persistence:** 1 DatabaseHelper, 2 SharedPreferences, 3 File I/O helpers

## 1.7 Infrastructure Layer

| Clase | Lineas | Funcion |
|-------|--------|---------|
| ModelContext | ~200 | Service Locator: crea todas las instancias de services/DAOs manualmente |
| AppContext | ~100 | Contexto global estatico: ModelContext + PersonalEntity + PIN |
| Aplicacion | ~50 | Application singleton, inicializa ModelContext |
| AnrWatchDog | ~100 | Deteccion de ANR (Application Not Responding) |
| DiagnosticoManager | ~300 | Manejo de diagnosticos ANR |
| DiagnosticoEstado | ~150 | Estado del diagnostico |
| DiagnosticoLogger | ~200 | Logger de diagnosticos |
| Validator / ValidationHandler | ~200 | Validaciones de formularios |
| CuestionarioContext | ~100 | Contexto del cuestionario actual |

**Total Infrastructure:** 9+ clases

---

# 2. DEPENDENCIAS REALES

## 2.1 Flujo de Dependencias

`
UI Layer
  |
  +--> AppContext.getServices() [acceso estatico global]
  |       |
  |       +--> XxxService
  |               |
  |               +--> XxxDAO (via AbstractService.setDao())
  |                       |
  |                       +--> MyDatabaseHelper (SQLiteOpenHelper)
  |                               |
  |                               +--> SQLite Database
  |
  +--> EnahoWebService
          |
          +--> EnahoHttpClient (HttpURLConnection)
          |       |
          |       +--> HTTPS Endpoints (hardcoded URLs)
          |
          +--> AsyncHttpClient (loopj)
                  |
                  +--> HTTP Endpoints
`

## 2.2 Dependencias por Clase

### AppContext (Punto central - 100+ referencias)
`java
// Patron: AppContext.getServices().getXxxService()
// Uso en Activities: 52+ llamadas en 8 archivos
// Uso en Fragments: 100+ llamadas en 50+ archivos
`

### ModelContext (Service Locator)
`java
// Crea manualmente todas las instancias:
hogarService.setDao(new HogarDao(openHelper));
marcoService.setDao(new MarcoDAO(openHelper));
viviendaService.setDao(new ViviendaDao(openHelper));
// ... 18+ servicios mas
`

### AbstractController (Dependencia directa a Activity)
`java
// Campos del controller:
fragmento: FragmentForm
cuestionarioContext: CuestionarioContext
preguntaController: PreguntaController
validator: Validator
validationHandler: ValidationHandler
gson: Gson

// Casting problem:
((CuestionarioFragmentActivity) fragmento.getActivity()).setLyexterno(scroll);
`

---

# 3. CLASES CENTRALES

## 3.1 Top 10 Clases por Acoplamiento

| Rank | Clase | Lineas | Referencias | Responsabilidad |
|------|-------|--------|-------------|-----------------|
| 1 | AppContext | ~100 | 100+ | Service Locator global, estado global |
| 2 | ModelContext | ~200 | 50+ | Creador manual de servicios |
| 3 | FragmentForm | 7530 | 100+ | Base de fragments, contenedor de UI/logica |
| 4 | SQLiteDAO | 1600 | 185+ | ORM completo por refleccion |
| 5 | AbstractController | ~200 | 7+ | Coordinacion UI-Negocio |
| 6 | EnahoWebService | 1950 | 25+ metodos | Comunicacion HTTP |
| 7 | Entity | ~400 | 185+ | Mapeo de datos |
| 8 | CuestionarioFragmentActivity | 1200+ | 50+ | Activity principal |
| 9 | MyDatabaseHelper | 5000+ | 1 | Esquema de BD |
| 10 | InfoGPS | ~300 | 1 Activity | Servicio GPS con referencia a Activity |

## 3.2 Clases con Multiples Responsabilidades

### FragmentForm (7530 lineas)
- Construccion de UI (50+ metodos de layout)
- Logica de negocio (validaciones, calculos)
- Navegacion entre fragments
- Persistencia directa (lectura/escritura de BD via servicios)
- Comunicacion HTTP (via servicios)
- Manejo de estado temporal

### EnahoWebService (1950 lineas)
- 25+ metodos HTTP
- Parseo de JSON (Gson)
- Acceso a Fragment fields directamente
- Manejo de errores
- Logging

### SQLiteDAO (1600 lineas)
- CRUD completo por refleccion
- Manejo de transacciones (3-level savepoint)
- Parseo de Entity (fillEntity)
- Generacion de SQL dinamico

---

# 4. DEPENDENCIAS CIRCULARES

## Ciclo 1: Activity <-> Service (CRITICO)

`
CuestionarioFragmentActivity
  |
  +--> InfoGPS (via bindService)
          |
          +--> CuestionarioFragmentActivity (via field reference)
                  |
                  +--> getNro_captura() / setNro_captura()
`

**Archivos:**
- CuestionarioFragmentActivity.java:73,1001-1010 - importa y usa InfoGPS
- InfoGPS.java:8,52,70,108,286 - importa y almacena referencia a Activity

**Riesgo:** Memory leak si Activity se destruye mientras Service esta corriendo

## Ciclo 2: Service <-> Fragment (CRITICO)

`
EnahoWebService
  |
  +--> MensajesFragment (import, parameter)
  |       |
  |       +--> EnahoWebService (via AppContext.getServices())
  |
  +--> PlanRutaFragment (import, parameter)
          |
          +--> EnahoWebService (via AppContext.getServices())
`

**Archivos:**
- EnahoWebService.java:60-61,757-776,796-827,1026-1055 - importa y escribe a Fragment fields
- MensajesFragment.java:11,38 - importa EnahoWebService
- PlanRutaFragment.java:13,46 - importa EnahoWebService

**Problema:** Service escribe directamente a campos publicos del Fragment

## Ciclo 3: Service con parametros Activity (HIGH)

`
EnahoWebService
  +--> invokeWSUploadFileExportacion1(Activity activity, ...)
  +--> invokeDownload1(Activity activity, ...)
  +--> importarDataInicial1(FragmentActivity activity, ...)
  +--> ... (13+ metodos)

ViviendaService
  +--> eliminarVivienda(Activity activity, ...)
  +--> guardarDataInicialReentrevista(FragmentActivity activity, ...)

HogarService
  +--> generarCopiaBD(Activity activity)
  +--> eliminarHogar(Activity activity, ...)
`

**Total:** 17+ metodos que aceptan Activity/FragmentActivity

---

# 5. FLUJOS DE DATOS

## 5.1 Flujo Local (UI -> SQLite)

`
FragmentForm
  |
  +--> Controller.getXxxService() [via AppContext.getServices()]
  |       |
  |       +--> XxxService.guardar(entity)
  |               |
  |               +--> XxxDAO.insert/update(entity)
  |                       |
  |                       +--> entity.getContentValues() [refleccion]
  |                               |
  |                               +--> database.insert/update()
`

**Ejemplo real - Guardar vivienda:**
`java
// FragmentFormEnaho.java
viviendaService.guardar(viviendaEntity);

// ViviendaService.java
public void guardar(ViviendaEntity vivienda) {
    dao.insert(vivienda);  // AbstractService delega a DAO
}

// SQLiteDAO.java
public int insert(T entity) {
    ContentValues values = entity.getContentValues();  // refleccion
    return database.insert(tableName, null, values);
}
`

## 5.2 Flujo Remoto (UI -> Server)

`
FragmentForm
  |
  +--> EnahoWebService
          |
          +--> EnahoHttpClient.invokeUpload(url, params, file)
          |       |
          |       +--> HttpsURLConnection (SSL hardcoded)
          |               |
          |               +--> HTTPS POST/GET
          |
          +--> AsyncHttpClient.post(url, params)
                  |
                  +--> HTTP POST
`

**Ejemplo real - Subir datos:**
`java
// EnahoWebService.java
public void invokeWSUploadFileExportacion1(final Activity activity, ...) {
    EnahoHttpClient.invokeUpload(URL_UPLOAD, params, new AsyncHttpResponseHandler() {
        @Override
        public void onSuccess(String response) {
            // Parseo JSON
            // Actualizacion UI via activity.runOnUiThread()
        }
    });
}
`

---

# 6. PERSISTENCIA

## 6.1 Arquitectura de Persistencia

`
MyDatabaseHelper (SQLiteOpenHelper)
  |
  +--> onCreate() - 300+ CREATE TABLE statements
  +--> onUpgrade() - Migraciones por version (v1 -> v56)
  |
  +--> SQLiteDAO<T extends Entity>
          |
          +--> insert(entity) - via entity.getContentValues()
          +--> update(entity) - via entity.getContentValues()
          +--> delete(id)
          +--> findById(id) - via entity.fillEntity(cursor)
          +--> findAll()
          +--> saveWithTransaction(entity) - 3-level savepoint
`

## 6.2 Patron Entity (Refleccion)

`java
// Entity base.java
public abstract class Entity {
    public abstract void fillEntity(Cursor cursor);  // refleccion
    public abstract ContentValues getContentValues(); // refleccion
}

// Ejemplo: ViviendaEntity
public class ViviendaEntity extends Entity {
    private String idVivienda;
    private String nombre;
    // ... 20+ campos
    
    @Override
    public void fillEntity(Cursor cursor) {
        // Mapeo manual de cursor -> campos
    }
    
    @Override
    public ContentValues getContentValues() {
        // Mapeo manual de campos -> ContentValues
    }
}
`

## 6.3 SQLiteDAO (ORM por Refleccion)

`java
// SQLiteDAO.java (~1600 lineas)
public class SQLiteDAO<T extends Entity> {
    
    public int insert(T entity) {
        ContentValues values = entity.getContentValues();
        return database.insert(tableName, null, values);
    }
    
    public int update(T entity) {
        ContentValues values = entity.getContentValues();
        return database.update(tableName, values, "id=?", new String[]{entity.getId()});
    }
    
    // 3-level savepoint para transacciones
    public void beginTransaction() {
        // SAVEPOINT level1 -> SAVEPOINT level2 -> SAVEPOINT level3
    }
}
`

## 6.4 Patrones de Acceso

| Patron | Implementacion | Problema |
|--------|----------------|----------|
| Service Locator | AppContext.getServices().getXxxService() | Acoplamiento global |
| Repository pattern | No implementado | Services actuan como repositories |
| Unit of Work | No implementado | Cada operacion es independiente |
| Identity Map | No implementado | Cada consulta crea nuevos objetos |
| Lazy Loading | No implementado | Todas las relaciones se cargan eagerly |

---

# 7. RED

## 7.1 Clientes HTTP

### EnahoHttpClient (Custom)
`java
// Implementacion: HttpsURLConnection
// SSL: TrustManager personalizado (inseguro)
// Async: Thread + Handler
// Uso: Subida/descarga de archivos grandes
`

### AsyncHttpClient (loopj)
`java
// Libreria externa
// Uso: Llamadas API REST
// Callbacks: AsyncHttpResponseHandler
`

## 7.2 Metodos HTTP (EnahoWebService)

| Metodo | Tipo | URL | Funcion |
|--------|------|-----|---------|
| invokeWSUploadFileExportacion | POST | URL_UPLOAD | Subir datos |
| invokeDownload | GET | URL_DOWNLOAD | Descargar datos |
| importarDataInicial | POST | URL_IMPORT | Importar datos iniciales |
| consultaHoraServidor | GET | URL_HORA | Sincronizar hora |
| invokeConsultaInfo | POST | URL_INFO | Consultar informacion |
| invokeGpsUpload | POST | URL_GPS | Subir coordenadas |
| invokeGrabarNuevaRuta | POST | URL_RUTA | Guardar ruta |
| invokeActualizarNuevaRuta | POST | URL_RUTA | Actualizar ruta |
| invokeConsultaTotalMensajes | GET | URL_MENSAJES | Consultar mensajes |
| invokeConsultaListaMensajes | GET | URL_MENSAJES | Listar mensajes |
| 15+ metodos mas | - | - | - |

**Total:** 25+ metodos HTTP

## 7.3 Configuracion SSL Hardcoded (4 ubicaciones)

`java
// RutasFragment.java:192-199
// LoginActivity.java:65-72
// CuestionarioFragmentActivity.java:845-852
// Conexion.java:43-50

TrustManager[] trustAllCerts = new TrustManager[]{
    new X509TrustManager() {
        public void checkClientTrusted(X509Certificate[] chain, String authType) {}
        public void checkServerTrusted(X509Certificate[] chain, String authType) {}
        public X509Certificate[] getAcceptedIssuers() { return null; }
    }
};
`

**RIESGO CRITICO:** Desactiva verificacion SSL completamente

---

# 8. PROBLEMAS ARQUITECTONICOS

## 8.1 Problemas Criticos

| # | Problema | Impacto | Evidencia |
|---|----------|---------|-----------|
| 1 | **Service Locator** | Acoplamiento global, dificulta testing | AppContext.getServices() en 150+ ubicaciones |
| 2 | **Activity References en Services** | Memory leaks, ciclos de vida | InfoGPS.java:52, EnahoWebService.java:136 |
| 3 | **AsyncTask** | Bloqueo UI, sin manejo de errores | Inicializacion, Importacion, Exportacion |
| 4 | **Credenciales Hardcoded** | Seguridad | Conexion.java:10-11, ConexionSGD.java:15-16 |
| 5 | **SSL Hardcoded** | Seguridad | 4 ubicaciones (ver seccion 7.3) |

## 8.2 Problemas Altos

| # | Problema | Impacto | Evidencia |
|---|----------|---------|-----------|
| 6 | **Clases Gigantes** | Mantenibilidad | FragmentForm:7530, MyDatabaseHelper:5000+, SQLiteDAO:1600 |
| 7 | **Responsabilidades Mezcladas** | Violacion SRP | FragmentForm tiene UI+logica+persistencia |
| 8 | **Sin Inyeccion de Dependencias** | Testing difficult | Todos los servicios se crean en ModelContext |
| 9 | **Estado Global** | Condiciones de carrera | AppContext.personalEntity, AppContext.pin |
| 10 | **Casting Directo a Activity** | Fragilidad | AbstractController.java:94, Cap200Controller.java:99 |

## 8.3 Problemas Medios

| # | Problema | Impacto | Evidencia |
|---|----------|---------|-----------|
| 11 | **Sin Tests** | Calidad | Ningun archivo de测试 encontrado |
| 12 | **Codigo Muerto** | Mantenibilidad | AbstractService tiene stubs no-op |
| 13 | **Refleccion Excesiva** | Performance | Entity.getContentValues(), SQLiteDAO |
| 14 | **Hardcoded UI** | Mantenibilidad | FragmentForm:7530 lineas de layout |
| 15 | **Falta de Repository** | Arquitectura | Services actuan como repositories |

## 8.4 Problemas Bajos

| # | Problema | Impacto | Evidencia |
|---|----------|---------|-----------|
| 16 | **Variables No Usadas** | Codigo muerto | ValidarGps:72 |
| 17 | **Comparacion Incorrecta** | Bugs potenciales | ValidarGps:89 con != en vez de >= |
| 18 | **Log Hardcoded** | Seguridad | Credenciales en logs |
| 19 | **Inconsistencia Naming** | Legibilidad | Mezcla de convenciones |
| 20 | **Falta de Documentacion** | Mantenibilidad | Sin javadoc en la mayoria |

---

# 9. DIAGRAMA GENERAL

## 9.1 Diagrama de Capas

`
+================================================================================+
|                              PRESENTATION LAYER                                |
+================================================================================+
|  MasterActivity (Navigation Drawer)                                             |
|    |                                                                            |
|    +-> AbstractCuestionarioFragmentActivity                                     |
|           |                                                                     |
|           +-> CuestionarioFragmentActivity                                      |
|                 |                                                               |
|                 +-> FragmentForm (7530 lineas)                                  |
|                       |                                                         |
|                       +-> FragmentFormWithoutXML                                |
|                             |                                                   |
|                             +-> FragmentFormDNCE                                |
|                                   |                                             |
|                                   +-> FragmentFormEnaho                         |
|                                         |                                       |
|                                         +-> Cap00Fragment ... Cap30Fragment     |
|                                         +-> VisitasFragment                     |
|                                         +-> RutaFragment                        |
|                                         +-> MensajesFragment                    |
|                                         +-> PlanRutaFragment                    |
+================================================================================+
                                      |
                                      | AppContext.getServices()
                                      v
+================================================================================+
|                              BUSINESS LAYER                                     |
+================================================================================+
|  AbstractController                                                             |
|    |                                                                            |
|    +-> Cap200Controller                                                         |
|    +-> AbstractVisitaController                                                 |
|    +-> Controllers especificos                                                  |
|                                                                               |
|  VALIDACION: Controller -> Activity (CASTING DIRECTO)                          |
|    ((CuestionarioFragmentActivity) fragmento.getActivity()).setLyexterno()      |
+================================================================================+
                                      |
                                      v
+================================================================================+
|                              SERVICE LAYER                                      |
+================================================================================+
|  AbstractService<D>                                                             |
|    |                                                                            |
|    +-> EnahoWebService (1950 lineas, 25+ metodos HTTP)                         |
|    |     |                                                                      |
|    |     +-> EnahoHttpClient (HttpURLConnection)                                |
|    |     +-> AsyncHttpClient (loopj)                                            |
|    |     +-> MensajesFragment (IMPORT DIRECTO - CICLO)                          |
|    |     +-> PlanRutaFragment (IMPORT DIRECTO - CICLO)                          |
|    |                                                                            |
|    +-> HogarService                                                             |
|    +-> ViviendaService                                                          |
|    +-> MarcoService                                                             |
|    +-> SegmentacionService                                                      |
|    +-> Enaho01Service - Enaho04Service                                          |
|    +-> CopiaBDService                                                           |
|    +-> DiagnosticoService                                                       |
|    +-> InicializacionService                                                    |
|    +-> 10+ servicios adicionales                                                |
|                                                                               |
|  PROBLEMA: Services aceptan Activity/FragmentActivity como parametros          |
+================================================================================+
                                      |
                                      v
+================================================================================+
|                               DATA LAYER                                        |
+================================================================================+
|  SQLiteDAO<T extends Entity> (~1600 lineas)                                     |
|    |                                                                            |
|    +-> insert(entity) - via entity.getContentValues()                           |
|    +-> update(entity) - via entity.getContentValues()                           |
|    +-> delete(id)                                                               |
|    +-> findById(id) - via entity.fillEntity(cursor)                             |
|    +-> findAll()                                                                |
|    +-> saveWithTransaction() - 3-level savepoint                                |
|                                                                               |
|  Entity (base)                                                                 |
|    |                                                                            |
|    +-> MarcoEntity                                                              |
|    +-> ViviendaEntity                                                           |
|    +-> HogarEntity                                                              |
|    +-> Cap200Entity                                                             |
|    +-> Cap100Entity - Cap900Entity (185+ total)                                 |
+================================================================================+
                                      |
                                      v
+================================================================================+
|                           PERSISTENCE LAYER                                     |
+================================================================================+
|  MyDatabaseHelper (SQLiteOpenHelper v56)                                        |
|    |                                                                            |
|    +-> onCreate() - 300+ CREATE TABLE                                           |
|    +-> onUpgrade() - Migraciones v1->v56                                        |
|    +-> createDatabaseCopy() - Backup                                            |
|                                                                               |
|  AbstractSharedPreferences                                                      |
|  RutasFiles / ModuloFiles                                                      |
|  ArchivoModulo                                                                 |
+================================================================================+
                                      |
                                      v
+================================================================================+
|                             SQLITE DATABASE                                     |
+================================================================================+
|  DB_NAME: "ENAHODATA.db"                                                        |
|  VERSION: 56                                                                    |
|  TABLES: 300+                                                                   |
|  INDEXES: 30+                                                                   |
+================================================================================+
`

## 9.2 Flujo de Datos - Diagrama

`
+-------------------+     +-------------------+     +-------------------+
|     Fragment      |     |     Service       |     |      Server       |
+-------------------+     +-------------------+     +-------------------+
        |                         |                         |
        | 1. getService()         |                         |
        +------------------------>|                         |
        |                         |                         |
        | 2. service.method()     |                         |
        +------------------------>|                         |
        |                         |                         |
        |                    +---------+                    |
        |                    |   DAO   |                    |
        |                    +---------+                    |
        |                         |                         |
        |                    +---------+                    |
        |                    | SQLite  |                    |
        |                    +---------+                    |
        |                         |                         |
        |                    +---------+                    |
        |                    |  HTTP   |-------------------->|
        |                    +---------+                    |
        |                         |                         |
        | 3. resultado            | 4. respuesta             |
        |<------------------------+<------------------------|
`

---

# RESUMEN EJECUTIVO

## Arquitectura Real Identificada

**NO es MVC, MVP, MVVM, ni Clean Architecture.**

La arquitectura real es un **Patron de Servicio Locator con Acoplamiento Global**:

1. **Service Locator** (AppContext.getServices()) como punto central
2. **Estado Global** (AppContext.personalEntity, AppContext.pin)
3. **Acoplamiento Direc to Activity** en Services y Controllers
4. **Refleccion Excesiva** en ORM (Entity/DAO)
5. **AsyncTask Obsoleto** para operaciones en segundo plano
6. **SSL Hardcoded** (4 ubicaciones) - RIESGO DE SEGURIDAD

## Metricas de Complejidad

| Metrica | Valor |
|---------|-------|
| Lineas de codigo total | ~500,000+ |
| Clases principales | ~400 |
| Activities | 22 |
| Fragments | 100+ |
| Entities | 185+ |
| Services | 18+ |
| DAOs | 17+ |
| Metodos HTTP | 25+ |
| Tablas BD | 300+ |
| Dependencias criticas | 5 |
| Problemas arquitectonicos | 20 |

---

**Documento generado:** 2026-08-30
**Estado:** EN REVISION
**Siguiente paso:** Aprobacion del usuario para Etapa 3
