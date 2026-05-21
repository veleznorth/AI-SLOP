Nota: esta es una copia de lectura en español. La fuente operativa para agentes sigue siendo la versión en inglés dentro de ai-rules-starter/. No copies archivos desde ai-rules-starter-es/ al repo destino.

# Configuración De Pi Agent

Pi Agent es el harness de solo lectura para el trabajo de planner y reader en AI Rules. Úsalo
solo para inspeccionar contexto del repositorio, buscar archivos y producir evidence packets
que otro agente o humano pueda revisar.

El Reading Planner también puede usar Pi para exploración preliminar acotada antes de
emitir entradas reading_leaf. Esa exploración sirve solo para encontrar archivos probables,
tests, docs, implementaciones existentes, implementaciones parciales y riesgo de duplicación.

Pi no es un implementador. No debe escribir código, editar archivos, ejecutar shell
commands, instalar dependencias, habilitar MCP ni mantener estado de sesión por defecto.
Pi permanece limitado a read, grep, find, ls.

## Comando Por Defecto

```sh
pi --tools read,grep,find,ls --no-session
```

Usa el comando por defecto para tareas normales de planner/reader cuando extensions,
skills, prompt templates y themes ya están controlados por el entorno circundante.

## Comando Estricto

```sh
pi --tools read,grep,find,ls --no-session --no-extensions --no-skills --no-prompt-templates --no-themes
```

Usa el comando estricto cuando la tarea necesita la superficie de solo lectura
reproducible más pequeña.

## Capacidades Permitidas

- `read`: abrir archivos específicos solicitados por el reading plan.
- `grep`: buscar patrones de texto en el alcance permitido.
- `find`: descubrir archivos y directorios en el alcance permitido.
- `ls`: listar directorios en el alcance permitido.

## Capacidades Prohibidas

- Escribir archivos.
- Editar archivos.
- Ejecutar bash o shell commands.
- Instalar dependencias.
- Usar MCP.
- Persistir una sesión por defecto.

## Stop Labels

Usa estas etiquetas cuando Pi no pueda continuar de forma segura como harness de solo lectura:

- `HARNESS_SCOPE_EXCEEDED`: la solicitud requiere acceso fuera del alcance permitido
  o pide que Pi actúe como implementador.
- `HARNESS_INSUFFICIENT`: las herramientas de solo lectura no bastan para responder la solicitud
  del planner o de evidencia.
- `STOP-SCOPE-ESCALATION-REQUIRED`: continuar requeriría escritura, edición, bash,
  instalación de dependencias, MCP o comportamiento de sesión persistente.
