# Contexto, output y diff

## W efectivo

- W efectivo no es la ventana maxima anunciada por el modelo.
- El contexto real incluye instrucciones de sistema, developer, user,
  `AGENTS.md`, archivos, tools, imagenes, summaries y overhead del harness.
- No hay cifra publica oficial para W efectivo exacto de Codex por superficie.
- El operador humano controla el contexto visible y decide cuando recortar,
  resumir, abrir instancia nueva o dividir la hoja.
- El agente puede reportar riesgo de saturacion, pero no inventa porcentajes
  seguros de ventana ni overhead numerico.
- Compaction puede ayudar continuidad operativa, pero no sustituye evidencia ni
  autoriza claims nuevos.

## Reglas de output

- Usar Markdown para `final_prompt.md`, guias modelo, reportes humanos y notas
  de operacion.
- Usar JSON estricto con Structured Outputs como direccion futura para contratos
  machine-readable: `root_requirement_packet`, `reading_leaf`,
  `evidence_packet`, `diff_estimate`, `audit_packet` y reportes estructurados.
- YAML actual queda como v0.1 humano-operable, no como ideal final para
  contratos estrictos.
- Si el output es machine-readable, no mezclar prosa fuera del contrato.
- Si falta informacion obligatoria, devolver stop code o error estructurado; no
  inventar valores.
- Limitar longitud visible con `human_summary_short` y listas acotadas.

## Diff estimate

- `diff_estimate` es forecasting, no medicion exacta.
- El forecast debe incluir `low`, `likely`, `high`, unidad, drivers, unknowns y
  nota de calibracion.
- No esconder unknowns dentro de rangos optimistas.
- La medicion final debe ser git-native y reportada.
- Registrar diff real frente al forecast en un ledger cuando exista una fase
  autorizada para hacerlo.

## R1 / R2

- R1/R2 son limites de autorizacion, no metas flexibles.
- Si el forecast o el diff real excede R1/R2, dividir la hoja o detenerse.
- Si terminar exige tocar areas no autorizadas, usar
  `STOP-SCOPE-ESCALATION-REQUIRED`.
- Si el forecasting no basta para autorizar, usar
  `STOP-DIFF-ESTIMATE-UNCERTAIN`.

## numstat_method / untracked files

Para archivos nuevos untracked, `git diff --numstat` no los mide hasta que Git
los conozca. Usar `git add -N ai-rules-starter` antes de medir:

```sh
git add -N ai-rules-starter
git diff --numstat -- ai-rules-starter/docs/model-guides \
  ai-rules-starter/examples/spanish-copy-smoke-test/artifacts/33-gpt55_guide_split_report.md
```

`git add -N` registra intent-to-add sin staged content real. El reporte debe
explicar el metodo si hay archivos nuevos.
