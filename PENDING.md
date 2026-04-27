# Pendientes y plan de acción

> Última actualización: 2026-04-27
> Rama de trabajo: `claude/premiere-pro-editing-LV0nT`

## Estado actual del setup

### Skills activos (6)
- `design-taste-frontend` — diseño frontend premium (React/Next + Tailwind + Framer Motion)
- `full-output-enforcement` — evita truncación de output largo
- `image-to-code` — workflow imagen → código para diseños visualmente importantes
- `redesign-existing-projects` — auditar y mejorar proyectos existentes
- `webapp-testing` — testear webapps locales con Playwright Python (oficial Anthropic)
- `mcp-builder` — construir servidores MCP en TS o Python (oficial Anthropic)

### Skills desactivados (en `.claude/skills/_disabled/`)
- `gpt-taste`, `high-end-visual-design`, `industrial-brutalist-ui`, `minimalist-ui`, `stitch-design-taste`
- Reactivar moviendo el symlink de vuelta a `.claude/skills/` si se necesita una estética puntual.

### Runtime listo
- Playwright 1.58.0 operativo. Browsers en `/opt/pw-browsers` (env `PLAYWRIGHT_BROWSERS_PATH` ya seteado).
- Smoke test E2E pasa: `chromium.launch(headless=True)` funciona sin args extra.

---

## Pendiente 1 — Edición en Adobe Premiere Pro vía ExtendScript

### Objetivo
Que Claude pueda asistir con edición de video en Premiere automatizando tareas (importar clips, crear secuencias, aplicar transiciones, exportar).

### Limitación
Claude no puede controlar Premiere directamente (no hay GUI access). Solución: Claude escribe scripts `.jsx`, el usuario los ejecuta dentro de Premiere.

### Flujo de trabajo acordado
1. Usuario describe la tarea de edición.
2. Claude escribe un script `.jsx` (ExtendScript) con la lógica.
3. Usuario lo ejecuta desde Premiere: `File > Scripts > Run Script File...`
4. Iteramos sobre el resultado (errores, ajustes, mejoras).

### Plan de acción
- [ ] **Paso 1 — Crear estructura de carpetas en el repo**
  - `premiere/scripts/` para los `.jsx`
  - `premiere/templates/` para plantillas reutilizables
  - `premiere/docs/` para documentación de la API de Premiere relevante
- [ ] **Paso 2 — Tarea piloto** (a definir con el usuario)
  - Empezar con algo simple: importar un clip + crear secuencia + colocar el clip en el timeline.
  - Validar el flujo end-to-end antes de tareas más complejas.
- [ ] **Paso 3 — Catálogo de scripts comunes**
  - Importación batch
  - Cortes automáticos por silencios
  - Aplicar transiciones
  - Exportar con presets
- [ ] **Paso 4 — Documentar gotchas** (descubrimientos durante iteración)

### Recursos clave para escribir `.jsx`
- API: `app.project`, `app.project.activeSequence`, `QE DOM` para acciones avanzadas
- Doc oficial: Adobe ExtendScript Toolkit / Premiere Pro Scripting Guide
- Ejemplos community: `pymiere` (Python wrapper) tiene ejemplos de ExtendScript que se pueden adaptar

---

## Pendiente 2 — MCP server para Premiere Pro

### Objetivo (ambicioso)
Construir un servidor MCP que exponga a Claude tools como `import_clip`, `create_sequence`, `apply_transition`, `export_sequence`. Así Claude puede *invocar* operaciones en Premiere directamente, en vez de escribir `.jsx` que el usuario ejecuta a mano.

### Arquitectura propuesta
```
Claude  <--MCP-->  MCP server (TS o Python)  <--ExtendScript bridge-->  Premiere Pro
```

El MCP server traduce llamadas a herramientas en scripts `.jsx` y los ejecuta vía:
- Opción A: `pymiere` (Python) — ya tiene un puente funcional
- Opción B: CEP/UXP panel — más nativo pero más complejo
- Opción C: Adobe Remote Procedure Call (newer Adobe APIs)

### Plan de acción
- [ ] **Fase 0 — Validar Pendiente 1** (escribir y ejecutar scripts `.jsx` manualmente). No tiene sentido construir el MCP sin entender el flujo manual primero.
- [ ] **Fase 1 — Research y diseño** (usar skill `mcp-builder`)
  - Decidir entre Python (con `pymiere` y FastMCP) vs TypeScript (con CEP/UXP)
  - Recomendación inicial: **Python + pymiere + FastMCP** — más rápido, menos fricción
  - Definir las primeras 5-10 tools a exponer
- [ ] **Fase 2 — Implementación**
  - Seguir las 4 fases del skill `mcp-builder`: Plan → Implement → Review → Evaluate
  - Tools mínimos: `list_projects`, `import_media`, `create_sequence`, `add_clip_to_timeline`, `export_sequence`
- [ ] **Fase 3 — Configuración del MCP en Claude**
  - Agregarlo a `.mcp.json` del proyecto o a settings global
  - Documentar comandos para arrancar el server
- [ ] **Fase 4 — Iteración**
  - Agregar tools según necesidades reales
  - Añadir manejo de errores robusto

### Pre-requisitos del usuario para fase 2+
- Premiere Pro instalado y abierto durante las pruebas
- Python con `pymiere` instalado en la máquina local (no en el sandbox de Claude)

---

## Cómo retomar

Para reactivar cualquiera de estos pendientes, decirme:
- **"retomemos Premiere"** → arrancamos con Pendiente 1 (scripts `.jsx`)
- **"construyamos el MCP de Premiere"** → arrancamos con Pendiente 2 (requiere haber avanzado en P1)

Yo leeré este archivo al inicio para recargar el contexto.
