Nota: esta es una copia de lectura en español. La fuente operativa para agentes sigue siendo la versión en inglés dentro de ai-rules-starter/.

# Guía básica

AI Rules separa roles de razonamiento para que la implementación empiece con evidencia.

## Límites de autoridad

Una fuente de especificación, como AI Rules v0.4 u otra especificación completa, explica el modelo y el vocabulario.
El prompt de fase es la autoridad operativa para la tarea actual: define los archivos, acciones, checks y condiciones de stop permitidos.

Codex no debe tratar documentos largos como autorización para expandir el alcance más allá del prompt de fase actual.

## Guardrails de tamaño

R1 mantiene acotados los archivos productivos: los archivos productivos nuevos quedan en 500 líneas o menos, y los archivos productivos existentes de más de 500 líneas no crecen sin autorización.
R2 mantiene revisable cada hoja ejecutable: las líneas agregadas + eliminadas de `git diff --numstat` quedan en 600 líneas o menos.

## Roles

**Humano**: Declara la solicitud, restricciones, criterios de aceptación y tolerancia al riesgo.

**Classifier**: Convierte la solicitud en un paquete raíz y decide si el trabajo debe dividirse.

**Reading Planner**: Elige qué archivos, docs o tests se deben leer antes de implementar.

**Reader**: Lee sólo las fuentes planificadas y registra evidencia con estado de claim.

**Prompt Creator**: Construye el prompt final acotado para el implementador desde la solicitud, evidencia y restricciones.

**Implementer**: Codex. Implementa sólo desde el prompt final, mantiene el alcance pequeño y reporta prueba.

**Auditor**: Revisa el diff, checks, evidencia y riesgos restantes.

Codex no debe saltar directamente de una solicitud humana a la implementación cuando AI Rules está activo.
