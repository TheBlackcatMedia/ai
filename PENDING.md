# Pendientes y plan de acción

> Última actualización: 2026-04-28
> Rama de trabajo: `claude/premiere-pro-editing-LV0nT`

## Estado actual del setup

### Skills activos (35)

**Base (6):**
- `design-taste-frontend` — diseño frontend premium (React/Next + Tailwind + Motion)
- `full-output-enforcement` — evita truncación de output largo
- `image-to-code` — workflow imagen → código para diseños visualmente importantes
- `redesign-existing-projects` — auditar y mejorar proyectos existentes
- `webapp-testing` — testear webapps locales con Playwright Python (oficial Anthropic)
- `mcp-builder` — construir servidores MCP en TS o Python (oficial Anthropic)

**Koda Stack — pipeline creativo (10):**
- `/brief`, `/trends`, `/concept`, `/script`, `/art-direction`, `/storyboard`, `/generate`, `/assemble`, `/publish`, `/repurpose`

**Higgsfield AI — generación de imagen/video (19):**
- Estilos artísticos: `01-cinematic`, `02-3d-cgi`, `03-cartoon`, `04-comic-to-video`, `05-fight-scenes`, `08-anime-action`
- Comerciales: `06-motion-design-ad`, `07-ecommerce-ad`, `09-product-360`, `11-social-hook`, `12-brand-story`
- Verticales: `10-music-video`, `13-fashion-lookbook`, `14-food-beverage`, `15-real-estate`
- Automatización: `higgsfield-image-auto`, `seedance-auto-generate`, `ugc-hot-girl`, `ugc-video-auto`

### Skills desactivados (en `.claude/skills/_disabled/`)
- `gpt-taste`, `high-end-visual-design`, `industrial-brutalist-ui`, `minimalist-ui`, `stitch-design-taste`
- Reactivar moviendo el symlink de vuelta a `.claude/skills/` si se necesita una estética puntual.

### Runtime listo
- Playwright 1.58.0 (Python) operativo. Browsers en `/opt/pw-browsers` (env `PLAYWRIGHT_BROWSERS_PATH` ya seteado).
- Smoke test E2E pasa: `chromium.launch(headless=True)` funciona sin args extra.
- **Playwright MCP** (`@playwright/mcp@latest`) configurado en `.mcp.json` para automatización browser desde skills Higgsfield. Se activa al reiniciar la sesión Claude.

### Cuentas externas
- **Higgsfield AI** — cuenta del usuario, login requerido en la sesión de browser para los skills de automatización.

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

## Pendiente 3 — Configurar el pipeline creativo Koda + Higgsfield

### Objetivo
Aprovechar los 29 skills nuevos (Koda Stack + Higgsfield) para producir contenido AI end-to-end (script → storyboard → generación → reel publicable).

### Estado
- 29 skills instalados.
- Playwright MCP configurado en `.mcp.json` (se activa al reiniciar la sesión).
- Cuenta Higgsfield: ✅ (del usuario).

### Plan de acción
- [ ] **Paso 1 — Definir el DNA creativo (CLAUDE.md extendido)**
  - Voice (cómo habla la marca: tono, vocabulario)
  - Visual style (paleta, lighting, composición preferida)
  - Tools (qué herramientas externas usás aparte de Higgsfield: fal.ai, Runway, Remotion, etc.)
  - Audience (a quién apuntás)
  - Rules (formato, longitud, no-negociables)
  - Esta sección la lee cada skill antes de generar contenido.
- [ ] **Paso 2 — Validar Playwright MCP**
  - Reiniciar la sesión Claude.
  - Confirmar que las tools `mcp__playwright__*` aparecen disponibles.
  - Smoke test: pedirle a Claude que navegue a higgsfield.ai (sin autenticar) para validar el bridge.
- [ ] **Paso 3 — Login persistente en Higgsfield**
  - Definir cómo mantener la sesión logueada (browser context persistente, cookies).
  - Documentar el comando o flujo para arrancar el browser con el usuario logueado.
- [ ] **Paso 4 — Pipeline piloto end-to-end**
  - Pedir un brief simple (`/brief I want a reel about ...`).
  - Ejecutar el pipeline completo: `/brief → /concept → /script → /art-direction → /storyboard → /generate → /assemble`.
  - Validar que los handoffs entre skills funcionan.
- [ ] **Paso 5 — Iterar y ajustar**
  - Refinar prompts en `CLAUDE.md` según qué funciona y qué no.
  - Decidir qué skills usar más vs. desactivar.

### Notas de operación
- Los skills `higgsfield-image-auto`, `seedance-auto-generate`, `ugc-video-auto` requieren Playwright MCP activo + sesión logueada.
- Los skills de prompts puros (`01-cinematic` ... `15-real-estate`) son agnósticos: solo generan strings, no automatizan nada.
- El pipeline Koda funciona sin Higgsfield (los outputs son texto/JSON; vos podés usarlos en cualquier herramienta de generación).

---

## Pendiente 4 — Definir el DNA por marca (Koda DNA)

### Objetivo
Llenar las 5 dimensiones (Voice, Visual style, Tools, Audience, Rules) de cada archivo en `brands/`, para que todo skill creativo genere contenido alineado a la identidad de cada marca/proyecto.

### Estado
- ✅ Estructura `brands/` creada (INDEX + 13 archivos con plantilla y meta pre-llenada).
- ✅ `CLAUDE.md` instruye a los skills a leer el archivo de marca correspondiente.
- ✅ Voice de **THE BLACKCAT Media** definido (commit `fbe8cb4`).

### Orden de trabajo acordado (4 bloques, después pausa)
1. **THE BLACKCAT Media** — Voice ✅ · falta Visual / Tools / Audience / Rules
2. **Cluster Powering Chicago** — Powering Chicago + The Power of Better
3. **NUMEN Artes Vivas**
4. **Cluster eléctrico restante** — IBEW Local 134 + ECA + IN-Tech

Quedan para una segunda etapa: los 3 coros de NUMEN, NDC – UNOPS, Rob Johnson Communications, Rotary Club Córdoba.

### Manifesto histórico de BLACKCAT (a recuperar)
El usuario recuerda que en una sesión previa de Claude Code armaron un manifesto de la marca. Vive en el Mac local del usuario (`~/Desktop/2026/Claude Code`), no en este repo. **Recuperarlo cuando esté hecho el setup local** y agregarlo a `brands/blackcat-media.md` como sección `## Manifesto`.

---

## Pendiente 5 — Setup de Claude Code CLI en el Mac local del usuario

### Objetivo
Que el usuario pueda trabajar este mismo repo desde su Mac (Claude Code CLI local) además de la web (sandbox actual). GitHub es el puente.

### Estado
En curso (sesión 2026-04-28, opción C: paso a paso guiado).

### Pasos
- [ ] Verificar prerequisitos en el Mac: Claude Code CLI instalado (`claude --version`), Git configurado, autenticación GitHub funcionando.
- [ ] Decidir path para clonar el repo (sugerido: `~/Desktop/2026/Claude Code/ai`).
- [ ] `git clone` + `git checkout` a la branch `claude/premiere-pro-editing-LV0nT`.
- [ ] Verificar que Claude Code CLI lee `CLAUDE.md` y `brands/` correctamente.
- [ ] Resolver fricción de `.mcp.json` (paths Linux específicos del sandbox no funcionan en macOS).

---

## Pendiente 6 — Customizar Claude para uso desde celular (WhatsApp / SMS / mensajes)

### Objetivo
Configurar un canal por el que el usuario pueda mandarle instrucciones a Claude desde su celular (vía WhatsApp, SMS u otro) sin tener que abrir la web ni la terminal.

### Estado
Idea pendiente — no investigado aún.

### Por explorar
- ¿Existe oficialmente un bridge de Claude / Anthropic a WhatsApp? ¿O hay que armarlo custom?
- Opciones técnicas conocidas a evaluar:
  - **Twilio + WhatsApp Business API + Anthropic API** → se programa un bot que recibe mensajes y los relaya a Claude.
  - **Telegram Bot API + Anthropic API** → más simple que WhatsApp Business, mismo concepto.
  - **iMessage shortcut + Anthropic API** → automation nativa de macOS/iOS.
  - **Claude.ai mobile app** (si existe) → la opción "oficial" sin fricción técnica.
- Decidir qué tipo de instrucciones se mandarían desde el celular: ¿chat libre? ¿comandos pre-formateados (`/brief ...`)? ¿sólo lectura de status del proyecto?
- Consideraciones de seguridad: API keys, autenticación del remitente, rate limiting.

### Cuándo retomar
Cuando el usuario diga **"armemos el bridge a celular"** o **"configuremos WhatsApp"**.

---

## Cómo retomar

Para reactivar cualquiera de estos pendientes, decirme:
- **"retomemos Premiere"** → Pendiente 1 (scripts `.jsx`)
- **"construyamos el MCP de Premiere"** → Pendiente 2 (requiere haber avanzado en P1)
- **"armemos el DNA creativo"** o **"sigamos con las marcas"** → Pendientes 3 y 4
- **"hagamos el setup en mi Mac"** → Pendiente 5
- **"configuremos WhatsApp / celular"** → Pendiente 6

Yo leeré este archivo al inicio para recargar el contexto.
