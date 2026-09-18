# 07 - RIESGOS TECNICOS Y ANR

> **Fecha:** 2026-08-30
> **Estado:** EN REVISION
> **Clasificacion:** HECHO (verificaciones directas contra el codigo)

---

# INDICE

1. [ANR](#1-anr)
2. [Concurrencia](#2-concurrencia)
3. [Memory Leaks](#3-memory-leaks)
4. [NullPointerException](#4-nullpointerexception)
5. [Android Moderno](#5-android-moderno)
6. [Red](#6-red)
7. [Base de Datos](#7-base-de-datos)
8. [Clasificacion](#8-clasificacion)
9. [Tabla Resumen](#9-tabla-resumen)
10. [Candidatos ANR Detallados](#10-candidatos-anr-detallados)

---

# 1. ANR

## 1.1 Candidatos Criticos

### 1.1.1 SyncHttpClient en Main Thread (10+ instancias)

**Archivo:** Enaho2017/src/gob/inei/enaho/service/EnahoWebService.java
**Severidad:** CRITICO

SyncHttpClient de loopj ejecuta peticiones HTTP **sincronicamente en el hilo llamador**. Hay 10+ instancias:

| Metodo | Linea | Descripcion |
|--------|-------|-------------|
| invokeGpsUpload1 | 475 | Subida GPS individual |
| invokeGpsUploadListPoints1 | 640 | Subida lista GPS |
| invokeConsultaTotalMensajes1 | 761 | Consulta total mensajes |
| invokeConsultaListaMensajes1 | 809 | Lista de mensajes |
| invokeConsultaInfo1 | 855 | Consulta informacion ciudadano |
| invokeConsultaInfo (variant) | 941 | Variante consulta |
| invokeConsultaInfo (variant) | 963 | Variante consulta |
| invokeConsultaInfo (variant) | 984 | Variante consulta |
| invokeConsultaInfo (variant) | 1006 | Variante consulta |
| invokeConsultaInfo (variant) | 1035 | Variante consulta |

**Ejemplo (linea 849-901):**
`java
public ConsultaInfo invokeConsultaInfo1(final Context context, String dni) throws ... {
    final ConsultaInfo info = new ConsultaInfo();
    SyncHttpClient client = new SyncHttpClient();  // BLOQUEA EL HILO LLAMADOR
    setAutorizacion(client);
    client.post(SERVICE_URL_CONSULTA_INFO, params, new JsonHttpResponseHandler() { ... });
    return info; // Patron de retorno sincronico
}
`

**Impacto:** Si algun invokedor ejecuta esto desde el Main Thread, la app se congela hasta 30 segundos (timeout de lectura).

---

### 1.1.2 NetworkUtil.isInternetAvailable() - HTTP Sincronico

**Archivo:** Enaho2017/src/gob/inei/enaho/http/NetworkUtil.java
**Lineas:** 56-82
**Severidad:** CRITICO

`java
public static boolean isInternetAvailable() {
    HttpURLConnection connection = null;
    try {
        URL url = new URL("https://webapp.inei.gob.pe");
        connection = (HttpURLConnection) url.openConnection(); // BLOQUEANTE
        connection.setConnectTimeout(3000);
        connection.setReadTimeout(3000);
        connection.setRequestMethod("HEAD");
        int responseCode = connection.getResponseCode(); // LECTURA BLOQUEANTE
        return responseCode >= 200 && responseCode < 400;
    } catch (Exception e) { ... }
}
`

**Analisis:** Peticion HTTP HEAD sincronica con 6 segundos de timeout total (3s conexion + 3s lectura). Sin enforcement de hilo. Si se llama desde onCreate(), onClick(), o cualquier callback de UI, causa ANR inmediato.

---

### 1.1.3 ConfigActivity.onClick - Base de Datos + XML en Main Thread

**Archivo:** Enaho2017/src/gob/inei/enaho2026/activity/ConfigActivity.java
**Lineas:** 78-118
**Severidad:** CRITICO

`java
correccion_upgrade.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View arg0) {
        // ESTO SE EJECUTA EN EL MAIN THREAD
        MyDatabaseHelper openHelper = AppContext.getServices().getOpenHelper();
        SQLiteUtil.executeTransaccion(openHelper, new Command1<SQLiteDatabase>() {
            @Override
            public void execute(SQLiteDatabase db) throws Exception {
                for(int v=desde; v<=hasta; v++) {
                    cargarXML(db, MyDatabaseHelper.upgrades[v-2], tag, true);
                }
            }
        });
    }
});
`

Y cargarXML (linea 163-176) parsea XML + multiples execSQL:
`java
private void cargarXML(SQLiteDatabase db, int xml, String tag, boolean ...) throws Exception {
    DocumentBuilder dbuilder = DocumentBuilderFactory.newInstance().newDocumentBuilder();
    InputStream in = this.getResources().openRawResource(xml);
    Document doc = dbuilder.parse(in, null);          // PARSING XML EN MAIN THREAD
    NodeList statements = doc.getElementsByTagName(tag);
    for (i=0; i<statements.getLength(); i++) {
        sql = statements.item(i).getChildNodes().item(0).getNodeValue();
        if(!sql.trim().isEmpty())
            db.execSQL(sql);                           // EJECUCION SQL EN MAIN THREAD
    }
}
`

**Impacto:** Handler de click ejecuta: (1) transaccion BD, (2) parseo de archivo XML, (3) loop de declaraciones SQL.

---

### 1.1.4 ConfigActivity.cerrarVisitas() - BD en Main Thread

**Archivo:** Enaho2017/src/gob/inei/enaho2026/activity/ConfigActivity.java
**Lineas:** 66-71, 122-128
**Severidad:** CRITICO

`java
correccion_cv.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View arg0) {
        cerrarVisitas(ConfigActivity.this, ConfigActivity.this); // BD EN MAIN THREAD
        mostrarDialog("Realizado");
    }
});
`

ViviendaDao.cerrarVisitasNoCerradas() (lineas 56-95):
`java
public void cerrarVisitasNoCerradas(SQLiteDatabase db) throws SQLException, ParseException {
    List<VisitaEncuestadorEntity> lista = hogarDao.findVisitasEncuestadorsinDatosObligatorios(db,null,null);
    for(VisitaEncuestadorEntity ve : lista) {
        // Multiples lecturas y escrituras de BD por iteracion
        SQLiteUtil.saveOrUpdate(db, HogarDao.T_VISITA_ENC, r, null);
    }
}
`

**Impacto:** Si hay muchas visitas sin cerrar, itera cientos de registros con E/S de BD en Main Thread.

---

### 1.1.5 LoginActivity.cerrar() - Copia Archivo en Main Thread

**Archivo:** Enaho2017/src/gob/inei/enaho2026/activity/LoginActivity.java
**Lineas:** 390-398
**Severidad:** CRITICO

`java
public void cerrar(View v) {
    try {
        copiaBDService.generarEnMemoriaInterna(getApplicationContext()); // COPIA ARCHIVO EN MAIN THREAD
    } catch (IOException e) {
        Log.e(this.getClass().toString(), e.getMessage(), e);
    }
    android.os.Process.killProcess(android.os.Process.myPid());
    finish();
}
`

CopiaBDService.generarEnMemoriaInterna() (lineas 76-107) copia todo el archivo de BD.

---

### 1.1.6 ThreadUtil.dormirHilo() - Sleep Bloqueante

**Archivo:** EnahoAndroidUtil/src/util/ThreadUtil.java
**Lineas:** 16-22
**Severidad:** CRITICO

`java
public static void dormirHilo(long milisegundos) {
    try {
        Thread.sleep(milisegundos); // BLOQUEA EL HILO LLAMADOR
    } catch(InterruptedException ex) {
        Thread.currentThread().interrupt();
    }
}
`

---

# 2. CONCURRENCIA

## 2.1 Synchronized Blocks

### 2.1.1 DiagnosticoLogger - I/O en Bloque Sincronizado

**Archivo:** Enaho2017/src/gob/inei/enaho/diagnostico/DiagnosticoLogger.java
**Lineas:** 82, 255-269

`java
synchronized (LOCK) { ... } // Linea 82 - cada llamada adquiere el lock

// Lineas 255-269: E/S de archivos dentro del bloque sincronizado
synchronized (LOCK) {
    java.io.BufferedWriter writer = new java.io.BufferedWriter(
        new java.io.FileOutputStream(...)  // ESCRITURA DE ARCHIVO
    );
}
`

### 2.1.2 DiagnosticoEstado - 12+ Bloques Sincronizados

**Archivo:** Enaho2017/src/gob/inei/enaho/diagnostico/DiagnosticoEstado.java
**Lineas:** 37-190

`java
synchronized (LOCK) { ... }  // Linea 37
synchronized (LOCK) { ... }  // Linea 53
synchronized (LOCK) { ... }  // Linea 63
// ... continua para cada metodo
`

**Riesgo:** Si el Main Thread llama a cualquier metodo sincronizado mientras un hilo de fondo tiene el lock, el Main Thread se bloquea.

### 2.1.3 GPS_SYNC_LOCK

**Archivo:** Enaho2017/src/gob/inei/enaho/service/EnahoWebService.java
**Linea:** 1507-1556

`java
synchronized (GPS_SYNC_LOCK) {
    // Subida de puntos GPS - puede tomar minutos
    for (final UbicacionUsuario item : listaPuntos) {
        HttpResponse response = client.postForm(SERVICE_URL_GPS_UPLOAD, params);
    }
}
`

## 2.2 AsyncTask (7 instancias - DEPRECATED)

| Clase | Linea | Archivo |
|-------|-------|---------|
| Inicializacion | 22 | ackground/Inicializacion.java |
| Importacion | 68 | ackground/Importacion.java |
| Exportacion | 47 | ackground/Exportacion.java |
| TareasBackground | 9 | ackground/TareasBackground.java |
| CopiaBDAutomatica | 16 | ackground/CopiaBDAutomatica.java |
| ImportacionGPX | 25 | DNCE/components/ImportacionGPX.java |
| PrintScreen | 6167 | DNCE/components/FragmentForm.java |

**Problemas:**
- AsyncTask deprecado desde API 30
- Referencias a Activity en clases internas anonim - potencial memory leak
- Ejecucion serial en un solo hilo de fondo - cuello de botella
- Sin conciencia de lifecycle - referencias a Activities destruidas causan crashes

---

# 3. MEMORY LEAKS

## 3.1 Criticos

### 3.1.1 InfoGPS - Referencia Estatica a Activity

**Archivo:** Enaho2017/src/gob/inei/enaho/service/background/InfoGPS.java
**Lineas:** 52, 286-291

`java
private CuestionarioFragmentActivity actividad; // Referencia directa a Activity

// Metodo setData() almacena la referencia y nunca la limpia
public void setData(CuestionarioFragmentActivity act) {
    this.actividad = act;
}

// Timer (linea 36) ejecuta TimerTask (linea 104) que accede a actividad
// Si el Activity se destruye, el Service mantiene la referencia viva
`

**Impacto:** El Timer se ejecuta cada 10 minutos indefinidamente. Si pararTracker() no se llama, el Activity nunca se libera de memoria.

### 3.1.2 AsyncTask con Activity como Campo

| Clase | Campo | Linea |
|-------|-------|-------|
| Inicializacion | private Activity activity | 24 |
| Importacion | private Activity activity | 72 |
| Exportacion | private Activity activity | 49 |
| CopiaBDAutomatica | private Activity activity | 19 |

**Patron problematico:** AsyncTask mantiene referencia a Activity durante toda su vida util. Si el dispositivo rota, el Activity anterior se filtra.

## 3.2 Altos

### 3.2.1 Context Estatico

| Archivo | Linea | Campo |
|---------|-------|-------|
| UnCaughtException.java | 23 | private static Context context1 |
| ExportacionFragmentActivity.java | 71 | public static Context baseContext |
| ImportacionFragmentActivity.java | 73 | public static Context baseContext |
| CoberturaFragmentActivity.java | 78 | public static Context baseContext |

### 3.2.2 Callbacks Anonimos con Activity

**Archivo:** Enaho2017/src/gob/inei/enaho/service/EnahoWebService.java
**Lineas:** 136-259, 261-307, 309-396

Clases AsyncHttpResponseHandler anonimas capturan el parametro ctivity. Si el Activity se destruye antes de que el callback se ejecute, el Activity se filtra.

---

# 4. NULLPOINTEREXCEPTION

## 4.1 Criticos

### 4.1.1 NPE Garantizado en invokeGpsUpload1

**Archivo:** Enaho2017/src/gob/inei/enaho/service/EnahoWebService.java
**Lineas:** 447-453

`java
if (location==null) {
    params.put("latitud", "-99.9999999");
    ...
    System.out.println("JUCE_EWS: WS_LAT1:"+location.getLatitude()); // NPE!
    System.out.println("JUCE_EWS: WS_LONG1:"+location.getLongitude()); // NPE!
}
`

**Impacto:** El codigo entra en la rama location==null pero luego llama a location.getLatitude(), lo que causara NullPointerException garantizado.

## 4.2 Altos

### 4.2.1 ProgressDialog despues de Activity destruido

**Archivo:** Enaho2017/src/gob/inei/enaho/service/EnahoWebService.java
**Lineas:** 193-194, 227

prgDialog.hide() en callbacks asincronos. Si el Activity se destruye antes del callback, lanzara WindowManager o NPE.

### 4.2.2 Fragment.getActivity() sin verificacion null

**Archivo:** Enaho2017/src/gob/inei/enaho/service/EnahoWebService.java

ragmento.getActivity() se llama en multiples lineas sin verificar si el fragmento esta desenganchado: 267, 300, 781, 788, 801, 833, 841, 1027, 1061, 1069, 1363, 1822, 1930.

## 4.3 Medios

### 4.3.1 Cursor puede ser null

**Archivo:** DNCE/src/gob/inei/dnce/components/camera/CameraClass.java
**Lineas:** 186-207

cursor.moveToLuist() se llama sin verificar si query() retorno null.

### 4.3.2 LocationManager.getBestProvider puede retornar null

**Archivo:** Enaho2017/src/gob/inei/enaho/service/EnahoWebService.java
**Lineas:** 1566-1568

---

# 5. ANDROID MODERNO

## 5.1 Estado Actual

| Campo | Valor |
|-------|-------|
| targetSdkVersion | 24 (Android 7.0 Nougat) |
| minSdkVersion | 23 (Android 6.0 Marshmallow) |
| Lenguaje | Java (sin Kotlin) |
| Build System | Sin Gradle moderno para app principal |

## 5.2 APIs Deprecated

### 5.2.1 AsyncTask (Deprecated API 30)

7 clases usan AsyncTask (ver seccion 2.2).

### 5.2.2 ProgressDialog (Deprecated API 26)

30+ usos en el proyecto:
- EnahoWebService.java - 11+ instancias
- Inicializacion.java - linea 72
- Importacion.java - linea 116
- Exportacion.java - linea 95
- DialogUtil.java - linea 11
- Todas las clases Fragment que usan ProgressDialog como campo

### 5.2.3 ConnectivityManager.getActiveNetworkInfo() (Deprecated API 29)

**Archivo:** LoginActivity.java lineas 277, 317

### 5.2.4 TelephonyManager.getCellLocation() (Deprecated API 29)

**Archivo:** LoginActivity.java linea 353

## 5.3 Compatibilidad Android 12+ (API 31)

| Problema | Archivo | Impacto |
|----------|---------|---------|
| Foreground Service restricciones | AndroidManifest.xml:103-107 | InfoGPS no es foreground service - crash en API 31+ |
| PendingIntent sin FLAG_IMMUTABLE | MapsWithMe/MwmRequest.java:100 | Crash en API 31+ |

## 5.4 Compatibilidad Android 13+ (API 33)

| Problema | Archivo | Impacto |
|----------|---------|---------|
| Falta POST_NOTIFICATIONS | AndroidManifest.xml | Notificaciones silenciadas |
| READ_PHONE_STATE restricciones | AndroidManifest.xml:21 | Solo identificadores no-resetables |

## 5.5 Compatibilidad Android 14+ (API 34)

| Problema | Archivo | Impacto |
|----------|---------|---------|
| Runtime receiver sin RECEIVER_EXPORTED | InfoGPS.java:216, 229 | Crash en API 34+ |
| Broadcast implicitos restringidos | CapturadorGPS.java:237, 247, 260 | Funcionalidad rota |

---

# 6. RED

## 6.1 SSL/TLS - VALIDACION COMPLETAMENTE DESHABILITADA

**Archivo:** Enaho2017/src/gob/inei/enaho/service/MySSLSocketFactory.java
**Lineas:** 27-37

`java
// TrustManager con implementaciones VACIAS
public void checkClientTrusted(X509Certificate[] chain, String authType) { }
public void checkServerTrusted(X509Certificate[] chain, String authType) { }
public X509Certificate[] getAcceptedIssuers() { return null; }
`

**Impacto:** La app acepta TODOS los certificados, incluyendo auto-firmados y falsificados. Vulnerable a ataques Man-in-the-Middle (MITM).

## 6.2 Credenciales Hardcoded

| Credencial | Archivo | Linea |
|------------|---------|-------|
| Admin200786 / Admin200786 | HttpConfig.java | 4-5 |
| Admin200786 (inline) | EnahoWebService.java | 126, 942, 964, 985, 1007, 1036 |
| ADMIN / 270314 | LoginActivity.java | 407 |
| SIS / 123 | Configuracion.java | 100-101 |
| ENC1 / 123456 (debug) | LoginActivity.java | 105-106 |

## 6.3 HTTP en Texto Claro

**Archivo:** Configuracion.java
**Linea:** 97

`java
public static final String URL_SERVIDOR_WEB_DEVELOP = "http://development.inei.gob.pe:8080";
`

Sin ndroid:usesCleartextTraffic="false" en AndroidManifest.xml.

## 6.4 Logcat con Credenciales

**Archivo:** Enaho2017/src/gob/inei/enaho/http/EnahoHttpClient.java
**Linea:** 408

`java
Log.e("HEADER_AUTH:->", conn.getRequestProperty("Authorization")); // Logea credenciales Base64
`

## 6.5 HTTP Crudo (Sin Pooling)

**Archivo:** Enaho2017/src/gob/inei/enaho/http/EnahoHttpClient.java (594 lineas)

Construido sobre HttpsURLConnection crudo. Sin connection pooling, sin retry automatico, sin HTTP/2. Auth Basic manual en cada peticion.

---

# 7. BASE DE DATOS

## 7.1 Indices

**0 declaraciones CREATE INDEX** en toda la base de datos de 137 tablas.

Consultas frecuentes con multiples JOINs (hasta 6 tablas) sin indices de apoyo.

## 7.2 Inyeccion SQL

100+ puntos con concatenacion directa de strings en queries SQL:
`java
String query = "SELECT * FROM " + tabla + " WHERE id=" + id; // INYECCION SQL
`

## 7.3 Acceso desde Main Thread

| Metodo | Archivo | Linea |
|--------|---------|-------|
| UsuarioDAO.getUsuario() | UsuarioDAO.java | 27-97 |
| cerrarVisitasNoCerradas() | ViviendaDao.java | 56-95 |
| cargarXML() | ConfigActivity.java | 163-176 |
| eliminarVivienda() | ViviendaDao.java | 278-330 |
| eliminarHogar() | HogarDao.java | 2652-2700 |

## 7.4 Transacciones

`java
// Patron comun - sin manejo de errores
SQLiteUtil.executeTransaccion(openHelper, new Command1<SQLiteDatabase>() {
    @Override
    public void execute(SQLiteDatabase db) throws Exception {
        // Multiples operaciones sin rollback explicito
    }
});
`

## 7.5 Sin Estrategia de Migracion

No hay clase Migration ni versionado de esquema. Los upgrades se manejan con XML de sentencias SQL crudas.

---

# 8. CLASIFICACION

| Severidad | Cantidad | Descripcion |
|-----------|----------|-------------|
| **CRITICO** | 12 | Causa crash, ANR, o fuga de seguridad grave |
| **ALTO** | 20 | Causa comportamiento inesperado, fuga de memoria, o degradacion |
| **MEDIO** | 7 | Codigo deprecated, patrones problematicos, riesgo futuro |
| **BAJO** | 0 | - |
| **TOTAL** | **39** | |

---

# 9. TABLA RESUMEN

| # | Problema | Archivo | Clase | Metodo | Linea | Severidad | Impacto |
|---|----------|---------|-------|--------|-------|-----------|---------|
| 1 | SyncHttpClient en Main Thread | EnahoWebService.java | EnahoWebService | invokeConsultaInfo1 | 475,640,761,809,855 | CRITICO | Congelamiento app hasta 30s |
| 2 | HTTP sincronico en isInternetAvailable | NetworkUtil.java | NetworkUtil | isInternetAvailable | 56-82 | CRITICO | Bloqueo 6 segundos |
| 3 | ConfigActivity onClick DB+XML | ConfigActivity.java | ConfigActivity | onClick | 78-118 | CRITICO | Bloqueo Main Thread |
| 4 | cerrarVisitas en Main Thread | ConfigActivity.java | ConfigActivity | onClick | 66-71 | CRITICO | Iteracion BD en UI |
| 5 | Copia archivo en Main Thread | LoginActivity.java | LoginActivity | cerrar | 390-398 | CRITICO | I/O archivo grande en UI |
| 6 | Thread.sleep util | ThreadUtil.java | ThreadUtil | dormirHilo | 16-22 | CRITICO | Bloqueo configurable |
| 7 | NPE garantizado location null | EnahoWebService.java | EnahoWebService | invokeGpsUpload1 | 447-453 | CRITICO | Crash garantizado |
| 8 | MySSLSocketFactory sin validacion | MySSLSocketFactory.java | MySSLSocketFactory | checkServerTrusted | 27-37 | CRITICO | Vulnerabilidad MITM |
| 9 | Credenciales Admin hardcoded | HttpConfig.java | HttpConfig | - | 4-5 | CRITICO | Credenciales expuestas |
| 10 | InfoGPS referencia Activity | InfoGPS.java | InfoGPS | setData | 286-291 | CRITICO | Memory leak permanente |
| 11 | Foreground Service sin declarar | AndroidManifest.xml | - | - | 103-107 | CRITICO | Crash Android 12+ |
| 12 | PendingIntent sin FLAG_IMMUTABLE | MwmRequest.java | MwmRequest | - | 100 | CRITICO | Crash Android 12+ |
| 13 | AsyncTask deprecated | Inicializacion.java | Inicializacion | - | 22 | ALTO | Memory leak, lifecycle |
| 14 | ProgressDialog deprecated | EnahoWebService.java | EnahoWebService | multiplos | 11+ | ALTO | 30+ instancias |
| 15 | ProgressDialog despues de destroy | EnahoWebService.java | EnahoWebService | onSuccess | 193-194 | ALTO | BadTokenException |
| 16 | Fragment.getActivity() sin null check | EnahoWebService.java | EnahoWebService | multiplos | 15+ | ALTO | NPE fragment desenganchado |
| 17 | Referencia estatica Context | UnCaughtException.java | UnCaughtException | - | 23 | ALTO | Memory leak |
| 18 | Static Context en Activities | ExportacionFragmentActivity.java | ExportacionFragmentActivity | - | 71 | ALTO | Memory leak |
| 19 | HTTP texto claro | Configuracion.java | Configuracion | - | 97 | ALTO | Datos sin cifrar |
| 20 | android:allowBackup=true | AndroidManifest.xml | - | - | 30 | ALTO | Extraccion de datos via USB |
| 21 | android:exported=true sin permiso | AndroidManifest.xml | - | - | 106 | ALTO | Servicio expuesto |
| 22 | Credenciales en logcat | EnahoHttpClient.java | EnahoHttpClient | - | 408 | ALTO | Credenciales visibles |
| 23 | Eliminar vivienda masivo | ViviendaDao.java | ViviendaDao | eliminarVivienda | 278-330 | ALTO | 30+ DELETE en transaccion |
| 24 | Eliminar hogar masivo | HogarDao.java | HogarDao | eliminarHogar | 2652-2700 | ALTO | 20+ DELETE en transaccion |
| 25 | invokeGpsUploadListPoints loop | EnahoWebService.java | EnahoWebService | invokeGpsUploadListPoints | 1507-1556 | ALTO | Loop HTTP bloqueante |
| 26 | DiagnosticoLogger synchronized | DiagnosticoLogger.java | DiagnosticoLogger | - | 82,255 | ALTO | Contencion de locks |
| 27 | File write en onSuccess | EnahoWebService.java | EnahoWebService | onSuccess | 1727-1760 | ALTO | I/O en Main Thread |
| 28 | ConnectivityManager deprecated | LoginActivity.java | LoginActivity | - | 277,317 | ALTO | API obsoleta |
| 29 | POST_NOTIFICATIONS faltante | AndroidManifest.xml | - | - | - | ALTO | Notificaciones silenciadas |
| 30 | Runtime receiver sin RECEIVER_EXPORTED | InfoGPS.java | InfoGPS | - | 216,229 | ALTO | Crash Android 14+ |
| 31 | DiagnosticoEstado 12+ synchronized | DiagnosticoEstado.java | DiagnosticoEstado | multiplos | 37-190 | MEDIO | Potencial contencion |
| 32 | TableComponent synchronized | TableComponent.java | TableComponent | Filter | 922 | MEDIO | Potencial deadlock |
| 33 | 0 CREATE INDEX | (137 tablas) | - | - | - | MEDIO | Queries lentas |
| 34 | 100+ puntos inyeccion SQL | Multiples archivos | - | - | - | MEDIO | Seguridad |
| 35 | ViewPager.setOnPageChangeListener | ExportacionFragmentActivity.java | ExportacionFragmentActivity | - | 87 | MEDIO | Deprecated |
| 36 | ActionBarDrawerToggle support | ExportacionFragmentActivity.java | ExportacionFragmentActivity | - | 32 | MEDIO | Soporte AndroidX |
| 37 | sin estrategia migracion | (proyecto) | - | - | - | MEDIO | Mantenibilidad |
| 38 | Credentials debug hardcoded | LoginActivity.java | LoginActivity | - | 105-106 | MEDIO | Credenciales test |
| 39 | IP internas hardcoded | Configuracion.java | Configuracion | - | 83-85 | MEDIO | Informacion expuesta |

---

# 10. CANDIDATOS ANR DETALLADOS

## ANR-1: SyncHttpClient invokeConsultaInfo1

| Campo | Valor |
|-------|-------|
| **Operacion** | Peticion HTTP POST a SERVICE_URL_CONSULTA_INFO |
| **Hilo** | Main Thread (si invokedor no valida hilo) |
| **Escenario** | Usuario realiza consulta de ciudadano durante encuesta |
| **Motivo** | SyncHttpClient bloquea el hilo llamador hasta completar la red |
| **Impacto** | App congelada hasta 30 segundos. Sistema puede mostrar ANR dialog |
| **Solucion** | Migrar a Retrofit+Coroutines. Ejecutar en viewModelScope.launch(Dispatchers.IO) |

## ANR-2: NetworkUtil.isInternetAvailable()

| Campo | Valor |
|-------|-------|
| **Operacion** | HTTP HEAD a https://webapp.inei.gob.pe |
| **Hilo** | Cualquier hilo (sin enforcement) |
| **Escenario** | Verificacion de conexion antes de operacion |
| **Motivo** | 6 segundos de timeout total (3s+3s) |
| **Impacto** | Congelamiento de 6 segundos si la red esta lenta |
| **Solucion** | Usar ConnectivityManager con NetworkCallback. No hacer peticion HTTP para verificar conectividad |

## ANR-3: ConfigActivity onClick (upgrade)

| Campo | Valor |
|-------|-------|
| **Operacion** | Transaccion BD + parseo XML + loop SQL |
| **Hilo** | Main Thread (via View.OnClickListener) |
| **Escenario** | Administrador ejecuta correccion de upgrade |
| **Motivo** | Multiples operaciones pesadas en secuencia |
| **Impacto** | Bloqueo prolongado del Main Thread |
| **Solucion** | Ejecutar en Coroutine con ProgressIndicator |

## ANR-4: ConfigActivity.cerrarVisitas()

| Campo | Valor |
|-------|-------|
| **Operacion** | Query + iteracion + UPDATE por cada visita sin cerrar |
| **Hilo** | Main Thread (via View.OnClickListener) |
| **Escenario** | Administrador corrige visitas |
| **Motivo** | Itera cientos de registros con I/O de BD |
| **Impacto** | Bloqueo proporcional al numero de visitas |
| **Solucion** | Ejecutar en Coroutine con indicador de progreso |

## ANR-5: LoginActivity.cerrar()

| Campo | Valor |
|-------|-------|
| **Operacion** | Copia completa del archivo de base de datos |
| **Hilo** | Main Thread (via XML click handler) |
| **Escenario** | Encuestador cierra sesion |
| **Motivo** | I/O de archivo puede ser varios MB |
| **Impacto** | Congelamiento durante la copia |
| **Solucion** | Ejecutar en Coroutine/Worker antes de cerrar proceso |

## ANR-6: invokeGpsUploadListPoints()

| Campo | Valor |
|-------|-------|
| **Operacion** | Loop de peticiones HTTP POST para cada punto GPS pendiente |
| **Hilo** | Hilo que invoque el metodo |
| **Escenario** | Sincronizacion GPS automatica |
| **Motivo** | Una peticion HTTP por cada punto en la lista |
| **Impacto** | Si hay 100+ puntos, puede tomar minutos |
| **Solucion** | Enviar puntos en batch. Usar WorkManager con constraints de red |

---

# HALLAZGOS PRINCIPALES

1. **12 problemas CRITICOS** que causan crash, ANR, o vulnerabilidades de seguridad
2. **20 problemas ALTOS** que causan comportamiento inesperado o fuga de memoria
3. **7 problemas MEDIOS** de codigo deprecated o patrones problematicos
4. **1 NPE GARANTIZADO** en invokeGpsUpload1 (linea 447-453)
5. **SSL COMPLETAMENTE DESHABILITADO** - todos los certificados aceptados
6. **CREDENCIALES HARDCODED** en 5+ ubicaciones
7. **targetSdk=24** - 4 versiones de Android sin soporte (12, 13, 14, 15)
8. **137 tablas sin indices** - rendimiento degradado
9. **100+ puntos de inyeccion SQL**
10. **7 AsyncTask deprecated** con patrones de memory leak

---

*Documento generado como parte de la Etapa 7 - Riesgos y ANR*
*Proximo paso: Etapa 8 - Arquitectura Objetivo*
