Nota: esta es una copia de lectura en español. La fuente operativa para agentes sigue siendo la versión en inglés dentro de ai-rules-starter/. No copies archivos desde ai-rules-starter-es/ al repo destino.

# Guía Básica

AI Rules separa roles de pensamiento para que la implementación empiece con evidencia.

## Límites De Autoridad

Una fuente de especificación, como AI Rules v0.4 u otra especificación completa, explica el modelo y el vocabulario.
El prompt de fase es la autoridad operativa para la tarea actual: define los archivos, acciones, checks y condiciones de parada permitidos.

Codex no debe tratar documentos largos como autorización para ampliar el alcance más allá del prompt de fase actual.

## Guardrails De Tamaño

R1 mantiene acotados los archivos productivos: los archivos productivos nuevos permanecen en 500 líneas o menos, y los archivos productivos existentes de más de 500 líneas no crecen sin autorización.
R2 mantiene revisable cada hoja ejecutable: las líneas añadidas + eliminadas de `git diff --numstat` permanecen en 600 líneas o menos.

## Guardrails De Contexto

Lee `docs/operator-context-policy.md` antes de tareas largas. El operador controla
el monitoreo visible del contexto; los agentes no deben inventar porcentajes de `W`. Cuando el
operador tenga una medición visible, regístrala con
`.ai-rules/templates/operator_context_log.yaml`; usa una instancia fresca antes de una
fase nueva si la sesión ya está alta.

## Roles

**Humano**: Declara la solicitud, restricciones, criterios de aceptación y tolerancia al riesgo.

**Clasificador**: Corre una vez por solicitud humana o feature cohesiva. Crea el root requirement packet, feature tree, candidate executable leaves y estimaciones iniciales de diff sin lectura profunda del codebase ni implementación.

**Reading Planner**: Elige reading leaves concretas, puede usar Pi solo lectura para exploración superficial acotada, evita leaves sobredimensionadas y busca implementación existente, implementación parcial, riesgo de duplicación, evidencia faltante y reuse scans requeridos.

**Reader**: Ejecuta entradas reading_leaf y produce evidence packets con campos verified, inferred, unknown, contradiction y risk. Si el operador reporta una leaf sobre 15% W, reporta `READ_LEAF_OVERSIZE` o `CONTEXT_LIMIT_EXCEEDED` y devuelve el caso al Reading Planner.

**Prompt Creator**: Lee la guía local de prompting GPT-5.5 y luego construye el prompt final acotado del implementador desde la solicitud, evidencia, unknowns y restricciones. Los unknowns no se convierten en instrucciones.

**Implementer**: Codex. Implementa solo desde el final prompt, mantiene el alcance pequeño y reporta prueba.

**Auditor**: Revisa el diff, checks, evidencia y riesgos restantes.

Codex no debe saltar directamente desde una solicitud humana a la implementación cuando AI Rules está activo.
