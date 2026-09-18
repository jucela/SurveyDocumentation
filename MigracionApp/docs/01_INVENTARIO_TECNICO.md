# 01 - INVENTARIO TECNICO

## Aplicacion

ENAHO2026 - Encuesta Nacional de Hogares 2026

Instituto Nacional de Estadistica e Informatica (INEI) - Peru

---

# 1. INFORMACION GENERAL

| Campo | Valor | Fuente |
|-------|-------|--------|
| Nombre del proyecto | ENAHO2025 (workspace) / ENAHO2017 (modulo app) | Estructura de carpetas |
| applicationId / package | gob.inei.enaho2026 | AndroidManifest.xml |
| Version | 3.6p (versionCode 1) | AndroidManifest.xml |
| Lenguaje | Java 100% | Todos los archivos fuente .java |
| compileSdk | android-20 (project.properties target) | project.properties |
| minSdk | 23 (Android 6.0 Marshmallow) | AndroidManifest.xml |
| targetSdk | 24 (Android 7.0 Nougat) | AndroidManifest.xml |
| Gradle | NO UTILIZADO (solo 2 modulos satelite tienen build.gradle obsoleto) | Busqueda en proyecto |
| Build System | Eclipse ADT (project.properties + .classpath + .project) | Archivos de configuracion |
| IDE | Eclipse con ADT | Archivos .classpath, .project |
| Proguard | Configurado pero deshabilitado (comentado) | project.properties |

Clasificacion: HECHO - Toda esta informacion fue verificada directamente en los archivos de configuracion del proyecto.

---

# 2. ESTRUCTURA DEL PROYECTO

## 2.1 Modulos

- ENAHO2017/                      [APP] Aplicacion principal
- DNCE/                           [LIB] Framework de componentes UI
- EnahoAndroidUtil/               [LIB] Utilidades generales
- MapsWithMe/                     [LIB] API de mapas
- ParianFileSelectorDialog/       [LIB] Selector de archivos
- TableFixHeaders/                [LIB] Tabla con cabecera fija
- docs/                           Documentacion de migracion
- prompts/                        Prompts de migracion

## 2.2 Grafo de Dependencias

ENAHO2017 (App Principal) depende de:
- DNCE (Framework UI) que a su vez depende de MapsWithMe (API Mapas)
- ParianFileSelectorDialog (Selector Archivos)
- EnahoAndroidUtil (Utilidades) que a su vez depende de TableFixHeaders (Tabla Fija)

HECHO: Verificado en project.properties de cada modulo.

## 2.3 Estructura del Modulo Principal (ENAHO2017)

`
ENAHO2017/
  AndroidManifest.xml
  project.properties
  libs/                           (7 JARs)
    android-async-http-1.4.9.jar
    android-support-v4.jar
    commons-net-3.6.jar
    gson-2.3.1.jar
    httpclient-4.3.6.jar
    zip4j_1.3.2.jar
  res/
    layout/                       (10 layouts)
    layout-sw600dp/               (1 layout tablet)
    menu/                         (3 menus)
    raw/                          (121 XMLs: upgrades, enaho, ubigeo)
    values/
  assets/
    scripts/cap601.sql
  src/gob/inei/
    enaho/                        (core de la app)
      adapter/                    (2 adapters)
      annotations/                (1 anotacion)
      background/                 (5 tareas background)
      common/                     (10 utilidades comunes)
      context/                    (5 clases de contexto)
      controller/                 (4 controllers)
      dao/                        (17 DAOs + mapping/)
      diagnostico/                (5 diagnosticadores)
      dnce/                       (componentes ENAHO sobre DNCE)
      fragment/                   (100+ fragments organizados por modulo)
      http/                       (15 clases HTTP/red)
      listener/                   (1 listener)
      menu/                       (6 action mode callbacks)
      model/                      (185 entities + domain/)
      service/                    (18 services)
    enaho2026/                    (activities)
      activity/                   (17 activities)
`

---

# 3. COMPONENTES ANDROID

## 3.1 Activities (22 total)

### Activities Concretas (extends Activity)

| Clase | Paquete | Responsabilidad |
|-------|---------|-----------------|
| LoginActivity | gob.inei.enaho2026.activity | Login de usuario (LAUNCHER) |
| AboutActivity | gob.inei.enaho2026.activity | Acerca de |
| AdminActivity | gob.inei.enaho2026.activity | Administracion |
| ConfigActivity | gob.inei.enaho2026.activity | Configuracion/upgrade BD |
| InfoActivity | gob.inei.enaho2026.activity | Informacion del sistema |
| VerificarAplicacionesActivity | gob.inei.enaho2026.activity | Verificar aplicaciones instaladas |
| FileSelectionActivity | paul.arian.fileselector | Seleccion de archivo (libreria) |
| FolderSelectionActivity | paul.arian.fileselector | Seleccion de carpeta (libreria) |
| CameraClass | gob.inei.dnce.components.camera | Captura de foto (libreria DNCE) |
| FileBrowserActivity | ua.com.vassiliev.androidfilebrowser | Navegador de archivos (DNCE) |

### Activities FragmentActivity (extends MasterActivity)

| Clase | Paquete | Responsabilidad |
|-------|---------|-----------------|
| CuestionarioFragmentActivity | gob.inei.enaho2026.activity | Cuestionario principal |
| ExportacionFragmentActivity | gob.inei.enaho2026.activity | Exportacion de datos |
| CoberturaFragmentActivity | gob.inei.enaho2026.activity | Cobertura de viviendas |
| ReemplazoFragmentActivity | gob.inei.enaho2026.activity | Reemplazo de encuestador |
| CopiaDBFragmentActivity | gob.inei.enaho2026.activity | Copia de base de datos |
| ImportacionFragmentActivity | gob.inei.enaho2026.activity | Importacion de datos |
| PlanRutaFragmentActivity | gob.inei.enaho2026.activity | Plan de ruta del encuestador |
| VerificacionFragmentActivity | gob.inei.enaho2026.activity | Verificacion de datos |
| MensajesFragmentActivity | gob.inei.enaho2026.activity | Sistema de mensajes |

### Clases Abstractas Intermedias

| Clase | Tipo |
|-------|------|
| MasterActivity | extends FragmentActivity |
| AbstractCuestionarioFragmentActivity | extends MasterActivity |
| CuestionarioFragmentActivity (util) | abstract, extends FragmentActivity |

## 3.2 Fragments (100+ total)

### Fragment Framework (base)

- FragmentForm (abstract, extends Fragment) - DNCE
- FragmentFormWithoutXML (abstract, extends FragmentForm) - DNCE
- CuestionarioFragment (abstract, deprecated) - EnahoAndroidUtil
- FragmentFormDNCE (extends FragmentFormWithoutXML) - ENAHO
- FragmentFormEnaho (extends FragmentFormDNCE) - ENAHO

### Modulos ENAHO (fragments concretos por capitulo)

| Modulo | Paquete | Cantidad estimada |
|--------|---------|-------------------|
| ENAHO01 (Vivienda/Hogar) | enaho01 | 44 fragments + 42 dialogs |
| ENAHO01A (Personas) | enaho01a | 55 fragments + 26 dialogs |
| ENAHO01B (Gobernabilidad) | enaho01b | 15 fragments + 4 dialogs |
| ENAHO02 (Ingresos) | enaho02 | 21 fragments + 47 dialogs |
| ENAHO04 (Educacion/Salud) | enaho04 | 26 fragments + 14 dialogs |
| Cobertura | cobertura | 10+ fragments |
| Consistencia | consistencia | 8+ fragments |
| Supervisor | supervisor | 7+ fragments + 9 dialogs |
| Verificacion | verificar | 7+ fragments + 7 dialogs |
| Rutas | rutas | 4+ fragments + 2 dialogs |

TOTAL ESTIMADO: 100+ Fragment classes

## 3.3 Services (2 total)

| Clase | Paquete | Responsabilidad |
|-------|---------|-----------------|
| CapturadorGPS | gob.inei.dnce.util | Captura de coordenadas GPS |
| InfoGPS | gob.inei.enaho.service.background | Servicio de informacion GPS |

## 3.4 Adapters (11+ total)

| Clase | Tipo | Paquete |
|-------|------|---------|
| DrawerAdapter | abstract, BaseAdapter | gob.inei.dnce.adapter |
| MyDrawerAdapter | extends DrawerAdapter | gob.inei.enaho.adapter |
| SpinnerAdapter | ArrayAdapter | gob.inei.dnce.adapter |
| EntitySpinnerAdapter | ArrayAdapter | gob.inei.dnce.adapter |
| MyFragmentPagerAdapter | FragmentStatePagerAdapter | gob.inei.dnce.adapter |
| CuestionarioFragmentAdapter | abstract, FragmentStatePagerAdapter | util.android.cuestionario.fragment |
| ObjectSpinnerAdapter | ArrayAdapter | gob.inei.enaho.adapter |
| ItemSpinnerAdapter | ArrayAdapter | util.android.adapters |
| TableComponentAdapter | ArrayAdapter (inner) | gob.inei.dnce.components |
| TableComponentMapAdapter | ArrayAdapter (inner) | gob.inei.dnce.components |
| ImagenesAdaptador | BaseAdapter (inner) | gob.inei.dnce.components.ui |

## 3.5 Custom Views (19+ total)

| Clase | Extiende | Paquete |
|-------|----------|---------|
| GifView | View | gob.inei.dnce.components |
| LabelComponent | TextView | gob.inei.dnce.components |
| TextBoxField | abstract, EditText | gob.inei.dnce.components |
| ImageViewField | ImageView | gob.inei.dnce.components |
| ButtonComponent | Button | gob.inei.dnce.components |
| SpinnerField | Spinner | gob.inei.dnce.components |
| FragmentViewPager | ViewPager | gob.inei.dnce.components |
| TableFixHeaders | ViewGroup | com.inqbarna.tablefixheaders |
| CheckableRelativeLayout | RelativeLayout | paul.arian.fileselector |
| TableComponent | LinearLayout | gob.inei.dnce.components |
| GridComponent3 | LinearLayout | gob.inei.dnce.components |
| CheckGroupOtherField | LinearLayout | gob.inei.dnce.components |
| TextPicker | LinearLayout | gob.inei.dnce.components |
| TablaCabeceraFijaView | LinearLayout | util.android.widgets |
| LayoutSeccion | LinearLayout | util.android.widgets |
| LayoutPregunta | LinearLayout | util.android.widgets |
| AbstractView | abstract, LinearLayout | util.android.widgets |
| QuestionSection | LinearLayout | gob.inei.enaho.dnce.components.widgets |
| RevisionDeSupervision | LinearLayout | gob.inei.enaho.dnce.components |

## 3.6 DialogFragment (65+ total)

### Framework de Dialogos (DNCE)

- DialogFragmentComponent (abstract, extends DialogFragment)
- DialogFragmentComponentWithoutXML (abstract, extends DialogFragmentComponent)
- DialogFragmentComponentItem (extends DialogFragmentComponentWithoutXML)

### Dialogos por Categoria

| Categoria | Cantidad | Paquetes |
|-----------|----------|----------|
| ENAHO01 (Vivienda) | ~6 | enaho01.dialog |
| ENAHO01A (Personas) | ~3 | enaho01a.dialog |
| ENAHO02 (Ingresos) | ~16 | enaho02.dialog |
| ENAHO04 (Educacion/Salud) | ~10 | enaho04.dialog |
| Supervisor | ~5 | supervisor.dialog |
| Verificacion | ~7 | verificar |
| Consistencia | ~4 | consistencia |
| Rutas | ~3 | rutas |
| DNCE UI | 3 | dnce.components.ui |
| Otros | ~8 | Varios |

---

# 4. ANDROIDMANIFEST

## 4.1 Permisos (11)

- ACCESS_COARSE_LOCATION (Ubicacion)
- ACCESS_FINE_LOCATION (Ubicacion)
- ACCESS_NETWORK_STATE (Red)
- INTERNET (Red)
- WAKE_LOCK (Sistema)
- READ_PHONE_STATE (Sistema)
- RECEIVE_BOOT_COMPLETED (Sistema)
- ACCESS_WIFI_STATE (Red)
- CAMERA (Hardware)
- WRITE_EXTERNAL_STORAGE (Almacenamiento)
- READ_EXTERNAL_STORAGE (Almacenamiento)

## 4.2 Activities Declaradas (22)

Todas las activities usan android:configChanges="orientation|screenSize" para manejo manual de rotacion.

## 4.3 Services Declarados (1)

- gob.inei.enaho.service.background.InfoGPS (enabled=true, exported=true)

## 4.4 Otros Componentes

- No BroadcastReceiver declarados
- No ContentProvider declarados
- Application class: gob.inei.enaho.context.Aplicacion

---

# 5. PERSISTENCIA

## 5.1 SQLite (Persistencia Principal)

### Arquitectura de Capas

`
SQLiteOpenHelper (DatabaseHelper)
    |
SQLiteDAO (clase base generica con CRUD por reflexion)
    |
DAOs especificos (17+)
    |
Services (18+)
    |
Controllers / Fragments
`

### DatabaseHelper (Abstracto)

Archivo: DNCE/src/gob/inei/dnce/dao/DatabaseHelper.java

- Extiende SQLiteOpenHelper
- Carga schema inicial desde XML raw resources usando execSQL()
- Parser DOM (DocumentBuilder) para ejecutar SQL desde XML

### MyDatabaseHelper (Concreto)

Archivo: ENAHO2017/src/gob/inei/enaho/dao/MyDatabaseHelper.java

| Campo | Valor |
|-------|-------|
| DATABASE_NAME | enaho2026_produccion.db |
| DATABASE_VERSION | 56 |
| Script de creacion | 28+ raw XML resources |
| Upgrade scripts | R.raw.upgrade02 a R.raw.upgrade93 (55 upgrades) |

### SQLiteDAO (Clase Base)

Archivo: DNCE/src/gob/inei/dnce/dao/SQLiteDAO.java (~1600 lineas)

Funcionalidades:
- Transacciones (startTX, commitTX, endTX)
- Existencia de registros (existeRegistro)
- Auto-incremento de ID (nextID)
- CRUD generico por reflexion (saveOrUpdate)
- Consulta generica (getBeans, getMaps)
- Mapeo de entidades usando Entity.getContentValues() y Entity.fillEntity()

### DAOs Especificos (17+)

| DAO | Responsabilidad |
|-----|-----------------|
| UsuarioDAO | Usuarios del sistema |
| UbigeoDAO | Geografia (departamentos, provincias, distritos) |
| SegmentacionDAO | Segmentacion muestral |
| MarcoDAO | Marco de viviendas |
| ParameterDAO | Parametros del sistema |
| PersonalDao | Personal/encuestadores |
| DiccionarioDao | Diccionario de datos |
| Enaho01Dao | Datos ENAHO modulo 01 |
| Enaho01ADao | Datos ENAHO modulo 01A |
| Enaho01BDao | Datos ENAHO modulo 01B |
| Enaho02Dao | Datos ENAHO modulo 02 |
| Enaho04Dao | Datos ENAHO modulo 04 |
| HogarDao | Datos de hogares |
| ViviendaDao | Datos de viviendas |
| ConfiguracionDao | Configuracion de la app |
| SupervisionDao | Datos de supervision |
| SQLiteWithoutIdDao | DAO para tablas sin campo ID |

### Scripts SQL (Raw Resources)

121 archivos XML en res/raw/:
- sistema.sql - Tablas del sistema
- enaho_diccionario.sql - Diccionario de datos
- ubigeo_*.sql - Datos geograficos (multiples archivos)
- upgrade02.xml a upgrade93.xml - 55 scripts de migracion
- enaho_*.xml - Scripts de datos ENAHO

## 5.2 SharedPreferences

### Preferencias.java (Centralizado)

Archivo: ENAHO2017/src/gob/inei/enaho/context/Preferencias.java

| Constante | Tipo | Descripcion |
|-----------|------|-------------|
| USER | String | Usuario logueado |
| ODEI | String | Oficina Desconcentrada |
| ANHO | String | Ano de encuesta |
| MES | String | Mes de encuesta |
| CONGLOMERADO | String | Codigo conglomerado |
| VIVIENDA | String | Codigo vivienda |
| HOGAR | String | Codigo hogar |
| CUESTIONARIO | String | Tipo cuestionario |
| SECCION | String | Seccion actual |
| MODO | String | Modo de logueo |
| SUBSISTEMA | String | Subsistema activo |

Archivo de preferencias: configuracion (Context.MODE_PRIVATE)

Uso: 9+ activities y 12+ fragments

## 5.3 File I/O

| Operacion | Archivos | Descripcion |
|-----------|----------|-------------|
| Backup/Restore BD | SQLiteUtil.java, FileUtil.java | Copia byte a byte de archivos .db |
| ZIP compression | Util.java (DNCE) | ZipOutputStream para comprimir datos |
| XML Import/Export | XMLReader.java, XMLWriter.java | Serializacion/deserializacion de entidades a XML |
| Image handling | FragmentForm.java, CameraClass.java | FileInputStream/FileOutputStream para fotos |
| GPX Import | ImportacionGPX.java | Lectura de archivos GPX de GPS |
| File Upload | EnahoWebService.java | Lectura de archivos para envio |
| File Download | EnahoWebService.java, EnahoUtil.java | Descarga de backups |

---

# 6. RED

## 6.1 Clientes HTTP

### AsyncHttpClient (LoopJ) - Cliente Legado

Archivo: ENAHO2017/src/gob/inei/enaho/service/EnahoWebService.java (~1950 lineas)

| Caracteristica | Valor |
|----------------|-------|
| Libreria | android-async-http-1.4.9.jar |
| Uso | Cliente principal legado |
| Autenticacion | Basic Auth hardcoded |
| SSL | MySSLSocketFactory (trust-all) |
| Timeouts | Connect: 20s, Response: 30s |
| Reintentos | 2 con 5s timeout |

### EnahoHttpClient (Custom HttpsURLConnection)

Archivo: ENAHO2017/src/gob/inei/enaho/http/EnahoHttpClient.java

| Caracteristica | Valor |
|----------------|-------|
| Base | HttpsURLConnection nativa |
| Autenticacion | Basic Auth hardcoded |
| Timeouts | Connect: 20s, Read: 30s |
| Formatos POST | application/x-www-form-urlencoded, multipart/form-data |
| Metodos | POST (form), POST (multipart), GET, Binary download |
| Async | new Thread() + Handler(Looper.getMainLooper()) |
| Cancelacion | Soporte via volatile boolean + disconnect() |

## 6.2 SSL/TLS

Archivo: ENAHO2017/src/gob/inei/enaho/service/MySSLSocketFactory.java

TrustManager que acepta TODOS los certificados (checkClientTrusted y checkServerTrusted vacios).

RIESGO: SSL sin validacion de certificados. Accept-all trust manager.

## 6.3 Autenticacion

### HTTP Basic Auth

Archivo: ENAHO2017/src/gob/inei/enaho/http/HttpConfig.java

Credenciales hardcoded: Admin200786 / Admin200786

Tambien hardcodeado en: EnahoWebService.java (lineas 126, 942, 964, 986, 1008, 1036)

RIESGO: Credenciales hardcoded en codigo fuente.

### SIS Credentials

Archivo: Configuracion.java

Credenciales: SIS / 123

## 6.4 Endpoints REST

### URLs Base

| Entorno | URL | Estado |
|---------|-----|--------|
| Produccion | https://webapp.inei.gob.pe:8443 | ACTIVO |
| Desarrollo | http://development.inei.gob.pe:8080 | Configurado |
| Desarrollo local 1 | http://192.168.193.39:8080 | Comentado |
| Desarrollo local 2 | http://192.168.192.121:8084 | Comentado |

### Endpoints

| Endpoint | Metodo | Descripcion |
|----------|--------|-------------|
| /EnahoWeb/webresources/files/ubicacionGpsUsuario | POST | Subir puntos GPS |
| /EnahoWeb/webresources/files/consultaInfo | POST | Consultar info del usuario |
| /EnahoWeb/webresources/files/consultaRuta | POST | Consultar rutas |
| /EnahoWeb/webresources/files/grabarRuta | POST | Crear ruta |
| /EnahoWeb/webresources/files/actualizarRuta | POST | Actualizar ruta |
| /EnahoWeb/webresources/files/consultaHora | GET | Hora del servidor |
| /EnahoWeb/webresources/files/consultaSubidos_v2 | POST | Consultar archivos subidos |
| /EnahoWeb/webresources/files/uploadMultiple_v2 | POST | Exportar viviendas |
| /EnahoWeb/webresources/files/descargaBackup_v2 | POST | Descargar backup |
| /EnahoWeb/webresources/files/aguaService | POST | Datos P110A agua |
| /EnahoWeb/webresources/files/descargaDataInicialReentrevista | POST | Data inicial reentrevista |
| /EnahoWeb/webresources/files/consultaMensajes | POST | Consultar mensajes |
| /EnahoWeb/webresources/files/mensajes | POST | Enviar mensajes |

Formato de respuesta: JSON con tag, status, error_msg

## 6.5 Serializacion JSON

| Libreria | Uso |
|----------|-----|
| org.json (JSONObject/JSONArray) | Construccion y parsing de JSON en endpoints |
| Gson 2.3.1 | Deserializacion de listas con TypeToken |
| JSONObjectDecorator | Wrapper con valores por defecto |
| JsonUtil | Utilidad estatica para serializar registros |

## 6.6 Formato XML (Intercambio de Datos)

| Clase | Funcion |
|-------|---------|
| XMLReader | Deserializacion XML a Entity (686 lineas) |
| XMLWriter | Serializacion Entity a XML |
| XMLObject | Interfaz para entidades serializables |
| XMLDataObject | Interfaz para datos tipo Map |
| FiltroXML | Filtros de lectura XML |

Uso principal: Backup/restore de base de datos en formato XML.

---

# 7. LIBRERIAS

## 7.1 JARs (ENAHO2017/libs/)

| Libreria | Version | Uso | Estado |
|----------|---------|-----|--------|
| android-async-http-1.4.9.jar | 1.4.9 | Cliente HTTP asincrono (LoopJ) | DEPRECATED |
| android-support-v4.jar | v4 | Support Library | REEMPLAZADO por AndroidX |
| commons-net-3.6.jar | 3.6 | FTP (Apache Commons Net) | ACTIVO |
| gson-2.3.1.jar | 2.3.1 | Serializacion JSON (Google) | OBSOLETO |
| httpclient-4.3.6.jar | 4.3.6 | HTTP Client (Apache) | REEMPLAZADO |
| zip4j_1.3.2.jar | 1.3.2 | Compresion ZIP | ACTIVO |

## 7.2 JARs (Otras librerias)

- android-support-v4.jar (duplicado en DNCE y EnahoAndroidUtil)
- merge-1.0.1.jar (ParianFileSelectorDialog)

## 7.3 Modulos Biblioteca

| Modulo | Package | Uso |
|--------|---------|-----|
| DNCE | gob.inei.dnce | Framework de componentes UI y formularios |
| EnahoAndroidUtil | com.androidutil | Utilidades: SQLite, GPS, formularios, widgets |
| MapsWithMe | com.mapwithme.maps.api | API de mapas MapsWithMe |
| ParianFileSelectorDialog | paul.arian.fileselector | Selector de archivos/carpetas |
| TableFixHeaders | com.inqbarna.tablefixheaders | Tabla con cabecera fija |

## 7.4 Clasificacion

| Categoria | Librerias |
|-----------|-----------|
| Android Oficial | android-support-v4 |
| Tercera parte | AsyncHttpClient, Gson, HttpClient, Commons-Net, Zip4j, Merge |
| Propia | DNCE, EnahoAndroidUtil, MapsWithMe, ParianFileSelectorDialog, TableFixHeaders |
| Obsoleta | AsyncHttpClient (deprecated), android-support-v4 (reemplazado por AndroidX) |

---

# 8. CONCURRENCIA

## 8.1 Thread / Runnable (11 menciones en 8 archivos)

| Archivo | Uso |
|---------|-----|
| AnrWatchDog.java | extends Thread - Watchdog de ANR con heartbeat |
| GPSDialog.java | Thread anonimo para refresco periodico de tabla GPS |
| InfoActivity.java | new Thread para trabajo en background |
| EnahoWebService.java | new Thread para operaciones GPS |
| MarcoFragment.java | new Thread para verificar version APK |
| EnahoUtil.java | new Thread para descarga en background |
| UnCaughtException.java | new Thread para reporte de crash |
| EnahoHttpClient.java | new Thread para ejecucion async de HTTP |

## 8.2 AsyncTask (DEPRECATED) - 28 menciones en 7 archivos

| Clase | Funcion |
|-------|---------|
| Inicializacion | Inicializacion de la app/login |
| TareasBackground | Tareas de startup |
| Importacion | Importacion de datos |
| Exportacion | Exportacion de datos |
| CopiaBDAutomatica | Copia automatica de BD |
| ImportacionGPX | Importacion de archivos GPX |
| FragmentForm.PrintScreen | Captura de pantalla |

## 8.3 Handler (63 menciones en 18 archivos)

Usos principales:
- mainHandler.post() en EnahoHttpClient.java para dispatch de resultados HTTP
- new Handler().post() en MasterActivity.java para ViewPager
- postDelayed en 12+ activities para cierre de DrawerLayout
- Handler(Looper.getMainLooper()) en fragments para delays
- Handler en AnrWatchDog.java para heartbeat
- Handler en MiLocationManager.java para GPS polling

## 8.4 ExecutorService (12 menciones en 2 archivos)

| Archivo | Uso |
|---------|-----|
| MiLocationManager.java | ScheduledExecutorService para polling GPS cada 10 segundos |
| TestAndroidTask.java | Ejemplo/demo de Executor patterns |

## 8.5 synchronized (26 menciones en 6 archivos)

| Archivo | Bloques | Descripcion |
|---------|---------|-------------|
| DiagnosticoEstado.java | 16 | Thread-safe state tracking |
| DiagnosticoLogger.java | 2 | Thread-safe init y log writing |
| DiagnosticoManager.java | 2 | Thread-safe ANR watchdog lifecycle |
| EnahoWebService.java | 3 | GPS_UPLOAD_LOCK, GPS_SYNC_LOCK |
| GPSDialog.java | 1 | mostrarTabla() |
| TableComponent.java | 1 | Bloque synchronized(this) |

## 8.6 Coroutines / Flow / WorkManager

NO UTILIZADOS. No existe codigo Kotlin en el proyecto.

---

# 9. UI

## 9.1 XML Layouts (50+ archivos)

### ENAHO2017 (App Principal) - 11 layouts

- login.xml, login_sw600dp.xml
- activity_principal.xml, activity_configuracion.xml, activity_info.xml
- activity_admin.xml, activity_verificar_aplicaciones.xml
- about.xml, componentes_nativos.xml, elemento_lista_drawer.xml, prueba.xml

### DNCE - 5 layouts
### EnahoAndroidUtil - 22 layouts
### ParianFileSelectorDialog - 3 layouts
### MapsWithMe - 1 layout

## 9.2 ViewBinding / DataBinding

NO UTILIZADOS. Todas las vistas se acceden via findViewById().

## 9.3 RecyclerView

NO UTILIZADO. No existe RecyclerView en el proyecto.

## 9.4 ListView (Mecanismo de Listas Principal)

100+ menciones en 30+ archivos.

Usos: Navigation Drawer (9 activities), Context menus (10+ fragments), Formularios, Tablas.

## 9.5 Navegacion

### Navigation Drawer (Legacy) - 30 menciones en 10 activities

Patron: DrawerLayout + ActionBarDrawerToggle + ListView

NO se usa NavigationView (Jetpack Navigation).

### ViewPager

- FragmentViewPager (custom ViewPager) - DNCE
- MyFragmentPagerAdapter (FragmentStatePagerAdapter) - DNCE
- MasterActivity (activity base con ViewPager) - DNCE

## 9.6 ProgressDialog (DEPRECATED)

64 menciones en 15+ archivos. Usado extensivamente para progreso de operaciones largas.

## 9.7 AlertDialog

Usado en multiples activities para confirmaciones y alertas.

---

# 10. CONFIGURACION

## 10.1 URLs

| Constante | Valor | Archivo |
|-----------|-------|---------|
| URL_SERVIDOR_WEB_PRODUCCION | https://webapp.inei.gob.pe:8443 | Configuracion.java |
| URL_SERVIDOR_WEB_DEVELOP | http://development.inei.gob.pe:8080 | Configuracion.java |

## 10.2 Constantes Principales

| Constante | Valor | Descripcion |
|-----------|-------|-------------|
| nANHO | 2026 | Ano de encuesta |
| RUTA_BASE | /sdcard/ENAHO2026/ | Ruta base de almacenamiento |
| DIRECTORIO_COPIABD | RUTA_BASE/BASES | Directorio de copias BD |
| PATRON_FECHA_CAPITULO | dd/MM/yyyy HH:mm:ss | Formato de fecha |
| MINUTOS_PARA_CIERRE_VISITA | 30 | Minutos para cerrar visita |

## 10.3 Feature Flags (20 flags)

Flags principales:
- FUNCIONALIDAD_ECE = false
- FUNCIONALIDAD_INSEGURIDAD_ALIMENTARIA_P130 = true
- FUNCIONALIDAD_SUPERVISION_OBSERVACIONES = true
- FUNCIONALIDAD_SERVIDOR_ENVIAR_BACKUP = true
- FUNCIONALIDAD_SERVIDOR_DESCARGA_BACKUP = true
- FUNCIONALIDAD_MODO_SUPERVISION = true
- FUNCIONALIDAD_LUGAR_ENTREVISTA = true
- FUNCIONALIDAD_REPORTES_INGRESOS = true
- FUNCIONALIDAD_REPORTES_OTROS_CONSUMO = true
- FUNCIONALIDAD_REPORTES_OTROS_PRECIOS = true
- FUNCIONALIDAD_REPORTES_OTROS_BALANCE = true
- CAP900 = true

## 10.4 Credenciales

RIESGO: Credenciales hardcoded en codigo fuente.

- HTTP Basic Auth: Admin200786 / Admin200786 (HttpConfig.java)
- SIS: SIS / 123 (Configuracion.java)

---

# 11. RESUMEN

## 11.1 Tecnologias

| Categoria | Tecnologia Actual | Estado |
|-----------|-------------------|--------|
| Lenguaje | Java 100% | Sin Kotlin |
| Build System | Eclipse ADT (project.properties) | LEGACY |
| Support Library | android-support-v4 | REEMPLAZADO por AndroidX |
| Persistencia | SQLite raw + custom DAOs | FUNCIONAL |
| HTTP | AsyncHttpClient (loopj) + HttpsURLConnection | DEPRECATED/LEGACY |
| JSON | org.json + Gson 2.3.1 | ANTIGUO |
| XML Parsing | XmlPullParser + DocumentBuilder | FUNCIONAL |
| Concurrencia | Thread + AsyncTask + Handler | DEPRECATED |
| UI | XML + findViewById + ListView | LEGACY |
| Navegacion | DrawerLayout + ListView + ViewPager | LEGACY |
| Dialogos | DialogFragment custom | FUNCIONAL |
| GPS | LocationManager custom | FUNCIONAL |
| Camara | Camera API v1 (implicito) | DEPRECATED |

## 11.2 Arquitectura Aparente

Sistema de Formularios Dinamicos basado en XML.

La aplicacion NO sigue un patron arquitectonico estandar (no es MVC, MVP, MVVM ni Clean Architecture).

La arquitectura real es:

`
Activity (MasterActivity/FragmentActivity)
    |
Fragment (FragmentForm -> FragmentFormDNCE -> FragmentFormEnaho)
    |
Service (AbstractService<T>)
    |
DAO (SQLiteDAO -> DAO especifico)
    |
DatabaseHelper (SQLiteOpenHelper)
    |
SQLiteDatabase
`

Patrones de diseno observados:
- Service Layer sobre DAOs
- Controller pattern (AbstractController, Cap200Controller)
- Observer pattern (XMLReader extends Observable)
- Singleton pattern (XMLReader, Aplicacion)
- Memento pattern (Caretaker, Memento en DNCE)
- Factory pattern (EnahoFactory)
- Decorator pattern (JSONObjectDecorator, IterableDecorator)

## 11.3 Principales Componentes

| Componente | Cantidad |
|------------|----------|
| Activities | 22 |
| Fragments | 100+ |
| Services | 2 |
| Adapters | 11+ |
| Custom Views | 19+ |
| Dialogs | 65+ |
| DAOs | 17+ |
| Services (Java) | 18+ |
| Entities | 185 |

## 11.4 Principales Dependencias

| Dependencia | Tipo | Critica |
|-------------|------|---------|
| android-support-v4 | Android Oficial | REEMPLAZAR por AndroidX |
| AsyncHttpClient 1.4.9 | Tercera parte | REEMPLAZAR (deprecated) |
| Gson 2.3.1 | Tercera parte | ACTUALIZAR |
| HttpClient 4.3.6 | Tercera parte | REEMPLAZAR |
| Commons-Net 3.6 | Tercera parte | EVALUAR necesidad |
| Zip4j 1.3.2 | Tercera parte | ACTUALIZAR |
| Merge 1.0.1 | Tercera parte | EVALUAR necesidad |

## 11.5 Diagnostico ANR

Sistema personalizado de diagnostico:

| Clase | Funcion |
|-------|---------|
| AnrWatchDog | Thread watchdog que detecta ANR via heartbeat |
| DiagnosticoManager | Lifecycle del watchdog |
| DiagnosticoEstado | Estado thread-safe de pantalla/fragment/service |
| DiagnosticoLogger | Logging de diagnostico |
| HttpDiagnostic | Diagnostico de conexiones HTTP |

---

# CLASIFICACION DE INFORMACION

- HECHO: Toda la informacion en este documento fue verificada directamente en el codigo fuente o archivos de configuracion del proyecto.
- INFERENCIA: Las clasificaciones de DEPRECATED, OBSOLETO, LEGACY se basan en el conocimiento general del estado de las librerias y APIs de Android.
- NO DETERMINADO: No se determino la version exacta de Java/JDK utilizada (solo se sabe que es Eclipse ADT legacy).

---

ESTADO: EN REVISION

Documento generado como parte de la Etapa 1 del proceso de migracion.
