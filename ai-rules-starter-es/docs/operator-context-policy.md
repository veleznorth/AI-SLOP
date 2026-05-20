Nota: esta es una copia de lectura en español. La fuente operativa para agentes sigue siendo la versión en inglés dentro de ai-rules-starter/.

# Politica de contexto del operador

status: empirical provisional policy

## Definicion de W

`W` es la ventana de contexto efectiva visible/usable del modelo y harness
actual. No es solo la ventana maxima anunciada del modelo.

`W` incluye model + harness + tools + loaded context + outputs + history.

Los agentes no se automiden `W` salvo que el harness lo exponga
explicitamente. Los agentes no deben inventar porcentajes de W/contexto. El
operador humano es responsable del control de contexto de sesion mediante la UI
del harness o reportes explicitos del operador.

## Presupuesto formal de reading_leaf

Estos limites aplican formalmente a la ejecucion de `reading_leaf`:

- 8% of W = proxy planning limit.
- 10% of W = exact-count target.
- 15% of W = hard cap.

Ejemplo con `W = 258,400`:

- 8% = 20,672.
- 10% = 25,840.
- 15% = 38,760.

## Politica provisional por fase

- Classifier: una peticion/feature cohesiva por instancia; no leer codebase
  profunda.
- Reading Planner: Pi read-only; exploracion amplia pero superficial.
- Reader: una `reading_leaf` por ejecucion cuando sea posible.
- Final Prompt Builder: usar instancia fresca si los evidence packets son
  grandes.
- Implementer/Codex: no iniciar implementacion si el operador ve contexto alto.
- Auditor: puede correr en instancia fresca con reportes/diffs minimos.

Esta politica de sesion completa por rol/harness es empirica, provisional y
calibrable.

Codex tiene overhead alto. Preferir Pi Agent para exploracion/lectura
read-only.

## Regla de oversize

Si el operador marca una `reading_leaf` por encima de 15% W, la evidencia no es
autoridad limpia.

El reader reporta `READ_LEAF_OVERSIZE` o `CONTEXT_LIMIT_EXCEEDED`, registra que
alcanzo a verificar y no divide la hoja.

El operador devuelve el caso al Reading Planner. El Reading Planner divide la
`reading_leaf` y emite hojas mas pequenas.

## Prevencion desde planner

El planner explora para hacer mejores preguntas; el reader lee para responder
preguntas.

El planner debe prevenir oversize con exploracion superficial en Pi:

- Dividir si `allowed_scope` tiene mas de 3-5 archivos relevantes.
- Dividir si una pregunta contiene multiples decisiones independientes.
- Dividir si UI/backend/tests estan mezclados y son decisiones independientes.
- Crear un `reuse_scan` separado cuando exista riesgo de duplicacion.
- Preferir `grep`/`find` sobre lectura profunda durante planning.
