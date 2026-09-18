# 03 - FLUJOS FUNCIONALES

## REFERENCIA

- docs/01_INVENTARIO_TECNICO.md
- docs/02_ARQUITECTURA_ACTUAL.md
- Verificacion directa contra codigo fuente

---

# 1. PUNTOS DE ENTRADA

## 1.1 Application (Inicializacion Global)

`
Aplicacion.onCreate()
  |
  +--> DiagnosticoManager.iniciar(this)     [diagnostico ANR]
  +--> inicializar(Preferencias.getSubsistema(this))
          |
          +--> AppContext.inicializar(tipo)
                  |
                  +--> ModelContext.inicializar()    [crea services/DAOs]
                  +--> MiDatabaseHelper()            [abre BD v56]
`

**Archivo:** ENAHO2017/src/gob/inei/enaho/context/Aplicacion.java:19-41

## 1.2 Activity Inicial (Launcher)

**Launcher:** LoginActivity (unico punto de entrada)

**Archivo:** ENAHO2017/AndroidManifest.xml:36-45

`
LoginActivity
  |-- Modo: ENCUESTADOR / DIGITADOR
  |-- Subsistema: CAMPO / REENTREVISTA / SUPERVISION
  |-- Credenciales: usuario + clave + PIN
  |-- Validacion GPS, SIM, datos (si FUNCIONALIDAD_GPSDATOS=true)
  +--> Inicializacion (AsyncTask)
          |
          +--> loguear() -> PersonalService.findByUsuarioClave()
          +--> Permisos (cargo, subsistema, reentrevista)
          +--> TareasBackground (carga diccionario)
          +--> CuestionarioFragmentActivity
`

## 1.3 Todas las Activities Declaradas

| Activity | Funcion |
|----------|---------|
| LoginActivity | **LAUNCHER** - Autenticacion |
| CuestionarioFragmentActivity | Cuestionario principal (149 fragments) |
| ExportacionFragmentActivity | Exportacion de datos |
| CoberturaFragmentActivity | Reportes de cobertura |
| ReemplazoFragmentActivity | Gestion de reemplazos |
| CopiaDBFragmentActivity | Backup de BD |
| ImportacionFragmentActivity | Importacion de datos |
| PlanRutaFragmentActivity | Planificacion de rutas |
| VerificacionFragmentActivity | Verificacion de literales |
| MensajesFragmentActivity | Buzon de mensajes |
| FileSelectionActivity | Selector de archivos |
| FolderSelectionActivity | Selector de carpetas |
| CameraClass | Camara |
| AboutActivity | Acerca de |
| AdminActivity | Restaurar BD (oculto) |
| ConfigActivity | Configuracion |
| InfoActivity | Informacion |
| VerificarAplicacionesActivity | Verificar aplicaciones |

---

# 2. FLUJOS

## 2.1 Flujo: Login

`
1. Usuario
   |-- Ingresa usuario, clave, PIN
   |-- Selecciona modo (ENCUESTADOR/DIGITADOR)
   +-- Selecciona subsistema (CAMPO/REENTREVISTA/SUPERVISION)

2. Pantalla
   +-- LoginActivity

3. Evento
   +-- Boton "Ingresar" -> validarLogin()

4. Activity
   +-- LoginActivity.validarLogin() [linea 400]

5. Servicio
   +-- Inicializacion (AsyncTask)
         +-- loguear() [linea 96]
         +-- PersonalService.findByUsuarioClave(usuario, clave)
         +-- Verificacion de permisos por cargo/subsistema

6. Regla de Negocio
   |-- Si usuario == null -> "Usuario o clave no existe"
   |-- Si sin permisos reentrevista -> "Usuario no tiene permisos para Reentrevista"
   |-- Si subsistema SUPERVISION + cargo incorrecto -> "Usuario no permitido"
   +-- Si ADMIN/270314 -> muestra boton Admin

7. BD
   +-- PersonalService.findByUsuarioClave() -> PersonalEntity

8. Red
   +-- Ninguna (login offline)

9. Resultado
   |-- Exito -> CuestionarioFragmentActivity + finish()
   +-- Error -> Dialog con mensaje de error
`

**Archivos:**
- LoginActivity.java:400-422 (validarLogin)
- Inicializacion.java:96-143 (loguear)
- Inicializacion.java:169-179 (onAccept - navegacion)

## 2.2 Flujo: Guardar (Grabar)

`
1. Usuario
   +-- Toca "Grabar" en overflow menu

2. Pantalla
   +-- CuestionarioFragmentActivity (overflow menu)

3. Evento
   +-- onOptionsItemSelected(R.id.action_grabar) [linea 288]

4. Activity
   +-- AbstractCuestionarioFragmentActivity.continuar() [linea 366]
         +-- grabarYContinuar(currentPage+1, grabar=false)
               +-- nextFragment(position) [linea 385]

5. ViewPager
   +-- CuestionarioSimpleOnPageChangeListener.onPageSelected() [linea 41]
         +-- ff_previo.onAdelantarPagina() [linea 55]
               +-- grabar() [linea 7517-7518]

6. Fragment
   +-- Cap200Fragment.grabarOrThrow() [linea 77]
         |-- Validaciones de negocio (P203, informante, P205, etc.)
         +-- enaho01Service.completarCap200(hogar)

7. Service
   +-- Enaho01Service.completarCap200() [linea 519]
         +-- Enaho01Service.guardarCap200(reg, validarVisita=true)

8. DAO
   +-- Enaho01Dao.guardarCap200() [linea 758]
         +-- EnahoDao.saveOrUpdateConVisita(T_CAP200, reg, validarVisita=true)

9. Regla de Negocio (DAO)
   +-- EnahoDao.onAntesInsertarRegistro() [linea 108]
         |-- Verifica visita activa
         |-- Si visita cerrada + validarVisita=true -> MensajeException
         +-- Asigna columna VISITA automaticamente

10. BD
    +-- SQLiteWithoutIdDao.saveOrUpdate() -> SQLiteDatabase

11. Resultado
    |-- Exito -> Toast "GUARDADO" + avanza pagina
    +-- Error -> MensajeException -> Dialog / Exception -> Toast + Log
`

**Archivos:**
- AbstractCuestionarioFragmentActivity.java:288-289,366-391
- CuestionarioSimpleOnPageChangeListener.java:41-59
- FragmentForm.java:7517-7518
- Cap200Fragment.java:77-137
- EnahoDao.java:108-226

## 2.3 Flujo: Navegar entre Fragments

`
1. Usuario
   +-- Swipe izquierda/derecha en ViewPager

2. Pantalla
   +-- ViewPager (fragment actual)

3. Evento
   +-- Page change -> CuestionarioSimpleOnPageChangeListener

4. Activity
   +-- onPageSelected(newPosition) [linea 41]

5. Fragment Anterior
   |-- onAdelantarPagina() -> grabar() (si avanza)
   +-- onRetrocederPagina() -> grabarSinValidar() (si retrocede)

6. Fragment Siguiente
   |-- preCargarDatos(true) [linea 117-130]
   |     +-- Retorna true/false (puede bloquear navegacion)
   |
   +-- cargarDatos() [linea 130]
         +-- cargarDatosOrThrow()
               +-- entityToUI() o mapToUI() [reflexion]

7. BD
   +-- Service.findXxx() -> Entity -> fillEntity(cursor)

8. Resultado
    |-- Exito -> Fragment mostrado con datos cargados
    +-- Error -> Retroceso automatico a pagina anterior
`

**Archivos:**
- CuestionarioSimpleOnPageChangeListener.java:41-160
- FragmentFormWithoutXML.java:155-166 (cargarDatos)
- FragmentForm.java:249-379 (entityToUI - reflexion)

## 2.4 Flujo: Exportar Datos

`
1. Usuario
   |-- Selecciona viviendas en ExportacionFragment
   +-- Toca "Exportar"

2. Pantalla
   +-- ExportacionFragmentActivity -> ExportacionFragment

3. Evento
   +-- Boton exportar -> Exportacion (AsyncTask)

4. Background
   +-- Exportacion.doInBackground() [linea 108]
         +-- escribirXML()
               |-- Crea archivo .xml en SD card
               |-- Lee de: HogarService, Enaho01Service, Enaho01AService,
               |     Enaho01BService, Enaho02Service, Enaho04Service,
               |     ViviendaService
               |-- Usa mapping: EnahoHogar, EnahoHogar01, etc.
               +-- XMLWriter -> archivo

5. BD
   +-- Service.findXxx() -> Entity -> getContentValues()

6. Red
   +-- Ninguna (export local)

7. Resultado
   |-- Exito -> Dialog "Exportacion exitosa" + ruta archivo
   +-- Error -> Exception -> Dialog con mensaje
`

**Archivos:**
- ExportacionFragment.java:858 lineas
- Exportacion.java:108,382-397
- ExportacionFragmentActivity.java:355 lineas

## 2.5 Flujo: Importar Datos

`
1. Usuario
   |-- Selecciona archivo .xml o .zip desde FileSelectionActivity
   +-- Confirma importacion

2. Pantalla
   +-- ImportacionFragmentActivity -> FileSelectionActivity

3. Evento
   +-- Archivo seleccionado -> Importacion (AsyncTask)

4. Background
   +-- Importacion.doInBackground() [linea 127]
         +-- procesarXML()
               |-- XMLReader -> parsea archivo
               |-- Importa: Usuarios, Ubigeos, Parameters,
               |     Conglomerados, ViviendasMarco, PersonasPanel,
               |     Caps100Panel, Hogares
               |-- Usa transacciones: getMarcoService().startTX()
               +-- DAO.insert() para cada registro

5. BD
   +-- DAO.insert() -> SQLiteDatabase (transaccional)

6. Red
   +-- Ninguna (import local)

7. Resultado
   |-- Exito -> Dialog + recarga MarcoFragment
   +-- Error -> Exception -> Dialog con mensaje
`

**Archivos:**
- Importacion.java:127,737 lineas
- ImportacionFragmentActivity.java:376 lineas

## 2.6 Flujo: GPS Tracking

`
1. Sistema
   +-- Timer cada 10 minutos (InfoGPS Service)

2. Background
   +-- InfoGPS.TimerTick() [linea 126]
         +-- enahoWebService.invokeGpsUpload(InfoGPS.this)

3. Service
   +-- EnahoWebService.invokeGpsUpload() [linea 1396]
         |-- synchronized(GPS_UPLOAD_LOCK)
         |-- NetworkUtil.isNetworkAvailable()?
         |
         |-- SI online:
         |     +-- EnahoHttpClient.invokeUpload(URL_GPS, params)
         |           |-- Si status 200 -> invokeGpsUploadListPoints()
         |           +-- Si error -> saveLocationUser(paramsDB)
         |
         +-- NO offline:
               +-- viviendaService.saveLocationUser(paramsDB)

4. BD (fallback offline)
   +-- viviendaService.saveLocationUser() -> tabla GPS

5. Red
   +-- HTTPS POST a URL_GPS (sincronizado)

6. Resultado
   |-- Online -> GPS subido al servidor
   +-- Offline -> GPS guardado localmente (pendiente sync)
`

**Archivos:**
- InfoGPS.java:8,52,126,286
- EnahoWebService.java:1396-1504

## 2.7 Flujo: Marco (Home)

`
1. Usuario
   +-- Abre app despues de login

2. Pantalla
   +-- MarcoFragment (pagina 0 del ViewPager)

3. Evento
   +-- onCreateView() -> llenarListado()

4. Fragment
   +-- MarcoFragment.llenarListado() [linea 366]
         +-- hogarService.findReporteHome(odei, mes, periodo, conglomerado)
               +-- TableComponentEnaho1 (tabla con viviendas)

5. BD
   +-- HogarService -> HogarDao -> SQLite

6. Interacciones
   |-- Long-press en fila -> Context menu:
   |     |-- "Aperturar Vivienda" -> APERTURA_VIVIENDA1
   |     |-- "Aperturar Hogar" -> APERTURA_HOGAR0A/1
   |     |-- "Exportar y enviar" -> export + upload
   |     +-- Submenus ENAHO 01/01A/01B/02/04
   |
   |-- Filtros: ODEI, MES, PERIODO, CONGLOMERADO
   +-- Colores: Azul=seleccionada, Rojo=reemplazada, Gris=fuera de marco
`

**Archivos:**
- MarcoFragment.java:366-432,434-600
- AbstractMarcoFragment.java:70-128

## 2.8 Flujo: Sincronizacion Background

`
1. Inicializacion (post-login)
   +-- TareasBackground.doInBackground() [linea 20]
         +-- Si FUNCIONALIDAD_CIIU_CAP500:
               +-- enaho01AService.findDiccionarioActividad()
               +-- Carga en memoria (HashMap)

2. Automatica (en MarcoFragment)
   +-- copiaBDService.generarEnSDExterna(activity) [linea 428]
         +-- Crea backup .db en SD card

3. Manual (por usuario)
   +-- Navigation Drawer -> "Cargar muestra" -> FileSelectionActivity
   +-- Navigation Drawer -> "Importar" -> FileSelectionActivity
   +-- Navigation Drawer -> "Copia BD" -> CopiaDBFragmentActivity
`

---

# 3. NAVEGACION

## 3.1 Arbol General de Navegacion

`
APP START
  |
  v
[Aplicacion] -> DiagnosticoManager + AppContext
  |
  v
[LoginActivity] (LAUNCHER)
  |-- Modo: ENCUESTADOR / DIGITADOR
  |-- Subsistema: CAMPO / REENTREVISTA / SUPERVISION
  |
  v (post-login)
[CuestionarioFragmentActivity] <-- activity_principal.xml
  |
  |-- Navigation Drawer (ListView izquierda):
  |   [0] Info              -> InfoActivity
  |   [1] Marco             -> CuestionarioFragmentActivity (recarga)
  |   [2] Cobertura         -> CoberturaFragmentActivity
  |   [3] Exportar          -> ExportacionFragmentActivity
  |   [4] Importar          -> FileSelectionActivity (.xml/.zip)
  |   [5] Cargar muestra    -> FileSelectionActivity (.cfg/.zip)
  |   [6] Reemplazo         -> ReemplazoFragmentActivity
  |   [7] Imp. compartida   -> FileSelectionActivity
  |   [8] Verificar         -> VerificacionFragmentActivity
  |   [9] Copia BD          -> CopiaDBFragmentActivity
  |
  |-- Overflow Action Menu:
  |   [Grabar]       -> continuar() -> grabarYContinuar()
  |   [Salir]        -> grabarYSalir() -> finish()
  |   [Config]       -> ConfigActivity
  |   [Ir a Marco]   -> nextFragment(MARCO.ordinal())
  |   [Ir a Visitas] -> irAVisitas()
  |   [Observaciones] -> abrirObservaciones()
  |   [ENAHO 01]     -> Enaho01ActionModeCallback (submenu)
  |   [ENAHO 01A]    -> nextFragment(C01A_CARATULA)
  |   [ENAHO 01B]    -> nextFragment(C01B_CARATULA)
  |   [ENAHO 02]     -> nextFragment(C02_CARATULA)
  |   [ENAHO 04]     -> nextFragment(C04_CARATULA)
  |
  |-- ViewPager (149 fragments, ordinal = pagina):
        |
        +-- Page 0: MARCO (MarcoFragment)
        |     |-- Long-press -> Context menu:
        |           |-- "Aperturar Vivienda"
        |           |-- "Aperturar Hogar"
        |           |-- "Exportar y enviar"
        |           +-- Submenus ENAHO 01/01A/01B/02/04
        |
        +-- Pages 1-13: APERTURA (Vivienda, Hogar, Supervisor)
        |
        +-- Pages 14-43: ENAHO01 (Cap100-Cap800 + Seguridad Alimentaria)
        |
        +-- Pages 44-90: ENAHO01A (Cap300-Cap500)
        |
        +-- Pages 91-102: ENAHO01B (Gobernabilidad, Corrupcion, Percepcion)
        |
        +-- Pages 103-116: ENAHO02 (Cap2000-Cap2800)
        |
        +-- Pages 117-138: ENAHO04 (Cap10-Cap50)
        |
        +-- Pages 139-148: Reportes Especiales
`

## 3.2 ENAHO01 ActionMode Sub-Menu

`
[ENAHO 01] (ActionMode)
  |
  +-- Cap100            -> CAP100_1 (P101)
  +-- Cap100-Agua       -> CAP100_5_AGUA_SEGURA
  +-- Cap.Mascota       -> CAP100_5 (P118)
  +-- Cap200            -> CAP200
  +-- CAP600 (submenu):
  |     +-- Cap600, Cap601, Cap601-Recuperacion
  |     +-- Cap602, Cap603, Cap604, Cap605
  |     +-- Cap606, Cap606D, Cap607
  |     +-- Cap609, Cap610, Cap611, Cap612, Cap612I
  +-- Cap700            -> CAP700
  +-- Cap700A           -> CAP700A
  +-- Cap800            -> CAP800
  +-- CapInsAlimentaria -> CAP109_4SEG_ALIM
`

## 3.3 Activities Secundarias (desde Navigation Drawer)

`
CuestionarioFragmentActivity
  |
  +--> ExportacionFragmentActivity
  |       +-- ExportacionFragment (858 lineas)
  |             +-- Seleccion de viviendas
  |             +-- Exportacion (AsyncTask)
  |
  +--> ImportacionFragmentActivity
  |       +-- FileSelectionActivity
  |             +-- Importacion (AsyncTask)
  |
  +--> CoberturaFragmentActivity
  |       +-- CoberturaFragment
  |
  +--> ReemplazoFragmentActivity
  |       +-- ReemplazoFragment
  |
  +--> CopiaDBFragmentActivity
  |       +-- CopiaBDService.generarEnSDExterna()
  |
  +--> VerificacionFragmentActivity
  |       +-- VerificacionFragment
  |
  +--> MensajesFragmentActivity
  |       +-- MensajesFragment
  |             +-- EnahoWebService.invokeConsultaTotalMensajes()
  |
  +--> PlanRutaFragmentActivity
  |       +-- PlanRutaFragment
  |             +-- EnahoWebService.invokeConsultaUploadPlanRuta()
  |
  +--> ConfigActivity
  |       +-- Configuracion de funcionalidades
  |
  +--> InfoActivity
  |       +-- Informacion del sistema
  |
  +--> AdminActivity (oculto, password: ADMIN/270314)
          +-- Restaurar BD desde archivo .db/.zip
`

---

# 4. FLUJOS DE DATOS

## 4.1 Flujo Local (UI -> SQLite)

`
+-------------------+     +-------------------+     +-------------------+
|     Fragment      |     |     Service       |     |      DAO          |
+-------------------+     +-------------------+     +-------------------+
        |                         |                         |
        | 1. entityToUI()         |                         |
        |    (cargarDatos)        |                         |
        |<------------------------+                         |
        |                         |                         |
        | 2. uiToEntity()         |                         |
        |    (grabar)             |                         |
        +------------------------>|                         |
        |                         |                         |
        |                    3. service.method(entity)       |
        |                         +------------------------>|
        |                         |                         |
        |                    4. dao.saveOrUpdate(entity)     |
        |                         |                         |
        |                    5. entity.getContentValues()    |
        |                         |                    [reflexion]
        |                         |                         |
        |                    6. database.insert/update()     |
        |                         |                    [SQLite]
        |                         |                         |
        | 7. resultado            |                         |
        |<------------------------+<------------------------+
`

**Mapeo UI <-> Entity (reflexion):**
`java
// FragmentForm.entityToUI() [linea 249-379]
// Lee campos de Entity via reflexion
// Mapea a widgets UI por nombre:
//   txt{Campo} -> EditText
//   spn{Campo} -> Spinner
//   chb{Campo} -> CheckBox
//   rg{Campo}  -> RadioGroup
//   rb{Campo}  -> RadioButton

// FragmentForm.uiToEntity() [linea 436-634]
// Lee widgets UI y escribe en Entity via reflexion
`

## 4.2 Flujo Remoto (UI -> Server)

`
+-------------------+     +-------------------+     +-------------------+
|     Fragment      |     |   EnahoWebService |     |  EnahoHttpClient  |
+-------------------+     +-------------------+     +-------------------+
        |                         |                         |
        | 1. service.method()     |                         |
        +------------------------>|                         |
        |                         |                         |
        |                    2. HTTP request                |
        |                         +------------------------>|
        |                         |                         |
        |                    3. HttpsURLConnection          |
        |                         |                    [SSL hardcoded]
        |                         |                         |
        |                    4. HTTPS POST/GET              |
        |                         |                    [Server]
        |                         |                         |
        | 5. respuesta            |                         |
        |<------------------------+<------------------------+
        |                         |
        | 6. parseo JSON (Gson)   |
        |<------------------------+
`

## 4.3 Flujo Export (UI -> SD Card)

`
+-------------------+     +-------------------+     +-------------------+
| ExportacionFrag   |     |   Exportacion     |     |    XMLWriter      |
+-------------------+     +-------------------+     +-------------------+
        |                         |                         |
        | 1. seleccionar viviendas|                         |
        +------------------------>|                         |
        |                         |                         |
        |                    2. doInBackground()             |
        |                         +------------------------>|
        |                         |                         |
        |                    3. Service.findXxx()            |
        |                    [HogarService, Enaho01Service,  |
        |                     Enaho01AService, etc.]        |
        |                         |                         |
        |                    4. Entity -> XML                |
        |                         +------------------------>|
        |                         |                         |
        |                    5. archivo .xml/.zip            |
        |                         |                    [SD card]
        |                         |                         |
        | 6. resultado            |                         |
        |<------------------------+<------------------------+
`

## 4.4 Flujo Import (SD Card -> UI)

`
+-------------------+     +-------------------+     +-------------------+
| FileSelection     |     |   Importacion     |     |    XMLReader      |
+-------------------+     +-------------------+     +-------------------+
        |                         |                         |
        | 1. archivo seleccionado |                         |
        +------------------------>|                         |
        |                         |                         |
        |                    2. doInBackground()             |
        |                         +------------------------>|
        |                         |                         |
        |                    3. XMLReader -> parse           |
        |                         |                         |
        |                    4. Service.startTX()            |
        |                    [MarcoService]                  |
        |                         |                         |
        |                    5. DAO.insert()                 |
        |                    [UbigeoDAO, MarcoDAO,           |
        |                     HogarDAO, Enaho01Dao]         |
        |                         |                         |
        |                    6. Service.commit()             |
        |                         |                    [transaccional]
        |                         |                         |
        | 7. resultado            |                         |
        |<------------------------+<------------------------+
`

---

# 5. ERRORES

## 5.1 BD falla

`
Excepcion lanzada
  |
  +--> MensajeException? (validacion de negocio)
  |       |
  |       +--> SI: Dialog con mensaje amigable
  |       |     "Debe iniciar una visita para poder continuar."
  |       |
  |       +--> NO: Exception tecnica
  |               |
  |               +--> Toast "ERROR: " + mensaje
  |               +--> Log.e() con stack trace
  |               +--> Retroceso a pagina anterior (si aplica)
`

**Archivos:**
- MensajeException.java:1-11 (extends SQLException)
- AbstractCuestionarioFragmentActivity.java:602-613
- CuestionarioSimpleOnPageChangeListener.java:163-173

**Casos especiales:**
- SQLiteConstraintException -> Silenciosamente ignorado (digitador log)
- onUpgrade() failure -> e.printStackTrace() (sin notificacion al usuario)

## 5.2 Red falla

`
HTTP request
  |
  +--> Excepcion de red
  |       |
  |       +--> HttpCallback.onFailure(e) [EnahoHttpClient]
  |       |       +--> Callback con HttpException
  |       |
  |       +--> AsyncHttpClient.onFailure() [legacy]
  |               +--> Status 404: Toast "Recurso no encontrado"
  |               +--> Status 500: Toast "Error en el Servidor"
  |               +--> Status 403: Dialog "No tiene permisos"
  |               +--> Otro: Dialog "Active datos o verifique signal"
  |
  +--> GPS offline fallback
          +--> saveLocationUser() -> guardado local
`

**Archivos:**
- EnahoHttpClient.java:326-367
- EnahoWebService.java:225-257,298-302,344-352

## 5.3 Datos invalidos

`
Validacion de campo
  |
  +--> Campo vacio
  |       +--> Toast "El campo no puede estar vacio"
  |
  +--> Valor fuera de rango
  |       +--> Toast "Valor fuera de rango"
  |
  +--> Spinner sin seleccion
  |       +--> Toast "Debe seleccionar una opcion"
  |
  +--> Regla de negocio
          +--> Toast con mensaje especifico
          +--> return false (bloquea navegacion)
`

**Patron comun en fragmentos:**
`java
// En listening() o validacion inline
validar(String valor) {
    if(valor == null || valor.isEmpty()) {
        ToastMessage.msgBox(context, "Campo requerido");
        return false;
    }
    return true;
}
`

## 5.4 Servidor devuelve error

`
Respuesta HTTP
  |
  +--> Status 200 + JSON valido
  |       +--> Exito -> procesar respuesta
  |
  +--> Status 200 + JSON invalido
  |       +--> Toast "Hubo un error en el servidor!"
  |
  +--> Status != 200
  |       +--> Status 403: Dialog "No tiene permisos"
  |       +--> Status 404: Toast "Recurso no encontrado"
  |       +--> Status 500: Toast "Error en el Servidor"
  |       +--> Otro: Dialog "Hubo un error, revise su conexion"
  |
  +--> JSONObject parsing error
          +--> Dialog "Hubo un error, revise su conexion o intente en unos instantes"
`

**Archivos:**
- EnahoWebService.java:197-216,720-738

## 5.5 Usuario cancela

`
Back button / Cancelar
  |
  +--> LoginActivity
  |       +--> super.onDestroy() + finish() (sin confirmacion)
  |
  +--> ExportacionFragmentActivity
  |       +--> AlertDialog: "Esta seguro de salir?"
  |             +--> Si: finish()
  |             +--> No: dismiss()
  |
  +--> ImportacionFragmentActivity
  |       +--> AlertDialog: "Esta seguro de salir?"
  |             +--> Si: finish()
  |             +--> No: dismiss()
  |
  +--> Digitador mode switch
          +--> AlertDialog (setCancelable=false)
                +--> Aceptar: setModoLogueo(DIGITADOR)
                +--> Cancelar: rbModoEncuestador.setChecked(true)
`

## 5.6 Ocurre excepcion

`
Excepcion no controlada
  |
  +--> UnCaughtException Handler
  |       |
  |       +--> Build device report:
  |       |     - Locale, Version, Model, Android version
  |       |     - Board, Brand, Device, Host, ID, Product
  |       |     - Stack trace completo
  |       |
  |       +--> AlertDialog: "La aplicacion ha presentado un error"
  |       |     +--> "Cancelar" -> System.exit(0)
  |       |     +--> "Reportar" -> email Intent -> System.exit(0)
  |       |
  |       +--> Email destinatarios:
  |             - enaho11@inei.gob.pe
  |             - juliolavado5@gmail.com
  |             - Asunto: "Enaho2019 Error!"
  |
  +--> Aplicacion.inicializar()
          +--> catch Exception
          +--> Toast "No se pudo inicializar el contexto"
          +--> App en estado degradado (sin crash)
`

**Archivos:**
- UnCaughtException.java:58-135
- Aplicacion.java:33-40

---

# 6. FLUJOS CRITICOS

## 6.1 Login + Autenticacion (CRITICO)

**Importancia:** Puerta de entrada unica. Sin login correcto, no hay acceso a la app.

`
LoginActivity
  |-- Valida campos vacios
  |-- Valida GPS/SIM/datos (si FUNCIONALIDAD_GPSDATOS)
  |-- Crea Inicializacion (AsyncTask)
        |
        +-- doInBackground():
        |     |-- PersonalService.findByUsuarioClave()
        |     |-- Verifica permisos por cargo
        |     |-- Verifica subsistema
        |     +-- Lanza TareasBackground
        |
        +-- onPostExecute():
              |-- Si error -> Dialog con mensaje
              +-- Si exito -> CuestionarioFragmentActivity + finish()
`

**Riesgos:**
- Credenciales hardcoded: ADMIN/270314 (loginActivity:407)
- Sin rate limiting en intentos de login
- PIN almacenado en AppContext.pin (estado global)

## 6.2 Guardado con Validacion de Visita (CRITICO)

**Importancia:** Integridad de datos. Cada registro debe estar asociado a una visita activa.

`
Fragment.grabar()
  |
  +--> Service.method(entity, validarVisita=true)
        |
        +--> EnahoDao.saveOrUpdateConVisita()
              |
              +--> onAntesInsertarRegistro()
              |     |
              |     +--> Obtiene visita activa
              |     +--> Si visita cerrada:
              |     |     +--> throw MensajeException("Debe iniciar una visita...")
              |     |
              |     +--> Si visita activa:
              |           +--> Asigna columna VISITA
              |           +--> Actualiza timestamp (A_FECFIN_ENTRE)
              |
              +--> SQLiteWithoutIdDao.saveOrUpdate()
                    |
                    +--> Si timeout expirado:
                          +--> Auto-cierra visita
                          +--> actualizarResultadoFinalHogar()
`

**Riesgos:**
- Si alidarVisita=false, se salta la validacion (punto de entrada para datos inconsistentes)
- Auto-cierre de visita puede causar perdida de datos parciales
- MensajeException vs Exception: manejo inconsistente

## 6.3 Exportacion/Importacion (ALTO)

**Importancia:** Transferencia de datos entre dispositivos y servidor.

**Exportacion:**
`
Exportacion (AsyncTask)
  |-- exportarViviendas() -> lista de viviendas seleccionadas
  |-- escribirXML() -> XMLWriter -> archivo .xml
  |-- Si error -> categorizado por tipo:
  |     - NullPointerException -> "Error de ausencia de datos"
  |     - FileNotFoundException -> "El archivo no pudo ser encontrado"
  |     - IOException -> "El archivo no pudo ser leido"
  |     - Exception -> mensaje generico
  +-- onPostExecute() -> dialog resultado
`

**Importacion:**
`
Importacion (AsyncTask)
  |-- procesarXML() -> XMLReader -> parse
  |-- Service.startTX() -> transaccion
  |-- DAO.insert() por cada registro
  |-- Service.commit()
  |-- Si error -> rollback + dialog
  +-- onPostExecute() -> recarga MarcoFragment
`

**Riesgos:**
- Sin validacion de integridad del archivo
- Sin backup antes de importar
- Transacciones largas pueden causar ANR
- Formato XML hardcodeado (no versionado)

## 6.4 Deteccion ANR + Diagnostico (MEDIO)

**Importancia:** Monitoreo de rendimiento y diagnostico de problemas.

`
AnrWatchDog (thread background)
  |-- Cada 2000ms:
  |     +--> Handler.post(token) -> Main thread
  |     +--> Espera 2000ms
  |     +--> Si token != ultimoHeartbeat:
  |           +--> Espera 2000ms mas (total 4s)
  |           +--> Si sigue sin respuesta:
  |                 +--> DiagnosticoManager.onAnrDetected()
  |                       |
  |                       +--> DiagnosticoEstado.getResumenEstado()
  |                       |     - pantallaActual
  |                       |     - fragmentActual
  |                       |     - ultimoServicio
  |                       |     - ultimoEvento
  |                       |     - ultimaOperacionBD
  |                       |
  |                       +--> Thread stack trace (top 20 frames)
  |                       +--> Lista de threads activos
  |                       +--> DiagnosticoLogger.anr()
  |                             +--> Escribe a LOGS/diagnostico_YYYYMMDD.log
  |
  +--> Si respuesta OK:
        +--> Resetea anrReportado = false
`

**Archivos:**
- AnrWatchDog.java:1-100
- DiagnosticoManager.java:1-136
- DiagnosticoEstado.java:1-211
- DiagnosticoLogger.java:1-418
- HttpDiagnostic.java:1-127

**Configuracion:**
- DiagnosticoConfig.ENABLED = true
- DiagnosticoConfig.MAX_LOG_DAYS = 15
- Deteccion HTTP en Main Thread via HttpDiagnostic.inicio()
- HTTP lento (>5000ms) -> WARNING "SERVICIO LENTO"

---

# RESUMEN EJECUTIVO

## Flujos Documentados

| # | Flujo | Criticidad | Archivos Clave |
|---|-------|------------|----------------|
| 1 | Login + Autenticacion | CRITICO | LoginActivity, Inicializacion |
| 2 | Guardar (Grabar) | CRITICO | Cap200Fragment, EnahoDao |
| 3 | Navegar entre fragments | ALTO | CuestionarioSimpleOnPageChangeListener |
| 4 | Exportar datos | ALTO | Exportacion, ExportacionFragment |
| 5 | Importar datos | ALTO | Importacion |
| 6 | GPS Tracking | MEDIO | InfoGPS, EnahoWebService |
| 7 | Marco (Home) | MEDIO | MarcoFragment |
| 8 | Sincronizacion background | BAJO | TareasBackground, CopiaBDAutomatica |

## Patrones de Error

| Patron | Implementacion |
|--------|----------------|
| MensajeException | Business validation -> Dialog |
| Exception | Technical error -> Toast + Log |
| HttpCallback.onFailure() | Network error -> Status-specific messages |
| UnCaughtException | Crash -> Email report -> Exit |
| ANR WatchDog | Thread monitoring -> Diagnostic log |

## Puntos Criticos Identificados

1. **Credenciales hardcoded** (ADMIN/270314) - RIESGO SEGURIDAD
2. **Sin rate limiting en login** - RIESGO SEGURIDAD
3. **PIN en estado global** - RIESGO SEGURIDAD
4. **Auto-cierre de visita** - RIESGO INTEGRIDAD
5. **Transacciones largas en import** - RIESGO ANR
6. **Sin backup antes de import** - RIESGO PERDIDA DATOS
7. **Formato XML sin versionado** - RIESGO COMPATIBILIDAD

---

**Documento generado:** 2026-08-30
**Estado:** EN REVISION
**Siguiente paso:** Aprobacion del usuario para Etapa 4
