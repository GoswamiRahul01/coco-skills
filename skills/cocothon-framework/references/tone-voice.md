# Guía de Tono y Voz por Audiencia

Cada output del Cocothon tiene una audiencia específica. Esta guía define 3 personas y cuándo usar cada tono.

---

## Personas

| Persona | Rol en el Cocothon | Enfoque | Vocabulario |
|---------|-------------------|---------|-------------|
| **AE** (Account Executive) | Configura, diseña, vende el valor | Negocio, ROI, impacto, pipeline | Revenue, adoption, time-to-value, stakeholders |
| **SE** (Solutions Engineer) | Configura, diseña, ejecuta técnicamente | Técnico, SQL, configs, arquitectura | Cortex, warehouse, credits, RBAC, stages |
| **Cliente** (Participante/Sponsor) | Recibe, acepta, participa | Profesional, beneficios claros, sin jerga interna | Resultados, equipo, solución, datos, innovación |

---

## Regla de Asignación por Output

| Output | Tono |
|--------|------|
| Propuesta inicial (email al cliente) | Cliente |
| Correo de resultados post-evento | Cliente |
| Agenda HTML (vista participante) | Cliente |
| Briefing interno pre-evento | SE |
| Cheat sheet / Tutorial técnico | SE |
| Countdown checklist (setup técnico) | SE |
| Resumen ejecutivo para deal review | AE |
| Email de follow-up para pipeline | AE |
| Slide deck de propuesta comercial | AE |

---

## Ejemplos Comparativos

La misma información expresada en cada tono:

### Ejemplo 1: Describir qué es un Cocothon

**Tono AE:**
> Un Cocothon es un evento colaborativo de alto impacto donde equipos del cliente construyen soluciones reales con datos propios en pocas horas. Genera pipeline, acelera adopción y posiciona a Snowflake como plataforma estratégica de datos e IA.

**Tono SE:**
> Un Cocothon es un hackathon guiado donde equipos trabajan con Cortex Code sobre sus datos reales en una cuenta Snowflake. Cada equipo tiene un problem statement, datos pre-cargados, y acceso a Cortex AI functions (COMPLETE, EXTRACT, CLASSIFY). El SE provee soporte técnico en vivo y valida que las queries ejecuten correctamente.

**Tono Cliente:**
> Un Cocothon es una jornada de innovación donde sus equipos exploran soluciones prácticas con sus propios datos. En pocas horas, cada equipo desarrolla un prototipo funcional que resuelve un desafío real de su área, con acompañamiento técnico de Snowflake.

---

### Ejemplo 2: Comunicar resultados del evento

**Tono AE:**
> 11 equipos participaron, 10 entregaron soluciones funcionales. El engagement fue excepcional — el sponsor ejecutivo felicitó públicamente al equipo de datos. Next step: agendar revisión técnica de las 3 soluciones ganadoras para evaluar productivización. Oportunidad de upsell: Cortex AI + SPCS.

**Tono SE:**
> 11 equipos ejecutaron en paralelo sobre la cuenta JAMAR_COCOTHON. Consumo total: 141.25 créditos (dentro del presupuesto de 150). Stack usado: Cortex AI (COMPLETE, EXTRACT), Streamlit, Snowpark Python. 3 soluciones requieren compute pools para SPCS si van a producción. Recomendación: migrar las queries de Cortex Code a stored procedures para scheduling.

**Tono Cliente:**
> Felicitaciones por el éxito del evento. Sus 11 equipos demostraron un nivel de creatividad y compromiso extraordinario. Las soluciones presentadas — desde auditoría inteligente de contratos hasta predicción de demanda — muestran el potencial real de sus datos. Adjuntamos el documento de resultados con los detalles de cada equipo y los próximos pasos sugeridos.

---

### Ejemplo 3: Explicar el consumo de créditos

**Tono AE:**
> El evento consumió 141 créditos (~$420 USD en lista). Excelente ratio costo/valor: 11 soluciones funcionales por menos de $40 por equipo. Esto demuestra que Snowflake permite innovar rápido sin inversiones masivas.

**Tono SE:**
> Consumo breakdown: Cortex AI = 89 créditos (63%), Warehouse compute = 38 créditos (27%), SPCS = 14 créditos (10%). El pico fue entre 10:00-12:00 cuando todos los equipos corrieron inference en paralelo. Recomendación para próximo evento: usar warehouse size M en vez de L para la primera hora (warm-up).

**Tono Cliente:**
> Durante el evento se utilizaron recursos de cómputo equivalentes a aproximadamente 141 créditos de Snowflake. Este consumo cubrió las soluciones de los 11 equipos, incluyendo procesamiento de datos e inteligencia artificial. El consumo se mantuvo dentro del presupuesto acordado.

---

## Reglas Generales

1. **Nunca usar jerga interna de Snowflake con el Cliente.** No mencionar: RBAC, stages, warehouses, credits (usar "recursos de cómputo"), SPCS, UDFs, Cortex Code.
2. **El AE necesita números de impacto.** Siempre incluir: cuántos equipos, cuántas soluciones, ratio costo/valor, next steps de pipeline.
3. **El SE necesita detalles técnicos accionables.** Incluir: stack usado, consumo por servicio, configs relevantes, recomendaciones de sizing.
4. **El Cliente necesita claridad y confianza.** Frases cortas, beneficios concretos, sin acrónimos sin explicar. El tono es profesional pero cálido.
5. **Cuando en duda, usar tono Cliente.** Es el más seguro — un AE entiende tono Cliente, pero un Cliente no entiende tono SE.
