Nota: esta es una copia de lectura en español. La fuente operativa para agentes sigue siendo la versión en inglés dentro de ai-rules-starter/. No copies archivos desde ai-rules-starter-es/ al repo destino.

# Política De Contexto Del Operador

status: política provisional empírica

## Definición De W

`W` es la ventana de contexto visible/usable efectiva del modelo actual más el
harness. No es solo la ventana de contexto máxima anunciada para el modelo.

`W` incluye modelo + harness + herramientas + contexto cargado + salidas + historial.

Los agentes no se auto-miden `W` salvo que el harness lo exponga explícitamente.
los agentes no deben inventar porcentajes de W/contexto. El operador humano controla
el contexto a nivel de sesión mediante la UI del harness o reportes explícitos del
operador.

## Presupuesto Formal De Reading Leaf

Estos límites aplican formalmente a la ejecución de `reading_leaf`:

- 8% of W = proxy planning limit.
- 10% of W = exact-count target.
- 15% of W = hard cap.

Ejemplo con `W = 258,400`:

- 8% = 20,672.
- 10% = 25,840.
- 15% = 38,760.

## Política Provisional De Fase

- Classifier: una solicitud/feature cohesiva por instancia; sin lectura profunda del codebase.
- Reading Planner: Pi solo lectura; exploración amplia pero superficial.
- Reader: una `reading_leaf` por run cuando sea posible.
- Final Prompt Builder: usa una instancia fresca si los evidence packets son grandes.
- Implementer/Codex: no iniciar implementación si el operador ve contexto alto.
- Auditor: puede correr en una instancia fresca con reportes/diffs mínimos.

Esta política a nivel de sesión es empírica, provisional y calibrable por rol y
harness.

Codex tiene overhead alto. Prefiere Pi Agent para exploración y lectura de solo lectura.

## Regla De Oversize

Si el operador marca una `reading_leaf` sobre 15% W, la evidencia no es autoridad
limpia.

El reader reporta `READ_LEAF_OVERSIZE` o `CONTEXT_LIMIT_EXCEEDED`, registra
lo que fue verificado y no divide la leaf.

El operador enruta el caso de vuelta al Reading Planner. El Reading Planner
divide la `reading_leaf` y emite leaves más pequeñas.

## Prevención Del Planner

El planner explora para hacer mejores preguntas; el reader lee para responder
preguntas.

El planner debe prevenir oversize con exploración superficial de Pi:

- Dividir si `allowed_scope` tiene más de 3-5 archivos relevantes.
- Dividir si una pregunta contiene varias decisiones independientes.
- Dividir si UI/backend/tests están mezclados y representan decisiones independientes.
- Crear un `reuse_scan` separado cuando exista riesgo de duplicación.
- Preferir `grep`/`find` sobre lectura profunda durante la planificación.
