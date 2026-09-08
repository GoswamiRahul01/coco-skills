# Generación Paralela de Outputs

Cuando hay 3+ outputs HTML pendientes (típicamente Pasos 4-5), lanzar generación en paralelo para reducir tiempo de espera.

---

## Cuándo Activar

- Paso 4 (Propuesta) + Paso 5 (Tutorial + Agenda interna) están pendientes
- El JSON de contexto está completo y aprobado por el AE
- No hay dependencias entre los outputs (cada uno es self-contained)

---

## JSON de Contexto Compartido

Antes de lanzar la generación paralela, construir un único JSON de contexto que se comparte idéntico entre los 3 Task calls:

```json
{
  "empresa": "Nombre del Cliente",
  "industria": "Sector",
  "fecha": "2026-MM-DD",
  "modalidad": "standard|fullday|extended",
  "formato": "presencial|virtual|hibrido",
  "idioma": "es|en",
  "ownership": "snowflake-led|customer-led|mix",
  "num_equipos": 5,
  "equipos": [
    {
      "nombre": "Equipo Finanzas",
      "categoria": "finanzas",
      "miembros": ["Persona A", "Persona B", "Persona C"],
      "problem_statement": "Conciliación automática de pagos",
      "datos": "tabla PAYMENTS + tabla INVOICES"
    }
  ],
  "agenda": {
    "inicio": "09:00",
    "fin": "13:00",
    "bloques": [
      {"hora": "09:00", "duracion": 15, "actividad": "Apertura"},
      {"hora": "09:15", "duracion": 30, "actividad": "Tutorial Cortex Code"},
      {"hora": "09:45", "duracion": 120, "actividad": "Hackathon"},
      {"hora": "11:45", "duracion": 30, "actividad": "Presentaciones"},
      {"hora": "12:15", "duracion": 15, "actividad": "Evaluación y Premios"}
    ]
  },
  "cuentas": "hol|existente|demo",
  "region": "AWS_US_WEST_2",
  "jurado": ["Sponsor Cliente", "SE Lead"],
  "premios": "Snowstore $200 + fast-track POC",
  "branding": {
    "primary": "#11567F",
    "accent": "#29B5E8",
    "fonts": "Montserrat + Lato"
  }
}
```

---

## Instrucciones de Ejecución

Lanzar **3 Task calls en paralelo** en una sola respuesta. Cada Task recibe:
1. El JSON de contexto completo (arriba)
2. El template HTML correspondiente (de `templates/`)
3. Instrucciones específicas del output

### Task 1: Agenda HTML (Propuesta para el Cliente)

```
Genera la propuesta/invitación HTML para el cliente usando:
- Template: templates/cocothon_agenda_template.html
- Contexto: {JSON_CONTEXTO}
- Tono: CLIENTE (negocio, valor, sin jerga técnica)
- Output: {directorio}/YYYY-MM-DD_cocothon_{empresa}_agenda.html

La propuesta debe:
- Explicar qué es una Cocothon en lenguaje de negocio
- Mostrar la agenda visual del evento
- Listar problem statements en lenguaje del cliente
- Incluir logística (fecha, hora, lugar)
- Branding Snowflake (colores, tipografía, logo)
- Ser self-contained e imprimible
- Mencionar track record
```

### Task 2: Tutorial HTML

```
Genera el tutorial/guía para participantes usando:
- Template: templates/tutorial_template.html
- Contexto: {JSON_CONTEXTO}
- Tono: MIXTO (accesible pero con SQL real)
- Output: {directorio}/YYYY-MM-DD_tutorial_cocothon_{empresa}.html

El tutorial debe:
- Explicar Cortex Code y comandos básicos
- Incluir scripts SQL iniciales por equipo
- Mostrar ejemplos de prompts efectivos
- Incluir cheat sheet de funciones AI (AI_COMPLETE, SUMMARIZE, etc.)
- Cada equipo ve su tabla/schema específico
```

### Task 3: Propuesta Interna HTML (Briefing SE)

```
Genera el briefing técnico para SEs usando:
- Template: templates/resultados_template.html (adaptar para briefing)
- Contexto: {JSON_CONTEXTO}
- Tono: SE (técnico, directo, SQL y configs)
- Output: {directorio}/YYYY-MM-DD_briefing_cocothon_{empresa}.html

El briefing debe:
- Agenda con tiempos exactos
- Problem statements con contexto técnico de datos
- Modelo de datos (CREATE TABLE por equipo)
- Skills ISF recomendados por problem statement
- Setup checklist (warehouse, roles, AI verification)
- Tips de troubleshooting para el día del evento
```

---

## Patrón de Código (para el skill principal)

```markdown
<!-- En la respuesta del skill, lanzar así: -->

Lanzando generación paralela de 3 outputs...

[Task 1: Agenda HTML - instrucciones con JSON_CONTEXTO]
[Task 2: Tutorial HTML - instrucciones con JSON_CONTEXTO]
[Task 3: Briefing SE HTML - instrucciones con JSON_CONTEXTO]
```

El skill debe:
1. Construir el JSON de contexto a partir de la información recopilada en Pasos 1-3
2. Validar que el JSON tiene todos los campos requeridos
3. Lanzar los 3 Tasks en una sola respuesta (paralelo)
4. Esperar resultados y presentar los 3 outputs al AE para aprobación

---

## Campos Requeridos (Validación Pre-Launch)

Antes de lanzar la generación paralela, verificar que el JSON contiene:

| Campo | Requerido | Fallback |
|-------|-----------|----------|
| `empresa` | Si | - |
| `industria` | Si | - |
| `fecha` | Si | - |
| `modalidad` | Si | "standard" |
| `equipos` (array) | Si | - |
| `equipos[].nombre` | Si | - |
| `equipos[].problem_statement` | Si | - |
| `agenda.bloques` | Si | Template por modalidad |
| `branding` | No | Defaults Snowflake |

Si falta algún campo requerido, NO lanzar paralelo — volver al paso interactivo correspondiente.

---

## Post-Generación

1. Revisar los 3 outputs con QA checklist (`references/qa-checklist.md`)
2. Presentar al AE en orden: Propuesta → Tutorial → Briefing
3. Iterar según feedback (cada output es independiente, se puede regenerar uno sin afectar los otros)
4. Aplicar naming convention: `YYYY-MM-DD_` prefix en todos los archivos
