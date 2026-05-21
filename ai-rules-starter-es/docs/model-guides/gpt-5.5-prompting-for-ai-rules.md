Nota: esta es una copia de lectura en español. La fuente operativa para agentes sigue siendo la versión en inglés dentro de ai-rules-starter/. No copies archivos desde ai-rules-starter-es/ al repo destino.

# Prompting GPT-5.5 Para AI Rules

Este es un placeholder operativo local para creación de final prompts.

## Uso Requerido

- Hacer que el final prompt sea trazable a la solicitud humana, requirement packet,
  reading leaves, evidence packets, restricciones y presupuesto de diff.
- Separar evidencia verificada de unknowns. Los unknowns son bloqueadores, riesgos o
  preguntas explícitas de seguimiento, no instrucciones de implementación.
- Usar condiciones de parada claras para evidencia faltante, criterios de aceptación poco claros,
  límites R1/R2, conflictos de alcance y duplicación probable.
- Mantener el alcance pequeño. No añadir cleanup adyacente, refactors, tooling ni archivos
  salvo que el prompt actual los autorice explícitamente.
- No inventar archivos, rutas, símbolos, APIs, comandos ni nombres de tests.
- Respetar R1 y R2 exactamente como los define el starter kit.
- Exigir un reporte final con status, archivos cambiados, checks ejecutados, tamaño del diff,
  límites de evidencia y riesgos restantes.

Esta guía debe reemplazarse o expandirse solo después de investigación autorizada sobre prompting GPT-5.5.
