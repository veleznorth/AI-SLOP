Nota: esta es una copia de lectura en español. La fuente operativa para agentes sigue siendo la versión en inglés dentro de ai-rules-starter/.

# Fundamentos

## Evidencia antes de implementar

No implementes desde suposiciones. Lee primero el mínimo contexto útil y registra lo observado.

## Separación de roles

La clasificación, lectura, captura de evidencia, creación de prompt, implementación y auditoría son responsabilidades separadas.

## Cambios pequeños

Prefiere diffs pequeños y revisables. Si el diff requerido crece más allá del límite actual, detente y reporta el riesgo.

## Estado de claim

Usa estos labels:

- `VERIFICADO`: confirmado por evidencia directa.
- `INFERIDO`: razonado desde evidencia pero no observado directamente.
- `DESCONOCIDO`: aún no conocido.

## Escaneo de reutilización antes de código nuevo

Busca patrones, helpers, tests y docs existentes antes de agregar código nuevo.

## Detenerse es válido

Si falta evidencia, el alcance es demasiado grande o las restricciones entran en conflicto, detente y reporta por qué.
