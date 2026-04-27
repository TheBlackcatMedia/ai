# Project context

## Pendientes activos
Lee `PENDING.md` al inicio de cada sesión para recargar el plan de acción y el estado de los pendientes (Premiere Pro `.jsx`, MCP server para Premiere).

## Skills activos
35 skills habilitados en `.claude/skills/` (6 base + Koda Stack 10 + Higgsfield 19). Otros 5 desactivados en `.claude/skills/_disabled/`.

## MCP servers
`.mcp.json` configura Playwright MCP (`@playwright/mcp@latest`) para automatizar browsers. Necesario para los skills Higgsfield de generación.

## Cuentas externas
Higgsfield AI (cuenta del usuario) — requerida para los skills `higgsfield-image-auto`, `seedance-auto-generate`, `ugc-video-auto`, etc. La sesión del browser debe estar logueada en higgsfield.ai cuando se invocan esos skills.

## Branch
Trabajamos en `claude/premiere-pro-editing-LV0nT`.
