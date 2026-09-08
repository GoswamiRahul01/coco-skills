# 🏆 Cocothon Framework — Skill de Cortex Code

## ¿Qué es este skill?

El **Cocothon Framework** es un skill de Cortex Code Desktop que automatiza la planificación, ejecución y documentación de **Cocothons** — hackathons estructurados donde equipos de Snowflake + clientes/partners resuelven problemas de negocio reales usando Cortex Code.

## ¿Por qué existe?

En abril 2026, el equipo de Francia organizó la primera Cocothon formal durante el CPG Enablement Day. En solo **2 horas**, 7 equipos (SEs + partners como Meltone y Jakala) entregaron 7 use cases completos de CPG. El formato fue tan exitoso que Simon Contreras (Industry Principal) propuso replicarlo globalmente.

**El problema**: Organizar una Cocothon requiere:
- Preparar datos (sintéticos o reales)
- Configurar cuentas, roles, permisos
- Crear problem statements por industria
- Generar materiales de briefing
- Evaluar y documentar resultados
- Convertir soluciones ganadoras en skills replicables

**La solución**: Este skill automatiza TODO el proceso. Un AE o SE puede organizar una Cocothon end-to-end usando Cortex Code como facilitador.

## ¿Para quién es?

| Persona | Uso |
|---------|-----|
| **SE** | Organizar Cocothon per-account para activar una cuenta o demostrar valor |
| **AE** | Sponsor de evento, generar pipeline, fortalecer relación con cliente |
| **Industry Principal / FCTO** | Organizar Cocothons regionales multi-cuenta |
| **Sales Manager** | Incorporar Cocothons como mecanismo de enablement y GTM |

## ¿Cómo se usa?

### Instalación

1. Copiar la carpeta `cocothon-framework/` a tu directorio de skills de Coco Desktop
2. Configurar el profile `cocothon-organizer` en Coco Desktop
3. Listo — el skill se activa con triggers como "cocothon", "hackathon", "organizar cocothon"

### Flujo básico

```
Tú: "Quiero organizar una cocothon para [EMPRESA] en [INDUSTRIA]"

Coco: Presenta menú de configuración → 
      Genera datos sintéticos → 
      Prepara setup técnico →
      Genera materiales de briefing →
      [DÍA DEL EVENTO: facilita y evalúa] →
      Documenta resultados →
      Convierte soluciones en skills
```

### Ejemplo real

```
Tú: "Organiza una cocothon para Grupo Éxito, industria retail, 
     formato standard 2 horas, 5 equipos. Problem statements: 
     1) Optimización de inventario
     2) Personalización de ofertas
     3) Predicción de demanda
     4) Retail Media Network
     5) Supply Chain Visibility"

Coco: [Configura todo automáticamente y genera los 5 datasets]
```

## Estructura del skill

```
cocothon-framework/
├── SKILL.md                        # Skill principal (instrucciones para Coco)
├── README.md                       # Este documento
├── references/
│   └── cocothon-framework.md       # Marco de referencia completo
├── profiles/
│   └── cocothon-organizer.md       # Perfil para Coco Desktop
└── templates/
    └── (reservado para HTML/email templates futuros)
```

## Componentes

### 1. SKILL.md
El archivo principal que Cortex Code lee cuando se activa el skill. Contiene:
- Workflow de 6 pasos (Configuración → Datos → Setup → Materiales → Ejecución → Documentación)
- SQL de setup base para DB/schemas/roles
- Stopping points para validación con el organizador

### 2. references/cocothon-framework.md
Documento de referencia completo con:
- Definición y objetivos de la Cocothon
- Modalidades (Sprint, Standard, Extended)
- Roles y participantes
- Estructura del evento minuto a minuto
- Criterios de evaluación con pesos
- Problem statements modelo por industria (CPG, FinServ, Manufacturing, Tech, Healthcare)
- Premiación y reconocimiento
- Gobernanza y escalamiento
- Checklist de preparación
- Lecciones aprendidas del evento de París

### 3. profiles/cocothon-organizer.md
Perfil de Coco Desktop que configura al asistente como organizador de Cocothons. Incluye:
- Menú de configuración interactivo
- Capacidades específicas (datos, materiales, evaluación)
- Reglas de comportamiento (neutralidad, tiempo, documentación)
- Comandos rápidos (`/cocothon setup`, `/cocothon datos`, etc.)
- Criterios de evaluación con rúbrica detallada
- Templates de correo (invitación + resultados)

## Relación con el ISF

El **ISF (Industry Solutions Framework)** es el repositorio central de Snowflake para demos, skills, y assets de GTM. La Cocothon es uno de los dos mecanismos para alimentarlo:

```
                    ┌─────────────────────────────────┐
                    │  Industry Solutions Framework    │
                    │  (Repositorio central)           │
                    └──────────┬──────────────────────┘
                               │
              ┌────────────────┼────────────────┐
              │                                 │
    ┌─────────▼──────────┐         ┌───────────▼──────────┐
    │  Push Motion       │         │  Organic Motion      │
    │  (COCOTHON)        │         │  (Demos espontáneas) │
    │                    │         │                      │
    │  • Estructurado    │         │  • Individual        │
    │  • Multi-equipo    │         │  • Hero demos        │
    │  • Time-boxed      │         │  • Umbral calidad    │
    │  • Evaluado        │         │  • Sin deadline      │
    └────────────────────┘         └──────────────────────┘
```

## Métricas de éxito

Después de cada Cocothon, mide:

- **Assets generados**: # skills/demos incorporados al ISF
- **Pipeline**: $ influenciado post-evento
- **Adopción**: Lift en créditos consumidos en cuentas participantes
- **Satisfacción**: NPS de participantes
- **Replicación**: # veces que las demos se reusan en otras regiones

## Historial

| Fecha | Evento | Región | Resultado |
|-------|--------|--------|-----------|
| Abr 2026 | CPG Enablement Day | Francia | 7 use cases, 7 equipos, 2 horas |
| May 2026 | Propuesta QUADIS | España | Planificado para julio |
| (próximo) | ... | LATAM | Por definir |

## Contactos clave

- **Simon Contreras** — Industry Principal, promotor del concepto globalmente
- **Luca Bandini** — Field CTO CPG/Retail, sugirió agregar hackathon al Enablement Day
- **Nassim Kasdali** — Organizador original de la Cocothon París
- **Bachir Kane** — Principal SE, documentó y distribuyó resultados
- **Michael Ellert** — CPG Business Process expertise

## FAQ

**¿Puedo usar este skill para industrias fuera de CPG?**
Sí. El framework es multi-industria. Los problem statements se adaptan según el vertical.

**¿Necesito aprobación para organizar una Cocothon?**
Para formato Sprint (per-account), no. Para Standard o Extended, alinea con tu manager y el Industry Principal de tu región.

**¿Qué pasa con las soluciones ganadoras?**
Se documentan como skills y el FCTO las evalúa para incluirlas en el ISF. Si pasan el umbral de calidad, se hacen disponibles cross-region.

**¿Puedo hacerla sin partners?**
Sí, pero incluir partners (SIs, ISVs) enriquece las soluciones y fortalece el ecosistema. La de París incluyó Meltone y Jakala.

**¿Se necesitan datos reales del cliente?**
No necesariamente. El skill puede generar datos sintéticos representativos. Lo importante es que sean relevantes al problem statement.
