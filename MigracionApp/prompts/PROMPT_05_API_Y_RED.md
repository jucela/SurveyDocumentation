# 05 - API Y RED

## OBJETIVO

Documentar completamente la comunicación con servidores.

---

# 1. CLIENTES HTTP

Identificar:

- Retrofit
- OkHttp
- AsyncHttpClient
- SyncHttpClient
- HttpURLConnection
- otros

---

# 2. ENDPOINTS

Para cada endpoint:

| Endpoint | Método | Clase | Método | Request | Response |

---

# 3. REQUEST

Documentar:

- URL
- headers
- parámetros
- body
- formato
- autenticación

---

# 4. RESPONSE

Documentar:

- formato
- estructura
- códigos HTTP
- parseo
- modelos

---

# 5. AUTENTICACIÓN

Identificar:

- Basic Auth
- Token
- JWT
- cookies
- certificados

No mostrar credenciales reales.

---

# 6. SSL/TLS

Analizar:

- certificados
- TrustManager
- HostnameVerifier
- CertificatePinner
- configuraciones inseguras

No asumir una solución.

---

# 7. TIMEOUTS

Identificar:

- connect timeout
- read timeout
- write timeout

---

# 8. ERRORES

Documentar manejo de:

- 400
- 401
- 403
- 404
- 500
- timeout
- no conexión
- respuesta inválida
- excepciones

---

# 9. FLUJO

Construir:

View
 ↓
Service
 ↓
Repository
 ↓
HTTP
 ↓
Server

---

# 10. PROPUESTA FUTURA

Sin implementar:

Repository
 ↓
RemoteDataSource
 ↓
Retrofit
 ↓
OkHttp

---

# RESULTADO

Generar:

docs/05_API_Y_RED.md