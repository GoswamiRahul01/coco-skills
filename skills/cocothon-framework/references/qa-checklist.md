# QA Checklist — Validación Pre-entrega Cocothon

Asume que cometiste errores y revísalo todo con ojo crítico. Ejecuta ANTES de entregar cualquier output al AE o al cliente.

---

## 1. Estructura de Equipos

- [ ] Todos los equipos tienen 3-4 personas (nunca 2, nunca 5+)
- [ ] No hay personas sin asignar a un equipo
- [ ] Cada equipo tiene un nombre descriptivo de su categoría
- [ ] Categorías son coherentes (miembros pueden colaborar en un tema común)
- [ ] Duplicados detectados y fusionados (mismo caso = mismo equipo)
- [ ] Lista de espera/refuerzo documentada con plan de asignación

---

## 2. Problem Statements

- [ ] Cada PS es concreto y accionable en el tiempo del evento
- [ ] Cada PS tiene datos disponibles para resolverlo
- [ ] No hay duplicados sin fusionar entre equipos distintos
- [ ] Cada PS está redactado en lenguaje de negocio (sin jerga técnica)
- [ ] Cada PS tiene un "so what" claro (impacto medible o beneficiario identificado)
- [ ] El alcance de cada PS es realista para un MVP en 2h de build

---

## 3. Setup Técnico

- [ ] Cortex AI validado: `SELECT SNOWFLAKE.CORTEX.COMPLETE('claude-haiku-4-5', 'test')` ejecuta sin error
- [ ] Snowflake Intelligence habilitado desde UI (Snowsight > SI > Get Started)
- [ ] Cross-region inference ON: `CORTEX_ENABLED_CROSS_REGION = 'ANY_REGION'`
- [ ] Warehouses creados y con AUTO_RESUME = TRUE
- [ ] Roles creados con permisos suficientes (CREATE TABLE, VIEW, STREAMLIT, SEMANTIC VIEW, etc.)
- [ ] Datos pre-cargados y validados (al menos 1 query por tabla ejecuta correctamente)
- [ ] Skills de CoCo relevantes preinstalados en máquinas de participantes
- [ ] CoCo Desktop instalado y conectado en al menos 1 máquina de prueba

---

## 4. Propuesta para Cliente

- [ ] Sin jerga técnica (no menciona warehouses, SQL, schemas, roles)
- [ ] Branding Snowflake correcto (#29B5E8 / #11567F, fuentes Montserrat + Lato)
- [ ] Agenda con tiempos realistas y buffers incluidos
- [ ] Logística completa: fecha, hora, lugar/link, duración, qué traer
- [ ] Track record mencionado (ej: "7 equipos, 7 soluciones en 2h — París 2026")
- [ ] Entregables claros para el participante (qué se llevan)
- [ ] Criterios de evaluación visibles
- [ ] Idioma consistente (no mezclar ES/EN)

---

## 5. Materiales Internos

- [ ] Cheat sheet actualizado con comandos vigentes de CoCo
- [ ] Rúbrica de evaluación lista e imprimible (5 criterios con pesos)
- [ ] Briefing técnico incluye ERD o descripción de modelo de datos
- [ ] Skills ISF relevantes identificados y verificados funcionales
- [ ] Plan B documentado (qué pasa si falla internet/cuenta/Cortex)
- [ ] Agenda detallada con roles asignados (quién hace qué en cada bloque)

---

## 6. Seguridad y Datos

- [ ] Sin PII en datos sintéticos (nombres, emails, cédulas generadas son ficticias)
- [ ] Roles con least-privilege (participantes NO tienen ACCOUNTADMIN)
- [ ] Datos validados: queries principales ejecutan sin error
- [ ] No hay credenciales hardcodeadas en scripts compartidos
- [ ] Participantes externos no tienen acceso a datos de producción

---

## Reporte QA

### Formato:

```
═══ QA REPORT — Cocothon [EMPRESA] ═══

Fecha revisión: [DD/MM/YYYY]
Revisor: [Nombre]

FINDINGS:
─────────────────────────────────────────

[CRITICAL] Sección X — [Descripción del problema]
  → Fix: [Qué hacer]

[MAJOR] Sección X — [Descripción del problema]
  → Fix: [Qué hacer]

[MINOR] Sección X — [Descripción del problema]
  → Fix: [Qué hacer]

─────────────────────────────────────────

VEREDICTO: [ ] APPROVED  [ ] REQUIRES CORRECTIONS  [ ] REJECTED

Notas: [Comentarios adicionales]
```

### Reglas de severidad:

| Severidad | Criterio | Acción |
|-----------|----------|--------|
| **CRITICAL** | Bloquea la ejecución del evento o expone datos sensibles | NO entregar hasta resolver |
| **MAJOR** | Degradará la experiencia pero no bloquea | Corregir antes de entregar al cliente |
| **MINOR** | Cosmético o mejora opcional | Advertir; entregar con nota |

### Veredicto:

- **APPROVED** — 0 Critical, 0 Major. Listo para entregar.
- **REQUIRES CORRECTIONS** — 0 Critical, 1+ Major. Corregir y re-validar.
- **REJECTED** — 1+ Critical. No entregar hasta resolver todos los Critical.
