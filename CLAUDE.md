# Project context

## Pendientes activos
Lee `PENDING.md` al inicio de cada sesión para recargar el plan de acción y el estado de los pendientes (Premiere Pro `.jsx`, MCP server para Premiere).

## Skills activos
35 skills habilitados en `.claude/skills/` (6 base + Koda Stack 10 + Higgsfield 19). Otros 5 desactivados en `.claude/skills/_disabled/`.

## MCP servers
`.mcp.json` configura Playwright MCP (`@playwright/mcp@latest`) para automatizar browsers. Necesario para los skills Higgsfield de generación.

## Cuentas externas
Higgsfield AI (cuenta del usuario) — requerida para los skills `higgsfield-image-auto`, `seedance-auto-generate`, `ugc-video-auto`, etc. La sesión del browser debe estar logueada en higgsfield.ai cuando se invocan esos skills.

## Marcas / proyectos activos
Cada marca o proyecto tiene su DNA en `brands/<slug>.md`. **Antes de generar contenido creativo (script, brief, concept, art-direction, generate, storyboard, assemble, publish, etc.) leer el archivo de la marca correspondiente.** Si el usuario no aclara para qué marca es, preguntar. Ver `brands/INDEX.md` para el árbol completo de marcas y la plantilla.

Resumen rápido:
- **THE BLACKCAT Media** — agencia madre del usuario.
- **Cluster eléctrico Chicago** — Powering Chicago, The Power of Better, IBEW Local 134, ECA, IN-Tech.
- **NUMEN Artes Vivas** — proyecto propio sobre música. Sub-proyectos: Coro de Cámara de Córdoba, Coro de Egresados del Zipoli, Coral Resonancia.
- **Otros:** NDC – UNOPS, Rob Johnson Communications, Rotary Club Córdoba.

## Branch
Trabajamos en `claude/premiere-pro-editing-LV0nT`.
