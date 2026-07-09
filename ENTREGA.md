# PARTE A
*Micro-tarea:* Extraccion de datos de una factura de electricidad

*Pilar 1 — Herramienta:* ¿Cuál eliges? Completion (IDE-integrated, modo Tab), porque probablemente sea un script simple que quepa en un archivo.
 ¿Por qué esta y no otra? Tambien se podria hacer con CLI agentico, tipo Claude Code o Codex CLI. Curiosamente, me costaba usarlo con Codex CLI porque no se acceder al terminal desde ahi.

*Pilar 2 — Contexto:* ¿Qué información estás aportando? (lenguaje, framework, restricciones, ejemplos…)
 ¿Hay algo del contexto que has decidido omitir conscientemente? No necesitaba ningun framework concreto, pero si la info que tengo en el prompt de abajo, extension de las facturas, etc.

*Pilar 3 — Prompt:* ¿Cómo lo estructuras? (estilo, formato de salida, ejemplos…)
 Pega aquí el prompt final que vas a lanzar.

``'md
Exporta datos estructurados desde unas facturas electronicas de electricidad.

- Muestralo por pantalla.
- En los campos exportados, tiene que estar: el identificador de la factura, la fecha de emision de factura, direccion de suministrado, periodo facturado e importe total.

No lo guardes a disco.

Las facturas son en formato pdf y han sido emitidas por ENDESA.

Si tienes dudas, pregunta.
```

*Resultado:* ¿Funcionó a la primera o tuviste que iterar? funciono a la primera
 Una mejora que harías si volvieras a hacerlo. Especificarle que lo quiero en formato YML.

# PARTE 2

```bash
lisan@kraftwek:/data/workspace/Modulo 2: Spec-Driven Development/ai4devs-openspect-sandbox-202606-roo$ openspec --version
1.5.0
lisan@kraftwek:/data/workspace/Modulo 2: Spec-Driven Development/ai4devs-openspect-sandbox-202606-roo$ openspec init
✔ Select tools to set up (30 available) Codex
▌ OpenSpec structure created
✔ Setup complete for Codex

OpenSpec Setup Complete

Created: Codex
5 skills and 5 commands in .codex/
Config: openspec/config.yaml (schema: spec-driven)

Getting started:
  Start your first change: /opsx:propose "your idea"

Learn more: https://github.com/Fission-AI/OpenSpec
Feedback:   https://github.com/Fission-AI/OpenSpec/issues

Restart your IDE for slash commands to take effect.

lisan@kraftwek:/data/workspace/Modulo 2: Spec-Driven Development/openspec-sandbox$ ls -R openspec/
openspec/:
changes  config.yaml  specs

openspec/changes:
archive

openspec/changes/archive:
2026-07-09-add-oauth-integration

openspec/changes/archive/2026-07-09-add-oauth-integration:
design.md  proposal.md  specs  tasks.md

openspec/changes/archive/2026-07-09-add-oauth-integration/specs:
oauth-integration

openspec/changes/archive/2026-07-09-add-oauth-integration/specs/oauth-integration:
spec.md

openspec/specs:
oauth-integration

openspec/specs/oauth-integration:
spec.md
lisan@kraftwek:/data/workspace/Modulo 2: Spec-Driven Development/openspec-sandbox$ 
```
