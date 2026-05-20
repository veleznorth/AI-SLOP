Nota: esta es una copia de lectura en español. La fuente operativa para agentes sigue siendo la versión en inglés dentro de ai-rules-starter/.

# Setup de Pi Agent

Pi Agent es el harness de sólo lectura para el trabajo de planner y reader de AI Rules. Úsalo sólo para inspeccionar contexto del repositorio, buscar archivos y producir paquetes de evidencia que otro agente o humano pueda revisar.

Pi no es un implementador. No debe escribir código, editar archivos, ejecutar comandos shell, instalar dependencias, habilitar MCP ni mantener estado de sesión por defecto.

## Comando por defecto

```sh
pi --tools read,grep,find,ls --no-session
```

Usa el comando por defecto para tareas normales de planner/reader cuando extensions, skills, prompt templates y themes ya estén controlados por el entorno alrededor.

## Comando estricto

```sh
pi --tools read,grep,find,ls --no-session --no-extensions --no-skills --no-prompt-templates --no-themes
```

Usa el comando estricto cuando la tarea necesite la superficie de sólo lectura reproducible más pequeña.

## Capacidades permitidas

- `read`: abrir archivos específicos pedidos por el plan de lectura.
- `grep`: buscar patrones de texto en el alcance permitido.
- `find`: descubrir archivos y directorios en el alcance permitido.
- `ls`: listar directorios en el alcance permitido.

## Capacidades prohibidas

- Escribir archivos.
- Editar archivos.
- Ejecutar bash o comandos shell.
- Instalar dependencias.
- Usar MCP.
- Persistir una sesión por defecto.

## Stop Labels

Usa estos labels cuando Pi no pueda continuar con seguridad como harness de sólo lectura:

- `HARNESS_SCOPE_EXCEEDED`: la solicitud requiere acceso fuera del alcance permitido o pide que Pi actúe como implementador.
- `HARNESS_INSUFFICIENT`: las herramientas de sólo lectura no son suficientes para responder la solicitud de planner o evidencia.
- `STOP-SCOPE-ESCALATION-REQUIRED`: continuar requeriría comportamiento de escritura, edición, bash, instalación de dependencias, MCP o sesión persistente.
