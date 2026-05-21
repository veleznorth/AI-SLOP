Nota: esta es una copia de lectura en español. La fuente operativa para agentes sigue siendo la versión en inglés dentro de ai-rules-starter/. No copies archivos desde ai-rules-starter-es/ al repo destino.

# Seguridad MCP

Trata las herramientas MCP como capacidades externas con riesgo específico del proyecto.

Reglas mínimas:

- Identificar la herramienta y el entorno destino antes de usarla.
- No exponer secretos en packets ni reportes.
- Preferir llamadas de solo lectura hasta que se requiera comportamiento de escritura.
- Registrar salidas de herramientas como evidencia cuando afecten la implementación.
