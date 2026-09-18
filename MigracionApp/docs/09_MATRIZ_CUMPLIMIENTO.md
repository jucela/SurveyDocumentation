# 09 - MATRIZ DE CUMPLIMIENTO PARA PILOTO CAP500

> **Fecha:** 2026-08-30
> **Tipo:** Evaluación de cumplimiento - No implementación
> **Base:** docs/09_AUDITORIA_PRE_IMPLEMENTACION.md (22 criterios sección 28.1)

---

# 1. RESUMEN EJECUTIVO

```
22 criterios evaluados
0 cumplidos
13 parciales (tienen evidencia incompleta)
9 sin evidencia
0 requieren ajuste
```

**Resultado: ETAPA 09 NO APROBABLE**

Existen 22 criterios bloqueantes (13 parciales + 9 sin evidencia). Se requiere crear documentos de soporte y evidencia concreta antes de aprobar el piloto Cap500.

---

# 2. MATRIZ PRINCIPAL

| # | Criterio | Estado | Evidencia existente | Fuente | Evidencia faltante | ¿Bloqueante? | Acción requerida |
|---|----------|--------|---------------------|--------|-------------------|--------------|------------------|
| 1 | Alcance Cap500 | PARCIAL | Fragments Cap500 identificados (149 aprox), reglas principales documentadas, limpiezas cross-chapter identificadas | Secciones 3, 4, 7 | Lista completa fragments, reglas por pregunta, consultas SQL específicas, endpoints exactos, tablas exactas, dependencias completas | SI | Crear CAP500_ALCANCE.md con inventario completo |
| 2 | Navegación | PARCIAL | 42+ reglas getPaginaSiguiente identificadas, caso P537 documentado parcialmente | Secciones 4, 5 | Catálogo TransitionRule completo, validación prioridades, destinos inválidos, reglas inalcanzables, duplicadas, empate | SI | Crear CAP500_REGLAS.md con catálogo navegación |
| 3 | Visibilidad | PARCIAL | 100+ usos setRelevancia identificados, Cap500 mencionado | Sección 6 | Catálogo VisibilityRule Cap500 completo, cada regla verificada individualmente | SI | Crear CAP500_VISIBILIDAD.md con catálogo |
| 4 | Limpieza local | PARCIAL | limpiar538_540, limpiar538_543, limpiar544, limpiar545_555 identificados | Sección 7 | DB diff antes/después, tablas exactas, registros afectados por cada limpieza | SI | Crear CAP500_LIMPIEZA.md con catálogo detallado |
| 5 | Limpieza cross-chapter | PARCIAL | Cap500→ENAHO02, Cap500→ENAHO04 identificados | Secciones 7, 18 | Catálogo completo: origen, condición, tablas, operación, datos eliminados/no eliminados | SI | Crear CAP500_LIMPIEZA.md con sección cross-chapter |
| 6 | Estado mínimo | PARCIAL | currentChapter, currentFragment, currentMember, respuestas documentados en SurveyState | Sección 9 | Definición completa destino lógico, estado derivado, persistencia, recuperación process death | SI | Crear CAP500_ESTADO.md con definición completa |
| 7 | Persistencia | PARCIAL | DB legacy puede utilizarse, se menciona encapsulación | Secciones 11, 12 | Tablas exactas Cap500, consultas exactas, INSERT/UPDATE/DELETE, transacciones, abstracción documentada | SI | Crear CAP500_ESTADO.md con sección persistencia |
| 8 | Legacy vs Engine | PARCIAL | Estrategia MATCH/MISMATCH/NO DETERMINADO definida conceptualmente | Sección 28.3 | Mecanismo concreto: identificador caso, entrada, resultado Legacy, resultado Engine, diferencia, severidad, evidencia, reproducción | SI | Crear CAP500_VALIDACION.md con mecanismo concreto |
| 9 | Casos de prueba | FALTA EVIDENCIA | No existe evidencia de casos de prueba documentados | Ninguna | Escenario, entrada, respuestas, condición, resultado esperado, navegación, visibilidad, limpieza, comparación | SI | Crear CAP500_VALIDACION.md con casos de prueba |
| 10 | Rollback | FALTA EVIDENCIA | No existe evidencia de procedimiento rollback | Ninguna | Detección fallo, activación rollback, regreso Legacy, conservación respuestas, prevención duplicidad/corrupción, inconsistencia, registro | SI | Crear CAP500_RIESGOS.md con procedimiento rollback |
| 11 | Consultas | PARCIAL | Consultas Cap500 parcialmente identificadas en sección 12 | Sección 12 | Inventario completo: método, DAO/service, tablas, parámetros, operación, transacción, destino futuro | SI | Crear CAP500_ESTADO.md con sección consultas |
| 12 | GPS | FALTA EVIDENCIA | GPS identificado como riesgo, pero no decidido para piloto | Secciones 3, 19 | Decisión concreta: estrategia, justificación, riesgos | NO | Documentar decisión GPS |
| 13 | ANR | PARCIAL | ANR identificado como riesgo, SyncHttpClient documentado | Secciones 7, 19 | Plan concreto: operaciones bloqueantes piloto, mitigation específica | SI | Crear CAP500_RIESGOS.md con sección ANR |
| 14 | Dependencias | PARCIAL | Dependencias identificadas (AppContext, Activity, Fragment, Services) | Secciones 17, 23 | Estrategia concreta de aislamiento durante piloto | SI | Crear CAP500_ESTADO.md con sección dependencias |
| 15 | Integridad | FALTA EVIDENCIA | No existe evidencia | Ninguna | Plan: registros antes/después, tablas, INSERT/UPDATE/DELETE, consistencia referencial, comparación Legacy vs Engine | SI | Crear CAP500_RIESGOS.md con plan integridad |
| 16 | Transacciones | FALTA EVIDENCIA | No existe evidencia | Ninguna | Definición: operaciones atómicas, falla intermedia, rollback DB, cross-chapter | SI | Crear CAP500_ESTADO.md con sección transacciones |
| 17 | Cobertura | FALTA EVIDENCIA | No existe evidencia | Ninguna | Matriz: Regla Legacy → Ubicación código → Regla documentada → Regla nueva → Caso prueba → Resultado | SI | Crear CAP500_VALIDACION.md con matriz cobertura |
| 18 | Errores | FALTA EVIDENCIA | Manejo errores mencionado como pendiente | Sección 3 | Plan: error validación, navegación, persistencia, limpieza, Engine, red, recuperación | SI | Crear CAP500_RIESGOS.md con plan errores |
| 19 | Logging | FALTA EVIDENCIA | No existe evidencia | Ninguna | Definición: caso, pregunta, regla, destino, visibilidad, cleanup, Legacy, Engine, mismatch, error | SI | Crear CAP500_VALIDACION.md con plan logging |
| 20 | Seguridad | PARCIAL | SSL trust-all, credenciales hardcoded identificados | Secciones 7, 19 | Distinguir: riesgos heredados temporalmente vs riesgos bloqueantes piloto vs bloqueantes generales | SI | Crear CAP500_RIESGOS.md con sección seguridad |
| 21 | Compatibilidad | FALTA EVIDENCIA | No existe evidencia | Ninguna | Estrategia: Legacy, Engine, navegación, DB, estado, versiones Android, rotación, recuperación, coexistencia | SI | Crear CAP500_RIESGOS.md con plan compatibilidad |
| 22 | Dual execution | PARCIAL | Se menciona "dual execution o shadow engine" en sección 26 | Sección 26 | Definición concreta: cuándo, qué entrada, qué resultado, cómo compara, cómo registra mismatch, cómo evita afectar datos, cómo decide pasar a ejecución real | SI | Crear CAP500_VALIDACION.md con mecanismo dual |

---

# 3. EVIDENCIA EXISTENTE DETALLADA

## Criterio 1 - Alcance

| Elemento | Estado | Fuente |
|----------|--------|--------|
| Fragments Cap500 | PARCIAL - 149 fragments mencionados, no lista completa | Sección 3 |
| Reglas negocio Cap500 | PARCIAL - P537, P541 documentados | Secciones 4, 5 |
| Consultas SQL | FALTA - No inventariadas específicamente para Cap500 | - |
| Limpiezas locales | PARCIAL - 4 limpiezas identificadas | Sección 7 |
| Limpiezas cross-chapter | PARCIAL - ENAHO02/04 identificadas | Secciones 7, 18 |
| Endpoints | FALTA - No definidos específicamente para Cap500 | - |
| Tablas afectadas | FALTA - No inventariadas para Cap500 | - |
| Dependencias cross-chapter | PARCIAL - Cap200→Cap500 identificada | Sección 18 |

## Criterio 2 - Navegación

| Elemento | Estado | Fuente |
|----------|--------|--------|
| getPaginaSiguiente | PARCIAL - 42+ referencias, no catálogo completo | Sección 4 |
| getPaginaAnterior | PARCIAL - 33 referencias, no catálogo completo | Sección 4 |
| P537 reglas | PARCIAL - Caso parcial documentado | Sección 5.2 |
| P541 reglas | FALTA - Solo mencionado | Sección 4 |
| Prioridades | FALTA | - |
| Destinos inválidos | FALTA | - |
| Reglas inalcanzables | FALTA | - |
| Reglas duplicadas | FALTA | - |
| Empate prioridades | FALTA | Sección 10 |

## Criterio 3 - Visibilidad

| Elemento | Estado | Fuente |
|----------|--------|--------|
| setRelevancia Cap500 | PARCIAL - 100+ usos mencionados | Sección 6 |
| setRelevanciaFija | FALTA | - |
| Catálogo VisibilityRule | FALTA | - |
| Preguntas ocultas | FALTA | - |
| Fragmentos ocultos | FALTA | - |

## Criterio 4 - Limpieza Local

| Elemento | Estado | Fuente |
|----------|--------|--------|
| limpiar538_540 | PARCIAL - Identificado, sin DB diff | Sección 7 |
| limpiar538_543 | PARCIAL - Identificado, sin DB diff | Sección 7 |
| limpiar544 | PARCIAL - Identificado, sin DB diff | Sección 7 |
| limpiar545_555 | PARCIAL - Identificado, sin DB diff | Sección 7 |
| DB diff | FALTA | - |

## Criterio 5 - Limpieza Cross-Chapter

| Elemento | Estado | Fuente |
|----------|--------|--------|
| Cap500→ENAHO02 | PARCIAL - Identificado | Secciones 7, 18 |
| Cap500→ENAHO04 | PARCIAL - Identificado | Secciones 7, 18 |
| Eliminación miembro | PARCIAL - Identificado | Sección 7 |
| Catálogo completo | FALTA | - |
| Datos eliminados/no eliminados | FALTA | - |

## Criterio 6 - Estado

| Elemento | Estado | Fuente |
|----------|--------|--------|
| currentChapter | PARCIAL - Definido en SurveyState | Sección 9 |
| currentFragment | PARCIAL - Definido en SurveyState | Sección 9 |
| currentMember | PARCIAL - Definido en SurveyState | Sección 9 |
| answers | PARCIAL - Map<String, String> definido | Sección 9 |
| Destino lógico | FALTA | - |
| Estado derivado | FALTA | - |
| Recuperación process death | FALTA - Se menciona como BLOQUEANTE | Sección 20 |
| Recuperación desde DB | FALTA | - |

## Criterio 7 - Persistencia

| Elemento | Estado | Fuente |
|----------|--------|--------|
| DB legacy encapsulada | PARCIAL - Decisión conceptual | Sección 27.2 |
| Tablas Cap500 | FALTA | - |
| Consultas Cap500 | FALTA | - |
| INSERT/UPDATE/DELETE | FALTA | - |
| Transacciones | FALTA | - |
| Abstracción documentada | FALTA | - |

## Criterio 8 - Legacy vs Engine

| Elemento | Estado | Fuente |
|----------|--------|--------|
| Estrategia MATCH/MISMATCH | PARCIAL - Definición conceptual | Sección 28.3 |
| Mecanismo concreto | FALTA | - |
| Identificador caso | FALTA | - |
| Evidencia/logs | FALTA | - |

## Criterio 9 - Casos de Prueba

| Elemento | Estado | Fuente |
|----------|--------|--------|
| Casos documentados | FALTA | - |
| Escenario completo | FALTA | - |
| Entrada/salida | FALTA | - |

## Criterio 10 - Rollback

| Elemento | Estado | Fuente |
|----------|--------|--------|
| Procedimiento | FALTA | - |
| Detección fallo | FALTA | - |
| Conservación respuestas | FALTA | - |

## Criterio 11 - Consultas

| Elemento | Estado | Fuente |
|----------|--------|--------|
| Inventario consultas | PARCIAL - Algunas identificadas | Sección 12 |
| Consultas específicas Cap500 | FALTA | - |

## Criterio 12 - GPS

| Elemento | Estado | Fuente |
|----------|--------|--------|
| Decisión | FALTA | - |
| Estrategia | FALTA | - |

## Criterio 13 - ANR

| Elemento | Estado | Fuente |
|----------|--------|--------|
| Identificación riesgos | PARCIAL - ANR documentado | Secciones 7, 19 |
| Plan mitigation piloto | FALTA | - |

## Criterio 14 - Dependencias

| Elemento | Estado | Fuente |
|----------|--------|--------|
| Identificación | PARCIAL - Dependencias documentadas | Secciones 17, 23 |
| Estrategia aislamiento piloto | FALTA | - |

## Criterios 15-22

Todos sin evidencia suficiente.

---

# 4. EVIDENCIA FALTANTE CONSOLIDADA

## Documentos de soporte necesarios

| Documento | Criterios que cubre | Prioridad |
|-----------|---------------------|-----------|
| CAP500_ALCANCE.md | 1 | PRIORIDAD 1 |
| CAP500_REGLAS.md | 2, 17 | PRIORIDAD 1 |
| CAP500_VISIBILIDAD.md | 3 | PRIORIDAD 1 |
| CAP500_LIMPIEZA.md | 4, 5 | PRIORIDAD 1 |
| CAP500_ESTADO.md | 6, 7, 11, 14, 16 | PRIORIDAD 1 |
| CAP500_VALIDACION.md | 8, 9, 17, 19, 22 | PRIORIDAD 1 |
| CAP500_RIESGOS.md | 10, 13, 15, 18, 20, 21 | PRIORIDAD 1 |
| Documento GPS | 12 | PRIORIDAD 2 |

---

# 5. CRITERIOS BLOQUEANTES

| # | Criterio | Estado | Acción específica |
|---|----------|--------|-------------------|
| 1 | Alcance Cap500 | PARCIAL | Crear CAP500_ALCANCE.md |
| 2 | Navegación | PARCIAL | Crear CAP500_REGLAS.md |
| 3 | Visibilidad | PARCIAL | Crear CAP500_VISIBILIDAD.md |
| 4 | Limpieza local | PARCIAL | Crear CAP500_LIMPIEZA.md |
| 5 | Limpieza cross-chapter | PARCIAL | Crear CAP500_LIMPIEZA.md |
| 6 | Estado mínimo | PARCIAL | Crear CAP500_ESTADO.md |
| 7 | Persistencia | PARCIAL | Crear CAP500_ESTADO.md |
| 8 | Legacy vs Engine | PARCIAL | Crear CAP500_VALIDACION.md |
| 9 | Casos de prueba | FALTA | Crear CAP500_VALIDACION.md |
| 10 | Rollback | FALTA | Crear CAP500_RIESGOS.md |
| 11 | Consultas | PARCIAL | Crear CAP500_ESTADO.md |
| 13 | ANR | PARCIAL | Crear CAP500_RIESGOS.md |
| 14 | Dependencias | PARCIAL | Crear CAP500_ESTADO.md |
| 15 | Integridad | FALTA | Crear CAP500_RIESGOS.md |
| 16 | Transacciones | FALTA | Crear CAP500_ESTADO.md |
| 17 | Cobertura | FALTA | Crear CAP500_VALIDACION.md |
| 18 | Errores | FALTA | Crear CAP500_RIESGOS.md |
| 19 | Logging | FALTA | Crear CAP500_VALIDACION.md |
| 20 | Seguridad | PARCIAL | Crear CAP500_RIESGOS.md |
| 21 | Compatibilidad | FALTA | Crear CAP500_RIESGOS.md |
| 22 | Dual execution | PARCIAL | Crear CAP500_VALIDACION.md |

---

# 6. CRITERIOS NO BLOQUEANTES

| # | Criterio | Estado | Nota |
|---|----------|--------|------|
| 12 | GPS | FALTA | Solo bloquea si Cap500 requiere GPS directamente |

---

# 7. PRIORIZACIÓN DE ACCIONES

## PRIORIDAD 1 — CRÍTICA (antes de aprobar Etapa 09)

1. Crear CAP500_ALCANCE.md con inventario completo fragments, reglas, consultas, limpiezas, endpoints, tablas, dependencias
2. Crear CAP500_REGLAS.md con catálogo TransitionRule completo Cap500
3. Crear CAP500_VISIBILIDAD.md con catálogo VisibilityRule Cap500
4. Crear CAP500_LIMPIEZA.md con catálogo CleanupAction local y cross-chapter
5. Crear CAP500_ESTADO.md con definición estado, persistencia, consultas, dependencias, transacciones
6. Crear CAP500_VALIDACION.md con mecanismo Legacy vs Engine, casos de prueba, cobertura, logging, dual execution
7. Crear CAP500_RIESGOS.md con rollback, ANR, integridad, errores, seguridad, compatibilidad

## PRIORIDAD 2 — IMPORTANTE (antes de iniciar piloto)

8. Documentar decisión GPS

## PRIORIDAD 3 — MEJORA (durante piloto)

9. Optimizar logging
10. Refinar métricas de validación

---

# 8. DECISIÓN FINAL

## **ETAPA 09 NO APROBABLE**

**Justificación:**

De los 22 criterios evaluados:
- **0 cumplidos** (ninguno tiene evidencia suficiente)
- **13 parciales** (tienen evidencia pero incompleta)
- **9 sin evidencia** (no existen documentos de soporte)

Los 21 criterios bloqueantes requieren evidencia suficiente para aprobar el piloto. El criterio 12 (GPS) es no bloqueante y deberá documentarse como decisión antes de iniciar el piloto si Cap500 depende directamente de GPS. Sin estos documentos de soporte, no es posible garantizar que el piloto se ejecutará sin riesgo de:
- Pérdida de reglas de negocio
- Pérdida de datos
- Navegación incorrecta
- Visibilidad incorrecta
- Limpieza incorrecta
- Inconsistencia de estado

**Para llegar a ETAPA 09 APROBABLE:**

1. Crear los 7 documentos de soporte listados en Prioridad 1
2. Cada documento debe incluir evidencia concreta, no solo intención arquitectónica
3. Cada criterio debe responder: "¿Cómo sabemos objetivamente que está cumplido?"
4. Presentar evidencia verificable para cada uno de los 22 criterios

**No avanzar a implementación hasta que la Etapa 09 sea APROBABLE.**

---

*Documento generado como parte de la Etapa 09 - Cierre de Criterios.*
