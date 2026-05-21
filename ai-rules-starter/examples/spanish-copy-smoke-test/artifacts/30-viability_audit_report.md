# Viability Audit Report B8i

## 1. Executive verdict

VIABILITY_RESULT: viable_with_required_changes

confidence: medium

AI Rules demostro viabilidad parcial para una tarea real, acotada y documental: obligo a crear requisito, hoja ejecutable, estimaciones de diff, hojas de lectura, evidence packets, replan ante oversize y un final prompt implementable antes de crear `ai-rules-starter-es/`. El flujo fue mas seguro que pedir directamente "traduce esto" porque separo evidencia limpia de evidencia oversized, explicito archivos permitidos, conservo tokens protegidos y bloqueo claims no probados como Pi enforcement. No es viable como adopcion real "as is": el costo operativo fue alto para una tarea pequena, los artifacts planos crearon friccion, varias reading_leaf heredaron prohibiciones incompatibles con el rol lector, la guia GPT-5.5 sigue siendo placeholder, Pi Agent no fue probado, y no se demostro uso en un repo de codigo real con AGENTS.md instalado.

## 2. What worked

- El flujo cumplio la mision central en forma parcial: no salto directo de la peticion humana a implementacion.
- La primera lectura oversized fue manejada correctamente: artifacts 08 y 10 quedaron marcados como `READ_LEAF_OVERSIZE`, y el stop report 11 impidio usarlos como autoridad limpia.
- La segunda ronda separo mejor la evidencia por grupos: root docs, docs directos, model-guides, overlay, comandos/rutas, labels/markers, constantes numericas y notice.
- El final_prompt 29 fue trazable, ejecutable y superior a un prompt directo de traduccion. Definio objetivo, alcance permitido, escrituras permitidas, preflight, stop conditions, archivos a crear, reglas de preservacion, verificaciones y reporte final.
- El implementer obedecio el alcance principal observado: creo 13 archivos bajo `ai-rules-starter-es/`, agrego el notice obligatorio en la primera linea de cada archivo, conservo markers en `AGENTS.patch.es.md`, y no creo scripts, tooling o dependencias.
- R1 no fue un problema para esta tarea porque los archivos creados son documentales y todos quedaron por debajo de 500 lineas.
- R2 quedo razonablemente dentro del estimado pre_prompt: la copia espanola tiene 397 lineas agregadas por conteo de archivos, por debajo del high 560 y del limite 600. El comando `git diff --numstat -- ai-rules-starter-es` no mide archivos untracked, asi que el metodo de medicion debe corregirse.

## 3. What failed or created friction

- La carga operativa fue desproporcionada para una copia documental: 29 artifacts antes de implementar una salida de 397 lineas.
- Los artifacts planos confunden. La secuencia 01-29 es auditable, pero no es ergonomica; mezcla clasificacion, lectura, stop report, replan, evidencia y final prompt en un solo directorio sin indice por rol o fase.
- El planner emitio hojas iniciales demasiado amplias. Artifacts 05 y 07 ya contenian senales de oversize antes de que el reader las ejecutara.
- Varias reading_leaf incluyeron `No crear evidence_packet ni final_prompt` en disallowed_scope. Eso contradice el contrato del reader, que debe producir exactamente un evidence packet.
- Algunos evidence packets fueron utiles pero largos. Artifact 21 tiene 232 lineas para una decision de inventario simple, lo que aumenta carga humana y riesgo de que el final prompt builder pierda lo importante.
- La separacion verified/inferred/unknown existio, pero no siempre con la forma estricta esperada. Algunas inferencias carecen de un campo uniforme tipo `reasoning_note`; otras quedan como texto libre.
- El final_prompt convirtio una inferencia de wording de notice en decision humana `HUMAN_DECISION_SPANISH_NOTICE_001`. Puede ser aceptable si el humano realmente la decidio, pero el artifact trail por si solo no prueba esa decision previa.
- La verificacion de diff tiene una brecha: con archivos untracked, `git diff --numstat -- ai-rules-starter-es` devuelve vacio. Para adopcion real debe exigirse staging temporal, `git status --short` mas conteo, o `git diff --no-index /dev/null` por archivo nuevo.
- No hubo `diff_result` ni ledger entry estructurado despues de la implementacion; por tanto la calibracion historica de diff no quedo cerrada.

Findings evaluados:

- Confirmo: artifacts planos confunden.
- Ajusto: una instancia lectora debe ejecutar una sola reading_leaf en Standard/Strict; en Lite puede permitirse batch si esta declarado y con limite de salida.
- Confirmo: el planner debe dividir por decision/evidencia si hay riesgo de oversize.
- Confirmo: las respuestas al humano deben ser cortas y accionables.
- Confirmo: reading_leaf no debe heredar prohibiciones que bloqueen al reader de producir su evidence_packet.
- Confirmo: evidence packets necesitan limite de salida.
- Confirmo: la guia GPT-5.5 sigue siendo placeholder.
- Confirmo: `AGENTS.patch.es.md` debe quedar claramente como lectura humana; el archivo generado lo indica, pero debe mantenerse como regla formal.
- Confirmo: se necesitan modos Lite/Standard/Strict.
- Confirmo: se necesita runbook usable.
- Confirmo: el implementer debe recibir el final_prompt generado, no un wrapper nuevo; este smoke no prueba de forma independiente que eso haya ocurrido.
- Confirmo: Pi Agent queda NOT_PROVEN.

## 4. Scalability assessment

Escala bien en tareas con riesgo real de alucinacion, duplicacion, contratos compartidos o cambios de codigo donde leer antes de implementar cambia la calidad del resultado. La separacion classifier -> planner -> reader -> final prompt builder -> implementer es valiosa cuando hay multiples boundaries, reuse scan, arquitectura local, tests y riesgos de scope.

Escala mal si cada tarea pequena produce decenas de artifacts. En features complejas puede explotar por numero de reading leaves, evidence packets y contexto si no existen modos, limites de salida, indice de artifacts y compresion de evidencia. La estrategia correcta no es eliminar la separacion, sino imponer presupuestos por modo y dividir por decision/evidencia antes de leer, no despues de fallar.

Para features complejas de codigo real, el metodo deberia escalar mejor que un prompt unico solo si agrega: reuse scans obligatorios, architecture decision packets cuando apliquen, output caps, runbook claro, y una forma de pasar al implementer solo el final_prompt aprobado.

## 5. Human usability assessment

Un humano tecnico puede operar este smoke con esfuerzo alto, pero no un operador normal sin asistencia experta. El sistema requiere entender roles, labels, budgets, artifacts, oversize, authority, source of truth, prompt builder y diff estimation. La carga de lectura fue pesada: hay que revisar 29 artifacts para confiar en una tarea que finalmente crea 13 documentos.

Falta un runbook usable con comandos exactos, "que hago ahora", criterios de stop, plantilla de indice, y modos Lite/Standard/Strict. Las respuestas al humano deben ser cortas y accionables; los artifacts pueden contener detalle, pero cada fase necesita un resumen de una pantalla con estado, bloqueo, siguiente accion y artifacts relevantes.

Modo recomendado para esta tarea: Lite si el objetivo era simplemente crear la copia espanola; Standard si el objetivo era validar el framework con evidencia. Strict no estuvo justificado para el valor de la tarea, aunque el smoke termino acercandose a Strict por el replan y la cantidad de artifacts. Esto prueba que los modos no son opcionales.

## 6. Risk assessment

- Adoption risk: alto sin modos. El proceso puede ser percibido como burocracia y abandonarse.
- Context risk: medio-alto. Evidence packets largos y artifacts planos aumentan el riesgo de que el prompt builder use contexto viejo, oversized o irrelevante.
- Authority risk: medio. El sistema manejo bien la evidencia oversized, pero todavia hay confusion entre prompt operativo, framework completo, reading_leaf heredada y evidence packet autorizado.
- Implementation risk: medio. El final_prompt fue ejecutable, pero la medicion de diff para untracked files es defectuosa y el implementer no puede probar R2 con el comando indicado.
- Runtime/code risk: no medido. La tarea fue documental, no una feature de codigo real con tests, contratos, reuse scan o runtime.
- Tooling risk: alto para Pi Agent. Pi no estuvo configurado, asi que no se probo el supuesto operativo de lectura barata y read-only.
- Documentation risk: medio. `gpt-5.5-prompting-for-ai-rules.md` declara ser placeholder; no puede sostener una promesa fuerte de prompting final para GPT-5.5.

## 7. Not proven

- Pi Agent real: NOT_PROVEN. El rol reader fue simulado con Codex.
- Skills: NOT_PROVEN. No se probo instalacion, uso ni politica real de skills.
- AGENTS.md en repo destino: NOT_PROVEN. Solo existe `AGENTS.patch.md` y su copia humana `AGENTS.patch.es.md`.
- Codex usando solo final_prompt en un repo de codigo real: NOT_PROVEN. La salida es compatible con el prompt, pero no prueba aislamiento estricto ni ausencia de wrapper adicional.
- Guia GPT-5.5 final: NOT_PROVEN. La guia local sigue declarandose placeholder.
- Runbook completo: NOT_PROVEN. Quickstart y guide existen, pero no bastan para operar el flujo completo sin experto.
- Features complejas con codigo real: NOT_PROVEN. No hubo tests, runtime, reuse scan, arquitectura, contratos ni integracion real.
- Ledger/calibracion post-implementacion: NOT_PROVEN. No existe diff_result ni ledger entry estructurado para este smoke.

## 8. Required changes before real adoption

### P0

- Definir modos Lite/Standard/Strict con budgets concretos: numero maximo de artifacts, evidence packets, lineas por packet, cuando usar una sola instancia lectora, y cuando exigir instancias separadas.
- Corregir templates de reading_leaf para que no prohiban al reader producir su propio `evidence_packet`; deben prohibir implementacion, final prompt y ampliacion de scope, no el output del rol.
- Exigir que el planner divida por decision/evidencia antes de emitir hojas con riesgo de oversize. Regla practica: una primary_question, una decision_link, un grupo de evidencia.
- Agregar limite de salida para evidence packets: human_summary corto, claims compactos, fuentes suficientes, y detalles largos movidos a anexo solo si el modo lo permite.
- Crear un artifact index por fase/rol que marque autoridad: clean, oversized, superseded, context-only, final.
- Corregir verificacion de diff para archivos nuevos untracked. `git diff --numstat -- ai-rules-starter-es` no es suficiente antes de staging.
- Reemplazar o completar la guia GPT-5.5. Mientras diga placeholder, debe tratarse como guia local provisional, no como prueba de prompting final.
- Crear runbook operativo minimo: pasos, comandos, entradas/salidas esperadas, stop labels, handoff al implementer y cierre con diff_result/ledger.

### P1

- Confirmar formalmente que `AGENTS.patch.es.md` es solo lectura humana y que nunca se aplica como `AGENTS.md` operativo.
- Establecer que el implementer debe recibir el `final_prompt.md` generado sin wrapper nuevo que cambie autoridad, scope o evidencias.
- Estandarizar schema de `claims_verified`, `claims_inferred`, `claims_unknown`, `contradictions`, `risks`, `usable_as_prompt_authority` y `context_budget_result`.
- Separar artifacts por carpetas o prefijos de fase: classifier, planner, reader, prompt-builder, implementer, auditor.
- Hacer que las respuestas al humano durante operacion sean cortas: status, bloqueo, siguiente accion, artifact producido.
- Incluir una regla de "single reader per leaf" para Standard/Strict. En Lite puede permitirse batch solo si se declara explicitamente y mantiene output caps.

### P2

- Probar Pi Agent real con allowlist read-only y medicion de W efectiva por harness.
- Probar skills con politica auditable y evidencia de que no expanden scope.
- Ejecutar un smoke en repo de codigo real con AGENTS.md instalado, reuse scan, tests y verificacion runtime.
- Agregar ejemplos de tareas Lite, Standard y Strict con artifacts esperados.
- Agregar diff_result y diff_prediction_ledger_entry de este smoke para calibrar estimaciones futuras.

## 9. Final recommendation

proceed only after P0

El smoke demuestra que AI Rules tiene una base viable: reduce improvisacion, separa evidencia de inferencia, bloquea evidencia oversized y produce un prompt final mas seguro. Pero no debe adoptarse todavia como flujo general sin los P0. En su estado actual, el riesgo principal no es que AI Rules no funcione; es que funcione con demasiado costo, demasiada friccion y demasiada ambiguedad operativa para que humanos y agentes lo usen de forma consistente en trabajo real.
