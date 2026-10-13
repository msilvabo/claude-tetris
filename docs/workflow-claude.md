# Workflow automático: resolver issues con Claude

Workflow en `.github/workflows/claude-issue.yml` basado en
[`anthropics/claude-code-action`](https://github.com/anthropics/claude-code-action).
Lee un issue, modifica el código y abre un Pull Request.

## Setup (una sola vez)

1. **API key**: repo → **Settings → Secrets and variables → Actions → New repository secret**
   - Nombre: `ANTHROPIC_API_KEY`, valor: tu key de [console.anthropic.com](https://console.anthropic.com).
2. **App de GitHub** (recomendado, da permisos de la app al bot):
   - Opción A: en Claude Code local ejecuta `claude /install-github-app` (crea app + workflow automáticamente).
   - Opción B: manual en github.com/apps/claude para el repo `msilvabo/claude-tetris`.
3. Subir este workflow a `main`.

## Cómo se dispara

| Acción | Resultado |
|---|---|
| Comentar `@claude <instrucción>` en un issue existente | Claude lee el issue, implementa y abre PR |
| Abrir issue y asignarlo al usuario `claude` | Idem |
| Etiquetar un issue con `claude` | Idem |
| Comentar `@claude ...` en review de un PR | Claude ajusta el PR |

## Cómo ver acciones y detalles

- **Actions tab** del repo → workflow **"Claude Code"** → cada run muestra todos los pasos,
  herramientas usadas y logs completos (paso a paso lo que hizo Claude).
- El bot **comenta en el propio issue/PR** con un enlace al run y un resumen del trabajo.
- El **PR resultante** contiene el diff completo y enlaza al issue (`Closes #N`).

## Ejemplo de uso

1. Crear issue: *"Agregar puntuación por T-spin"*.
2. Comentar en el issue: `@claude implementa esto`.
3. En ~2-5 min aparece un comentario del bot con el PR creado.
4. Revisar el diff, pedir ajustes con otro `@claude ...` en el PR, o mergear.

## Límites

- Corre en Actions de GitHub (costo de minutos + API de Anthropic por run).
- Repos privados requieren los mismos secrets/permisos.
- Variables del juego (`COLS`/`ROWS`/`BLOCK`) — ver restricciones en `CLAUDE.md`.
