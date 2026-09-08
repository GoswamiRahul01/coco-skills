# Error Fallbacks — Recuperación ante Fallos

Principio rector: **Siempre producir un output.** Degradar gracefully, nunca fallar silenciosamente.

---

## Errores de Cortex AI

| Error | Síntoma | Recovery |
|-------|---------|----------|
| Modelo LLM no disponible | `Unknown model` o `Model not supported in region` | 1. Verificar CORTEX_ENABLED_CROSS_REGION = 'ANY_REGION'. 2. Probar otro modelo: claude-haiku-4-5 → llama3.3-70b → mistral-large2. 3. Si ninguno funciona, cambiar de región (AWS_US_WEST_2 tiene todos). |
| AI credits agotados | `Insufficient credits` o rate limit | 1. Verificar que NO es un trial self-service (límite ~10 cr/día). 2. Contactar al CSM para ampliar límite. 3. Workaround: reducir tamaño de prompts, usar modelos más baratos (haiku < sonnet). |
| Cortex COMPLETE timeout | Query se cuelga >60s | 1. Reducir tamaño del input (menos tokens). 2. Usar warehouse más grande temporalmente. 3. Dividir el prompt en partes. |
| Embeddings fallan | Error en AI_EMBED o Cortex Search | 1. Verificar modelo de embedding disponible. 2. Revisar tamaño del texto (<512 tokens recomendado por chunk). 3. Reducir corpus size. |

---

## Errores de Snowflake Intelligence

| Error | Síntoma | Recovery |
|-------|---------|----------|
| SI no aparece en UI | Menú lateral sin opción "Snowflake Intelligence" | Inicializar desde Snowsight > menú lateral > Snowflake Intelligence > Get Started. NO basta con crear la DB manualmente. |
| Agent no visible en UI | Agent existe (SHOW AGENTS) pero no aparece en SI chat | El agent DEBE estar en SNOWFLAKE_INTELLIGENCE.AGENTS schema. Moverlo ahí. |
| Agent responde con errores | "I cannot answer that" o respuestas vacías | 1. Verificar que las tablas referenciadas existen y tienen datos. 2. Verificar permisos del owner role sobre las tablas. 3. Verificar que la semantic view es válida (DESCRIBE SEMANTIC VIEW). |

---

## Errores de Setup Técnico

| Error | Síntoma | Recovery |
|-------|---------|----------|
| Permisos insuficientes | `Insufficient privileges` en CREATE o SELECT | 1. Verificar que el role tiene USAGE en DB + schema. 2. GRANT los privilegios faltantes. 3. Si es trial, usar SYSADMIN (no crear roles custom). |
| Warehouse suspendido | Queries no ejecutan, timeout | 1. Verificar AUTO_RESUME = TRUE. 2. ALTER WAREHOUSE ... RESUME. 3. Si es créditos agotados, contactar soporte. |
| Trial expirado | Login falla, "account suspended" | 1. Verificar vigencia (solicitar extensión si es posible). 2. Crear nuevo trial si hay tiempo. 3. Usar cuenta interna como fallback. |
| SQL de datos falla | Error en CREATE TABLE o INSERT | 1. Verificar sintaxis (comas, tipos). 2. Verificar que el schema existe. 3. Ejecutar statement por statement (no todo junto). |

---

## Errores de Participantes

| Error | Síntoma | Recovery |
|-------|---------|----------|
| CoCo Desktop no instalado | Participante no puede ejecutar | Alternativa: Snowflake Notebooks (browser-based, no requiere instalación). Compartir SQL snippets por chat. |
| No puede conectarse a Snowflake | Error de auth, "incorrect credentials" | 1. Verificar email/password correctos. 2. Resetear password si es trial. 3. Verificar que la cuenta no bloqueó IPs. |
| Participante perdido ("no sé qué hacer") | No avanza, no pregunta | 1. SE circula y pregunta proactivamente. 2. Dar prompt starter específico para su PS. 3. Pair programming: SE guía los primeros 5 min. |
| Grupo no funciona (conflicto o desconexión) | Miembros trabajan individualmente, no colaboran | 1. Redistribuir roles: presenter/builder/analyst. 2. Dar tarea específica a cada persona. 3. En último caso, fusionar con otro equipo. |

---

## Errores de Evento (Logística)

| Error | Síntoma | Recovery |
|-------|---------|----------|
| Internet caído (presencial) | Nadie se conecta | 1. Hotspot backup (4G/5G). 2. Si es imposible conectarse, pivotar a "diseño en papel" + demo post-evento. 3. Si se restaura, dar 15 min extra de build. |
| Proyector/pantalla falla | No se puede mostrar la demo de apertura | 1. Compartir pantalla del MC por Zoom/Teams a los laptops. 2. Demo desde el laptop del MC con participantes alrededor. |
| Participante clave no llega | El sponsor o un miembro crítico falta | 1. No cancelar. 2. Redistribuir si es participante de equipo. 3. Si es jurado, pedir al SE senior que tome ese rol. |
| Evento se atrasa >20 min | Los bloques se comprimen | 1. Reducir buffer de Prep Demos (15→10 min). 2. Reducir Demo Day (5 min/equipo → 3 min/equipo). 3. NUNCA reducir CoCo TIME — es el bloque core. |

---

## Principios de Degradación Graceful

1. **Prioridad 1**: Que los equipos puedan CONSTRUIR (CoCo TIME es sagrado)
2. **Prioridad 2**: Que los equipos puedan PRESENTAR (Demo Day es obligatorio)
3. **Prioridad 3**: Que la evaluación sea justa (rúbrica + tiempos iguales)
4. **Sacrificable**: Apertura larga, tutorial CoCo (dar cheat sheet en su lugar), premiación elaborada

Si NADA funciona técnicamente:
- Pivot a "Design Sprint": equipos diseñan la solución en papel/slides
- SE construye el MVP después basado en el diseño
- Se agenda sesión de follow-up para ver la demo funcionando
