# 06 - REGLAS DE NEGOCIO

## REFERENCIA

- docs/01_INVENTARIO_TECNICO.md
- docs/02_ARQUITECTURA_ACTUAL.md
- docs/03_FLUJOS_FUNCIONALES.md
- docs/04_BASE_DATOS.md
- docs/05_API_Y_RED.md
- Verificacion directa contra codigo fuente

---

# REGLA FUNDAMENTAL

Documentar lo que REALMENTE hace el codigo, no lo que "deberia hacer".

---

# 1. VALIDACIONES

## 1.1 Validaciones de Campo

| # | Regla | Error | Archivo | Linea |
|---|-------|-------|---------|-------|
| 1 | Campo obligatorio no vacio | "El campo no puede estar vacio" | FragmentForm | - |
| 2 | GPS activo (ENCUESTADOR) | "Por favor activar el GPS" | EnahoUtil.java | 1637 |
| 3 | GPS activo (_ENCUESTADOR) | "Debe tener activo el GPS" | AbstractVisitaController.java | 1001 |
| 4 | SIM card presente | "Debe tener Chip en el tablet" | AbstractVisitaController.java | 1009 |
| 5 | Plan de datos activo | "Debe tener activo el Plan de Datos" | AbstractVisitaController.java | 1017 |
| 6 | Usuario no vacio | "El usuario no puede estar vacio" | LoginActivity.java | 470 |
| 7 | Clave no vacia | "La clave no puede estar vacia" | LoginActivity.java | 475 |

## 1.2 Validaciones Cruzadas

| # | Regla | Error | Archivo | Linea |
|---|-------|-------|---------|-------|
| 8 | Suma de montos == Total | "La suma de montos (X) debe coincidir con (Y)" | EnahoUtil.java | 295,453,613,757,896,1037,1180 |
| 9 | Total > 0 | "El total debe ser mayor que cero" | EnahoUtil.java | 276,289,433,448,594,609,738,752,877,891,1018,1033,1161,1175 |
| 10 | Hora fin > Hora inicio | "La Hora Termino debe ser por lo menos un minuto despues..." | AbstractVisitaController.java | 394,666 |
| 11 | Hora inicio visita > Hora fin visita anterior | "La hora inicial de la visita actual no puede ser menor o igual..." | AbstractVisitaController.java | 447 |
| 12 | Hora proxima visita > Hora fin ultima entrevista | "La hora de la proxima visita debe ser mayor..." | VisitasEncuestadorController.java | 138 |
| 13 | No cerrar entrevista dia siguiente | "No puede cerrar una entrevista el dia siguiente o posterior" | AbstractVisitaController.java | 713 |

## 1.3 Validaciones de Negocio

| # | Regla | Error | Archivo | Linea |
|---|-------|-------|---------|-------|
| 14 | Ultima visita cerrada antes de iniciar nueva | "Antes presione el boton cerrar, para terminar la ultima visita" | AbstractVisitaController.java | 232 |
| 15 | No iniciar visita si desocupada | "Si la vivienda esta desocupada no puede iniciar otra visita" | AbstractVisitaController.java | 236 |
| 16 | Visita >= 1 para cerrar | "No se puede iniciar visita, vuelva a iniciar sesion" | AbstractVisitaController.java | 253 |
| 17 | Visita existente para cerrar | "Antes debe iniciar una entrevista" | AbstractVisitaController.java | 173 |
| 18 | Lugar requerido para eliminar | "Para eliminar la visita, antes debe indicar el lugar de la entrevista" | AbstractVisitaController.java | 947 |
| 19 | No resultado=6 si incompleta previa | "Si ya declaro una visita como incompleta, ya no puede ingresar el resultado con codigo 6" | AbstractVisitaController.java | 573 |
| 20 | Nucleo familiar obligatorio | "Miembro X: Falta indicar su nucleo familiar" | Cap200Controller.java | 283 |
| 21 | Nucleo 0 para persona sola | "Una persona sola que no forma nucleo con nadie debe tener nucleo 0" | Cap200Controller.java | 294 |
| 22 | Reentrevista editable solo con permisos | Editable solo si reentrevista=="1" AND movil=="1" | EnahoUtil.java | 1556 |

## 1.4 Validaciones de Formato

| # | Regla | Ejemplo | Archivo |
|---|-------|---------|---------|
| 23 | DNI 8 digitos | 12345678 | PersonalService |
| 24 | Conglomerado 6 digitos | 123456 | MarcoDAO |
| 25 | Vivienda 3 digitos | 123 | ViviendaDao |
| 26 | HOGAR 1-2 digitos | 1 | HogarDao |
| 27 | Fecha formato DD/MM/YYYY | 30/08/2026 | Diversos |
| 28 | Hora formato HH:MM | 12:00 | Diversos |

---

# 2. ESTADOS

## 2.1 Estados de Visita (Resultado)

| Codigo | Nombre | Efecto | Archivo |
|--------|--------|--------|---------|
| 1 | COMPLETA | Puede continuar encuesta | ResultadoEntrevistaHogar.java |
| 2 | INCOMPLETA | Puede continuar, es default de auto-close | ResultadoEntrevistaHogar.java |
| 3 | RECHAZO | Bloquea encuesta, muestra motivo (9 opciones) | ResultadoEntrevistaHogar.java |
| 4 | AUSENTE | Bloquea encuesta, subresultado (4 opciones) | ResultadoEntrevistaHogar.java |
| 5 | DESOCUPADA | Bloquea nueva visita, causa reemplazo vivienda | ResultadoEntrevistaHogar.java |
| 6 | SIN_INICIAR | Solo si no hay incompleta previa | ResultadoEntrevistaHogar.java |
| 7 | OTROS | Bloquea encuesta, subresultado (10 opciones) | ResultadoEntrevistaHogar.java |

## 2.2 Transiciones de Estado

`
[No visitado]
    |
    v (iniciar visita)
[Visita Abierta] <--+
    |                |
    +--> (guardar datos)
    |       |
    |       +--> (auto-close 30 min) --> [Visita Cerrada AUTO]
    |       |
    +--> (cerrar manual) --> [Visita Cerrada MANUAL]
    |
    v (resultado)
[COMPLETA] --> puede continuar encuesta
[INCOMPLETA] --> puede continuar encuesta
[RECHAZO] --> bloquea encuesta
[AUSENTE] --> bloquea encuesta
[DESOCUPADA] --> bloquea nueva visita + reemplazo
[SIN_INICIAR] --> solo si no hay incompleta
[OTROS] --> bloquea encuesta
`

## 2.3 Auto-Cierre

| Parametro | Valor | Archivo | Linea |
|-----------|-------|---------|-------|
| MINUTOS_PARA_CIERRE_VISITA | 30 | Configuracion.java | 111 |
| CIERRE_AUTOMATICO flag | "1" cuando se cierra automaticamente | EnahoDao.java | 147 |
| Default resultado auto-cierre | 2 (INCOMPLETA) | EnahoDao.java | 149 |

**Algoritmo de auto-cierre (HogarDao.java:132-170):**
1. Obtiene ultimo FECCRE_REG (timestamp ultimo guardado)
2. Obtiene fecha_entre + horaini_entre (inicio entrevista)
3. Si A_FECFIN_ENTRE existe y es menor que ultimo guardado, usa A_FECFIN_ENTRE
4. Suma MINUTOS_PARA_CIERRE_VISITA (30 min) a ultimo guardado
5. Si tiempo actual > ultimo guardado + 30 min → cierra visita

## 2.4 Calculo de RESFIN (Resultado Final Hogar)

**Algoritmo (HogarDao.java:2133-2177):**
1. Busca resultado 5 (desocupado) o 7 (otro) en visitas activas → si existe, retorna inmediatamente
2. Si no, retorna el **MINIMO** de todos los resultados activos (1-4)
3. COMPLETA(1) < INCOMPLETA(2) < RECHAZO(3) < AUSENTE(4)

**Se actualiza despues de:**
- Cada guardado de visita
- Cada eliminacion de visita
- Auto-cierre

---

# 3. CALCULOS

## 3.1 Consumo Per Capita

**Formula (Cap601ConsumoPercapitaFragment.java:90-126):**
`
consumoPercapita = (cantidad * frecuencia_equivalente * factor_equivalente) / totalPersonas
`

## 3.2 Frecuencia a Diario (12 equivalencias)

| Codigo | Frecuencia | Equivalente | Linea |
|--------|------------|-------------|-------|
| 1 | Directo | 1 | Cap601ConsumoPercapitaFragment.java:220-238 |
| 2 | Cada 2 dias | /2 | " |
| 3 | Semanal | /7 | " |
| 4 | Quincenal | /15 | " |
| 5 | Mensual | /30 | " |
| 6 | Bimestral | /60 | " |
| 7 | Trimestral | /90 | " |
| 8 | Semestral | /180 | " |
| 9 | Anual | *0.29 | " |
| 10 | (Otro) | *0.43 | " |
| 11 | (Otro) | *0.57 | " |
| 12 | (Otro) | /360 | " |

## 3.3 Umbrales Per Capita (50 categorias)

**50 productos con umbrales (Cap601ConsumoPercapitaFragment.java:240-301):**
- Producto 1: > 151.0
- Producto 47: > 945.0
- Cada categoria tiene su umbral especifico

## 3.4 Balance del Hogar

**Categorias de Ingreso (ConsistenciaBalanceHogarFragment.java:367-474):**
- Ingreso dependiente (trabajo por cuenta ajena)
- Ingreso independiente (trabajo por cuenta propia)
- ENAHO-02 (actividad economica)
- ENAHO-04 (gasto hogar)
- Transferencias extraordinarias
- Alquileres de propiedad
- Otros ingresos

**Categorias de Gasto:**
- Alimentos y bebidas
- Vestido
- Vivienda/alquiler/servicios
- Muebles/mantenimiento
- Salud
- Transporte/comunicacion
- Entretenimiento
- Otros

**Saldo:** 	otalIngresos - totalGastos

## 3.5 Balance de Ingresos (Trabajador Independiente)

**Metodos (ConsistenciaBalanceIngresosFragment.java:162-460):**
- Produccion (vi): calculo de produccion neta
- Comercio (ci): calculo de ganancia comercial
- Servicios (si): calculo de ingreso por servicios
- **Logica de allocation** (lineas 326-426): costos compartidos (wtot09, wtot23) se asignan segun sectores activos

## 3.6 P130 Aleatorio

**Parametros (EnahoUtil.java:1640):**
`java
// Seed: year + conglome + vivienda + hogar + "P130"
// Retorna: ubicacion (1-4), periodo (1-4)
`

---

# 4. REGLAS CONDICIONALES

## 4.1 Asignacion de Capitulos por Edad

**Regla (Cap200_BFragment.java:1897-1918):**

| Capitulo | Condicion | Edad Minima |
|----------|-----------|-------------|
| P212 (Cap300 Educacion) | Vive en hogar OR (nuevo AND p206="1") | >= 3 anos |
| P213 (Cap400 Salud) | Vive en hogar OR (nuevo AND p206="1") | >= 0 anos |
| P214 (Cap500 Empleo) | Vive en hogar OR (nuevo AND p206="1") | >= 14 anos |

**Si no vive en hogar (inViveHogar=="2"):** P212=P213=P214="0"

## 4.2 Cuestionario Covid/Reducido

**Regla (EnahoUtil.java:1382-1505):**

| Ano | Mes | Condicion | Resultado |
|-----|-----|-----------|-----------|
| >= 2021 | Cualquiera | covid19=0 | NORMAL |
| >= 2021 | Cualquiera | covid19=1 | REDUCIDO_SET |
| >= 2021 | Cualquiera | covid19=2 | COMPLETO |
| >= 2021 | Cualquiera | covid19=null | COMPLETO (default) |
| 2020 | Mar-Jul | - | REDUCIDO |
| 2020 | Ago | - | REDUCIDO |
| 2020 | Sep | flag covid19 | Segun flag |

## 4.3 Visibilidad por Subsistema

**Regla (Cap100_1Fragment_P101.java:930-948):**

| Subsistema | Preguntas Ocultas |
|------------|-------------------|
| REENTREVISTA | P102A, P103, P103A, P104A |
| REENTREVISTA (ano>=2025, mes>=9) | P104B1, P104B2 |
| COVID_REDUCIDO | P104A |
| COVID_REDUCIDO (ano>=2025, mes>=9) | P104B1, P104B2 |

**Regla (Cap200_BFragment.java:1800-1806):**

| Subsistema | Preguntas Ocultas |
|------------|-------------------|
| REENTREVISTA | P209, P200T, P211A, P211C, P211D |

## 4.4 Relevancia de Preguntas

**Patron comun en fragments:**
`java
if (valor == "1") {
    mostrarCampo();
} else {
    ocultarCampo();
}
`

## 4.5 Condicional en Grabar

**Patron (fragmentos grabarOrThrow):**
`java
// 1. Validar campos obligatorios
// 2. Validar reglas de negocio
// 3. Si error -> Toast/Dialog + return false
// 4. Si OK -> service.method(entity) + return true
`

---

# 5. PROCESOS

## 5.1 Ciclo de Vida de Visita

`
INICIAR
  |-- Pre-condiciones:
  |     GPS activo
  |     Ultima visita cerrada
  |     No desocupada
  |
  +--> Crear registro visita:
        visita = last.visita + 1
        visita_orden = last.orden + 1
        activo = 1
        fecha_entre = hoy (ENCUESTADOR)
        horaini_entre = ahora (ENCUESTADOR)
        |
        v
GUARDAR DATOS
  |-- En.onAntesInsertarRegistro():
  |     Asigna VISITA = num_visita_actual
  |     Verifica auto-close (30 min)
  |     Si auto-close -> cierra automaticamente
  |
  v
CERRAR
  |-- Pre-condiciones:
  |     Visita iniciada (horafin_entre == null)
  |
  +--> Calcular DIAFIN_ENTRE:
        Si hora fin < hora inicio -> diaFin=1
        Si hora fin >= hora inicio -> diaFin=0
        |
        v
RESULTADO
  |-- Seleccionar codigo (1-7)
  |-- Si resultado=3: mostrar MOTIVO RECHAZO (9 opciones)
  |-- Si resultado=4: mostrar SUBRESULTADO AUSENTE
  |-- Si resultado=7: mostrar SUBRESULTADO OTRO (10 opciones)
  |
  +--> Actualizar RESFIN del hogar
`

## 5.2 Proceso de Exportacion

`
VALIDACION PREVIA
  |-- GPS activo
  |-- Sin visitas sin cerrar (resultado, hora, lugar)
  |-- Al menos 1 hogar
  |-- Informante en Cap200 para resultado=1
  |-- Si CONGLOMERADO_PRUEBA: solo prueba="1"
  |
  v
GENERAR XML
  |-- Por vivienda seleccionada:
  |     EnahoVivienda (todas las tablas)
  |     EnahoHogar (todas las tablas)
  |     EnahoHogar01 (CAP_200+ siempre, 600+ segun opcion)
  |     EnahoHogar01A (si opcion="all" o "01A")
  |     EnahoHogar01B (si opcion="all")
  |     EnahoHogar02 (si opcion="all" o "01A")
  |     EnahoHogar04 (si opcion="all" o "01A")
  |
  v
CREAR ZIP
  |-- XML -> ZIP
  |-- Agregar: VERSION_APK, MODO, SUBSISTEMA, USUARIO
  |
  v
SUBIR A SERVIDOR
  |-- EnahoHttpClient.postAsync(multipart)
  |-- Si 2G: advertencia subir 1 archivo a la vez
  |-- Si exito: marcar INCENTIVO_EXPORTADO="1"
  |
  +--> 2 retries, 5s delay
`

## 5.3 Proceso de Importacion

`
SELECCIONAR ARCHIVO
  |-- .zip o .xml
  |-- Si .zip: extraer
  |-- Si personal: password "enahotablet"
  |
  v
VALIDAR FORMATO
  |-- XML parsing
  |-- Verificar estructura
  |
  v
TRANSACCION DB
  |-- startTX()
  |-- Desactivar marco ODEI/MES actual
  |-- Para cada tabla:
  |     saveOrUpdate() (upsert)
  |-- Ejecutar parametros post-import (V_*)
  |-- commitTX()
  |
  +--> Si error: rollback
`

## 5.4 Proceso GPS Offline

`
CADA 10 MINUTOS (InfoGPS Timer)
  |
  v
VERIFICAR RED
  |-- Si OFFLINE: saveLocationUser(paramsDB)
  |     [Guarda localmente]
  |
  +--> Si ONLINE:
        invokeGpsUpload()
          |-- synchronized(GPS_UPLOAD_LOCK)
          |-- Si ya ejecutando: return
          |-- HTTP POST a ubicacionGpsUsuario
          |
          +--> Si 200: invokeGpsUploadListPoints()
          |     |-- Obtiene puntos no enviados
          |     |-- Para cada punto: HTTP POST sync
          |     +--> Si 200: updateLocationUser()
          |
          +--> Si error: saveLocationUser()
`

---

# 6. REGLAS DE PERSISTENCIA

## 6.1 Creacion

| Entidad | Metodo | Condicion | Archivo |
|---------|--------|-----------|---------|
| Visita | iniciar() | Pre-condiciones cumplidas | AbstractVisitaController.java:239-288 |
| Miembro | grabarOrThrow() | Todos los campos obligatorios | Cap200_AFragment.java:1074 |
| Vivienda | aperturarVivienda() | Conglomerado activo | MarcoFragment.java |

## 6.2 Actualizacion

| Entidad | Trigger | Efecto | Archivo |
|---------|---------|--------|---------|
| Visita | guardar datos | Actualiza A_FECFIN_ENTRE | EnahoDao.java:174-184 |
| RESFIN | guardar/eliminar visita | Recalcula resultado final | HogarDao.java:2133-2201 |
| COMPLETO | cerrar visita | Marca COMPLETO="1" | AbstractVisitaController.java |
| INCENTIVO_EXPORTADO | export exitoso | Marca "1" | EnahoWebService.java:1205 |

## 6.3 Eliminacion

| Entidad | Metodo | Efecto | Archivo |
|---------|--------|--------|---------|
| Visita | eliminarVisita() | Soft delete (activo=0) + reordenar | HogarDao.java:2287-2321 |
| Miembro | eliminarMiembro() | Cascada: Cap300,400,500,02,04 | Cap200Controller.java:126-221 |
| Vivienda | eliminarVivienda() | Todas las tablas relacionadas | ViviendaDao.java:278-329 |

## 6.4 Soft Delete

`java
// HogarDao.java:2287-2321
UPDATE tabla SET activo=0 WHERE CONGLOME=? AND VIVIENDA=? AND HOGAR=? AND VISITA=?
-- Luego reordenar:
UPDATE tabla SET VISITA_ORDEN = VISITA_ORDEN - 1
WHERE VISITA_ORDEN > eliminated_order AND activo=1
`

---

# 7. REGLAS DE SINCRONIZACION

## 7.1 Que se Envia

| Dato | Endpoint | Cuando | Condicion |
|------|----------|--------|-----------|
| GPS | ubicacionGpsUsuario | Cada 10 min | Red disponible |
| Datos | uploadMultiple_v2 | Manual | Validacion previa |
| Ruta | grabarRuta/actualizarRuta | Manual | - |
| Agua | aguaService | Manual | Red disponible |

## 7.2 Condiciones de Envio

| Dato | Condicion | Reintentos |
|------|-----------|------------|
| GPS | Red disponible o offline queue | Flush cuando hay red |
| Datos | Export exitoso previo | 2 retries, 5s |
| Ruta | - | Sin retry |
| Agua | Red disponible | Sin retry (fire-and-forget) |

## 7.3 Offline Queue

`
GPS offline:
  saveLocationUser() -> tabla UBICACIONUSUARIO
  Cuando hay red -> getListPointsNoEnviados()
  Para cada punto -> HTTP POST -> updateLocationUser()
`

## 7.4 Incentivo (Una sola vez)

`
Primera export exitosa:
  INCENTIVO_EXPORTADO != "1"
  -> Envia JSON incentivo junto con archivos
  -> Si exito: INCENTIVO_EXPORTADO = "1"
Subsecuentes:
  INCENTIVO_EXPORTADO == "1"
  -> No envia JSON incentivo
`

---

# 8. REGLAS CRITICAS

## 8.1 Top 10 Reglas Criticas

| # | Regla | Impacto si se pierde | Ubicacion |
|---|-------|---------------------|-----------|
| 1 | Auto-close 30 min | Visitas nunca se cierran | Configuracion.java:111, EnahoDao.java:132-171 |
| 2 | Calculo RESFIN (precedencia 5/7) | Resultado final incorrecto | HogarDao.java:2133-2177 |
| 3 | Nucleo familiar obligatorio | Datos inconsistentes | Cap200Controller.java:283 |
| 4 | Asign capitulos por edad | Capitulos incorrectos | Cap200_BFragment.java:1897-1918 |
| 5 | Bloqueo por rechazo/ausente | Encuesta incompleta | AbstractVisitaController.java:561-577 |
| 6 | Cascada eliminar miembro | Datos huerfanos | Cap200Controller.java:126-221 |
| 7 | GPS offline fallback | Perdida ubicaciones | EnahoWebService.java:1474-1498 |
| 8 | Export validacion previa | Datos incompletos al servidor | ExportacionFragment.java:335-451 |
| 9 | Import transaccional | BD corrupta | Importacion.java:161-333 |
| 10 | Credenciales hardcoded | Seguridad comprometida | HttpConfig.java:4-5 |

---

# 9. TRAZABILIDAD

## 9.1 Tabla de Trazabilidad

| Regla | Clase | Metodo | Datos | Resultado |
|-------|-------|--------|-------|-----------|
| Auto-close | EnahoDao | onAntesInsertarRegistro | Visita, timestamp | Visita cerrada auto |
| RESFIN | HogarDao | getResultadoFinalHogarCalculado | Visitas activas | Resultado final |
| Nucleo obligatorio | Cap200Controller | validarAntesIrCap200B | Miembros | Error o OK |
| Capitulos por edad | Cap200_BFragment | actualizar212_213_214 | Edad, viveHogar | P212/P213/P214 |
| Bloqueo rechazo | AbstractVisitaController | procesarResultado | Resultado actual | Bloqueo o permitido |
| Cascada miembro | Cap200Controller | eliminarMiembro | CODPERSO | Eliminacion en cascada |
| GPS offline | EnahoWebService | invokeGpsUpload | Red, ubicacion | Guardado local o server |
| Export validacion | ExportacionFragment | validarAntesExportar | Visitas, hogares | Bloqueo o permitido |
| Import transaccion | Importacion | doInBackground | XML, DB | Commit o rollback |
| Suma==Total | EnahoUtil | validarCap600/605/611 | Montos individuales | Error o OK |

---

# RESUMEN EJECUTIVO

## Metricas de Reglas de Negocio

| Metrica | Valor |
|---------|-------|
| **Validaciones documentadas** | 28 |
| **Estados de visita** | 7 |
| **Transiciones de estado** | 10+ |
| **Calculos** | 6 categorias |
| **Reglas condicionales** | 15+ |
| **Procesos documentados** | 4 |
| **Reglas de persistencia** | 12 |
| **Reglas de sincronizacion** | 8 |
| **Reglas criticas** | 10 |
| **Puntos de trazabilidad** | 10 |

## Hallazgo Principal

**Las reglas de negocio mas criticas son:**
1. **Auto-close de 30 minutos** - afecta integridad de datos de visita
2. **Calculo de RESFIN** - precedencia 5/7 sobre 1-4
3. **Asignacion de capitulos por edad** - determina que se encuesta
4. **Bloqueo por rechazo/ausente** - afecta flujo de encuesta
5. **Cascada al eliminar miembro** - afecta 5+ capitulos

**Estas 10 reglas criticas deben conservarse EXACTAMENTE en la migracion.**

---

**Documento generado:** 2026-08-30
**Estado:** EN REVISION
**Siguiente paso:** Aprobacion del usuario para Etapa 7
