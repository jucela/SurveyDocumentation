# 04 - BASE DE DATOS

## REFERENCIA

- docs/01_INVENTARIO_TECNICO.md
- docs/02_ARQUITECTURA_ACTUAL.md
- docs/03_FLUJOS_FUNCIONALES.md
- Verificacion directa contra codigo fuente

---

# 1. TECNOLOGIA

| Componente | Tecnologia | Version |
|------------|------------|---------|
| **Motor** | SQLite (Android native) | - |
| **Helper** | MyDatabaseHelper extends SQLiteOpenHelper | v56 |
| **ORM** | SQLiteDAO (reflexion) | ~1600 lineas |
| **Entity** | Entity (base abstracta) | ~400 lineas |
| **Esquema** | 30+ archivos XML de creacion | - |
| **Upgrades** | upgrade02.xml - upgrade93.xml | 54 migraciones |

**Nombre BD:** enaho_produccion.db
**Variantes:** _supervision.db, _reentrevista.db

---

# 2. TABLAS

## 2.1 Tablas Geograficas/Referencia (7 tablas)

| # | Tabla | PK | FK | Proposito |
|---|-------|----|----|-----------|
| 1 | DPTO | (CCDD) | - | Departamentos |
| 2 | PROV | (CCDD, CCPP) | DPTO(CCDD) | Provincias |
| 3 | DISTRITO | (CCDD, CCPP, CCDI) | PROV(CCDD,CCPP) | Distritos |
| 4 | V_UBIGEO | VIEW | - | Vista unificada de ubigeo |
| 5 | ODEI | ID PK; UNIQUE(CODODEI) | DPTO(CCDD) | Oficinas descentralizadas |
| 6 | PERIODO | (CODPER) | - | Periodos de encuesta |
| 7 | PAIS | (CODPAIS) | - | Paises |

## 2.2 Tablas Marco (2 tablas)

| # | Tabla | PK | FK | Proposito |
|---|-------|----|----|-----------|
| 8 | CAMPO_CONGLOMERADO | ID PK; UNIQUE(CONGLOME) | PERIODO(CODPER), ODEI(CODODEI) | Conglomerados muestrales |
| 9 | CAMPO_VIVIENDA_MARCO | ID PK; UNIQUE(CONGLOME,VIVIENDA) | CAMPO_CONGLOMERADO(CONGLOME) | Viviendas del marco |

## 2.3 Tablas Nucleo (5 tablas)

| # | Tabla | PK | FK | Proposito |
|---|-------|----|----|-----------|
| 10 | CAMPO_VIVIENDA | (CONGLOME, VIVIENDA) | CAMPO_CONGLOMERADO(CONGLOME) | Viviendas activas |
| 11 | CAMPO_VIVIENDA_REEMPLAZO | (CONGLOME, VIVIENDA) | CAMPO_CONGLOMERADO(CONGLOME) | Viviendas reemplazadas |
| 12 | CAMPO_HOGAR | (CONGLOME, VIVIENDA, HOGAR) | CAMPO_VIVIENDA(CONGLOME,VIVIENDA) | Hogares |
| 13 | CAMPO_VISITA_ENCUESTADOR | (CONGLOME,VIVIENDA,HOGAR,VISITA) | CAMPO_HOGAR(CONGLOME,VIVIENDA,HOGAR) ON DELETE CASCADE | Visitas encuestador |
| 14 | CAMPO_VISITA_SUPERVISOR | (CONGLOME,VIVIENDA,HOGAR,VISITA) | CAMPO_HOGAR(CONGLOME,VIVIENDA,HOGAR) ON DELETE CASCADE | Visitas supervisor |

## 2.4 Tablas ENAHO01 - Vivienda/Hogar (30+ tablas)

| # | Tabla | PK | FK |
|---|-------|----|----|
| 15 | CAMPOENA01_FECHAVISITA | (CONGLOME,VIVIENDA,HOGAR) | CAMPO_HOGAR |
| 16 | CAMPOENA01_OBSERVACIONES | (CONGLOME,VIVIENDA,HOGAR,MODULO,OCURRENCIA) | CAMPO_HOGAR |
| 17 | CAMPOENA01_OTROS_HOGAR | (CONGLOME,VIVIENDA,HOGAR,PREGUNTA,OCURRENCIA) | CAMPO_HOGAR |
| 18 | CAMPOENA01_CAP_100 | (CONGLOME,VIVIENDA,HOGAR) | CAMPO_HOGAR |
| 19 | CAMPOENA01_CAP_100A | (CONGLOME,VIVIENDA,HOGAR,P117) | CAP_100 |
| 20 | CAMPOENA01_CAP_100B | (CONGLOME,VIVIENDA,HOGAR) | CAP_100 |
| 21 | CAMPOENA01_CAP_118 | (CONGLOME,VIVIENDA,HOGAR) | CAMPO_HOGAR |
| 22 | CAMPOENA01_CAP_130 | (CONGLOME,VIVIENDA,HOGAR) | CAMPO_HOGAR |
| 23 | CAMPOENA01_CAP_200 | (CONGLOME,VIVIENDA,HOGAR,P201) | CAMPO_HOGAR |
| 24 | CAMPO_TELEFONOS | (CONGLOME,VIVIENDA,HOGAR,P201,ORDEN) | CAP_200 |
| 25 | CAMPOENA01_CAP_200_ADICIONAL | (CONGLOME,VIVIENDA,HOGAR,P201) | CAP_200 |
| 26 | CAMPOENA01_OTROS_PRODUCTO | (CONGLOME,VIVIENDA,HOGAR,P601A) | CAP_601 |
| 27 | CAMPOENA01_CAP_600 | (CONGLOME,VIVIENDA,HOGAR) | CAMPO_HOGAR |
| 28 | CAMPOENA01_CAP_601 | (CONGLOME,VIVIENDA,HOGAR,P601A) | CAMPO_HOGAR |
| 29 | CAMPOENA01_CAP_602 | (CONGLOME,VIVIENDA,HOGAR,P602N) | CAMPO_HOGAR |
| 30 | CAMPOENA01_CAP_602A | (CONGLOME,VIVIENDA,HOGAR,P602AN) | CAMPO_HOGAR |
| 31 | CAMPOENA01_CAP_603 | (CONGLOME,VIVIENDA,HOGAR,P603N,P603N1) | CAMPO_HOGAR |
| 32 | CAMPOENA01_CAP_604 | (CONGLOME,VIVIENDA,HOGAR,P604N,P604N1) | CAMPO_HOGAR |
| 33 | CAMPOENA01_CAP_605 | (CONGLOME,VIVIENDA,HOGAR,P605N,P605N1) | CAMPO_HOGAR |
| 34 | CAMPOENA01_CAP_606 | (CONGLOME,VIVIENDA,HOGAR,P606N,P606N1) | CAMPO_HOGAR |
| 35 | CAMPOENA01_CAP_606D | (CONGLOME,VIVIENDA,HOGAR,P606DN,P606DN1) | CAMPO_HOGAR |
| 36 | CAMPOENA01_CAP_607 | (CONGLOME,VIVIENDA,HOGAR,P607N,P607N1) | CAMPO_HOGAR |
| 37 | CAMPOENA01_CAP_609 | (CONGLOME,VIVIENDA,HOGAR,P609N,P609N1) | CAMPO_HOGAR |
| 38 | CAMPOENA01_CAP_610 | (CONGLOME,VIVIENDA,HOGAR,P610N,P610N1) | CAMPO_HOGAR |
| 39 | CAMPOENA01_CAP_611 | (CONGLOME,VIVIENDA,HOGAR,P611N,P611N1) | CAMPO_HOGAR |
| 40 | CAMPOENA01_CAP_612 | (CONGLOME,VIVIENDA,HOGAR,P612N,P612N1) | CAMPO_HOGAR |
| 41 | CAMPOENA01_CAP_612A | (CONGLOME,VIVIENDA,HOGAR) | CAMPO_HOGAR |
| 42 | CAMPOENA01_CAP_613 | (CONGLOME,VIVIENDA,HOGAR) | CAMPO_HOGAR |
| 43 | CAMPOENA01_CAP_613H | (CONGLOME,VIVIENDA,HOGAR) | CAMPO_HOGAR |
| 44 | CAMPOENA01_CAP_700A | (CONGLOME,VIVIENDA,HOGAR) | CAMPO_HOGAR |
| 45 | CAMPOENA01_CAP_700B | (CONGLOME,VIVIENDA,HOGAR,P702,P703) | CAP_700A |
| 46 | CAMPOENA01_CAP_700C | (CONGLOME,VIVIENDA,HOGAR) | CAMPO_HOGAR |
| 47 | CAMPOENA01_CAP_700D | (CONGLOME,VIVIENDA,HOGAR,P711,P712) | CAP_700C |
| 48 | CAMPOENA01_CAP_800A | (CONGLOME,VIVIENDA,HOGAR) | CAMPO_HOGAR |
| 49 | CAMPOENA01_CAP_800B | (CONGLOME,VIVIENDA,HOGAR,P802,P803) | CAP_800A |
| 50 | CAMPOENA01_CAP_900 | (CONGLOME,VIVIENDA,HOGAR) | CAMPO_HOGAR |
| 51 | CAMPOENA01_CAP_900A | (CONGLOME,VIVIENDA,HOGAR) | CAP_900 |

## 2.5 Tablas ENAHO01A - Individual (15+ tablas)

| # | Tabla | PK | FK |
|---|-------|----|----|
| 52 | CAMPOENA01A_OBSERVACIONES_PERSONA | (CONGLOME,VIVIENDA,HOGAR,PERSONA,MODULO,OCURRENCIA) | CAMPO_HOGAR |
| 53 | CAMPOENA01A_OTROS_PERSONA | (CONGLOME,VIVIENDA,HOGAR,CODPERSO,PREGUNTA,OCURRENCIA) | CAMPO_HOGAR |
| 54 | CAMPOENA01A_CAP_300 | (CONGLOME,VIVIENDA,HOGAR,CODPERSO) | CAMPO_HOGAR |
| 55 | CAMPOENA01A_CAP_300A | (CONGLOME,VIVIENDA,HOGAR,CODPERSO,P311N,P311N1) | CAP_300 |
| 56 | CAMPOENA01A_CAP_300_A | (CONGLOME,VIVIENDA,HOGAR,CODPERSO) | CAP_300 |
| 57 | CAMPOENA01A_CAP_400 | (CONGLOME,VIVIENDA,HOGAR,CODPERSO) | CAP_200 |
| 58 | CAMPOENA01A_CAP_400A | (CONGLOME,VIVIENDA,HOGAR,CODPERSO,P414N,P414N1) | CAP_400 |
| 59 | CAMPOENA01A_CAP_500 | (CONGLOME,VIVIENDA,HOGAR,CODPERSO) | CAP_200 |
| 60 | CAMPOENA01A_CAP_500A | (CONGLOME,VIVIENDA,HOGAR,CODPERSO) | CAP_500 |
| 61 | CAMPOENA01A_CAP_500B | (CONGLOME,VIVIENDA,HOGAR,CODPERSO) | CAP_500 |
| 62 | CAMPOENA01A_CAP_500C | (CONGLOME,VIVIENDA,HOGAR,CODPERSO,P559N) | CAP_500 |
| 63 | CAMPOENA01A_CAP_500D | (CONGLOME,VIVIENDA,HOGAR,CODPERSO) | CAP_500 |
| 64 | CAMPOENA01A_CAP_500E | (CONGLOME,VIVIENDA,HOGAR,CODPERSO,P560N) | CAP_500 |
| 65 | CAMPOENA01A_CAP_900 | (CONGLOME,VIVIENDA,HOGAR,CODPERSO) | CAP_200 |
| 66 | CAMPOENA01A_CAP_900A | (CONGLOME,VIVIENDA,HOGAR,CODPERSO) | CAP_900 |

## 2.6 Tablas ENAHO01B - Educacion (7 tablas)

| # | Tabla | PK | FK |
|---|-------|----|----|
| 67 | CAMPOENA01B_A | (CONGLOME,VIVIENDA,HOGAR) | CAMPO_HOGAR |
| 68 | CAMPOENA01B_A_221 | (CONGLOME,VIVIENDA,HOGAR,P221N) | B_A |
| 69 | CAMPOENA01B_B | (CONGLOME,VIVIENDA,HOGAR) | B_A |
| 70 | CAMPOENA01B_C | (CONGLOME,VIVIENDA,HOGAR) | B_A |
| 71 | CAMPOENA01B_PERSONA | (CONGLOME,VIVIENDA,HOGAR,CODPERSO) | CAP_200 |
| 72 | CAMPOENA01B_OBSERVACIONES | (CONGLOME,VIVIENDA,HOGAR,MODULO,OCURRENCIA) | B_A |
| 73 | CAMPOENA01B_OTROS | (CONGLOME,VIVIENDA,HOGAR,PREGUNTA,OCURRENCIA) | B_A |

## 2.7 Tablas ENAHO02 - Actividad Economica (18+ tablas)

| # | Tabla | PK | FK |
|---|-------|----|----|
| 74 | CAMPOENA02_CAP_2000 | (CONGLOME,VIVIENDA,HOGAR,CODPERSO) | CAP_500 |
| 75 | CAMPOENA02_CAP_2000A | (CONGLOME,VIVIENDA,HOGAR,CODPERSO,P2005A) | CAP_2000 |
| 76 | CAMPOENA02_CAP_2100 | (CONGLOME,VIVIENDA,HOGAR,CODPERSO,P2100A) | CAP_2000 |
| 77 | CAMPOENA02_CAP_2200 | (CONGLOME,VIVIENDA,HOGAR,CODPERSO,P2200A) | CAP_2000 |
| 78 | CAMPOENA02_CAP_2300 | (CONGLOME,VIVIENDA,HOGAR,CODPERSO,P2300A) | CAP_2000 |
| 79 | CAMPOENA02_CAP_2400 | (CONGLOME,VIVIENDA,HOGAR,CODPERSO) | CAP_2000 |
| 80 | CAMPOENA02_CAP_2500 | (CONGLOME,VIVIENDA,HOGAR,CODPERSO,P2500A) | CAP_2000 |
| 81 | CAMPOENA02_CAP_2600 | (CONGLOME,VIVIENDA,HOGAR,CODPERSO,P2600A) | CAP_2000 |
| 82 | CAMPOENA02_CAP_2700 | (CONGLOME,VIVIENDA,HOGAR,CODPERSO) | CAP_2000 |
| 83 | CAMPOENA02_PRODSUBPRO | (CONGLOME,VIVIENDA,HOGAR,CODPERSO,CAPITULO,ORDEN,PRODUCTO,SUBPRODUCTO) | CAP_2000 |
| 84 | CAMPOENA02_OTROS | (CONGLOME,VIVIENDA,HOGAR,CODPERSO,PREGUNTA,OCURRENCIA) | CAP_2000 |
| 85 | CAMPOENA02_OBSERVACION | (CONGLOME,VIVIENDA,HOGAR,CODPERSO,PREGUNTA,OCURRENCIA) | CAP_2000 |
| 86 | CAMPOENA02_FECHAVISITA | (CONGLOME,VIVIENDA,HOGAR,CODPERSO) | CAP_500 |
| 87 | CAMPOENA02_DETALLECALCULO | (CONGLOME,VIVIENDA,HOGAR,CODPERSO,CAPITULO,CODPROD,PREGUNTA,CODOP) | CAP_2000 |
| 88 | CAMPOENA02_PRODUCTO | (ver enaho_campo_ena02_producto.xml) | CAP_2000 |
| 89 | PRODUCTO_E02_601 | (ver enaho_campo_ena02_producto.xml) | CAP_2000 |
| 90 | CAMPOENA02_REPORTE | (CONGLOME,VIVIENDA,HOGAR,CODPERSO) | CAP_500 |

## 2.8 Tablas ENAHO04 - Gasto Hogar (8 tablas)

| # | Tabla | PK | FK |
|---|-------|----|----|
| 91 | CAMPOENA04_CAP_1 | (CONGLOME,VIVIENDA,HOGAR,CODPERSO,ACTIVID) | CAP_500 |
| 92 | CAMPOENA04_CAP_2 | (CONGLOME,VIVIENDA,HOGAR,CODPERSO,ACTIVID,MODULO,NORDEN) | CAP_1 |
| 93 | CAMPOENA04_CAP_3 | (CONGLOME,VIVIENDA,HOGAR,CODPERSO,ACTIVID,E23N,E23N1) | CAP_1 |
| 94 | CAMPOENA04_CAP_4 | (CONGLOME,VIVIENDA,HOGAR,CODPERSO,ACTIVID,E24A) | CAP_1 |
| 95 | CAMPOENA04_OTROS | (CONGLOME,VIVIENDA,HOGAR,CODPERSO,ACTIVID,PREGUNTA,OCURRENCIA) | CAP_1 |
| 96 | CAMPOENA04_OBSERVACION | (CONGLOME,VIVIENDA,HOGAR,CODPERSO,ACTIVID,PREGUNTA,OCURRENCIA) | CAP_1 |
| 97 | CAMPOENA04_FECHAVISITA | (CONGLOME,VIVIENDA,HOGAR,CODPERSO,ACTIVID) | CAP_500 |
| 98 | CAMPOENA04_DETALLECALCULO | (CONGLOME,VIVIENDA,HOGAR,CODPERSO,ACTIVID,MODULO,NORDEN,CODOP) | CAP_2 |

## 2.9 Tablas Panel (2 tablas)

| # | Tabla | PK | FK |
|---|-------|----|----|
| 99 | PERSONA_PANEL | (CONGLOME,VIVIENDA,CODPANEL) | CAMPO_VIVIENDA_MARCO(CONGLOME,VIVIENDA) |
| 100 | CAP100PANEL | (ANHO,MES,CONGLOME,VIVIENDA,HOGAR) | CAMPO_HOGAR |

## 2.10 Tablas Supervision (5 tablas)

| # | Tabla | PK | FK |
|---|-------|----|----|
| 101 | SUPERVISION_ITEM_HOGAR | (CONGLOME,VIVIENDA,HOGAR,ITEM) | CAMPO_HOGAR, DICCIONARIO_ITEM |
| 102 | SUPERVISION_CAP_200 | (CONGLOME,VIVIENDA,HOGAR,P201) | CAMPO_HOGAR |
| 103 | SUPERVISION_ITEM_CAP200 | (CONGLOME,VIVIENDA,HOGAR,P201,ITEM) | SUPERVISION_CAP_200, DICCIONARIO_ITEM |
| 104 | SUPERVISION_HOGAR_PRODUCTO | (CONGLOME,VIVIENDA,HOGAR,CATEGORIA,CODIGO) | CAMPO_HOGAR, PRODUCTO |
| 105 | SUPERVISION_HOGAR_ANEXO601 | (CONGLOME,VIVIENDA,HOGAR,P601A) | CAP_601 |

## 2.11 Tablas Migracion (3 tablas)

| # | Tabla | PK | FK |
|---|-------|----|----|
| 106 | CAMPO_MIGRACION | (CONGLOME,VIVIENDA,HOGAR) | CAMPO_HOGAR |
| 107 | CAMPO_MIGRACION200B | (CONGLOME,VIVIENDA,HOGAR,CODIGO) | CAMPO_MIGRACION |
| 108 | CAMPO_MIGRACION200C | (CONGLOME,VIVIENDA,HOGAR,CODIGO) | CAMPO_MIGRACION |

## 2.12 Tablas Seguridad/Personal (6 tablas)

| # | Tabla | PK | FK |
|---|-------|----|----|
| 109 | PERFIL | (CODPERFIL) | - |
| 110 | OPCION | (CODOPCION) | - |
| 111 | PERFIL_OPCION | (CODPERFIL,CODOPCION) | PERFIL, OPCION |
| 112 | CARGO | (CARGO) | - |
| 113 | ESTADOCIVIL | (CODESTADO) | - |
| 114 | PERSONAL | (DNI) | PERFIL, CARGO, ESTADOCIVIL |

## 2.13 Tablas Configuracion/Sistema (3 tablas)

| # | Tabla | PK | FK |
|---|-------|----|----|
| 115 | CONFIGURACION | (CODIGO) | - |
| 116 | MENSAJES | (ID) | - |
| 117 | INCENTIVO | (CONGLOME,VIVIENDA) | CAMPO_VIVIENDA |

## 2.14 Tablas Catalogo/Referencia (12 tablas)

| # | Tabla | PK | FK |
|---|-------|----|----|
| 118 | CATEGORIA_PRODUCTO | (CODIGO) | - |
| 119 | PRODUCTO | (CATEGORIA,CODIGO) | CATEGORIA_PRODUCTO |
| 120 | ANEXO | (PREGUNTA,GRUPO,ORDEN) | PRODUCTO |
| 121 | GRUPO_601 | (ID) | - |
| 122 | UNIDAD_MEDIDA | (PRODUCTO,UNIDAD_ORDEN) | CATEGORIA_PRODUCTO |
| 123 | PRECIO_EXTREMO | (ODEI,PRODUCTO) | ODEI, PRODUCTO |
| 124 | CARRERASUPERIOR | (CODCARRERA) | - |
| 125 | NIVELSUPERIOR | (CODI,TIPO) | - |
| 126 | CARRERAMEDIOBASICO | (CODCARRERA) | - |
| 127 | NIVELMEDIOBASICO | (CODI) | - |
| 128 | ACTIVIDAD_ECONOMICA | (CODIGO) | - |
| 129 | DICCIONARIO_ACTIVIDAD | (CODIGO) | ACTIVIDAD_ECONOMICA |

## 2.15 Tablas Diccionario/Metadatos (5 tablas)

| # | Tabla | PK | FK |
|---|-------|----|----|
| 130 | DICCIONARIO_SECCION | (ID) | DICCIONARIO_SECCION(PADRE) |
| 131 | DICCIONARIO_ITEM | (ID) | DICCIONARIO_SECCION |
| 132 | DICCIONARIO_DATOS_TABLAS_TIPO | (COD_TIPO) | - |
| 133 | DICCIONARIO_DATOS_TABLAS | (NOMBRE) | DICCIONARIO_DATOS_TABLAS_TIPO |
| 134 | DICCIONARIO_DATOS_VARIABLES_SPSS | (VARIABLE) | DICCIONARIO_DATOS_TABLAS |

## 2.16 Tablas Log (3 tablas)

| # | Tabla | PK | FK |
|---|-------|----|----|
| 135 | CAMPO_LOG_INTERCAMBIO_VIVIENDAS | (ID) | - |
| 136 | CAMPO_LOG_HOGAR_DIGITADOR | (CONGLOME,VIVIENDA,HOGAR,TABLA,USUCRE,FECCRE) | CAMPO_HOGAR |
| 137 | CAMPO_LOG_HOGAR_CAMBIOS | (CONGLOME,VIVIENDA,HOGAR,TABLA,USUCRE,FECCRE) | CAMPO_HOGAR |

**TOTAL: 137 tablas documentadas**

---

# 3. COLUMNAS

## 3.1 Patron de Teclas Compuestas Jerarquicas

`
CONGLOME (6 digits)          -> Nivel conglomerado
+ VIVIENDA (3 digits)        -> Nivel vivienda
+ HOGAR (1-2 digits)         -> Nivel hogar
+ VISITA (2 digits)          -> Nivel visita
+ P201/CODPERSO (2 digits)   -> Nivel persona
+ PREGUNTA/OCURRENCIA        -> Nivel respuesta
+ MODULO/ORDEN               -> Nivel detalle
`

## 3.2 Columnas de Auditoria (presentes en casi todas las tablas)

| Columna | Tipo | Nullable | Descripcion |
|---------|------|----------|-------------|
| USUCRE | VARCHAR(35) | NO | Usuario creador |
| FECCRE | VARCHAR(35) | NO | Fecha creacion |
| USUREG | VARCHAR(35) | SI | Usuario registro |
| FECREG | VARCHAR(35) | SI | Fecha registro |
| FECENV | VARCHAR(35) | SI | Fecha envio |

## 3.3 Columnas de Campo (tablas de encuesta)

| Columna | Tipo | Nullable | Descripcion |
|---------|------|----------|-------------|
| USUARIODIG | VARCHAR(20) | SI | Usuario digitador |
| FECHADIG | DATETIME | SI | Fecha digitacion |
| USUARIOMOD | VARCHAR(20) | SI | Usuario modificador |
| FECHAMOD | DATETIME | SI | Fecha modificacion |
| MOVIL | VARCHAR(30) | SI | ID dispositivo movil |
| HORAINIDIG | DATETIME | SI | Hora inicio digitacion |
| HORAFINDIG | DATETIME | SI | Hora fin digitacion |
| COMPLETO | CHAR(1) | SI | Flag completado |
| VISITA | NUMERIC(2,0) | SI | Numero de visita |

## 3.4 Ejemplo: Columnas de CAMPO_VIVIENDA

| Columna | Tipo | PK | FK | Descripcion |
|---------|------|----|----|-------------|
| CONGLOME | VARCHAR(6) | PK | FK | Codigo conglomerado |
| VIVIENDA | VARCHAR(3) | PK | FK | Codigo vivienda |
| ACTIVO | CHAR(1) | - | - | Vivienda activa |
| RESULTADO | VARCHAR(2) | - | - | Resultado visita |
| DIRECCION | VARCHAR(200) | - | - | Direccion |
| PUERTA | VARCHAR(20) | - | - | Numero puerta |
| INTERIOR | VARCHAR(10) | - | - | Interior |
| PISO | VARCHAR(5) | - | - | Piso |
| MANZANA | VARCHAR(10) | - | - | Manzana |
| LOTE | VARCHAR(10) | - | - | Lote |
| USUCRE | VARCHAR(35) | - | - | Usuario creacion |
| FECCRE | VARCHAR(35) | - | - | Fecha creacion |

---

# 4. OPERACIONES

## 4.1 SELECT (Consultas)

### Multi-JOINs Complejas (40+ consultas)

| Metodo | Archivo | Lineas | Tablas JOIN | Complejidad |
|--------|---------|--------|-------------|-------------|
| ingresos02_10() | MarcoDAO.java | 160-260 | 8+ subqueries correlacionados | ALTA |
| indReporteHome() | HogarDao.java | 310-356 | 5 tablas + subquery | ALTA |
| indIngresosHogar() | MarcoDAO.java | 448-491 | 4+ INNER JOINs repetidos | ALTA |
| indConsumoPerCapita() | MarcoDAO.java | 1517-1523 | 3 tablas + LEFT JOIN | MEDIA |
| indPreciosExtremos() | MarcoDAO.java | 1685-1691 | 5 tablas JOIN | ALTA |
| indBalanceHogar() | MarcoDAO.java | 1742-1746 | 3 subqueries + GROUP BY | ALTA |
| indIngresosTotales() | MarcoDAO.java | 2065-2095 | 4 tablas JOIN x4 | ALTA |
| indIngresosDetalle() | MarcoDAO.java | 2669 | SUM de 7 subqueries | ALTA |
| indAllViviendas() | ViviendaDao.java | 456-467 | UNION ALL + LEFT JOIN | MEDIA |
| indCap601Producto() | Enaho01Dao.java | - | 3 tablas + LEFT JOIN | MEDIA |
| indDiccionarioBySeccion() | DiccionarioDao.java | 94-120 | 3 tablas anidadas | MEDIA |

### Queries Simples (100+ consultas)

| Metodo | Archivo | Tabla | Tipo |
|--------|---------|-------|------|
| indById() | SQLiteDAO | Cualquier Entity | SELECT * WHERE id=? |
| indAll() | SQLiteDAO | Cualquier Entity | SELECT * |
| indByUsuarioClave() | PersonalService | PERSONAL | WHERE usuario=? AND clave=? |
| indConfiguracion() | ConfiguracionDao | CONFIGURACION | WHERE codigo=? |
| indDiccionarioSeccion() | DiccionarioDao | DICCIONARIO_SECCION | WHERE seccion_padre=? |

## 4.2 INSERT (Insercion)

| Metodo | Archivo | Tabla | Metodo |
|--------|---------|-------|--------|
| insert() | SQLiteDAO | Cualquier | entity.getContentValues() -> db.insert() |
| saveOrUpdateConVisita() | EnahoDao | CAP_xxx | Verifica visita + asigna VISITA |
| saveOrUpdate() | SQLiteWithoutIdDao | Cualquier | insert or replace |
| importarDatos() | Importacion | Multiples | XMLReader -> DAO.insert() en loop |

## 4.3 UPDATE (Actualizacion)

| Metodo | Archivo | Tabla | Descripcion |
|--------|---------|-------|-------------|
| ctualizar() | SQLiteDAO | Cualquier | entity.getContentValues() -> db.update() |
| ctualizarResultado() | HogarDao | CAMPO_HOGAR | Actualiza resultado final |
| ctualizarActivo() | MarcoDAO | CAMPO_VIVIENDA_MARCO | Set activo=0/1 |
| cerrarVisita() | EnahoDao | CAMPO_VISITA_ENCUESTADOR | Cierra visita con timestamp |
| 	rasladoViviendasCampo() | ViviendaDao | Multiples | ~20 UPDATEs secuenciales |

## 4.4 DELETE (Eliminacion)

| Metodo | Archivo | Tabla | Descripcion |
|--------|---------|-------|-------------|
| eliminarMarco() | MarcoDAO | 3 tablas | 3 DELETEs secuenciales |
| eliminarHogar() | HogarDao | 6+ tablas | DELETEs secuenciales |
| eliminarEnaho02() | Enaho02Dao | 20+ tablas | 20+ DELETEs secuenciales |
| eliminarEnaho04() | Enaho04Dao | 5 tablas | 5 DELETEs secuenciales |
| deleteAll() | SQLiteDAO | Cualquier | DELETE FROM tabla |

## 4.5 JOINs

`
CAMPO_CONGLOMERADO (1) <-- CAMPO_VIVIENDA_MARCO (N)
    |
    +-- CAMPO_VIVIENDA (N)
            |
            +-- CAMPO_HOGAR (N)
                    |
                    +-- CAMPO_VISITA_ENCUESTADOR (N)
                    |       |
                    |       +-- CAP_100 (1)
                    |       +-- CAP_200 (N) --> CAP_100A, CAP_100B, CAP_200_ADICIONAL
                    |       +-- CAP_600 (1) --> CAP_601-612 (N)
                    |       +-- CAP_700A (1) --> CAP_700B (N)
                    |       +-- CAP_800A (1) --> CAP_800B (N)
                    |
                    +-- CAP_300 (N) --> CAP_300A (N)
                    +-- CAP_400 (N) --> CAP_400A (N)
                    +-- CAP_500 (N) --> CAP_500A-E (N)
                    +-- CAP_2000 (N) --> CAP_2100-2700 (N)
                    +-- CAP_1 (N) --> CAP_2-4 (N)
`

## 4.6 Transacciones

| Metodo | Archivo | Lineas | Uso |
|--------|---------|--------|-----|
| executeTransaccion() | SQLiteUtil | 507-519 | General transaction wrapper |
| startTX() / commitTX() / endTX() | SQLiteDAO | 90-103 | Manual transaction management |
| cargarXML() | DatabaseHelper | 86-100 | DB upgrade transaction |
| importarDatos() | Importacion | 163 | Import transaction |

**Problema:** Muchas operaciones multiples NO usan transacciones:
- MarcoDAO.eliminarMarco() - 3 DELETEs sin transaction
- ViviendaDao.trasladoViviendasCampo() - ~20 UPDATEs sin transaction
- Enaho02Dao - 20+ DELETEs sin transaction

---

# 5. RELACIONES

## 5.1 Diagrama de Relaciones

`
PERIODO (1)
    |
    +-- (N) CAMPO_CONGLOMERADO
                    |
                    +-- (N) CAMPO_VIVIENDA_MARCO
                    |           |
                    |           +-- (N) PERSONA_PANEL
                    |
                    +-- (N) CAMPO_VIVIENDA
                    |           |
                    |           +-- (N) CAMPO_HOGAR
                    |                   |
                    |                   +-- (N) CAMPO_VISITA_ENCUESTADOR
                    |                   |       |
                    |                   |       +-- (1) CAP_100 --> CAP_100A, CAP_100B
                    |                   |       +-- (N) CAP_200 --> CAP_TELEFONOS, CAP_200_ADICIONAL
                    |                   |       +-- (1) CAP_600 --> CAP_601(N) --> OTROS_PRODUCTO
                    |                   |       +-- (1) CAP_700A --> CAP_700B(N)
                    |                   |       +-- (1) CAP_800A --> CAP_800B(N)
                    |                   |       +-- (1) CAP_900 --> CAP_900A(N)
                    |                   |
                    |                   +-- (N) CAP_300 --> CAP_300A(N)
                    |                   +-- (N) CAP_400 --> CAP_400A(N)
                    |                   +-- (N) CAP_500 --> CAP_500A(N), CAP_500B(N), etc.
                    |                   +-- (N) CAP_2000 --> CAP_2100-2700(N)
                    |                   +-- (N) CAP_1 --> CAP_2(N), CAP_3(N), CAP_4(N)
                    |
                    +-- (N) CAMPO_VISITA_SUPERVISOR
                            |
                            +-- (N) SUPERVISION_ITEM_HOGAR
                            +-- (N) SUPERVISION_CAP_200 --> SUPERVISION_ITEM_CAP200(N)
                            +-- (N) SUPERVISION_HOGAR_PRODUCTO
`

## 5.2 Foreign Keys Documentadas

| Tabla Padre | Tabla Hijo | Columna FK | ON DELETE | ON UPDATE |
|-------------|------------|------------|-----------|-----------|
| CAMPO_CONGLOMERADO | CAMPO_VIVIENDA | CONGLOME | CASCADE | CASCADE |
| CAMPO_VIVIENDA | CAMPO_HOGAR | CONGLOME,VIVIENDA | NO ACTION | NO ACTION |
| CAMPO_HOGAR | CAMPO_VISITA_ENCUESTADOR | CONGLOME,VIVIENDA,HOGAR | CASCADE | CASCADE |
| CAMPO_HOGAR | CAMPO_VISITA_SUPERVISOR | CONGLOME,VIVIENDA,HOGAR | CASCADE | CASCADE |
| CAP_100 | CAP_100A | CONGLOME,VIVIENDA,HOGAR | CASCADE | CASCADE |
| CAP_100 | CAP_100B | CONGLOME,VIVIENDA,HOGAR | CASCADE | CASCADE |
| CAP_200 | CAMPO_TELEFONOS | CONGLOME,VIVIENDA,HOGAR,P201 | CASCADE | CASCADE |
| CAP_601 | OTROS_PRODUCTO | CONGLOME,VIVIENDA,HOGAR,P601A | CASCADE | CASCADE |
| PERSONAL | PERFIL | CODPERFIL | NO ACTION | NO ACTION |
| PERSONAL | CARGO | CARGO | NO ACTION | NO ACTION |
| PRODUCTO | CATEGORIA_PRODUCTO | CATEGORIA | NO ACTION | NO ACTION |
| DICCIONARIO_SECCION | DICCIONARIO_ITEM | ID_SECCION | NO ACTION | NO ACTION |

---

# 6. REGLAS DE INTEGRIDAD

## 6.1 Primary Keys

- **Patron:** Teclas compuestas jerarquicas (CONGLOME + VIVIENDA + HOGAR + ...)
- **Auto-increment:** Solo en tablas de referencia (ODEI, CAMPO_CONGLOMERADO, etc.)
- **Sin auto-increment:** Tablas de datos usan teclas compuestas

## 6.2 Foreign Keys

- **CASCADE en tablas nucleares:** CAMPO_HOGAR -> CAMPO_VISITA_ENCUESTADOR (ON DELETE CASCADE)
- **NO ACTION en relaciones debiles:** CAMPO_VIVIENDA -> CAMPO_HOGAR (sin cascade)
- **Sin FK definidas:** Muchas relaciones entre modulos (ENAHO01 -> ENAHO01A) no tienen FK explicita

## 6.3 UNIQUE Constraints

| Tabla | Columnas Unicas |
|-------|-----------------|
| ODEI | CODODEI |
| CAMPO_CONGLOMERADO | CONGLOME |
| CAMPO_VIVIENDA_MARCO | CONGLOME, VIVIENDA |
| PERSONAL | DNI |

## 6.4 NOT NULL

- **Implícito en PKs:** Todas las columnas de PK son NOT NULL
- **Auditoria:** USUCRE, FECCRE generalmente NOT NULL
- **Datos:** Muchas columnas de datos son nullable (respuestas opcionales)

## 6.5 CHECK Constraints

- **No documentadas** en el esquema actual
- **Sin validaciones a nivel BD** para valores fuera de rango

## 6.6 Indices

**CRITICO: No se encontraron CREATE INDEX en todo el codigo fuente.**

Las unas restricciones de unicidad actuan como indices implicitos para las PKs.

---

# 7. CONSULTAS IMPORTANTES

## 7.1 Ingresos por Hogar (ingresos02_10)

`sql
-- MarcoDAO.java:160-260
-- 8+ subqueries correlacionados con CASE, SUM, nested SELECTs
SELECT *,
    (SELECT SUM(CASE WHEN ... THEN ... END) FROM CAP_2100 WHERE ...) AS TOT2100,
    (SELECT SUM(CASE WHEN ... THEN ... END) FROM CAP_2200 WHERE ...) AS TOT2200,
    -- ... 6+ subqueries mas
FROM CAMPO_HOGAR H
WHERE ...
`

**Riesgo:** Subqueries correlacionados ejecutados por cada fila del resultado padre.

## 7.2 Balance del Hogar

`sql
-- MarcoDAO.java:1742-1746
-- 3 subqueries con GROUP BY
SELECT *,
    (SELECT SUM(...) FROM CAP_200 WHERE ...) AS INGRESOS,
    (SELECT SUM(...) FROM CAP_601 WHERE ...) AS GASTOS,
    (SELECT SUM(...) FROM CAP_500 WHERE ...) AS OTROS
FROM CAMPO_HOGAR
GROUP BY CONGLOME, VIVIENDA, HOGAR
`

## 7.3 Consumo Per Capita

`sql
-- MarcoDAO.java:1517-1523
-- 3 tablas + LEFT JOIN
SELECT C.*, P.DESCRIPCION, U.DESCRIPCION
FROM CAP_601 C
INNER JOIN PRODUCTO P ON C.CODIGO = P.CODIGO
INNER JOIN UNIDAD_MEDIDA U ON C.UNIDAD = U.CODIGO
LEFT JOIN PRECIO_EXTREMO PE ON P.CODIGO = PE.PRODUCTO
WHERE C.CONGLOME=? AND C.VIVIENDA=? AND C.HOGAR=?
`

## 7.4 Reporte de Ingresos Detallado

`sql
-- MarcoDAO.java:2065-2095
-- 4 tablas JOIN repetido 4 veces
SELECT *,
    (SELECT ... FROM CAP_1 INNER JOIN CAP_200 ... WHERE ...) AS ACT_1,
    (SELECT ... FROM CAP_1 INNER JOIN CAP_200 ... WHERE ...) AS ACT_2,
    (SELECT ... FROM CAP_1 INNER JOIN CAP_200 ... WHERE ...) AS ACT_3,
    (SELECT ... FROM CAP_1 INNER JOIN CAP_200 ... WHERE ...) AS ACT_4
FROM CAMPO_HOGAR
`

---

# 8. RIESGOS

## 8.1 Riesgos Criticos

| # | Riesgo | Impacto | Evidencia |
|---|--------|---------|-----------|
| 1 | **SQL Injection** (100+ ocurrencias) | Seguridad | ConfiguracionDao:24, DiccionarioDao:55-233, MarcoDAO:155, ViviendaDao:372-466, Enaho02Dao:151-1102, Enaho01Dao:218-4438, Enaho04Dao:130-1123, HogarDao:1930-4298, SQLiteUtil:94-474, SQLiteDAO:144-1621 |
| 2 | **Sin indices** (0 CREATE INDEX) | Performance | Todo el esquema - queries multi-JOIN sin indices |

## 8.2 Riesgos Altos

| # | Riesgo | Impacto | Evidencia |
|---|--------|---------|-----------|
| 3 | **Main Thread DB access** | ANR | ConfigActivity:80-114, CopiaDBFragmentActivity:400-592 |
| 4 | **Sin LIMIT en queries** | Memoria | SQLiteDAO:979-983, 1159-1186; todas las queries SELECT |
| 5 | **Queries pesadas** (40+ multi-JOINs) | Performance | MarcoDAO, HogarDao, Enaho02Dao, Enaho01Dao, ViviendaDao |
| 6 | **Transacciones faltantes** | Integridad | MarcoDAO:137-148, ViviendaDao:401-452, Enaho02Dao:304-587, HogarDao:2297-2594 |

## 8.3 Riesgos Medios

| # | Riesgo | Impacto | Evidencia |
|---|--------|---------|-----------|
| 7 | **N+1 queries** (10+ ubicaciones) | Performance | ViviendaDao:401-452, Enaho02Dao:168-184, ConfigActivity:106-108 |
| 8 | **Concurrency** (campos estaticos) | Race conditions | SQLiteDAO:USER_ID, ES_LECTURA, cursor compartido |
| 9 | **Backup sin locking** | Corrupcion | SQLiteUtil:691-758, recuperarBD():775-799 |
| 10 | **Sin CHECK constraints** | Integridad | No hay validaciones a nivel BD |

## 8.4 Riesgos Bajos

| # | Riesgo | Impacto | Evidencia |
|---|--------|---------|-----------|
| 11 | **releaseMemory() excesivo** | Performance | SQLiteDAO:20+ llamadas |
| 12 | **PRAGMA table_info frecuente** | Performance | SQLiteDAO:1399,1465 |
| 13 | **select * en todas las queries** | Red/ Memoria | DiccionarioDao, MarcoDAO, todas las entidades |

---

# 9. ROOM (Propuesta Conceptual)

## 9.1 Mapeo Tabla -> @Entity

`kotlin
// CAMPO_HOGAR -> HogarEntity
@Entity(
    tableName = "CAMPO_HOGAR",
    foreignKeys = [
        ForeignKey(
            entity = ViviendaEntity::class,
            parentColumns = ["CONGLOME", "VIVIENDA"],
            childColumns = ["CONGLOME", "VIVIENDA"],
            onDelete = ForeignKey.CASCADE
        )
    ]
)
data class HogarEntity(
    @PrimaryKey val conglome: String,
    @PrimaryKey val vivienda: String,
    @PrimaryKey val hogar: String,
    val resultado: String?,
    val completo: String?,
    val visita: Int?
)

// CAMPOENA01_CAP_200 -> Cap200Entity
@Entity(
    tableName = "CAMPOENA01_CAP_200",
    foreignKeys = [
        ForeignKey(
            entity = HogarEntity::class,
            parentColumns = ["CONGLOME", "VIVIENDA", "HOGAR"],
            childColumns = ["CONGLOME", "VIVIENDA", "HOGAR"],
            onDelete = ForeignKey.CASCADE
        )
    ]
)
data class Cap200Entity(
    @PrimaryKey val conglome: String,
    @PrimaryKey val vivienda: String,
    @PrimaryKey val hogar: String,
    @PrimaryKey val p201: String,
    val p202: String?,
    val p203: String?,
    // ... mas campos
)
`

## 9.2 Mapeo DAO

`kotlin
// HogarDao -> IHogarDao
@Dao
interface IHogarDao {
    @Query("SELECT * FROM CAMPO_HOGAR WHERE CONGLOME = :conglome AND VIVIENDA = :vivienda")
    suspend fun findByVivienda(conglome: String, vivienda: String): List<HogarEntity>

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insert(hogar: HogarEntity)

    @Update
    suspend fun update(hogar: HogarEntity)

    @Query("DELETE FROM CAMPO_HOGAR WHERE CONGLOME = :conglome AND VIVIENDA = :vivienda AND HOGAR = :hogar")
    suspend fun delete(conglome: String, vivienda: String, hogar: String)
}
`

## 9.3 Mapeo Repository

`kotlin
// HogarRepository
class HogarRepository @Inject constructor(
    private val hogarDao: IHogarDao,
    private val viviendaDao: IViviendaDao
) {
    suspend fun findHogares(conglome: String, vivienda: String): List<HogarEntity> {
        return hogarDao.findByVivienda(conglome, vivienda)
    }

    suspend fun save(hogar: HogarEntity) {
        hogarDao.insert(hogar)
    }
}
`

## 9.4 Indices Recomendados

`kotlin
@Entity(
    tableName = "CAMPO_HOGAR",
    indices = [
        Index(value = ["CONGLOME", "VIVIENDA"]),
        Index(value = ["CONGLOME", "VIVIENDA", "HOGAR"]),
        Index(value = ["RESULTADO"]),
        Index(value = ["COMPLETO"])
    ]
)
`

## 9.5 Tipo de Relaciones

| Relacion | Tipo | Room |
|----------|------|------|
| CONGLOMERADO -> VIVIENDA | 1:N | @ForeignKey |
| VIVIENDA -> HOGAR | 1:N | @ForeignKey |
| HOGAR -> VISITA | 1:N | @ForeignKey |
| HOGAR -> CAP_200 | 1:N | @ForeignKey |
| CAP_200 -> CAP_300 | 1:N | @ForeignKey |
| CAP_500 -> CAP_2000 | 1:N | @ForeignKey |

---

# RESUMEN EJECUTIVO

## Metricas de Base de Datos

| Metrica | Valor |
|---------|-------|
| **Total tablas** | 137 |
| **Version BD** | 56 |
| **Migraciones** | 54 (upgrade02-upgrade93) |
| **Archivos XML creacion** | 30+ |
| **Archivos XML upgrades** | 54 |
| **FKs documentadas** | 20+ |
| **Indices CREATE INDEX** | 0 (CRITICO) |
| **Queries multi-JOIN** | 40+ |
| **SQL injection points** | 100+ |
| **Transacciones sin wrapper** | 20+ |
| **N+1 patterns** | 10+ |

## Hallazgo Principal

**La base de datos tiene 137 tablas con esquema jerarquico complejo, pero carece fundamentalmente de:**
1. **Indices** (0 CREATE INDEX en todo el codigo)
2. **Validaciones a nivel BD** (sin CHECK constraints)
3. **Transacciones completas** (muchas operaciones multiples sin wrapper)
4. **Proteccion SQL Injection** (100+ puntos de concatenacion directa)

**La propuesta Room resolveria problemas de indices, tipos, y seguridad, pero requiere mapeo cuidadoso de las 137 tablas existentes.**

---

**Documento generado:** 2026-08-30
**Estado:** EN REVISION
**Siguiente paso:** Aprobacion del usuario para Etapa 5
