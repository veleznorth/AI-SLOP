Nota: esta es una copia de lectura en español. La fuente operativa para agentes sigue siendo la versión en inglés dentro de ai-rules-starter/. No copies archivos desde ai-rules-starter-es/ al repo destino.

# Fundamentos

## Evidencia Antes De Implementar

No implementes desde suposiciones. Lee primero el contexto mínimo útil y registra lo observado.

## Separación De Roles

Clasificación, lectura, captura de evidencia, creación de prompts, implementación y auditoría son responsabilidades separadas.

## Cambios Pequeños

Prefiere diffs pequeños y revisables. Si el diff requerido crece más allá del límite actual, detente y reporta el riesgo.

## Estado De Claims

Usa estas etiquetas:

- `VERIFICADO`: confirmado por evidencia directa.
- `INFERIDO`: razonado desde evidencia pero no observado directamente.
- `DESCONOCIDO`: aún no conocido.

## Reuse Scan Antes De Código Nuevo

Busca patrones, helpers, tests y docs existentes antes de añadir código nuevo.

## Detenerse Es Válido

Si falta evidencia, el alcance es demasiado grande o las restricciones entran en conflicto, detente y reporta por qué.
