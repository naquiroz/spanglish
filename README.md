# spanglish

Skill para que un agente hable como un dev hispano. El español carga la frase. El inglés nombra el código y los términos técnicos.

Este paquete sigue [Agent Plugins 1.0.0](https://agent-plugins.org/specification): `plugin.json` en la raíz y skills en `skills/`.

## Niveles

- `soft`: inglés solo para conceptos 100% técnicos (PR, Git, hook)
- `mid`: también anglicismos ambiguos (*naming*, *casing*, *hardcodeado*)

La preferencia vive en `~/.spanglish.json` (home del usuario, no el repo). El `level` se configura con `/spanglish-setup`. Los *overrides* de una *session* se guardan con `/persist-spanglish-overrides`.

## Agregar este repo en Cursor

Este plugin no está en el [Cursor Marketplace](https://cursor.com/marketplace) público. Lo instalas desde el Git repo.

URL: `https://github.com/naquiroz/spanglish`

Para más detalle, consulta [Plugins](https://cursor.com/docs/plugins.md).

### Pegar la URL en Customize

Cursor instala un plugin si pegas la URL del repo en la búsqueda.

1. Abre **Customize** en la barra lateral.
2. Pega `https://github.com/naquiroz/spanglish` en la búsqueda de plugins.
3. **Install** y elige alcance de proyecto o de usuario.

### Registrar el marketplace en el CLI

Para dejar el repo como fuente de plugins, no solo un install puntual:

```bash
agent plugin marketplace add https://github.com/naquiroz/spanglish
```

Pinnea *branch*, *tag* o commit con `--git-ref`. En una session del CLI, `/plugin marketplace add` acepta la misma URL.

Instala el plugin desde **Customize**.

### Marketplace de equipo

1. Ve a **Dashboard → Plugins**.
2. En **Team Marketplaces**, **Add Marketplace**.
3. Crea uno desde cero, o **Import from Repo** con la URL de GitHub.
4. Revisa los plugins con **Add to Marketplace**.
5. En **Marketplace Settings**, define **Marketplace Access**. Opcional: **Enable Auto Refresh**. Guarda.

Puedes verlo en **Customize** e instalarlo ahí.

## Activar el hook para usar la skill en automático

El *hook* `sessionStart` activa la skill `spanglish` al arrancar la conversación. Inyecta `~/.spanglish.json`. Sin el hook, hay que invocar `/spanglish` a mano.

Es una extensión de cliente (`com.cursor/`, `com.anthropic.claude/`, `com.openai.codex/`), no un componente portable de Agent Plugins. Setup no toca `~/.cursor/hooks.json`.

Necesitas `python3`. Si no existe `~/.spanglish.json`, el hook no inyecta nada: corre `/spanglish-setup` una vez.

### Cursor

```bash
mkdir -p ~/.cursor/plugins/local
ln -s https://github.com/naquiroz/spanglish ~/.cursor/plugins/local/spanglish
```

Recarga la ventana: **Developer: Reload Window**.

Docs: [Plugins](https://cursor.com/docs/plugins.md).

### Claude Code

Una session:

```bash
claude --plugin-dir https://github.com/naquiroz/spanglish
```

Para dejarlo instalado:

```bash
claude plugin marketplace add https://github.com/naquiroz/spanglish
claude plugin install spanglish@spanglish
```

O en el chat: `/plugin marketplace add` con `https://github.com/naquiroz/spanglish`, luego `/plugin install spanglish@spanglish`. Abre una conversación nueva. Si el hook no corre desde el marketplace, usa `--plugin-dir`.

### Codex

```bash
codex plugin marketplace add https://github.com/naquiroz/spanglish
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

Con el hook activo, la skill corre sola. Si no, invoca `/spanglish`, o pide "háblame en spanglish".

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
