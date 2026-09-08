# Anti-Patterns: Errores Comunes en Cocothons

Guía de errores frecuentes al organizar Cocothons y cómo evitarlos.

---

## 1. Equipos

| Evitar | Por qué | Hacer en su lugar |
|--------|---------|-------------------|
| Equipos de 2 personas | No hay suficiente diversidad de perspectivas; si uno se atasca, no hay backup | Mínimo 3 personas por equipo |
| Equipos de 5+ personas | Demasiada coordinación, alguien siempre queda sin rol activo | Máximo 4 personas; dividir si hay más |
| Mezclar áreas sin relación (ej: RRHH + Logística) | No encuentran un PS común; la colaboración es forzada | Agrupar por afinidad temática o proceso de negocio |
| Asignar personas a un tema que no les interesa | Baja motivación, presentaciones pobres, no hay follow-up | Dejar que propongan su caso (Customer-Led) o dar opciones |
| Un solo SE para 10+ equipos | El SE no puede dar soporte a todos; equipos se atascan | 1 SE por cada 3-4 equipos máximo |
| No definir roles dentro del equipo | Todos hacen lo mismo o nadie toma el liderazgo | Sugerir: 1 presenter + 1 builder + 1 analyst |

---

## 2. Problem Statements

| Evitar | Por qué | Hacer en su lugar |
|--------|---------|-------------------|
| PS vagos ("mejorar procesos", "usar IA") | El equipo no sabe por dónde empezar; resultado genérico | PS concreto con métrica: "Reducir WMAP de forecasting de 37% a 25%" |
| PS demasiado técnicos para business users | Participantes no-técnicos se desconectan | Redactar en lenguaje de negocio, no en jerga SQL/ML |
| PS sin datos disponibles | El equipo gasta todo el tiempo buscando datos en vez de construir | Validar ANTES del evento que hay datos cargados para cada PS |
| PS que no se puede resolver en el tiempo | Frustración; nadie termina; demo incompleta | Scoping: ¿se puede mostrar un MVP en 2h? Si no, reducir alcance |
| Todos los equipos con el mismo PS | No hay variedad; demos repetitivas | Mínimo 3 PS distintos para 5+ equipos |
| PS sin "so what" (impacto de negocio) | El jurado no puede evaluar Business Impact | Incluir contexto: quién se beneficia, cuánto pierde hoy, qué gana |

---

## 3. Datos

| Evitar | Por qué | Hacer en su lugar |
|--------|---------|-------------------|
| No preparar datos antes del evento | El 80% del tiempo se va en data wrangling | Datos pre-cargados y validados (SELECT funciona) |
| Datasets con <100 filas | Las funciones de Cortex AI no muestran valor; análisis trivial | Mínimo 10K filas en tablas de hechos |
| No validar que Cortex AI funciona en la región | Equipos descubren en vivo que los modelos no están disponibles | Dry-run 48h antes: `SELECT SNOWFLAKE.CORTEX.COMPLETE('claude-haiku-4-5','test')` |
| No verificar modelos LLM disponibles | Modelos del HOL no existen en la cuenta del evento | Verificar disponibilidad Y habilitar CORTEX_ENABLED_CROSS_REGION = 'ANY_REGION' |
| Datos con PII real del cliente | Riesgo de compliance; participantes ven datos sensibles | Usar datos sintéticos o anonimizados; nunca PII real en un hackathon |
| No documentar el modelo de datos | Equipos pierden 30 min entendiendo las tablas | Proveer ERD simplificado + descripción de columnas clave |

---

## 4. Logística

| Evitar | Por qué | Hacer en su lugar |
|--------|---------|-------------------|
| No dar cheat sheet de CoCo | Participantes no saben qué comandos usar; piden ayuda constante | Cheat sheet 1 página: prompts comunes + patrones SQL + tips |
| No hacer dry-run del setup | Descubres problemas de permisos/acceso el día del evento | Dry-run completo 48-72h antes con al menos 1 participante |
| Agenda sin buffers | Todo se retrasa; no hay tiempo para problemas técnicos | Agregar 5-10 min de buffer entre bloques; CoCo TIME con 15 min extra |
| No tener plan B si falla internet/cuenta | Evento se cancela o pierde impulso | Hotspot backup + SQL scripts offline + datos en CSV como fallback |
| No instalar CoCo Desktop antes | Participantes gastan 15-20 min instalando el día del evento | Enviar instrucciones de instalación 3+ días antes; verificar la víspera |
| Evento presencial sin pantalla compartida | El MC no puede mostrar demos ni guiar visualmente | Proyector/pantalla + cable/adaptador + zoom backup para share |

---

## 5. Evaluación

| Evitar | Por qué | Hacer en su lugar |
|--------|---------|-------------------|
| Jurado sin rúbrica previa | Evaluación subjetiva e inconsistente | Entregar rúbrica impresa ANTES de las demos |
| Evaluar sin haber visto todas las demos | Sesgo hacia las primeras/últimas presentaciones | Jurado puntúa después de TODAS las demos; no durante |
| No documentar resultados el mismo día | Se pierde el momentum; detalles se olvidan | Generar documento de resultados dentro de las 24h |
| Tiempo de demo desigual (unos 3 min, otros 10 min) | Injusto; equipos con más tiempo parecen mejores | Timer estricto: 5 min demo + 2 min Q&A = 7 min máx por equipo |
| No dar feedback constructivo | Equipos no saben cómo mejorar su solución | Jurado da 1 fortaleza + 1 sugerencia por equipo |

---

## 6. Post-evento

| Evitar | Por qué | Hacer en su lugar |
|--------|---------|-------------------|
| No hacer follow-up en 1-2 semanas | El cliente se enfría; la solución queda en el aire | Agendar sesión de follow-up antes de terminar el evento |
| No convertir soluciones ganadoras en skills | Se pierde el trabajo; no se puede replicar | Documentar como skill de CoCo dentro de la semana |
| No compartir grabaciones/screenshots | Participantes no pueden revisar lo que hicieron | Grabar demos; compartir screenshots del dashboard final |
| Perder el momentum con el cliente | El evento fue un éxito pero no lleva a nada | AE debe tener next steps claros: POC, trial extendido, propuesta |
| No reportar internamente (team call, Slack) | Otros SEs no se enteran; no se replica | Compartir resultados en team call + Slack channel |
| No medir adopción post-evento | No sabes si la Cocothon generó valor real | Revisar en 30 días: ¿el cliente activó la cuenta? ¿consume créditos? |
