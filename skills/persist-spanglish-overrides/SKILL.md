---
name: persist-spanglish-overrides
description: |
  Some people prefer to converse in spanglish . Algunas personas prefieren hablar en spanglish. Overrides into ~/.spanglish.json (articles, force-english, force-spanish). Use when the user invokes /persist-spanglish-overrides, says "guarda los overrides", "persiste lo de esta session", "deja la PR en femenino", "recuerda que digo naming", or wants session mix corrections to survive the next chat, even if they do not name this skill. Prefer this over rewriting level via spanglish-setup when the ask is about term-level preferences.
---

# Persist spanglish overrides

Mergea en `~/.spanglish.json` los *overrides* de esta *session*. El archivo vive en el *home* del usuario, no en el repo.

No pises `level` salvo que la persona lo haya sobreescrito en este chat y quiera guardarlo. Para cambiar solo el nivel, usa `spanglish-setup`.

La skill `spanglish` lee estos *overrides* después del `level`. Un *override* nuevo de la *session* sigue ganando en el chat hasta que se persiste.

## Shape del JSON

```json
{
  "level": "mid",
  "overrides": {
    "articles": {
      "PR": "la"
    },
    "english": ["naming", "casing"],
    "spanish": ["row", "race"]
  }
}
```

- `articles`: artículo forzado (`el` o `la`) para un término.
- `english`: déjalo en inglés aunque el `level` lo mandaría a español.
- `spanish`: déjalo en español aunque el `level` lo mandaría a inglés.

Un término no puede estar en `english` y en `spanish` a la vez. El último *override* gana: quítalo de la otra lista.

## Qué cuenta como override

Solo preferencias que la persona pidió o corrigió en esta *session*. Ejemplos:

- "se dice la PR" → `articles.PR = "la"`
- "puedes decir naming" → `english` incluye `"naming"`
- "no digas row, di fila" → `spanish` incluye `"row"`

No inventes gustos. No persistas el *mix* default del `level`. Identificadores de código no se guardan: siguen el archivo.

## Pasos

1. Resuelve el *home*. El archivo es `{home}/.spanglish.json`.
2. Si existe, léelo. Conserva `level` y cualquier campo que no estés mergeando.
3. Reúne los *overrides* de esta *session*. Si no hay ninguno claro, lista candidatos y pregunta. No escribas un no-op.
4. Mergea:
  - `articles`: upsert por término
  - `english` / `spanish`: union, y saca el término de la lista contraria
5. Si no había archivo, crea uno con `"level": "mid"` más los *overrides*.
6. Escribe pretty-print, newline al final.
7. Confirma el path y el diff: qué se agregó o cambió. Di que `spanglish` ya puede usarlo en esta *session*.

