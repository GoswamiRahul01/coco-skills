# Categorías de Cocothon

Índice de sub-skills por categoría de negocio. Cada archivo contiene:

- **Modelo de datos completo** (CREATE TABLE DDL con tipos)
- **Volúmenes recomendados** de filas por tabla
- **Foreign Keys** y relaciones
- **Patrones intencionales** (anomalías y escenarios a insertar para que los equipos descubran)
- **Validation queries** para verificar la carga de datos
- **2 ejemplos de output** (Streamlit dashboard + Cortex Agent)

---

## Categorías Disponibles

| # | Categoría | Archivo | Industrias típicas |
|---|-----------|---------|-------------------|
| 1 | [Finanzas](finanzas.md) | `categories/finanzas.md` | Banca, Seguros, Retail, Manufactura |
| 2 | [RRHH](rrhh.md) | `categories/rrhh.md` | Todas |
| 3 | [Logística](logistica.md) | `categories/logistica.md` | Retail, CPG, Manufactura, E-commerce |
| 4 | [Comercial](comercial.md) | `categories/comercial.md` | Retail, CPG, Tech, Servicios |
| 5 | [Calidad](calidad.md) | `categories/calidad.md` | Manufactura, Farma, Alimentos, Automotriz |
| 6 | [Analytics](analytics.md) | `categories/analytics.md` | Todas |
| 7 | [Servicio al Cliente](servicio-cliente.md) | `categories/servicio-cliente.md` | Telecomunicaciones, Banca, Retail, SaaS |

---

## Uso en el Skill

En el Paso 2 del workflow, después de definir los problem statements:

```
**Load**: categories/{categoria}.md para el modelo de datos y scripts del equipo
```

El SE usa la categoría correspondiente para:
1. Crear las tablas en el trial account del equipo
2. Insertar datos sintéticos con los volúmenes recomendados
3. Incluir los patrones intencionales (anomalías) que los equipos deben descubrir
4. Validar con las queries de verificación antes del evento

---

## Combinaciones Comunes

Para cocothons multi-equipo, es normal combinar varias categorías:

- **Empresa Retail**: Comercial + Logística + Servicio al Cliente
- **Manufactura**: Calidad + Logística + Finanzas
- **Servicios Financieros**: Finanzas + Analytics + Servicio al Cliente
- **Tech/SaaS**: Analytics + Comercial + RRHH
- **CPG**: Comercial + Logística + Calidad
