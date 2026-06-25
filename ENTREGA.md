# Entrega — Ejercicio previo SDD / OpenSpec

**Alumno:** cga-telice
**Copiloto usado:** Claude Code (Opus 4.8)
**Entorno:** Windows 11 · Node v22.13.0 · npm 11.0.0 · OpenSpec 1.4.1

---

## 1. Evidencia de OpenSpec instalado y operativo

### `openspec --version`

```text
1.4.1
```

### `ls -la` (raíz del proyecto tras `openspec init --tools claude`)

```text
drwxr-xr-x  .claude/        # skills + comandos del copiloto (Claude Code)
drwxr-xr-x  .git/
-rw-r--r--  README.md
drwxr-xr-x  openspec/       # estructura generada por OpenSpec (prueba viva)
-rw-r--r--  ENTREGA.md      # este documento
```

### `ls -R openspec/`

```text
openspec/:
changes
specs

openspec/changes:
archive

openspec/changes/archive:

openspec/specs:
```

### `ls -R .claude/` (integración con el copiloto)

```text
.claude/commands/opsx:
apply.md   archive.md   explore.md   propose.md   sync.md

.claude/skills:
openspec-apply-change/SKILL.md
openspec-archive-change/SKILL.md
openspec-explore/SKILL.md
openspec-propose/SKILL.md
openspec-sync-specs/SKILL.md
```

> Nota: usé `openspec init --tools claude` (modo no interactivo). La CLI muestra
> `Config: skipped (non-interactive mode)`, por lo que la estructura
> (`changes/`, `specs/`, `changes/archive/`) se crea, pero **no** se genera el
> archivo de "constitución" del proyecto (`project.md`/`config.yaml`), que solo
> produce el asistente interactivo. Lo recojo como observación más abajo.

---

## 2. Parte A — Los 3 Pilares en una micro-tarea

**Micro-tarea:** Función `isValidEmail(value)` en JavaScript que valide un email
de forma pragmática (sin pretender cubrir el RFC 5322 completo).

### Plantilla de registro

**Pilar 1 — Herramienta:** Claude Code (Opus 4.8).
*¿Por qué esta y no otra?* Frente a un chat web (ChatGPT/Claude.ai), Claude Code
trabaja **dentro del repo y la terminal**: puede escribir el archivo, **ejecutar
los casos de prueba con Node y verificar el resultado en el mismo ciclo**, sin
copiar y pegar. Para una tarea que se valida ejecutándola, esa capacidad de
"probar e iterar" es el factor decisivo.

**Pilar 2 — Contexto que aporto:**
- Lenguaje/runtime: **JavaScript sobre Node**, función **pura y sin dependencias**.
- Reglas de validación: exactamente un `@`, parte local y dominio no vacíos, sin
  espacios, y dominio con al menos un punto y un **TLD de 2+ letras**.
- Entradas no-string deben devolver `false` (defensa de tipos); recortar espacios
  con `trim()` antes de validar.
- Ejemplos válidos (`user@example.com`, `a.b-c@sub.domain.io`) e inválidos
  (`plainaddress`, `user@@example.com`, `user@example`, `user@.com`).

*¿Qué omito conscientemente?* (1) La validación **RFC 5322 completa** — su
gramática (comillas, comentarios, IPs entre corchetes) es enorme y aporta poco
para un caso de uso normal; (2) la **verificación real del buzón** (DNS/MX, SMTP),
que es de red y queda fuera del alcance de una función pura.

**Pilar 3 — Prompt:** estructurado, pidiendo función + batería de casos de prueba
ejecutable y formato de salida concreto (un único archivo, comentarios mínimos).
Prompt final lanzado:

> "Escribe en JavaScript (Node, sin dependencias) una función pura
> `isValidEmail(value)` que devuelva boolean. Reglas pragmáticas, no RFC 5322:
> exactamente un `@`, parte local y dominio no vacíos, sin espacios, y dominio con
> al menos un punto y TLD de 2+ letras. Si `value` no es string, devuelve `false`;
> aplica `trim()` antes de validar. Añade un array de casos `[entrada, esperado]`
> que cubra válidos, sin `@`, doble `@`, con espacios, sin TLD, TLD vacío, string
> vacío y `null`, e imprime PASS/FAIL por caso y un recuento final. Comentarios
> mínimos."

### Solución generada

```javascript
function isValidEmail(value) {
  if (typeof value !== 'string') return false;
  const email = value.trim();
  // Pragmático (no RFC 5322 completo): un único @, sin espacios,
  // parte local y dominio no vacíos, y TLD de 2+ letras.
  const re = /^[^\s@]+@[^\s@]+\.[A-Za-z]{2,}$/;
  return re.test(email);
}
```

### Resultado (ejecutado con Node)

```text
PASS  isValidEmail("user@example.com") => true (esperado true)
PASS  isValidEmail("a.b-c@sub.domain.io") => true (esperado true)
PASS  isValidEmail("  spaced@example.com  ") => true (esperado true)
PASS  isValidEmail("plainaddress") => false (esperado false)
PASS  isValidEmail("user@@example.com") => false (esperado false)
PASS  isValidEmail("user @example.com") => false (esperado false)
PASS  isValidEmail("user@example") => false (esperado false)
PASS  isValidEmail("user@.com") => false (esperado false)
PASS  isValidEmail("@example.com") => false (esperado false)
PASS  isValidEmail("user@example.") => false (esperado false)
PASS  isValidEmail("") => false (esperado false)
PASS  isValidEmail(null) => false (esperado false)

12/12 casos OK
```

*¿Funcionó a la primera o iteré?* Funcionó a la primera con los 12 casos: la clave
fue **dar el contexto y los casos límite por adelantado** (en lugar de pedir "un
validador de email" a secas), lo que evitó el típico vaivén de "esto no contempla
X". *Mejora si lo repitiera:* sacar los casos de prueba a un archivo de tests
(p. ej. `node:test`) en vez de imprimir PASS/FAIL a mano, y decidir explícitamente
la política con TLDs internacionalizados (IDN/Punycode).

---

## 3. Tres observaciones de la exploración de OpenSpec

1. **El motor es la CLI (estado), pero el "cerebro" son instrucciones para el
   copiloto.** `openspec init` no instala un runtime que razone: deja una CLI
   determinista para *andamiaje y estado* (`openspec new`, `status`,
   `instructions`, `validate`, `archive`) y, en paralelo, inyecta archivos
   Markdown en el directorio del asistente elegido (`.claude/skills/*/SKILL.md` y
   `.claude/commands/opsx/*.md`). Es decir, la "inteligencia" del flujo vive como
   *prompts/instrucciones* versionables, no como código. Buena separación de
   responsabilidades: lo reproducible en la CLI, lo creativo en el copiloto.

2. **Spec-first explícito: nada de código hasta tener artefactos.** El comando
   `/opsx:propose` genera, **en orden de dependencias**, tres artefactos antes de
   implementar: `proposal.md` (qué y por qué), `design.md` (cómo) y `tasks.md`
   (pasos). Solo entonces `/opsx:apply` implementa. Me recordó mucho a separar
   "diseño" de "ejecución" en un PR, pero forzado por la herramienta: te obliga a
   articular intención y plan como archivos revisables.

3. **El ciclo de vida `propose → apply → archive` se refleja en el árbol de
   carpetas.** Las propuestas viven en `openspec/changes/`, los specs estables en
   `openspec/specs/`, y al completar una propuesta se "mueve" a
   `openspec/changes/archive/`. Es una analogía directa con un flujo tipo git
   (rama → merge → histórico): el estado del trabajo se lee literalmente mirando
   en qué carpeta está cada cosa. Detalle que me sorprendió: el `archive/` ya
   aparece vacío desde el `init`, dejando claro el "destino final" de cada cambio
   desde el minuto cero.
