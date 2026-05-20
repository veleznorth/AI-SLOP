Nota: esta es una copia de lectura en español. La fuente operativa para agentes sigue siendo la versión en inglés dentro de ai-rules-starter/.

# Seguridad de MCP

Trata las herramientas MCP como capacidades externas con riesgo específico del proyecto.

Reglas mínimas:

- Identificar la herramienta y el entorno destino antes de usarla.
- No exponer secretos en paquetes o reportes.
- Preferir llamadas de sólo lectura hasta que se requiera comportamiento de escritura.
- Registrar outputs de herramientas como evidencia cuando afecten la implementación.
