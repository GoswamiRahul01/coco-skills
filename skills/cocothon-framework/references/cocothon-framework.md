# Marco de Referencia: Cocothon

## 1. Que es una Cocothon?

Una **Cocothon** es un hackathon estructurado de corta duracion (90-180 minutos) donde equipos conformados por SEs de Snowflake, clientes y/o partners resuelven problemas reales de negocio utilizando **Cortex Code (Coco)** como herramienta principal. El objetivo es demostrar valor tangible en tiempo record, generando soluciones funcionales que se pueden escalar y reutilizar.

### Origen

La primera Cocothon se realizo en **Paris, Francia, en abril de 2026**, como parte de un Industry Enablement Day enfocado en CPG (Consumer Packaged Goods). Fue organizada por **Nassim Kasdali**, **Jean-Philippe Hugon** y **Jeanne Schied**, con el sponsorship de **Miguel Angel Rivera** y **David DeLong**. En esa sesion, **7 equipos entregaron 7 soluciones completas en solo 2 horas**, con la participacion de partners como Meltone y Jakala.

### Relacion con el ISF

El **ISF (Industry Solutions Framework)** es el repositorio centralizado de Snowflake para demos orientadas a outcomes, skills de Cortex Code y assets de GTM por industria. La Cocothon es el **"Push Motion"** dentro del ISF — un mecanismo estructurado para alimentar el framework con soluciones validadas en campo (a diferencia del "Organic Motion", donde demos hero emergen naturalmente del trabajo diario de SEs).

---

## 2. Objetivos

- Demostrar velocidad y facilidad de Snowflake para resolver problemas complejos de negocio
- Generar assets reutilizables (skills, demos, semantic views, agents) para el ISF
- Acelerar adopcion y pipeline en cuentas estrategicas
- Crear cultura de "builders" entre SEs y partners
- Producir demos que se puedan escalar cross-region
- Fortalecer relaciones con clientes y partners a traves de co-creacion

---

## 3. Modalidades

| Modalidad | Duracion | Participantes | Objetivo |
|-----------|----------|---------------|----------|
| **Sprint** | 90 min | 2-4 equipos de 2-3 personas | Demo rapida, wow factor, activacion de cuenta |
| **Standard** | 2-4 horas | 4-6 equipos de 3-4 personas | Resolver problema completo, generar MVP funcional |
| **Extended** | 1-3 dias | 6-10 equipos de 4-5 personas | Multiples use cases, produccion de skills/apps |

> **Nota:** El evento de Paris fue modalidad "Standard" — 2 horas, 7 equipos, 7 soluciones completas entregadas.

---

## 4. Roles y Participantes

| Rol | Responsabilidad |
|-----|-----------------|
| **Organizador/MC** | Industry Principal o Field CTO. Facilita, presenta problema, evalua |
| **SE Champion** | SE asignado. Lidera equipo tecnico, conoce los datos del cliente |
| **AE Sponsor** | Valida alineacion con pipeline y objetivo de negocio |
| **Cliente/Partner** | Aporta contexto de negocio, datos reales, valida resultado |
| **Jurado** | 2-3 personas (Industry Principal + FCTO + cliente senior o liderazgo) |

> **Referencia Paris:** El jurado estuvo compuesto por Cecil, Clarisse Djomkam, David DeLong y Rida Lemkaalel.

---

## 5. Estructura del Evento

### Pre-evento (1-2 semanas antes)

1. **Seleccion de cuentas** — baja adopcion, oportunidad abierta, caso de uso claro
2. **Definicion de Problem Statements** — 2-7 problemas de negocio concretos alineados al ISF
3. **Preparacion de datos** — cargar datos del cliente o sinteticos en trial/sandbox
4. **Briefing a equipos** — problem statements, acceso a datos, skills disponibles
5. **Setup tecnico** — trial accounts, Cortex Code instalado, skills preloaded

### Dia del evento (formato Standard 2h)

| Tiempo | Actividad |
|--------|-----------|
| 0:00 - 0:15 | **Kick-off:** problema de negocio, reglas, criterios de evaluacion |
| 0:15 - 0:30 | **Ideacion:** equipos definen approach con Coco skills + datos |
| 0:30 - 1:30 | **Build:** construccion con Cortex Code |
| 1:30 - 1:45 | **Demo prep:** equipos preparan pitch de 3 min |
| 1:45 - 2:15 | **Show & Tell:** cada equipo presenta + Q&A del jurado |
| 2:15 - 2:30 | **Deliberacion y premiacion** |

---

## 6. Criterios de Evaluacion

| Criterio | Peso | Descripcion |
|----------|------|-------------|
| **Business Impact** | 30% | Resuelve un problema real? Cuanto valor genera? |
| **Feasibility** | 25% | Es implementable en produccion con esfuerzo razonable? |
| **Innovation** | 20% | Usa capacidades Snowflake de forma creativa? (AI, sharing, marketplace) |
| **Completeness** | 15% | Funciona end-to-end? Tiene UX aceptable? |
| **Reusability** | 10% | Se puede generalizar como skill/asset del ISF? |

---

## 7. Herramientas y Stack

### Obligatorio

- **Cortex Code (Coco)** como IDE/asistente principal
- **Snowflake Platform** (warehouse, stages, tables)
- Al menos uno de: Snowflake Intelligence Agent, Semantic View, Cortex Search, Streamlit

### Skills ISF disponibles (segun industria)

- **CPG/Retail**: Trade Promotions, Supply Chain Control Center, Campaign Activation, Competitive Pricing/RGM, Customer 360, Assortment Rationalization
- **Manufacturing**: OEE/Quality, Predictive Maintenance, Supply Chain Visibility
- **Financial Services**: Risk Analytics, Fraud Detection, Customer 360
- **Healthcare**: Clinical Analytics, Patient 360, Compliance
- **Technology/SaaS**: Product Analytics, Usage Metrics, Churn Prediction
- **General (cross-industry)**: Cortex Analyst, Cortex Search, Dynamic Tables, Data Sharing, Streamlit Apps

### Recursos para participantes

- Cortex Code con skills preinstalados
- Trial account con datos precargados
- Cheat sheet de comandos Coco y patterns comunes
- Acceso a Snowflake Marketplace datasets relevantes

---

## 8. Problem Statements Modelo (por industria)

### CPG/Retail

1. **Trade Promotion Intelligence** — Agente SI que analice ROI de promociones por canal/SKU
2. **Stock Availability Optimization** — Alertas de out-of-stock con datos de inventario + senales externas
3. **HORECA Channel Assignment** — Asignacion optima de productos a canal Hotel/Restaurant/Cafe
4. **Customer 360 para KAM** — Vista unificada por Key Account con sell-in/sell-out
5. **Field Sales Copilot** — Agente que briefea antes de cada visita con KPIs del POS

### Financial Services

1. **Risk Dashboard** — Visualizacion de riesgo crediticio con alertas tempranas
2. **AML Transaction Monitor** — Deteccion de patrones sospechosos con Cortex AI
3. **Customer Lifetime Value** — Prediccion de CLV con segmentacion automatica

### Manufacturing

1. **Predictive Maintenance** — Anticipar fallos usando IoT + historial de mantenimiento
2. **Quality Control Agent** — Agente que identifica causas raiz de defectos
3. **Supply Chain Visibility** — Dashboard de lead times y riesgos logisticos

### Technology/SaaS

1. **Product Usage Intelligence** — Analisis de feature adoption y churn signals
2. **Customer Health Score** — Score automatizado con datos de producto + soporte
3. **Competitive Intel Agent** — Busqueda y analisis de menciones competitivas

### General

1. **Executive Briefing Agent** — Agente que genera resumen diario para C-level
2. **Document Intelligence** — Extraccion y analisis de informacion de PDFs/contratos
3. **Data Marketplace Discovery** — Enriquecer datos internos con datasets del Marketplace

---

## 9. Premiacion y Reconocimiento

| Premio | Nivel |
|--------|-------|
| Snowstore credits ($100-500) | Equipo ganador |
| Asset publicado en ISF | Top 2-3 soluciones |
| Spotlight en team call/newsletter | Todos los participantes |
| "Cocothon Champion" badge | SE que organiza exitosamente |
| Fast-track a POC formal | Soluciones con buy-in del cliente |

---

## 10. Post-evento y Scaling

1. **Documentacion (misma semana):** SE Champion documenta solucion como skill o demo replicable
2. **Handoff a FCTO:** Si es generalizable, el FCTO la vetea e incorpora al ISF
3. **Follow-up con cliente (1-2 semanas):** AE usa resultado como hook para proximos pasos
4. **Retroalimentacion:** Survey a participantes para iterar formato
5. **Playback regional:** Compartir resultados en team calls para inspirar replicacion

---

## 11. Gobernanza y Escalamiento

### Quien puede convocar

- **Industry Principals / Field CTOs** — Regional/multi-cuenta
- **SEs individuales** — Per-account (formato Sprint)
- **Sales Leadership** (con apoyo de FCTO) — Eventos tipo "Industry Day"

### Frecuencia sugerida

- 1 Cocothon regional por quarter por vertical
- Cocothons per-account ilimitadas (formato Sprint)

### Metricas de exito

- Numero de skills/assets generados e incorporados al ISF
- Pipeline influenciado post-evento ($)
- Adoption lift en cuentas participantes (creditos consumidos)
- NPS de participantes (clientes + internos)
- Numero de demos replicadas en otras regiones

---

## 12. Checklist de Preparacion

```
[ ] Problem statements definidos y validados con AE
[ ] Datos del cliente cargados en Snowflake (o dataset sintetico)
[ ] Trial accounts creadas para participantes externos
[ ] Cortex Code instalado y probado por todos los SEs
[ ] Skills ISF relevantes preinstalados y funcionando
[ ] Jurado confirmado
[ ] Premios asegurados (Snowstore budget aprobado)
[ ] Sala/virtual setup confirmado (buena wifi si presencial)
[ ] Template de documentacion post-evento listo
[ ] Calendar invite con agenda enviado (2 semanas antes)
[ ] Coco Profile configurado con datos del evento
[ ] Datasets de prueba validados (que las queries funcionen)
```

---

## 13. Lecciones Aprendidas (Paris, Abril 2026)

1. **2 horas es suficiente** para equipos con Cortex Code — 7 teams entregaron 7 soluciones completas
2. **Mezclar SEs con partners** (Meltone, Jakala) enriquece las soluciones y fortalece el ecosistema
3. **Problem statements concretos** son clave — no dejarlo abierto, dar contexto de negocio claro
4. **El pitch de 3 min** fuerza a los equipos a focalizarse en el valor, no en la tecnica
5. **Jurado mixto** (liderazgo + especialistas + partners) da credibilidad y perspectiva diversa
6. **Seguimiento inmediato** — compartir demos para reuso y feedback para mejorar el formato

---

## 14. Referencia: ISF (Industry Solutions Framework)

El ISF es el repositorio central de Snowflake para:

- **Demos orientadas a outcomes** de negocio por industria
- **Skills de Cortex Code** reutilizables
- **Assets de GTM** (decks, battle cards, qualification questions)

### Motores de alimentacion

| Motor | Descripcion |
|-------|-------------|
| **Push Motion (Cocothon)** | Hackathons estructurados que producen soluciones validadas en campo |
| **Organic Motion** | Demos hero desarrolladas por SEs que cumplen umbral de calidad |

### Flujo de incorporacion al ISF

1. SE construye demo alineada a necesidades del cliente y al framework ISF
2. SE contacta al FCTO para agregar al ISF
3. FCTO valida calidad (outcomes, funcionalidad, alineacion GTM)
4. Se agrega al ISF para uso cross-regional

---

## 15. Modalidad Customer-Led (Casos propuestos por participantes)

### Cuando usar

- Eventos **Extended** (1-3 dias) donde hay tiempo para curar y preparar
- Clientes con **muchos stakeholders** (20+ participantes) de areas diversas
- Cuando el **ownership del participante** es mas importante que la direccion del tema
- Clientes que ya tienen problemas claros identificados y quieren resolverlos
- Eventos donde el objetivo es **activacion masiva** (muchas areas, muchos use cases)

### Proceso de Inscripcion

Crear un formulario/Excel con los siguientes campos por participante:

| Campo | Requerido | Descripcion |
|-------|-----------|-------------|
| Nombre completo | Si | Nombre del participante |
| Area/Departamento | Si | Donde trabaja dentro de la organizacion |
| Cargo | Si | Titulo o rol |
| Caso de uso propuesto | Si | Descripcion en 1-3 lineas del problema que quiere resolver |
| Datos disponibles | Opcional | Que datos tiene o podria traer para trabajar |
| Nivel tecnico | Opcional | Basico / Intermedio / Avanzado |
| Estado | Admin | DENTRO / LISTA DE ESPERA / RESERVADO |

### Algoritmo de Clasificacion por Afinidad

1. **Leer todos los casos** y extraer palabras clave / area funcional
2. **Agrupar por similitud tematica** — buscar patrones:
   - Mismo proceso de negocio (cobranzas, nomina, despacho)
   - Misma area funcional (finanzas, RRHH, logistica)
   - Mismo tipo de solucion (agente conversacional, dashboard, automatizacion)
3. **Formar categorias** de 3-10 personas cada una (objetivo: 5-9 categorias totales)
4. **Validar** que cada categoria tiene sentido como "mesa de trabajo" — los participantes deben poder colaborar en un tema comun

### Categorias Funcionales Comunes (cross-industria)

| Categoria | Casos tipicos |
|-----------|---------------|
| **Finanzas / Tesoreria / Compras** | Conciliaciones, proyecciones, informacion tributaria, auditoria, procesos contractuales |
| **Talento Humano / Nomina / RRHH** | Anomalias de nomina, planes de formacion, gestion de beneficios, onboarding |
| **Logistica / Supply Chain / Operaciones** | S&OP, picking/almacenamiento, despacho, inventario, capacidades de recibo |
| **Comercial / Ventas / CRM** | Agentes 24/7, cross-sell, entrenamiento comercial, simuladores, pedidos |
| **Calidad / Producto / QA** | Aseguramiento de datos, QA automatizado, trazabilidad, ambientes replicables |
| **Analytics / BI / Reporting** | NL querying (agentes), dashboards, estandarizacion de reportes, tableros operativos |
| **Servicio al Cliente / CX** | PQRS automatizadas, analisis de experiencia, calificacion de atencion |
| **AI / Automatizacion / Agentes** | Agentes multimodales, automatizacion de procesos, respuestas inteligentes |

> **Nota:** Estas categorias son un punto de partida. Adaptarlas segun la industria y los casos reales recibidos.

### Reglas de Formacion de Equipos

```
num_equipos = ceil(personas_en_categoria / 3.5)
```

| Personas en categoria | Equipos resultantes | Tamano |
|-----------------------|--------------------:|--------|
| 3 | 1 | 3 |
| 4 | 1 | 4 |
| 5-6 | 2 | 2-3 + 3 |
| 7 | 2 | 3 + 4 |
| 8-9 | 3 | 3 + 3 + 2-3 |
| 10 | 3 | 3 + 3 + 4 |

**Combinacion de categorias pequenas:** Si una categoria tiene <3 personas, combinar con la categoria mas afin (ej: "Logistica" + "Comercial" si ambos tratan de cadena de valor).

### Manejo de Duplicados

Cuando 2+ personas proponen esencialmente el mismo caso de uso:
1. **Detectar** — Buscar misma area + mismas palabras clave + mismo proceso
2. **Fusionar** — Asignar al mismo equipo
3. **Co-liderazgo** — Ambos son co-owners del proyecto (no hay "lider unico")
4. **Beneficio** — El equipo resultante tiene mas contexto y puede entregar algo mas robusto

### Manejo de Escala (20+ participantes)

| Situacion | Solucion |
|-----------|----------|
| **Participante con caso concreto** | Asignar a equipo por afinidad tematica |
| **Participante sin caso definido** | Pool de refuerzo — asignar a equipos que necesiten mas manos |
| **Cupo reservado (stakeholder prioritario)** | Guardar espacio; cuando defina su caso, integrarlo al equipo apropiado |
| **Lista de espera** | Priorizar a quienes tienen caso concreto; los demas entran como refuerzo si hay cancelaciones |
| **Mas de 15 equipos** | Considerar dividir en tracks paralelos con jurados separados |

### Ventajas vs. Snowflake-Led

| Dimension | Snowflake-Led | Customer-Led |
|-----------|---------------|--------------|
| Ownership | Bajo (Snowflake define el problema) | Alto (participante resuelve SU problema) |
| Preparacion de datos | SE genera sinteticos o prepara demo | Participantes traen o identifican sus datos |
| Escalabilidad | Limitada (SE debe dominar cada PS) | Alta (cada equipo es autonomo en su tema) |
| Control de calidad | Alto (PS estan validados) | Variable (algunos casos pueden ser ambiguos) |
| Post-evento | Demo generable pero puede no escalar | Solucion directamente aplicable al negocio |
| Engagement post | Moderado | Alto (quieren seguir porque es su problema) |

---

*Documento creado como parte del ISF — Industry Solutions Framework, Snowflake 2026.*
