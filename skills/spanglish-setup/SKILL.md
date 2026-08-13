---
name: spanglish-setup
description: >
  Saves the user's spanglish force level (soft or mid) to ~/.spanglish.json.
  Use when the user invokes /spanglish-setup, wants to configure spanglish,
  says "ponlo en soft", "cambia a mid", "guarda mi preferencia", "configura
  el mix", or asks where the spanglish level is stored — even if they do not
  name this skill. Prefer this over guessing a level or writing the JSON in
  the repo. The sessionStart hook ships with the plugin; this skill does not
  edit ~/.cursor/hooks.json.
---

# Spanglish setup

Guarda el nivel de fuerza del *spanglish* en el *home* del usuario: `~/.spanglish.json`.

No escribas el JSON en el repo. El path es el root del usuario, no el del proyecto.

El *hook* `sessionStart` vive en el plugin como extensión de cliente (`com.cursor/hooks/`). Cursor lo carga al habilitar el plugin. Esta skill no toca `~/.cursor/hooks.json`.

Si el JSON no existe, el *hook* no inyecta nada. La skill `spanglish` lee el archivo en cada *session*.

## Niveles

| `level` | Qué hace |
|---|---|
| `soft` | Muy sutil. Inglés solo para conceptos 100% técnicos: PR, Git, hook, commit, deploy. |
| `mid` | Soft más anglicismos ambiguos: *naming*, *casing*, *hardcodeado*, *override*, *default*. |

Valores legales: `soft`, `mid`. Nada más.

## Pasos

1. Resuelve el *home* (`echo $HOME`, o el equivalente en el OS). El archivo es `{home}/.spanglish.json`.
2. Si el archivo ya existe, léelo. Conserva cualquier campo que no sea `level` (incluye `overrides`).
3. Si la persona ya dijo `soft` o `mid`, usa ese valor. Si no, explica los dos niveles en una o dos frases y pregunta. No adivines.
4. Escribe el JSON (pretty-print, newline al final). Ejemplo mínimo:

```json
{
  "level": "mid"
}
```

5. Confirma el path absoluto y el `level` guardado. Di que aplica en esta *session* si `spanglish` ya está activa, y en chats nuevos vía el *hook* del plugin.

Si el valor pedido no es `soft` ni `mid`, no escribas. Pregunta de nuevo.

Un *override* suelto de términos (artículo, *naming*, etc.) no lo persiste esta skill. Eso es `/persist-spanglish-overrides`.
