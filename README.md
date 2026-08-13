# spanglish

Skill para que un agente hable como un dev hispano. El español carga la frase. El inglés nombra el código y los términos técnicos.

Este paquete sigue [Agent Plugins 1.0.0](https://agent-plugins.org/specification): `plugin.json` en la raíz y skills en `skills/`.

## Niveles

- `soft` — inglés solo para conceptos 100% técnicos (PR, Git, hook)
- `mid` — también anglicismos ambiguos (*naming*, *casing*, *hardcodeado*)

La preferencia vive en `~/.spanglish.json` (home del usuario, no el repo). El `level` se configura con `/spanglish-setup`. Los *overrides* de una *session* se guardan con `/persist-spanglish-overrides`.

## Activar el hook

El *hook* `sessionStart` inyecta `~/.spanglish.json` al arrancar la conversación. Es una extensión de cliente (`com.cursor/`, `com.anthropic.claude/`, `com.openai.codex/`), no un componente portable de Agent Plugins. Setup no toca `~/.cursor/hooks.json`.

Corre estos comandos **desde el clone**. Necesitas `python3`. Si no existe `~/.spanglish.json`, el hook no inyecta nada: corre `/spanglish-setup` una vez.

### Cursor

```bash
mkdir -p ~/.cursor/plugins/local
ln -s "$(pwd)" ~/.cursor/plugins/local/spanglish
```

Recarga la ventana: **Developer: Reload Window**.

Docs: [Plugins](https://cursor.com/docs/plugins.md).

### Claude Code

Una session:

```bash
claude --plugin-dir "$(pwd)"
```

Para dejarlo instalado:

```bash
claude plugin marketplace add "$(pwd)"
claude plugin install spanglish@spanglish
```

O en el chat: `/plugin marketplace add` con la ruta del clone, luego `/plugin install spanglish@spanglish`. Abre una conversación nueva. Si el hook no corre desde el marketplace local, usa `--plugin-dir`.

### Codex

```bash
codex plugin marketplace add "$(pwd)"
codex plugin add spanglish@spanglish
```

En la CLI, `/hooks` y *trust* el hook. Codex no corre *plugin hooks* hasta que los revisas.

Si tu Codex no carga hooks del plugin, pon esto en `~/.codex/hooks.json`. Si el archivo ya existe, mergea el bloque `SessionStart`. Cambia `/ruta/al/spanglish` por la ruta absoluta del clone:

```json
{
  "description": "Injects spanglish config from ~/.spanglish.json at session start",
  "hooks": {
    "SessionStart": [
      {
        "matcher": "startup|resume",
        "hooks": [
          {
            "type": "command",
            "command": "python3 \"/ruta/al/spanglish/scripts/spanglish-session-start\""
          }
        ]
      }
    ]
  }
}
```

Después `/hooks` y *trust*.

## Uso

Invoca `/spanglish`, o pide "háblame en spanglish".

## Estructura

```text
plugin.json
skills/
scripts/spanglish-session-start
com.cursor/hooks/
com.anthropic.claude/hooks/
com.openai.codex/hooks/
```

- `plugin.json` — manifiesto Agent Plugins
- `skills/` — skills portables
- `com.cursor/hooks/` — *hook* Cursor
- `com.anthropic.claude/hooks/` — *hook* Claude Code
- `com.openai.codex/hooks/` — *hook* Codex
- `.cursor-plugin/` / `.claude-plugin/` / `.codex-plugin/` — manifiestos nativos para que esos clientes carguen el *hook*
