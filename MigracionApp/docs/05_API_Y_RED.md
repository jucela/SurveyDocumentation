# 05 - API Y RED

## REFERENCIA

- docs/01_INVENTARIO_TECNICO.md
- docs/02_ARQUITECTURA_ACTUAL.md
- docs/03_FLUJOS_FUNCIONALES.md
- docs/04_BASE_DATOS.md
- Verificacion directa contra codigo fuente

---

# 1. CLIENTES HTTP

## 1.1 EnahoHttpClient (Nuevo - Custom)

| Caracteristica | Valor |
|----------------|-------|
| **Implementacion** | Wrapper sobre HttpsURLConnection |
| **Archivo** | ENAHO2017/src/gob/inei/enaho/http/EnahoHttpClient.java |
| **Lineas** | ~585 |
| **Connect Timeout** | 20,000 ms (20s) |
| **Read Timeout** | 30,000 ms (30s) |
| **Metodos HTTP** | POST (form, multipart, binary), GET |
| **Sync/Async** | Ambos (postForm sync, postAsync/GET async) |
| **SSL** | Usa SSL del sistema (sin custom TrustManager) |
| **Auth** | Basic Auth (HttpConfig.USER/PASSWORD) |

### Metodos Disponibles

| Metodo | HTTP | Tipo | Lineas | Descripcion |
|--------|------|------|--------|-------------|
| postForm(url, Map) | POST | Sync | 70-136 | URL-encoded form |
| postForm(url, HttpParams) | POST | Sync | 235-242 | Auto-detect multipart/form |
| postAsync(url, HttpParams, HttpCallback) | POST | Async | 245-260 | Delega a postForm en background |
| getForm(url) | GET | Sync | 265-307 | GET con auth |
| getAsync(url, HttpCallback) | GET | Async | 310-323 | Delega a getForm en background |
| downloadAsync(url, HttpParams, BinaryHttpCallback) | POST | Async | 476-512 | Binary download |
| postMultipart(url, HttpParams) | POST | Sync | 371-453 | Multipart file upload |
| postBinary(url, HttpParams) | POST | Sync | 514-573 | Binary response POST |
| cancel() | - | - | 576-585 | Cancela conexion actual |

### Paquete HTTP (clases de soporte)

| Clase | Archivo | Proposito |
|-------|---------|-----------|
| HttpConfig | HttpConfig.java | Credenciales estaticas (USER/PASSWORD) |
| HttpParams | HttpParams.java | Contenedor de parametros (String + File) |
| HttpResponse | HttpResponse.java | Wrapper respuesta texto (statusCode + body + JSON helpers) |
| BinaryHttpResponse | BinaryHttpResponse.java | Wrapper respuesta binaria (statusCode + byte[]) |
| HttpException | HttpException.java | Excepcion con statusCode + body |
| HttpCallback | HttpCallback.java | Interface async (onSuccess/onFailure) |
| BinaryHttpCallback | BinaryHttpCallback.java | Interface async binaria |
| NetworkUtil | NetworkUtil.java | Verificacion de conectividad |
| HttpDiagnostic | HttpDiagnostic.java | Diagnostico HTTP (main-thread, slow >5s) |

## 1.2 AsyncHttpClient / SyncHttpClient (Legacy - loopj)

| Caracteristica | Valor |
|----------------|-------|
| **Libreria** | loopj android-async-http |
| **Archivo** | EnahoWebService.java (metodos con sufijo 1) |
| **Connect Timeout** | 20,000 ms (20s) |
| **Read Timeout** | 30,000 ms (30s) |
| **Max Retries** | 2 retries, 5000ms delay |
| **SSL** | MySSLSocketFactory (acepta TODOS los certificados) |
| **Auth** | Basic Auth hardcoded |

### Configuracion Legacy

`java
// EnahoWebService.java lineas 148-152
final int DEFAULT_TIMEOUT = 30 * 1000;   // 30s
final int CONECTION_TIMEOUT = 20 * 1000;  // 20s
client.setConnectTimeout(CONECTION_TIMEOUT);
client.setResponseTimeout(DEFAULT_TIMEOUT);
client.setMaxRetriesAndTimeout(2, 5000);  // 2 retries, 5s
`

## 1.3 MySSLSocketFactory (Legacy - INSEGURO)

| Caracteristica | Valor |
|----------------|-------|
| **Archivo** | ENAHO2017/src/gob/inei/enaho/service/MySSLSocketFactory.java |
| **Protocolo** | TLS |
| **TrustManager** | Custom X509TrustManager que **acepta TODOS los certificados** |
| **HostnameVerifier** | Comentado (ALLOW_ALL_HOSTNAME_VERIFIER) |

**RIESGO CRITICO:** TrustManager vacio sin verificacion de certificados.

---

# 2. ENDPOINTS

## 2.1 URL Base

`java
// Configuracion.java linea 95
URL_SERVIDOR_WEB_PRODUCCION = "https://webapp.inei.gob.pe:8443"
`

**Base path:** {URL}/EnahoWeb/webresources/files/

## 2.2 Endpoints Activos (10)

| # | Constante | Endpoint | HTTP | Metodo(s) | Linea |
|---|-----------|----------|------|-----------|-------|
| 1 | SERVICE_URL_GPS_UPLOAD | /ubicacionGpsUsuario | POST | invokeGpsUpload, invokeGpsUpload1, invokeGpsUpload_incompleto, invokeGpsUploadListPoints | 95 |
| 2 | SERVICE_URL_CONSULTA_INFO | /consultaInfo | POST | invokeConsultaInfo, invokeConsultaInfo1 | 96 |
| 3 | SERVICE_URL_CONSULTA_PLAN_RUTA | /consultaRuta | POST | invokeConsultaUploadPlanRuta, invokeConsultaUploadPlanRuta1 | 97 |
| 4 | SERVICE_URL_GRABAR_RUTA | /grabarRuta | POST | invokeGrabarNuevaRuta, invokeGrabarNuevaRuta1 | 98 |
| 5 | SERVICE_URL_ACTUALIZAR_RUTA | /actualizarRuta | POST | invokeActualizarNuevaRuta, invokeActualizarNuevaRuta1 | 99 |
| 6 | SERVICE_URL_CONSULTA_HORA | /consultaHora | **GET** | consultaHoraServidor, consultaHoraServidor1 | 100 |
| 7 | SERVICE_URL_CONSULTA_EXPORTACION | /consultaSubidos_v2 | POST | invokeConsultaUploadExportacion, invokeConsultaUploadExportacion1 | 101 |
| 8 | SERVICE_URL_EXPORTACION | /uploadMultiple_v2 | POST (multipart) | invokeWSUploadFileExportacion, invokeWSUploadFileExportacion1 | 102 |
| 9 | SERVICE_URL_DESCARGA | /descargaBackup_v2 | POST (binary) | invokeDownload, invokeDownload1 | 103 |
| 10 | SERVICE_AGUA | /aguaService | POST | invokeAguaService, invokeAguaService1 | 104 |

## 2.3 Endpoints Deprecated/No Usados (5)

| # | Constante | Endpoint | Estado |
|---|-----------|----------|--------|
| 11 | SERVICE_URL_DATA_INICIAL_REENTREVISTA | /descargaDataInicialReentrevista | Sin uso |
| 12 | SERVICE_URL_CONSULTA | /consultaMensajes | Sin uso |
| 13 | SERVICE_URL_MENSAJES | /mensajes | Sin uso |
| 14 | SERVICE_URL_GRABAR_MENSAJE | /grabarRuta | Duplicado #4 |
| 15 | SERVICE_URL_ACTUALIZAR_MENSAJE | /actualizarRuta | Duplicado #5 |

## 2.4 Otras URLs Hardcoded

| URL | Archivo | Linea | Proposito |
|-----|---------|-------|-----------|
| https://webapp.inei.gob.pe | NetworkUtil.java | 59 | Verificar internet |
| https://webapp.inei.gob.pe:8443/EnahoWeb/dispatcher?controller=usuario&action=welcome | InfoActivity.java | 178 | URL Sistema Integrado |
| https://webapi.inei.gob.pe/enaho/ | InfoActivity.java | 190 | URL Sistema Monitoreo |
| https://www.inei.gob.pe/estadisticas/encuestas/ | InfoActivity.java | 247 | URL Identificar Encuestador |
| extranet.inei.gob.pe (FTP) | EnahoUtil.java | 1219 | FTP APK downloads |

---

# 3. REQUEST

## 3.1 Formato URL-Encoded POST (mayoria)

`
POST /EnahoWeb/webresources/files/ubicacionGpsUsuario HTTP/1.1
Host: webapp.inei.gob.pe:8443
Authorization: Basic QWRtaW4yMDA3ODY6QWRtaW4yMDA3ODY=
Content-Type: application/x-www-form-urlencoded
Accept: application/json

usuario=VALUE&serial=VALUE&estado=VALUE&anho=VALUE&conglomerado=VALUE&...
`

## 3.2 Formato Multipart POST (upload)

`
POST /EnahoWeb/webresources/files/uploadMultiple_v2 HTTP/1.1
Host: webapp.inei.gob.pe:8443
Authorization: Basic QWRtaW4yMDA3ODY6QWRtaW4yMDA3ODY=
Content-Type: multipart/form-data; boundary=Boundary123456789
Accept: application/json

--Boundary123456789
Content-Disposition: form-data; name="modo"
Content-Type: text/plain

ENCUESTADOR
--Boundary123456789
Content-Disposition: form-data; name="file"; filename="backup.zip"
Content-Type: application/octet-stream

[ binary data ]
--Boundary123456789--
`

## 3.3 Formato GET

`
GET /EnahoWeb/webresources/files/consultaHora HTTP/1.1
Host: webapp.inei.gob.pe:8443
Authorization: Basic QWRtaW4yMDA3ODY6QWRtaW4yMDA3ODY=
Accept: application/json
`

## 3.4 Headers Comunes

| Header | Valor |
|--------|-------|
| Authorization | Basic QWRtaW4yMDA3ODY6QWRtaW4yMDA3ODY= |
| Content-Type | pplication/x-www-form-urlencoded o multipart/form-data |
| Accept | pplication/json |

## 3.5 Parametros Comunes

| Parametro | Tipo | Descripcion |
|-----------|------|-------------|
| usuario | String | Codigo usuario |
| modo | String | ENCUESTADOR/DIGITADOR/SUPERVISOR |
| ase | String | Tipo de base |
| nho | String | Anio |
| conglomerado | String | Codigo conglomerado |
| ivienda | String | Codigo vivienda |
| hogar | String | Codigo hogar |
| pin | String | PIN usuario |
| serial | String | ID dispositivo |
| conexion | String | Tipo conexion |
| ateria | String | Nivel bateria |

---

# 4. RESPONSE

## 4.1 Estructura JSON Estándar

`json
{
    "tag": "status",
    "status": true,
    "error_msg": "<payload de datos>",
    "hora": "2026-08-30 12:00:00"
}
`

**NOTA:** El campo error_msg esta mal nombrado - es el **carrier de datos principal**.

## 4.2 Metodos de Parseo

`java
// HttpResponse.java lineas 37-55
getErrorMessage() -> getJson().optString("error_msg")  // datos
getStatus()       -> getJson().optBoolean("status")     // exito/error
getTag()          -> getJson().optString("tag")          // tipo
getHour()         -> getJson().optString("hora")         // hora servidor
`

## 4.3 Tipos de Payload en error_msg

| Tipo | Ejemplo | Uso | Lineas |
|------|---------|-----|--------|
| **String** | "ok" | Confirmacion simple | 1870, 1901 |
| **String (hora)** | "2026-08-30 12:00:00" | Hora servidor | 1277 |
| **JSONObject** | {"key":"value"} | Objeto anidado | 724, 871, 955, 1139, 1955 |
| **JSONArray (string)** | "[{...},{...}]" | Listas (Gson) | 286, 820, 1046, 1245, 1377, 1803, 1841 |

## 4.4 Parseo de Listas (Gson)

`java
// Patron comun en EnahoWebService.java
JSONObject obj = new JSONObject(response.getBody());
String listaJson = obj.getString("error_msg");  // extrae string
Type listType = new TypeToken<List<Archivo>>(){}.getType();
Gson gson = new Gson();
List<Archivo> lista = gson.fromJson(listaJson, listType);  // deserializa
`

## 4.5 Codigos HTTP

| Codigo | Significado | Manejo |
|--------|-------------|--------|
| **200** | Exito | Procesar respuesta |
| **403** | Forbidden | Dialog "No tiene permisos" |
| **404** | Not Found | Toast "Recurso no encontrado" |
| **500** | Server Error | Toast "Error en el Servidor" + check "No space left" |
| **Otros** | Otro error | Dialog "Active datos o verifique signal" |

---

# 5. AUTENTICACION

## 5.1 HTTP Basic Auth (Principal)

`java
// HttpConfig.java lineas 4-5
USER     = "Admin200786"
PASSWORD = "Admin200786"

// EnahoHttpClient.java linea 57-64
String authStr = base64(USER + ":" + PASSWORD);
connection.setRequestProperty("Authorization", "Basic " + authStr);
`

**Header:** Authorization: Basic QWRtaW4yMDA3ODY6QWRtaW4yMDA3ODY=

## 5.2 Credenciales Legacy

`java
// EnahoWebService.java linea 126
cliente.setBasicAuth("Admin200786","Admin200786");
`

## 5.3 Otras Credenciales

| Servicio | Usuario | Clave | Archivo | Linea |
|----------|---------|-------|---------|-------|
| HTTP API | Admin200786 | Admin200786 | HttpConfig.java | 4-5 |
| FTP | ftp-enaho | primavera | EnahoUtil.java | 1221 |
| SIS | SIS | 123 | Configuracion.java | 100-101 |
| Login App | ADMIN | 270314 | LoginActivity.java | 407 |

**RIESGO SEGURIDAD:** Credenciales hardcoded en codigo fuente.

---

# 6. SSL/TLS

## 6.1 MySSLSocketFactory (Legacy - INSEGURO)

`java
// MySSLSocketFactory.java lineas 27-37
TrustManager[] trustAllCerts = new TrustManager[]{
    new X509TrustManager() {
        public void checkClientTrusted(X509Certificate[] chain, String authType) {
            // VACIO - acepta todos
        }
        public void checkServerTrusted(X509Certificate[] chain, String authType) {
            // VACIO - acepta todos
        }
        public X509Certificate[] getAcceptedIssuers() {
            return null;
        }
    }
};
SSLContext sslContext = SSLContext.getInstance("TLS");
sslContext.init(null, trustAllCerts, new java.security.SecureRandom());
`

**RIESGO CRITICO:** Desactiva completamente la verificacion SSL.

## 6.2 EnahoHttpClient (Nuevo - SIN custom TrustManager)

`java
// EnahoHttpClient.java usa HttpsURLConnection directamente
// Sin custom TrustManager - usa SSL del sistema
// Sin HostnameVerifier custom
`

**NOTA:** El cliente nuevo es mas seguro que el legacy, pero ambos apuntan al mismo servidor.

## 6.3 Ubicaciones con SSL Hardcoded (4)

| # | Archivo | Lineas | Patron |
|---|---------|--------|--------|
| 1 | RutasFragment.java | 192-199 | TrustManager vacio |
| 2 | LoginActivity.java | 65-72 | TrustManager vacio |
| 3 | CuestionarioFragmentActivity.java | 845-852 | TrustManager vacio |
| 4 | Conexion.java | 43-50 | TrustManager vacio |

---

# 7. TIMEOUTS

| Componente | Connect | Read | Write | Retry | Archivo |
|------------|---------|------|-------|-------|---------|
| **EnahoHttpClient** | 20,000 ms | 30,000 ms | - | - | EnahoHttpClient.java:39-40 |
| **Legacy AsyncHttpClient** | 20,000 ms | 30,000 ms | - | 2 retries, 5s | EnahoWebService.java:148-152 |
| **NetworkUtil (check)** | 3,000 ms | 3,000 ms | - | - | NetworkUtil.java:63-64 |

**NOTA:** Timeouts para download (invokeDownload1) estan comentados (lineas 332-336) con nota: "if the internet is very slow the request is cancelled, but it does not work for me".

---

# 8. ERRORES

## 8.1 Manejo por Codigo HTTP

`
Respuesta HTTP
  |
  +--> 200 OK
  |       +--> Procesar JSON
  |       +--> Verificar status=true/false
  |       +--> Extraer error_msg (datos)
  |
  +--> 403 Forbidden
  |       +--> Dialog: "No tiene los permisos para realizar esta operacion"
  |
  +--> 404 Not Found
  |       +--> Toast: "Recurso no encontrado"
  |
  +--> 500 Server Error
  |       +--> Toast: "Error en el Servidor"
  |       +--> Check body for "No space left on device"
  |
  +--> Otro
          +--> Dialog: "Active la opcion de Habilitar datos o verifique su signal"
`

## 8.2 Manejo por Excepcion

`
Excepcion de red/timeout
  |
  +--> HttpException (con statusCode)
  |       +--> Verificar statusCode
  |       +--> Manejar como codigo HTTP
  |
  +--> Exception generica
          +--> Dialog: "Se produjo un error. Verifique su conexion a Internet
                        o vuelva a intentarlo en unos minutos."
`

## 8.3 Manejo Offline

`
NetworkUtil.isNetworkAvailable() == false
  |
  +--> GPS: saveLocationUser() -> guardado local
  |     (flush cuando hay red via invokeGpsUploadListPoints)
  |
  +--> Agua: return silencioso (sin guardado local)
  |
  +--> Otros: Dialog "Active la opcion de Habilitar datos"
`

## 8.4 Patron GPS con Synchronized

`java
// EnahoWebService.java lineas 1396-1503
synchronized (GPS_UPLOAD_LOCK) {
    if (gpsUploadEjecutando) return;  // ya esta enviando
    gpsUploadEjecutando = true;
}
// ... HTTP upload en background thread ...
if (response.getStatusCode() == 200) {
    invokeGpsUploadListPoints(usuario);  // flush offline points
} else {
    viviendaService.saveLocationUser(paramsDB);  // guardar local
}
finally {
    synchronized (GPS_UPLOAD_LOCK) { gpsUploadEjecutando = false; }
}
`

## 8.5 Retry Logic

| Cliente | Retries | Delay | Backoff |
|---------|---------|-------|---------|
| Legacy AsyncHttpClient | 2 | 5000 ms | Sin backoff exponencial |
| EnahoHttpClient | 0 | - | Sin retry |
| GPS Upload | Indirecto | Siguiente ciclo 10min | Cola offline |

---

# 9. FLUJO

## 9.1 Flujo General (Request/Response)

`
+-------------------+     +-------------------+     +-------------------+
|     Fragment      |     |  EnahoWebService  |     |  EnahoHttpClient  |
+-------------------+     +-------------------+     +-------------------+
        |                         |                         |
        | 1. service.method()     |                         |
        +------------------------>|                         |
        |                         |                         |
        |                    2. Construir HttpParams         |
        |                    (usuario, modo, base, etc.)    |
        |                         |                         |
        |                    3. EnahoHttpClient.postAsync()  |
        |                         +------------------------>|
        |                         |                         |
        |                    4. Background Thread            |
        |                         |                    postForm() / postMultipart()
        |                         |                         |
        |                    5. HttpsURLConnection           |
        |                         |                    Basic Auth header
        |                         |                    Content-Type header
        |                         |                    Write body
        |                         |                         |
        |                    6. HTTPS POST/GET               |
        |                         |                    [Server: webapp.inei.gob.pe:8443]
        |                         |                         |
        |                    7. Read Response                |
        |                         |                    BufferedReader / readBytes()
        |                         |                         |
        |                    8. HttpResponse(status, body)   |
        |                         |                    [o BinaryHttpResponse]
        |                         |                         |
        |                    9. HttpCallback.onSuccess()     |
        |                         |                    [o onFailure(e)]
        |                         |                         |
        | 10. Parse JSON           |                         |
        |<------------------------+                         |
        |     response.getErrorMessage()                    |
        |     Gson.fromJson()                               |
        |                         |                         |
        | 11. Actualizar UI        |                         |
        |     (runOnUiThread)     |                         |
`

## 9.2 Flujo GPS Offline

`
Timer cada 10 minutos (InfoGPS)
  |
  v
invokeGpsUpload()
  |
  +--> NetworkUtil.isNetworkAvailable()?
        |
        +--> NO: viviendaService.saveLocationUser(paramsDB)
        |     [Guarda localmente]
        |
        +--> SI: EnahoHttpClient.invokeUpload(URL_GPS, params)
              |
              +--> Exito (200):
              |     invokeGpsUploadListPoints(usuario)
              |       +--> viviendaService.getListPointsNoEnviados()
              |       +--> Para cada punto: HTTP POST sync
              |       +--> Si 200: updateLocationUser() [marca enviado]
              |
              +--> Error:
                    viviendaService.saveLocationUser(paramsDB)
                    [Guarda localmente]
`

## 9.3 Flujo Upload Multipart

`
ExportacionFragment
  |
  +--> HttpParams params = new HttpParams()
  |     params.put("file", listOfFiles)  // File[]
  |     params.put("usuario", usuario)
  |     params.put("conexion", conexion)
  |
  +--> enahoWebService.invokeWSUploadFileExportacion(activity, params)
        |
        +--> Agrega: modo, base, pin, json (incentivos)
        |
        +--> EnahoHttpClient.postAsync(url, params, callback)
              |
              +--> params.hasFiles() == true
              |     postMultipart(url, params)
              |       +--> multipart/form-data; boundary=Boundary<timestamp>
              |       +--> writeFileField() para cada File
              |       +--> writeFormField() para cada String
              |       +--> Chunked streaming mode
              |
              +--> Server procesa
              +--> Response JSON: {status, error_msg}
`

## 9.4 Flujo Download Binary

`
invokeDownload()
  |
  +--> EnahoHttpClient.downloadAsync(url, params, callback)
        |
        +--> postBinary(url, params)
        |     +--> POST URL-encoded
        |     +--> Lee response como byte[]
        |     +--> BinaryHttpResponse(status, data)
        |
        +--> Exito:
        |     Escribe byte[] a archivo .zip
        |     Lanza Importacion (AsyncTask)
        |
        +--> Error:
              Dialog segun codigo HTTP
`

---

# 10. PROPUESTA FUTURA (Conceptual)

## 10.1 Arquitectura Objetivo

`
Repository
    |
    +--> RemoteDataSource
            |
            +--> Retrofit (API definitions)
                    |
                    +--> OkHttp (HTTP client)
                            |
                            +--> Interceptors (Auth, Logging, SSL)
                            |
                            +--> Server
`

## 10.2 Componentes Propuestos

| Componente | Tecnologia | Responsabilidad |
|------------|------------|-----------------|
| **Repository** | Interface | Orquestacion data local/remota |
| **RemoteDataSource** | Interface | Abstraccion de red |
| **Retrofit** | Libreria | API definitions con anotaciones |
| **OkHttp** | Libreria | HTTP client con interceptors |
| **Gson/Moshi** | Libreria | Serializacion JSON |
| **CertificatePinner** | OkHttp | SSL pinning (opcional) |

## 10.3 Beneficios

| Actual | Propuesto |
|--------|-----------|
| HttpsURLConnection manual | Retrofit + OkHttp |
| JSON parsing manual | @Body/@Query automatico |
| Basic Auth hardcoded | Auth Interceptor |
| SSL TrustManager vacio | Certificados validados |
| Sin retry policy | RetryInterceptor |
| Sin logging | HttpLoggingInterceptor |
| Timeout manual | Timeout configuracion declarativa |

---

# RESUMEN EJECUTIVO

## Metricas de Red

| Metrica | Valor |
|---------|-------|
| **Clientes HTTP** | 2 (EnahoHttpClient + AsyncHttpClient) |
| **Endpoints activos** | 10 |
| **Endpoints deprecated** | 5 |
| **Metodos HTTP** | 28 (17 legacy + 11 nuevos) |
| **Timeout connect** | 20s |
| **Timeout read** | 30s |
| **Retry policy** | 2 retries (legacy), 0 (nuevo) |
| **Autenticacion** | Basic Auth hardcoded |
| **SSL** | TrustManager vacio (legacy) |
| **Offline support** | GPS only (con synchronized) |

## Hallazgo Principal

**La capa de red tiene dos clientes HTTP paralelos (legacy + nuevo) con:**
1. **Credenciales hardcoded** (Admin200786) - RIESGO SEGURIDAD
2. **SSL desactivado** (MySSLSocketFactory) - RIESGO SEGURIDAD
3. **Campo error_msg mal usado** como carrier de datos - DISEÑO INCORRECTO
4. **Sin retry policy** en cliente nuevo - FRAGILIDAD
5. **Solo GPS tiene offline support** - LIMITACION

**La propuesta Retrofit + OkHttp resolveria problemas de seguridad, diseno, y mantenibilidad.**

---

**Documento generado:** 2026-08-30
**Estado:** EN REVISION
**Siguiente paso:** Aprobacion del usuario para Etapa 6
