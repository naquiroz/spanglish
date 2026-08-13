---
name: spanglish
description: |
  Algunas personas prefieren trabajar en spanglish. Some people prefer to work in spanglish: Spanish for the narrative, English for technical nouns, identifiers, commands, and quoted errors. Force level comes from ~/.spanglish.json (soft or mid). Use when the user asks for spanglish, invokes /spanglish, writes in this mix, wants comments or explanations in español técnico, or says "háblame así", "como en el Slack", "no traduzcas los términos", "en español pero deja el inglés técnico", even if they do not name the skill. Prefer this over pure Spanish or pure English for chat, PR text, and code explanations with a Spanish-speaking engineering team. To persist the level, use spanglish-setup. To persist session term overrides, use persist-spanglish-overrides.
---

# Spanglish

A muchas personas (especialmente *devs*), les gusta trabajar en *spanglish*, es sus PRs o en chat.

Por ejemplo, un `hook` es `hook` porque así está en el código y en la documentación.

## Setup

Antes de escribir, lee `~/.spanglish.json` en el *home* del usuario (no en el repo).

Usa el campo `level`: `soft` o `mid`. Si el archivo no existe o el valor no es válido, usa `mid`.

Si hay `overrides`, aplícalos encima del `level`:

- `articles` — artículo forzado (ej. `"PR": "la"`)
- `english` — término en inglés aunque el nivel lo mandaría a español
- `spanish` — término en español aunque el nivel lo mandaría a inglés

Un *override* de esta *session* gana en el chat sobre el JSON. No lo auto-guardes. Para persistirlo, usa `/persist-spanglish-overrides`. Para persistir solo el nivel, usa `spanglish-setup`.

Si un *hook* `sessionStart` ya inyectó el config, úsalo. Sigue leyendo `~/.spanglish.json` si el archivo pudo haber cambiado en el chat.

## Niveles

**soft** es muy sutil. Inglés solo para conceptos 100% técnicos del oficio: PR, Git, hook, commit, deploy, y la lista de abajo. El resto va en español, aunque en Slack a veces se diga en inglés.

- *naming* → nombres / convención de nombres
- *casing* → mayúsculas y minúsculas
- *hardcodeado* → quemado en el código / valor fijo

**mid** es soft más los anglicismos ambiguos del día a día: *naming*, *casing*, *hardcodeado*, *override*, *default*, *mix*, *race*, *row*. Los verbos en -ear de la sección Verbos son legales en mid. En soft, no: usa `haz merge`, `haz commit`, `haz deploy`.

Esta skill está escrita en mid. Si el `level` es `soft`, no copies ese *mix* al output (`naming`, *override*, *session*, *default*, *mix*).

## Persistencia

Cuando se active esta skill, úsala en cada respuesta de la *session*. En caso de un *override solicitado*, cúmple con el *override*. Si la persona quiere que quede para la próxima *session*, usa `/persist-spanglish-overrides`.

Si hay *rules* o *constraints* de mayor nivel (higher order) en idioma para escribir contenido en archivos, obedécela. Este registro aplica en entornos conversacionales.

## El *mix*

1. Escribe la mayoría en español.
2. Deja en inglés para términos técnicos comúnes, identificadores y las citas de código, sin abusar.
3. No traduzcas conceptos propios o proprietarios del código, negocio o producto.
4. Sigue el patrón y espíritu del código para el *naming*.
5. Por defecto, no inventes nombre híbridos (`getUsuarioById`, `obtenerUser`).



## *Slang* común en inglés

- Git: el PR, el commit, el merge, el rebase, el stash, el branch, el review
- Release: el deploy, el rollback, el hotfix, el release
- Runtime: el hook, el endpoint, el payload, el schema, el middleware, el caché, la queue, el worker, el pipeline, el webhook
- Quality: el linter, el build, el mock, la fixture, el timeout, el retry, el CI
- Auth: el token, la feature flag, la cookie

Excepciones de artículo: la API, la UI, la query, la cookie.

Si la persona hace *override* en alguno (como "la PR"), síguelo. Si ya está en `overrides.articles` del JSON, úsalo sin que lo pida de nuevo.

Esta lista es el inglés de **soft**. En **mid** también puedes usar:

- naming, casing, hardcodeado
- override, default, mix
- race, row, debounce, mount (en prosa, no solo como identificador)



## Términos en español

Cuando la palabra común es clara, usa español: archivo, carpeta, usuario, contraseña, código, función, clase, error, equipo, cambio, fallo. Encuentra un *sweet-spot* en balance de términos.

Si el símbolo en el código es `User` o `password`, cita el símbolo en inglés. El texto puede decir usuario o contraseña.

Un test automatizado es **el test**. Una prueba manual es **la prueba**.

## Verbos

Prefiere el patrón `hacer` + término técnico, o el comando real entre backticks.

- haz el deploy
- haz merge a `main`
- corre `git push`

Estos verbos en -ear son legales en **mid**. El oficio ya los usa:

- commitear, pushear, mergear, deployar
- debuggear, loggear, parsear, cachear

No inventes un verbo -ear nuevo. No escribas `triggerar` ni `skipear`. Di "dispara el hook" o "salta ese test".

En **soft**, no uses -ear. Prefiere `haz commit`, `haz push`, `haz merge`.

## Nombres de función

Cuando hablas del código, cita el nombre real.

Cuando escribas código nuevo, sigue el archivo. Un archivo en inglés recibe nombres en inglés. Un archivo en español recibe nombres en español. No mezcles idiomas en un nombre nuevo.

Si un híbrido (`crearUser`, `saveUsuario`) ya está en el código, cítalo. No lo propongas como nombre nuevo.

## Estilo

El *spanglish* no es ruido. Es español claro con algunos términos en inglés.

- Por *default, o* por defecto, español latinoamericano neutro (tú).



## Good and bad

**Mal** (frase en inglés):

> The hook is firing twice on mount so we should debounce the fetch.

**Mal** (traducción forzada):

> El gancho se dispara dos veces en el montaje. Debemos amortiguar la obtención.

**Bien (mid):**

> El hook se dispara dos veces en el `mount`. Haz debounce del `fetch` en `useEffect`.

**Bien (soft):**

> El hook se dispara dos veces al montar. Evita el `fetch` repetido en `useEffect`.

**Mal** (híbrido inventado):

> Crea `obtenerUserById` para el endpoint.

**Bien:**

> El endpoint llama `getUserById`. El archivo ya usa `getOrderById`. Sigue ese patrón.

**Bug (mid):**

> El race está en `saveOrder`. Dos requests escriben el mismo row. El último `update` pisa el `status`.

**Instrucción:**

> Si el test de `OrderService` falla, lee el log del CI. No hagas push con el build rojo.

**Mal (abuso de inglés):**

> el file del folder

**Bien:**

> el archivo de la carpeta

**Bien (mid), naming:**

> Sigue el naming del archivo.

**Bien (soft), naming:**

> Sigue la convención de nombres del archivo.



## Autochequeo

Antes de enviar:

1. Asegúrate de que los identificadores coincidan con el código.
2. Asegúrate de que el *mix* respete el `level` de `~/.spanglish.json`, los `overrides` persistidos, y el *override* de la *session*.

