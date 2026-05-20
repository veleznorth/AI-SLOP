# AI Rules — Especificación consolidada del framework

**Versión:** 0.4 consolidada  
**Idioma:** Español  
**Estado:** Documento operativo formal para revisión y uso en codebases reales  
**Ámbito principal:** coordinación de múltiples instancias de IA para ingeniería de software  
**Stack específico cubierto por reglas condicionales:** Convex  
**Implementador final frecuente:** GPT-5.5/Codex  

> Esta versión consolida AI Rules v0.3 con reglas arquitectónicas para Convex y reglas de predicción, resultado y calibración histórica de diff. No asume Flutter, SQLite, Drift, local-first ni ningún cliente específico como stack base. Si un proyecto incluye clientes externos, persistencia local, sync, SDKs, HTTP, OpenAPI u otros runtimes, esos elementos se tratan como límites contractuales condicionales o dependencias externas relevantes para la hoja ejecutable.

---

## Índice

1. [Propósito del documento](#1-propósito-del-documento)
2. [Qué es AI Rules](#2-qué-es-ai-rules)
3. [Problemas que resuelve](#3-problemas-que-resuelve)
4. [Principios de diseño](#4-principios-de-diseño)
5. [Lenguaje normativo](#5-lenguaje-normativo)
6. [Flujo operativo completo](#6-flujo-operativo-completo)
7. [Separación estricta de roles](#7-separación-estricta-de-roles)
8. [Conceptos y definiciones centrales](#8-conceptos-y-definiciones-centrales)
9. [Presupuestos de contexto](#9-presupuestos-de-contexto)
10. [Límites de cambio y tamaño](#10-límites-de-cambio-y-tamaño)
11. [Predicción, resultado y calibración histórica de diff](#11-predicción-resultado-y-calibración-histórica-de-diff)
12. [Artefactos obligatorios](#12-artefactos-obligatorios)
13. [Ownership de artefactos](#13-ownership-de-artefactos)
14. [Roles del framework](#14-roles-del-framework)
15. [Reglas normativas R0–R10](#15-reglas-normativas-r0r10)
16. [Capa de decisiones arquitectónicas](#16-capa-de-decisiones-arquitectónicas)
17. [Lectura anti-duplicación y reuse scan](#17-lectura-anti-duplicación-y-reuse-scan)
18. [Anti-god-object, anti-manager y anti-utils prematuro](#18-anti-god-object-anti-manager-y-anti-utils-prematuro)
19. [Reglas específicas para Convex](#19-reglas-específicas-para-convex)
20. [Dependencias externas, documentación oficial y opensrc](#20-dependencias-externas-documentación-oficial-y-opensrc)
21. [Prompt final para GPT-5.5/Codex](#21-prompt-final-para-gpt-55codex)
22. [Catálogo de etiquetas de detención](#22-catálogo-de-etiquetas-de-detención)
23. [Formatos YAML obligatorios](#23-formatos-yaml-obligatorios)
24. [Ejemplos operativos](#24-ejemplos-operativos)
25. [Checklists de uso rápido](#25-checklists-de-uso-rápido)
26. [Errores frecuentes que AI Rules busca prevenir](#26-errores-frecuentes-que-ai-rules-busca-prevenir)
27. [Guía de evolución del documento](#27-guía-de-evolución-del-documento)

---

## 1. Propósito del documento

Este documento define formalmente **AI Rules**, un framework operativo para coordinar múltiples instancias de IA durante tareas de ingeniería de software.

AI Rules no es una feature de producto, no es una librería, no es un paquete de código y no es una arquitectura única que todos los repos deban adoptar. Es una disciplina de trabajo para que una petición humana se convierta en una cadena de artefactos auditables antes de que una instancia de IA implemente código.

El documento existe para que una persona o una instancia de IA pueda entender:

- qué problema resuelve AI Rules;
- qué roles existen;
- qué puede y no puede hacer cada rol;
- qué artefactos deben producirse;
- cómo se controla el contexto;
- cómo se predice, mide y calibra el tamaño de un cambio;
- cómo se evita la duplicación de código;
- cómo se evitan god objects y abstracciones prematuras;
- cómo se manejan decisiones arquitectónicas;
- cómo se decide cuándo leer dependencias externas;
- cómo se construye un prompt final trazable para GPT-5.5/Codex;
- cuándo detener el flujo en lugar de improvisar.

La intención central es que cada cambio de código esté precedido por evidencia suficiente, alcance claro, predicción de diff, decisiones explícitas y verificación posterior.

---

## 2. Qué es AI Rules

AI Rules es un framework de orquestación para convertir una petición humana en un proceso controlado de ingeniería asistida por IA.

Su unidad principal no es el código, sino la **trazabilidad entre intención, lectura, evidencia, decisión, predicción, implementación y resultado**.

En vez de pedirle a una única instancia de IA que lea, razone, diseñe e implemente todo de una vez, AI Rules separa el trabajo en roles especializados:

1. el humano define intención y decisiones de producto;
2. el clasificador transforma la petición en requisitos, árbol de features y primera predicción de diff;
3. el planificador de lectura decide qué debe leerse;
4. la instancia lectora lee dentro de un alcance limitado y produce evidencia;
5. el creador de prompt sintetiza evidencia, decisiones arquitectónicas y predicción final previa a implementación;
6. el implementador modifica código dentro de un alcance aprobado;
7. el reporte final mide el diff real y registra calibración histórica.

AI Rules se basa en una idea simple:

> Una IA no debe implementar por intuición lo que no fue definido, leído, evidenciado, trazado, dimensionado y autorizado.

---

## 3. Problemas que resuelve

AI Rules existe porque las instancias de IA, incluso modelos fuertes, suelen fallar en codebases reales de formas repetitivas.

### 3.1 Alucinación de archivos, símbolos y arquitectura

Una instancia puede asumir que existen archivos, funciones, servicios, DAOs, providers, helpers o patrones que realmente no existen. AI Rules obliga a confirmar fuentes mediante inventario real del repo, evidence packets o decisión humana explícita.

### 3.2 Implementación sin lectura suficiente

Una IA puede modificar código sin entender contratos existentes. AI Rules obliga a crear hojas de lectura y evidence packets antes del prompt final.

### 3.3 Duplicación de código existente

Una IA puede crear una función nueva aunque ya exista una equivalente o parcialmente equivalente. AI Rules introduce lectura anti-duplicación y `reuse_scan` obligatorio para hojas que introducen o modifican comportamiento.

### 3.4 Soluciones paralelas

Una IA puede crear un nuevo service, repository, mapper o helper en vez de extender el módulo existente. AI Rules obliga a verificar patrones locales y candidatos de reuso antes de crear abstracciones nuevas.

### 3.5 God objects y managers gigantes

Una IA puede concentrar validación, auth, ownership, reglas de negocio, persistencia y side effects en una sola función u objeto. AI Rules detecta este camino como `STOP-GOD-OBJECT-PATH`.

### 3.6 Abstracciones prematuras

Una IA puede crear `utils`, `helpers`, `shared`, `manager` o `common` sin ownership claro ni reutilización real. AI Rules bloquea esas abstracciones con `STOP-PREMATURE-ABSTRACTION`.

### 3.7 Cambios demasiado grandes

Una IA puede aceptar tareas que generan diffs enormes, difíciles de revisar. AI Rules usa un límite de diff por task: 600 líneas añadidas + eliminadas.

### 3.8 Predicciones de diff falsas o demasiado optimistas

Una IA puede decir “esto será pequeño” sin evidencia, sin rango de incertidumbre y sin revisar historial. AI Rules trata el diff estimado como predicción calibrable, no como medición exacta.

### 3.9 Uso excesivo o irrelevante de contexto

Una IA puede leer demasiado, llenar la ventana de contexto y perder precisión. AI Rules limita cada hoja de lectura mediante presupuesto porcentual de W efectiva.

### 3.10 Prompts finales no trazables

Una IA puede crear un prompt final con instrucciones que no vienen de evidencia ni decisiones humanas. AI Rules exige que cada instrucción crítica se enlace a un `packet_id`, `claim_id`, `decision_id` o decisión humana explícita.

### 3.11 Decisiones arquitectónicas improvisadas

Una IA puede introducir Clean Architecture, repository pattern, microservicios, managers o capas nuevas porque “parecen limpias”. AI Rules exige verificar patrones del repo o pedir autorización humana.

### 3.12 Contratos de datos rotos

En proyectos Convex, una IA puede romper contratos entre `schema.ts`, validators, queries, mutations, actions, internal functions, generated API/types, callers externos o integraciones. AI Rules exige evidencia de contrato antes de tocar esos límites.

---

## 4. Principios de diseño

### 4.1 Evidencia antes que implementación

Ninguna implementación debe depender de suposiciones si existe forma razonable de obtener evidencia dentro del alcance autorizado.

### 4.2 Separación de roles

Una instancia no debe cambiar de rol por iniciativa propia. Si una instancia necesita ejecutar una acción fuera de su rol, debe detenerse con `ROLE_BLOCKED_BY_SCOPE`.

### 4.3 Contexto mínimo suficiente

Leer más no siempre mejora la implementación. AI Rules promueve hojas de lectura pequeñas, con pregunta primaria única y presupuesto de contexto explícito.

### 4.4 Cambios pequeños y revisables

Cada hoja ejecutable debe tener un diff predicho seguro bajo el límite R2. Si el rango conservador supera ese límite, la hoja debe dividirse, reducirse o bloquearse.

### 4.5 Predicción explícita, medición posterior y calibración

El diff estimado es una predicción previa, no una medición. El diff real se mide después de implementar. La diferencia entre ambos debe registrarse para mejorar futuras predicciones.

### 4.6 Trazabilidad estricta

Toda instrucción final debe venir de:

- una decisión humana explícita;
- un claim VERIFICADO;
- un artefacto aprobado y trazable.

### 4.7 No convertir inferencias en hechos

Una inferencia puede orientar, pero no puede convertirse en instrucción obligatoria.

### 4.8 Arquitectura local por encima de arquitectura genérica

Si el repo tiene un patrón verificado, AI Rules debe respetarlo salvo autorización humana explícita.

### 4.9 Reuso antes que código nuevo

Antes de crear funciones, helpers, services, repositories, mappers o validators nuevos, se debe buscar implementación existente equivalente o parcialmente equivalente.

### 4.10 Seguridad y contratos como boundaries críticos

Auth, ownership, validación, schema, DTOs, contratos de API, generated code y dependencias externas deben tratarse como límites contractuales.

### 4.11 Detenerse es una acción válida

AI Rules considera correcto detenerse cuando faltan evidencia, permisos, decisiones humanas, calibración o presupuesto. Improvisar es un fallo.

### 4.12 No burocratizar cambios triviales

AI Rules debe proteger cambios reales sin convertir tareas triviales, localizadas y bien entendidas en procesos desproporcionados. Las hojas triviales pueden usar estimación ligera, pero estructurada.

---

## 5. Lenguaje normativo

Este documento usa lenguaje normativo deliberadamente.

- **DEBE** indica una obligación estricta.
- **NO DEBE** indica una prohibición estricta.
- **PUEDE** indica una acción permitida bajo condiciones.
- **SI/ENTONCES** define una regla ejecutable.
- **Etiqueta de detención** indica una etiqueta que debe reportarse cuando una regla aplicable no puede cumplirse.

Ejemplo:

```text
SI una hoja de lectura no contiene allowed_scope, ENTONCES NO DEBE emitirse.
```

### 5.1 Sobre etiquetas de detención

AI Rules usa el término **etiqueta de detención**, no “código de detención”.

Una etiqueta de detención no implica fracaso total. Significa que la instancia detectó una condición donde continuar sería menos seguro que detenerse, reportar y pedir evidencia, división, autorización o decisión humana.

---

## 6. Flujo operativo completo

El flujo completo es:

```text
Petición humana
↓
Paquete de Requisitos Raíz
↓
Árbol de features
↓
Feature nodes
↓
Hoja ejecutable
↓
Diff estimate inicial y refinado
↓
Árbol de lectura
↓
Hojas de lectura
↓
Instancias lectoras
↓
Evidence packets
↓
Reuse scan / external source decision / evidencia arquitectónica, si aplican
↓
Architecture decision packet, si aplica
↓
Diff estimate pre_prompt committed
↓
Creador de prompt
↓
Prompt final trazable
↓
Implementador GPT-5.5/Codex
↓
Verificación
↓
Diff result
↓
Diff prediction ledger
↓
Reporte final
```

### 6.1 Separación principal del flujo

- El clasificador divide lo que se va a construir.
- El planificador de lectura divide lo que se debe leer.
- La instancia lectora ejecuta lecturas.
- El creador de prompt sintetiza evidencia y construye prompt final.
- El implementador modifica código.
- El reporte final mide resultado y alimenta calibración.

### 6.2 Regla de no salto

SI una petición humana va a convertirse en implementación, ENTONCES NO DEBE saltar directo a código.

SI una petición humana es vaga, ENTONCES DEBE convertirse primero en Paquete de Requisitos Raíz o detenerse por falta de comportamiento esperado, criterios o decisión humana.

---

## 7. Separación estricta de roles

SI una instancia recibe un rol, ENTONCES DEBE operar exclusivamente dentro de ese rol.

SI una acción no pertenece al rol asignado, ENTONCES la instancia NO DEBE ejecutarla.

SI la siguiente acción necesaria no pertenece al rol asignado, ENTONCES la instancia DEBE detenerse y reportar `ROLE_BLOCKED_BY_SCOPE`.

SI una instancia reporta `ROLE_BLOCKED_BY_SCOPE`, ENTONCES el humano DEBE decidir si crea una nueva instancia con otro rol o si autoriza una instancia distinta.

La separación de roles evita que una misma instancia:

- convierta inferencias en requisitos;
- lea fuera de alcance;
- cree prompts finales sin evidencia;
- implemente sin autorización;
- mida su propio éxito sin reporte trazable;
- introduzca arquitectura no aprobada.

---

## 8. Conceptos y definiciones centrales

### 8.1 Ventana efectiva

`W` = ventana efectiva disponible para una instancia dentro del modelo+harness usado.

W efectiva NO ES la ventana máxima anunciada del modelo.

W efectiva DEBE considerar:

- modelo;
- harness;
- instrucciones del sistema;
- reglas del proyecto;
- herramientas;
- contexto cargado;
- outputs de herramientas;
- historial;
- artefactos requeridos.

SI W efectiva no está medida para el modelo+harness usado, ENTONCES se usa `258,400 tokens` como base conservadora.

Se distinguen:

- W efectiva del clasificador;
- W efectiva del planificador;
- W efectiva del lector;
- W efectiva del creador de prompt;
- W efectiva del implementador.

SI la instancia usa Pi Agent, ENTONCES W efectiva DEBE medirse en Pi Agent con el modelo configurado.

SI la instancia usa Codex, ENTONCES W efectiva DEBE medirse en Codex con el modelo configurado.

### 8.2 Requisitos y features

**Paquete de Requisitos Raíz** = artefacto raíz que compacta intención humana, comportamiento esperado, requisitos funcionales, requisitos no funcionales, restricciones, fuera de alcance, criterios de aceptación, criterios de no regresión, riesgos, decisiones humanas explícitas y desconocidos.

**Árbol de features** = árbol que divide una petición humana de software en features, subfeatures o tasks hasta llegar a hojas ejecutables.

**Feature node** = nodo del árbol de features que representa una feature, subfeature o task derivada del Paquete de Requisitos Raíz.

**Hoja ejecutable** = hoja terminal del árbol de features que tiene diff predicho seguro bajo R2, una sola responsabilidad, trazabilidad al Paquete de Requisitos Raíz, criterios de aceptación, criterios de no regresión, alcance incluido/excluido y ningún desconocido bloqueante.

**Task ejecutable** = hoja ejecutable del árbol de features.

### 8.3 Lectura y evidencia

**Árbol de lectura** = árbol creado por el planificador de lectura a partir de una sola hoja ejecutable del árbol de features.

**Hoja de lectura** = contrato de evidencia creado para responder una sola pregunta primaria sobre una hoja ejecutable.

Una hoja de lectura:

- NO ES task de implementación;
- NO ES mini-investigación abierta;
- DEBE ser pequeña, trazable, auditable y orientada a evidencia.

**Pregunta primaria** = una sola pregunta concreta, falsable y necesaria para entender o preparar la implementación de una hoja ejecutable.

**Decision link** = explicación explícita de qué decisión de implementación depende de la respuesta de una hoja de lectura.

**Allowed scope** = fuentes, rutas, símbolos, tests, documentos, comandos o dependencias que una instancia lectora PUEDE revisar.

**Disallowed scope** = fuentes, rutas, símbolos, tests, documentos, comandos o dependencias que una instancia lectora NO DEBE revisar salvo autorización humana o nueva planificación.

**Search plan** = lista corta de búsquedas o lecturas permitidas para responder la pregunta primaria.

**Success condition** = condición concreta que indica cuándo una hoja de lectura quedó respondida.

**Stop conditions** = condiciones explícitas que obligan a detener la lectura.

**Evidence packet** = reporte de lectura que separa claims VERIFICADOS, INFERIDOS, DESCONOCIDOS, contradicciones, riesgos, bloqueos y fuentes revisadas.

### 8.4 Claims

**Claim VERIFICADO** = afirmación soportada directamente por archivo, línea, símbolo, comando, output, documentación revisada, fuente externa autorizada o decisión humana explícita.

**Claim INFERIDO** = conclusión razonable basada en evidencia, pero no confirmada directamente.

**Claim DESCONOCIDO** = dato necesario o relevante que no fue probado.

### 8.5 Diff

**Diff total** = líneas añadidas + líneas eliminadas según `git diff --numstat`.

**Diff estimado** = predicción previa de tamaño de cambio hecha por una instancia autorizada antes de implementar. No es una medición exacta.

**Diff predicho** = valor o rango dentro de un `diff_estimate`, incluyendo `predicted_total_diff` y `uncertainty_range.low/likely/high`.

**Diff real** = resultado medido después de implementar mediante `git diff --numstat`.

**Diff resultado** = artefacto estructurado `diff_result` que registra el diff real, archivos cambiados, verificación y separación analítica de diff humano/generado cuando sea posible.

**Predicted total diff** = `predicted_added_lines + predicted_deleted_lines`.

**Actual total diff** = `git_numstat.added_lines + git_numstat.deleted_lines`.

**Absolute error** = `abs(predicted_total_diff - actual_total_diff)`.

**Signed error** = `predicted_total_diff - actual_total_diff`.

**Relative error** = `absolute_error / max(actual_total_diff, 1)`.

**Uncertainty range** = rango `low`, `likely`, `high`. AI Rules NO DEBE llamarlo intervalo de confianza salvo que exista calibración histórica suficiente para justificar cobertura empírica.

**Diff prediction ledger** = historial persistente de predicción vs resultado para calibrar estimaciones futuras.

### 8.6 Arquitectura y reuso

**Architecture decision packet** = artefacto que sintetiza decisiones arquitectónicas requeridas para una hoja ejecutable, incluyendo boundaries afectados, patrones existentes verificados, reuse candidates, riesgos de duplicación, riesgos de god object, riesgos de abstracción prematura, dependencias externas relevantes, movimientos arquitectónicos permitidos y movimientos arquitectónicos prohibidos.

**Reuse scan** = lectura anti-duplicación que busca implementación existente equivalente o parcialmente equivalente antes de permitir que una hoja ejecutable introduzca o modifique comportamiento.

**External source decision** = artefacto que documenta si una dependencia externa requiere docs oficiales, tipos/generated locales, fuente presente en repo, fuente externa vía opensrc, autorización humana o bloqueo.

**God object path** = ruta de solución donde una función, archivo, handler, service, manager u objeto concentra responsabilidades independientes como validación, auth, ownership, reglas de negocio, persistencia y side effects.

**Premature abstraction** = abstracción nueva, genérica o compartida que no tiene reutilización verificada, ownership claro ni necesidad arquitectónica trazable.

**Manager genérico** = objeto o módulo amplio que coordina responsabilidades de varios bounded contexts o casos de uso sin boundary explícito ni ownership claro.

**Utils prematuro** = helper, common, shared o utils creado antes de verificar usos reales compatibles dentro del mismo contexto semántico.

### 8.7 Harness e instancia

**Instancia** = ejecución separada de un agente con rol, alcance, entrada, salida y permisos definidos.

**Harness** = entorno o herramienta usada por una instancia para leer, escribir, ejecutar comandos o interactuar con el repo.

---

## 9. Presupuestos de contexto

AI Rules controla el tamaño de cada hoja de lectura para evitar sobrecargar el modelo.

### 9.1 Límites

Con W efectiva:

- umbral previo de emisión por proxy = 8% de W;
- límite objetivo con conteo exacto = 10% de W;
- límite máximo duro = 15% de W.

Si W = 258,400 tokens:

```text
8%  = 20,672 tokens
10% = 25,840 tokens
15% = 38,760 tokens
```

### 9.2 Reglas

SI el planificador sólo tiene estimación proxy, ENTONCES NO DEBE emitir una hoja de lectura cuya estimación conservadora supere 8% de W.

SI el planificador tiene conteo exacto del modelo objetivo, ENTONCES NO DEBE emitir una hoja de lectura cuyo tamaño total estimado supere 10% de W.

SI cualquier escenario razonable de una hoja de lectura puede superar 15% de W, ENTONCES el planificador NO DEBE emitir esa hoja.

SI una hoja de lectura excede el presupuesto permitido antes de emitirse, ENTONCES el planificador DEBE dividirla en hojas de lectura más pequeñas.

SI una instancia lectora descubre durante ejecución que la hoja supera 15% de W, ENTONCES DEBE detenerse y reportar `STOP-CONTEXT-WINDOW-EXCEEDED` o `CONTEXT_LIMIT_EXCEEDED`, según corresponda.

---

## 10. Límites de cambio y tamaño

### 10.1 R1: límite de archivo

SI un archivo es código fuente ejecutable del producto, ENTONCES su longitud máxima permitida es 500 líneas.

SI un archivo productivo ya supera 500 líneas antes de la task, ENTONCES la task NO DEBE aumentarlo salvo autorización humana explícita.

SI una task crea un archivo productivo nuevo, ENTONCES ese archivo NO DEBE superar 500 líneas.

SI un archivo es generado automáticamente, ENTONCES R1 NO aplica, pero el archivo DEBE identificarse como generado.

### 10.2 R2: límite de cambio

El límite de cambio por hoja ejecutable es:

```text
R2 = 600 líneas de diff total
```

El diff total se calcula como:

```text
líneas añadidas + líneas eliminadas
```

según `git diff --numstat`.

R2 se usa en dos momentos:

1. **Antes de implementar**, como regla de admisibilidad basada en `diff_estimate` committed.
2. **Después de implementar**, como regla de resultado basada en `diff_result` real.

SI una hoja tiene `uncertainty_range.high > 600`, ENTONCES DEBE dividirse, reducirse o bloquearse antes de implementar.

SI `uncertainty_range.likely <= 600` pero `uncertainty_range.high > 600`, ENTONCES la hoja DEBE tratarse como riesgo de subestimación y DEBE preferirse división o re-scoping conservador.

SI el diff real medido por `git diff --numstat` supera 600, ENTONCES el implementador DEBE reportar `R2_EXCEEDED` y NO DEBE iniciar otra task.

### 10.3 Diff humano y diff generado

AI Rules conserva una métrica canónica para R2:

```text
canonical_actual_total_diff = git_numstat.added_lines + git_numstat.deleted_lines
```

SI el harness puede atribuir diff generado y diff humano con fiabilidad razonable, ENTONCES DEBE registrarlos por separado.

SI el harness no puede atribuir diff generado y diff humano con fiabilidad razonable, ENTONCES DEBE marcar `attribution_status: unavailable` y NO DEBE bloquear la ejecución por esa ausencia.

SI se evalúa cumplimiento de R2, ENTONCES AI Rules DEBE usar `canonical_actual_total_diff`, NO DEBE sustituirlo por `human_authored_total`.

---

## 11. Predicción, resultado y calibración histórica de diff

### 11.1 Tesis operativa

AI Rules trata el tamaño de cambio como un problema de **forecasting y calibración**, no como una adivinanza única.

Una estimación de diff:

- es previa a la implementación;
- es incierta;
- DEBE expresar rango `low/likely/high`;
- DEBE registrar evidencia y factores de riesgo;
- DEBE compararse contra el resultado real;
- DEBE alimentar historial local para mejorar futuras estimaciones.

### 11.2 Etapas de estimación

AI Rules usa estas etapas:

1. `initial_classification`: primera predicción hecha por el clasificador desde la petición y el Paquete de Requisitos Raíz.
2. `feature_tree`: refinamiento al dividir features y formar hojas candidatas.
3. `post_reading`: refinamiento después de hojas de lectura y evidence packets.
4. `pre_prompt`: predicción final committed antes de enviar prompt al implementador.
5. `post_implementation`: resultado real medido después de implementar.

La estimación que gobierna la admisibilidad final bajo R2 es la estimación `pre_prompt` con `estimate_status: committed`.

### 11.3 Diferencias obligatorias

AI Rules DEBE diferenciar:

| Término | Qué es | Cuándo existe | Productor |
|---|---|---|---|
| `diff_estimate` | Artefacto de predicción | Antes de implementar | Clasificador / creador de prompt según etapa |
| `predicted_total_diff` | Número canónico predicho | Dentro de `diff_estimate` | Rol estimador autorizado |
| `uncertainty_range` | Rango low/likely/high | Dentro de `diff_estimate` | Rol estimador autorizado |
| `diff_result` | Artefacto de medición real | Después de implementar | Implementador en reporte final |
| `actual_total_diff` | Número real canónico | Dentro de `diff_result` | Implementador mediante `git diff --numstat` |
| `diff_prediction_ledger_entry` | Registro histórico | Después de `diff_result` | Auditor de cierre o sistema de ledger |

### 11.4 Reglas de predicción

SI una hoja ejecutable no tiene `diff_estimate` en etapa `pre_prompt`, ENTONCES NO DEBE ser enviada al implementador.

SI el clasificador crea un `feature_node` que puede convertirse en hoja ejecutable, ENTONCES DEBE producir un `diff_estimate` inicial en etapa `initial_classification`.

SI el clasificador refina el árbol de features y forma una hoja candidata, ENTONCES DEBE actualizar o crear un `diff_estimate` en etapa `feature_tree`.

SI las hojas de lectura o evidence packets revelan archivos, capas, validaciones, migraciones, tests, dependencias o contratos no previstos, ENTONCES el creador de prompt DEBE emitir un nuevo `diff_estimate` en etapa `post_reading` o `pre_prompt`.

SI existe más de un `diff_estimate` para la misma hoja, ENTONCES sólo el `diff_estimate` con `estimate_status: committed` DEBE usarse para la decisión final de admisibilidad.

SI GPT-5.5 produce una estimación, ENTONCES DEBE hacerlo mediante salida estructurada y NO DEBE devolver sólo una cifra única sin `low`, `likely`, `high`, `confidence`, `risk_factors` y `split_recommendation`.

SI el estimador no puede construir un rango creíble con la evidencia disponible, ENTONCES DEBE reportar `STOP-DIFF-ESTIMATE-UNCERTAIN`.

### 11.5 Componentes de trabajo

SI el trabajo incluye tests, docs, schema, migraciones, generated artifacts o refactor previo, ENTONCES esos componentes DEBEN estimarse por separado y NO DEBEN quedar escondidos dentro de una reserva implícita.

SI el cambio esperado cruza múltiples boundaries, auth/ownership, contratos compartidos o dependencias externas, ENTONCES el estimador DEBE marcar al menos un `risk_factor` explícito y reconsiderar la división.

SI el cambio esperado toca muchos archivos o áreas dispersas, ENTONCES el estimador DEBE registrar `expected_files_or_areas` y aumentar conservadoramente el `high`.

SI el cambio es feature + refactor, ENTONCES el estimador DEBE tratarlo como mayor riesgo de subestimación que un bug-fix localizado.

### 11.6 Resultado real

SI el implementador termina una hoja, ENTONCES DEBE producir `diff_result`.

SI existe `diff_result`, ENTONCES `actual_total_diff` DEBE calcularse con `git_numstat.added_lines + git_numstat.deleted_lines`.

SI falta `diff_result`, ENTONCES la hoja NO DEBE considerarse cerrada para fines de calibración.

SI falta actualización del ledger después de un `diff_result`, ENTONCES la hoja NO DEBE considerarse totalmente auditada.

### 11.7 Cálculo de error

SI existe `diff_estimate` committed y `diff_result`, ENTONCES AI Rules DEBE calcular:

```text
signed_error   = predicted_total_diff - actual_total_diff
absolute_error = abs(predicted_total_diff - actual_total_diff)
relative_error = absolute_error / max(actual_total_diff, 1)
within_range   = actual_total_diff >= uncertainty_range.low AND actual_total_diff <= uncertainty_range.high
```

SI `actual_total_diff > predicted_total_diff`, ENTONCES `underestimated = true`.

SI `actual_total_diff < predicted_total_diff`, ENTONCES `overestimated = true`.

### 11.8 Calibración histórica

AI Rules DEBE mantener un `diff_prediction_ledger` persistente o un mecanismo equivalente de historial local.

SI existe historial local del repo y también historial genérico, ENTONCES AI Rules DEBE priorizar el historial local.

SI hay menos de 10 ejemplos locales similares, ENTONCES AI Rules NO DEBE aplicar ajustes estadísticos fuertes y DEBE usar reglas conservadoras.

SI hay entre 10 y 29 ejemplos locales similares, ENTONCES AI Rules PUEDE aplicar ajustes numéricos suaves y acotados.

SI hay 30 o más ejemplos locales similares, ENTONCES AI Rules PUEDE usar `category_adjustment_factor` y revisar cobertura empírica de `within_range`.

SI el historial local muestra tendencia persistente a subestimar una categoría, ENTONCES AI Rules DEBE elevar conservadoramente `likely` y `high` para esa categoría.

SI el historial local muestra tendencia a sobreestimar, ENTONCES AI Rules PUEDE reducir `likely` gradualmente, pero NO DEBE reducir agresivamente `high` salvo evidencia abundante y estable.

SI AI Rules detecta el mismo patrón de error repetido en una categoría o harness, ENTONCES DEBE registrar `calibration_notes` y `future_adjustment_hint` en el ledger.

### 11.9 Ruta ligera para hojas triviales

SI una hoja es trivial, localizada, de bajo riesgo y bien entendida, ENTONCES AI Rules NO DEBE imponer burocracia desproporcionada.

Una hoja trivial PUEDE usar un `diff_estimate` compacto, pero DEBE incluir como mínimo:

- predicted added lines;
- predicted deleted lines;
- predicted total diff;
- low / likely / high;
- confidence;
- split recommendation;
- post-implementation `diff_result`.

---

## 12. Artefactos obligatorios

AI Rules usa artefactos para evitar que las decisiones vivan sólo en texto conversacional.

### 12.1 Artefactos base

| Artefacto | Obligatorio cuando | Propósito |
|---|---|---|
| `root_requirement_packet` | Siempre que una petición pase a feature tree | Compactar intención humana y requisitos. |
| `feature_node` | Siempre que se construya árbol de features | Dividir alcance en nodos trazables. |
| `diff_estimate` | Siempre que exista hoja candidata o ejecutable | Predecir tamaño de cambio y riesgo R2. |
| `reading_leaf` | Siempre que se requiera lectura | Contrato de evidencia atómico. |
| `evidence_packet` | Siempre que se ejecute una hoja de lectura | Reportar evidencia, claims y límites. |
| `prompt_final` | Siempre que se implemente | Dar instrucciones trazables al implementador. |
| `diff_result` | Siempre que se implemente | Medir diff real y verificación. |
| `diff_prediction_ledger_entry` | Siempre que exista `diff_result` | Registrar predicción vs resultado. |

### 12.2 Artefactos condicionales

| Artefacto | Obligatorio cuando | Propósito |
|---|---|---|
| `reuse_scan` | La hoja introduce o modifica comportamiento | Evitar duplicación de código existente. |
| `architecture_decision_packet` | La hoja requiere decisión arquitectónica | Evitar arquitectura improvisada. |
| `external_source_decision` | La hoja depende de comportamiento externo task-critical | Decidir docs/source/local/opensrc/bloqueo. |
| `read_leaf_oversize_report` | Una hoja de lectura excede presupuesto | Reportar sobredimensión. |
| `read_leaf_insufficient_report` | Una hoja no produce evidencia suficiente | Pedir nueva lectura o decisión. |

---

## 13. Ownership de artefactos

Cada artefacto DEBE tener productor claro. Si un artefacto no tiene productor claro, el flujo debe detenerse hasta asignar ownership o crear una instancia con el rol adecuado.

| Artefacto | Productor principal | Puede revisar/actualizar | Momento | Notas |
|---|---|---|---|---|
| `root_requirement_packet` | Clasificador de petición | Humano | Antes del árbol de features | El humano decide comportamiento esperado faltante. |
| `feature_node` | Clasificador de petición | Clasificador | Durante árbol de features | Debe trazarse al root packet. |
| `diff_estimate` inicial | Clasificador de petición | Clasificador | `initial_classification` | Es predicción temprana, no medición. |
| `diff_estimate` feature_tree | Clasificador de petición | Clasificador | Al formar hoja candidata | Refina tamaño por feature. |
| Solicitud de hojas arquitectónicas | Planificador de lectura | Humano si falta decisión | Antes de lectura | El planificador no produce evidencia; solicita lectura. |
| `reading_leaf` | Planificador de lectura | Planificador | Antes de lectura | Debe tener una pregunta primaria. |
| `evidence_packet` | Instancia lectora | Ninguna instancia debe reetiquetar claims sin nueva evidencia | Después de lectura | Incluye claims VERIFICADOS/INFERIDOS/DESCONOCIDOS. |
| Evidence packets arquitectónicos | Instancia lectora | Creador de prompt puede sintetizar, no reetiquetar | Después de hojas arquitectónicas | No son un formato distinto; son evidence packets normales con contenido arquitectónico. |
| `reuse_scan` | Instancia lectora | Creador de prompt puede sintetizar recomendación | Después de hoja anti-duplicación | Obligatorio si hay nuevo/modificado comportamiento. |
| `external_source_decision` | Creador de prompt o instancia sintetizadora autorizada | Humano para autorización opensrc | Después de evidence packets de dependencia | Puede bloquear antes de prompt final. |
| `architecture_decision_packet` | Creador de prompt o instancia sintetizadora autorizada | Humano si falta decisión | Después de evidence packets | Sintetiza movimientos permitidos/prohibidos. |
| `diff_estimate` post_reading | Creador de prompt | Creador de prompt | Después de evidence packets | Refleja scope descubierto. |
| `diff_estimate` pre_prompt committed | Creador de prompt | Sólo nueva instancia autorizada o humano | Antes del prompt final | Es el estimate que gobierna admisibilidad R2. |
| `prompt_final` | Creador de prompt | Humano puede aprobar/corregir | Antes de implementación | No puede contener instrucciones no trazables. |
| `diff_result` | Implementador | Auditor de cierre puede validar | Después de implementación | Basado en `git diff --numstat`. |
| `diff_prediction_ledger_entry` | Auditor de cierre o sistema de ledger | Humano/sistema de auditoría | Después de `diff_result` | Si no existe auditor separado, el implementador debe entregar una entrada candidata en su reporte final. |

### 13.1 Reglas de ownership

SI un artefacto obligatorio no tiene productor asignado, ENTONCES NO DEBE considerarse válido.

SI una instancia recibe un artefacto producido por otro rol, ENTONCES NO DEBE modificar su contenido normativo salvo que su rol lo autorice.

SI el creador de prompt sintetiza evidence packets, ENTONCES NO DEBE convertir claims INFERIDOS o DESCONOCIDOS en VERIFICADOS.

SI no existe auditor de cierre separado, ENTONCES el implementador DEBE producir `diff_result` y una entrada candidata de ledger, pero la hoja NO DEBE considerarse totalmente auditada hasta que el historial quede registrado.

---

## 14. Roles del framework

### 14.1 Humano

Propósito: definir objetivo, lógica de negocio, restricciones, prioridades y autorización de continuidad.

Reglas:

SI falta una decisión de producto o comportamiento esperado, ENTONCES ninguna instancia DEBE inventarla.

SI una instancia detecta que una decisión de producto faltante es necesaria para continuar, ENTONCES DEBE reportar `STOP-HUMAN-DECISION-REQUIRED`.

SI una decisión humana contradice evidencia verificada del repo, ENTONCES la instancia DEBE reportar la contradicción antes de implementar.

SI una instancia requiere autorización humana explícita, ENTONCES NO DEBE continuar hasta recibirla.

### 14.2 Clasificador de petición

Propósito: convertir una petición humana en un Paquete de Requisitos Raíz y luego en un árbol de features.

Responsabilidades:

- producir `root_requirement_packet`;
- producir `feature_node`;
- estimar diff inicial;
- dividir features hasta hojas candidatas;
- detectar requisitos faltantes;
- marcar hojas como `requires_split`, `executable_leaf` o `blocked`.

Reglas:

SI una petición humana va a convertirse en árbol de features, ENTONCES primero DEBE existir un Paquete de Requisitos Raíz.

SI el Paquete de Requisitos Raíz no define comportamiento esperado, ENTONCES el clasificador NO DEBE crear hojas ejecutables y DEBE reportar `STOP-EXPECTED-BEHAVIOR-MISSING`.

SI el Paquete de Requisitos Raíz no define criterios de aceptación, ENTONCES el clasificador NO DEBE crear hojas ejecutables y DEBE reportar `STOP-ACCEPTANCE-CRITERIA-MISSING`.

SI una petición tiene diff predicho seguro bajo R2, ENTONCES sólo PUEDE clasificarse como candidata a hoja ejecutable si existe Paquete de Requisitos Raíz, comportamiento esperado, criterios de aceptación, criterios de no regresión, trazabilidad y ausencia de desconocidos bloqueantes.

SI una petición tiene `uncertainty_range.high > 600`, ENTONCES DEBE dividirse antes de escribir código.

SI una hoja del árbol de features tiene `uncertainty_range.high > 600`, ENTONCES esa hoja NO ES ejecutable y DEBE dividirse, reducirse o bloquearse.

SI el clasificador no puede estimar diff con confianza razonable, ENTONCES DEBE marcar `DIFF_ESTIMATE_UNCERTAIN` o `STOP-DIFF-ESTIMATE-UNCERTAIN`.

SI una feature node no puede trazarse al Paquete de Requisitos Raíz, ENTONCES NO DEBE emitirse y DEBE reportar `STOP-FEATURE-NOT-TRACEABLE`.

SI una feature node convierte una inferencia en requisito, ENTONCES NO DEBE emitirse y DEBE reportar `STOP-REQUIREMENT-INVENTED`.

SI una feature node tiene desconocidos bloqueantes, ENTONCES NO PUEDE marcarse como hoja ejecutable.

### 14.3 Planificador de lectura

Propósito: tomar una hoja ejecutable o candidata a ejecutable y crear un árbol de lectura cuyas hojas sean contratos de evidencia concretos, pequeños y auditables.

Responsabilidades:

- producir `reading_leaf`;
- definir allowed/disallowed scope;
- solicitar hojas arquitectónicas cuando aplique;
- solicitar lectura anti-duplicación cuando aplique;
- solicitar lectura de dependencias externas cuando aplique;
- respetar presupuestos de contexto.

Reglas:

SI una hoja ejecutable va a ser implementada, ENTONCES una instancia planificadora de lectura DEBE crear un árbol de lectura para esa hoja.

SI una instancia planifica lectura, ENTONCES NO DEBE escribir código.

SI una instancia planifica lectura, ENTONCES NO DEBE construir el prompt final del implementador.

SI el planificador crea una hoja de lectura, ENTONCES DEBE tratarla como contrato de evidencia, NO como investigación abierta.

SI una hoja de lectura intenta responder más de una pregunta primaria independiente, ENTONCES el planificador DEBE dividirla.

SI una hoja de lectura introduce frases como “explora todo”, “lee todo lo relacionado” o “asume lo más probable”, ENTONCES NO DEBE emitirse.

SI el planificador no sabe qué leer, ENTONCES DEBE reportar `READ_PLAN_UNCERTAIN` y NO DEBE inventar archivos, símbolos, tests, comandos ni dependencias relevantes.

### 14.4 Instancia lectora

Propósito: ejecutar una hoja de lectura y producir evidencia trazable.

Responsabilidades:

- leer sólo dentro de allowed_scope;
- producir evidence packet;
- producir reuse scan cuando la hoja anti-duplicación lo exige;
- producir evidencia arquitectónica cuando la hoja lo exige;
- reportar contradicciones, insuficiencias y desconocidos.

Reglas:

SI una instancia lectora recibe una hoja de lectura, ENTONCES DEBE leer sólo dentro del `allowed_scope` de esa hoja.

SI una instancia lectora recibe una hoja de lectura, ENTONCES NO DEBE escribir código.

SI una instancia lectora necesita salir de `allowed_scope`, ENTONCES DEBE detenerse y reportar `STOP-SCOPE-ESCALATION-REQUIRED`.

SI falta evidencia, ENTONCES la instancia lectora NO DEBE adivinar.

SI una hoja de lectura fue ejecutada, ENTONCES la instancia lectora DEBE producir un evidence packet.

SI un claim es VERIFICADO, ENTONCES DEBE indicar soporte directo.

SI un claim es INFERIDO, ENTONCES DEBE incluir `reasoning_note`.

SI un claim es DESCONOCIDO, ENTONCES DEBE explicar qué faltó para verificarlo.

### 14.5 Creador de prompt

Propósito: construir el prompt final del implementador usando la hoja ejecutable original, árbol de lectura, evidence packets, decisiones humanas explícitas, artefactos derivados y guía de prompting del modelo usado.

Responsabilidades:

- leer la guía de prompting del modelo implementador;
- sintetizar evidence packets sin reetiquetar claims;
- crear architecture decision packet cuando aplique;
- crear external source decision cuando aplique;
- emitir diff_estimate `pre_prompt` committed;
- construir prompt final trazable.

Reglas:

SI el modelo implementador es GPT-5.5, ENTONCES la instancia creadora de prompt DEBE leer la guía de prompting de GPT-5.5 antes de construir el prompt final.

SI una instrucción no puede trazarse a evidencia VERIFICADA o decisión humana explícita, ENTONCES DEBE excluirse.

SI falta un evidence packet requerido, ENTONCES el creador de prompt NO DEBE construir el prompt final salvo autorización humana explícita.

SI falta `diff_estimate` `pre_prompt` committed, ENTONCES el creador de prompt NO DEBE enviar la hoja al implementador.

### 14.6 Implementador

Propósito: modificar código dentro del alcance autorizado, ejecutar verificaciones y reportar diff real.

Responsabilidades:

- operar desde prompt final aprobado;
- modificar sólo alcance permitido;
- detenerse si encuentra contradicciones;
- ejecutar verificaciones;
- medir `git diff --numstat`;
- producir `diff_result`;
- entregar reporte final.

Reglas:

SI una instancia implementa código, ENTONCES DEBE operar desde el prompt final aprobado para esa hoja ejecutable.

SI el implementador detecta que necesita cambiar el alcance, ENTONCES DEBE detenerse y reportar `IMPLEMENTATION_SCOPE_EXCEEDED`.

SI el implementador descubre complejidad material no modelada antes de expandir el diff, ENTONCES DEBE reportar `STOP-DIFF-UNDERPREDICTION-RISK` o `STOP-DIFF-ESTIMATE-UNCERTAIN`.

SI el diff real supera 600 líneas, ENTONCES DEBE reportar `R2_EXCEEDED` y NO DEBE iniciar otra task.

SI el implementador usa un claim DESCONOCIDO como instrucción, ENTONCES viola AI Rules.

SI el implementador necesita una decisión no incluida en el prompt final, ENTONCES DEBE detenerse y pedir decisión humana explícita.

### 14.7 Auditor de cierre o sistema de ledger

Propósito: validar resultado, registrar predicción vs resultado y alimentar calibración futura.

Este rol PUEDE ser una instancia separada, un sistema de reporte final o una etapa controlada del harness. Si no existe como rol separado, el implementador DEBE entregar suficiente información para que el historial pueda actualizarse.

Responsabilidades:

- validar `diff_result`;
- crear o registrar `diff_prediction_ledger_entry`;
- calcular errores;
- registrar patrones de subestimación/sobreestimación;
- marcar hojas no totalmente auditadas si falta ledger.

Reglas:

SI existe `diff_result`, ENTONCES el auditor o sistema de ledger DEBE registrar `diff_prediction_ledger_entry`.

SI falta actualización del ledger, ENTONCES DEBE reportarse `STOP-DIFF-LEDGER-UPDATE-MISSING` o marcarse la hoja como no totalmente auditada.

---

## 15. Reglas normativas R0–R10

### R0 — Principio central

SI una regla aplica a una acción, ENTONCES la instancia DEBE obedecerla antes de continuar.

SI una instancia no puede cumplir una regla aplicable, ENTONCES DEBE detenerse y reportar la etiqueta de detención correspondiente.

SI una instancia produce un artefacto para otra instancia, ENTONCES ese artefacto DEBE ser auditable, trazable y limitado por rol.

SI una regla exige evidencia, ENTONCES la instancia NO DEBE sustituir evidencia por intuición.

### R1 — Límite de longitud de archivos

SI un archivo es código fuente ejecutable del producto, ENTONCES su longitud máxima permitida es 500 líneas.

SI un archivo productivo ya supera 500 líneas antes de la task, ENTONCES la task NO DEBE aumentarlo salvo autorización humana explícita.

SI una task crea un archivo productivo nuevo, ENTONCES ese archivo NO DEBE superar 500 líneas.

SI un archivo es generado automáticamente, ENTONCES R1 NO aplica, pero el archivo DEBE identificarse como generado.

### R2 — Límite de cambio y diff calibration

SI una petición o task tiene `uncertainty_range.high <= 600`, ENTONCES PUEDE ser candidata a hoja ejecutable si cumple los demás criterios de ejecutabilidad.

SI una petición o task tiene `uncertainty_range.high > 600`, ENTONCES DEBE dividirse, reducirse o bloquearse antes de escribir código.

SI `uncertainty_range.likely <= 600` pero `uncertainty_range.high > 600`, ENTONCES la hoja DEBE tratarse como riesgo de subestimación y DEBE preferirse división o re-scoping conservador.

SI una task se divide, ENTONCES R2 aplica recursivamente a cada task hija.

SI una task ejecutada supera 600 líneas de diff real, ENTONCES el agente DEBE reportar `R2_EXCEEDED` y NO DEBE iniciar otra task.

SI una task excede R2 por archivos generados automáticamente, ENTONCES el reporte DEBE separar diff humano de diff generado cuando sea atribuible, pero R2 DEBE evaluarse contra el diff total canónico.

SI una hoja ejecutable no tiene `diff_estimate` `pre_prompt` committed, ENTONCES NO DEBE ser enviada al implementador.

SI falta `diff_result`, ENTONCES la hoja NO DEBE considerarse cerrada para calibración.

### R3 — Planificación de lectura

SI una hoja ejecutable del árbol de features va a ser implementada, ENTONCES primero DEBE crearse un árbol de lectura para esa hoja.

SI una instancia crea el árbol de lectura, ENTONCES NO DEBE escribir código.

SI una instancia crea el árbol de lectura, ENTONCES NO DEBE construir el prompt final.

SI una instancia crea el árbol de lectura, ENTONCES DEBE diseñar hojas de lectura como contratos de evidencia.

SI una hoja de lectura no tiene una sola pregunta primaria, ENTONCES NO DEBE emitirse.

SI una hoja de lectura no tiene `decision_link`, ENTONCES NO DEBE emitirse.

SI una hoja de lectura no tiene `allowed_scope`, ENTONCES NO DEBE emitirse.

SI una hoja de lectura no tiene `disallowed_scope`, ENTONCES NO DEBE emitirse.

SI una hoja de lectura no tiene `success_condition`, ENTONCES NO DEBE emitirse.

SI una hoja de lectura no tiene `stop_conditions`, ENTONCES NO DEBE emitirse.

SI una hoja de lectura no tiene `missing_evidence_policy`, ENTONCES NO DEBE emitirse.

SI una hoja de lectura no tiene `contradiction_policy`, ENTONCES NO DEBE emitirse.

SI una hoja de lectura no tiene `claim_label_policy`, ENTONCES NO DEBE emitirse.

SI una hoja de lectura no tiene `citation_format`, ENTONCES NO DEBE emitirse.

SI el planificador crea una hoja de lectura, ENTONCES DEBE estimar su tamaño antes de emitirla.

SI el planificador sólo tiene estimación proxy, ENTONCES NO DEBE emitir la hoja si la estimación conservadora supera 8% de W.

SI el planificador tiene conteo exacto del modelo objetivo, ENTONCES NO DEBE emitir la hoja si su tamaño total estimado supera 10% de W.

SI cualquier escenario razonable de la hoja puede superar 15% de W, ENTONCES la hoja NO DEBE emitirse.

SI una hoja de lectura fue ejecutada por una instancia lectora, ENTONCES DEBE producir un evidence packet.

### R4 — Evidencia y claims

R4 aplica a claims sobre:

- codebase;
- arquitectura;
- dependencias;
- runtime;
- contratos;
- documentación;
- comportamiento externo;
- APIs;
- generated code;
- resultados de comandos;
- decisiones humanas.

SI una instancia afirma algo sobre cualquiera de esos elementos, ENTONCES DEBE clasificarlo como VERIFICADO, INFERIDO o DESCONOCIDO.

SI un claim está DESCONOCIDO, ENTONCES NO PUEDE entrar al prompt del implementador como instrucción.

SI una instrucción entra al prompt del implementador, ENTONCES DEBE venir de una decisión humana explícita o de un claim VERIFICADO.

SI una síntesis combina varias hojas de lectura, ENTONCES NO PUEDE convertir INFERIDO o DESCONOCIDO en VERIFICADO.

SI un evidence packet contiene un claim VERIFICADO, ENTONCES DEBE incluir fuente suficiente para auditarlo.

SI un evidence packet contiene un claim INFERIDO, ENTONCES DEBE explicar qué evidencia lo sugiere y qué falta para verificarlo.

SI un evidence packet contiene un claim DESCONOCIDO, ENTONCES DEBE explicar por qué no fue probado y qué fuente podría resolverlo.

SI un claim VERIFICADO no contiene soporte directo, ENTONCES el evidence packet es inválido.

SI un claim INFERIDO no contiene `reasoning_note`, ENTONCES el evidence packet es inválido.

SI un evidence packet mezcla inferencia con verificación, ENTONCES DEBE reportarse `STOP-EPISTEMIC-MIX`.

### R5 — Prompt final del implementador

SI una hoja ejecutable va a pasar al implementador, ENTONCES una instancia creadora de prompt DEBE construir el prompt final usando todos los evidence packets requeridos, aunque exista un solo evidence packet.

SI todos los evidence packets requeridos están disponibles, ENTONCES el creador de prompt PUEDE construir el prompt final.

SI falta un evidence packet requerido, ENTONCES el creador de prompt NO DEBE construir el prompt final salvo autorización humana explícita.

SI una hoja ejecutable introduce o modifica comportamiento y falta reuse scan, ENTONCES el creador de prompt NO DEBE construir el prompt final y DEBE reportar `STOP-REUSE-SCAN-MISSING`.

SI una hoja ejecutable requiere decisión arquitectónica y falta architecture decision packet, ENTONCES el creador de prompt NO DEBE construir el prompt final y DEBE reportar `STOP-ARCHITECTURE-DECISION-MISSING`.

SI falta `diff_estimate` `pre_prompt` committed, ENTONCES el creador de prompt NO DEBE construir el prompt final y DEBE reportar `STOP-DIFF-ESTIMATE-MISSING`.

SI el prompt final se entrega al implementador, ENTONCES DEBE incluir objetivo, alcance permitido, archivos confirmados, cambios permitidos, cambios prohibidos, evidencia usada, comandos de verificación, riesgos, desconocidos, criterios de detención y reporte final requerido.

SI una instrucción entra al prompt final, ENTONCES DEBE venir de una decisión humana explícita o de un claim VERIFICADO.

SI una instrucción no puede trazarse, ENTONCES NO DEBE entrar al prompt final.

SI el prompt final se crea para GPT-5.5, ENTONCES DEBE estar escrito siguiendo la guía de prompting de GPT-5.5.

SI el prompt final se crea desde evidence packets, ENTONCES cada instrucción crítica DEBE enlazarse a `packet_id`, `claim_id`, `decision_id` o decisión humana explícita.

SI el prompt final incluye inferencias, ENTONCES DEBE separarlas de instrucciones obligatorias.

SI el prompt final incluye desconocidos, ENTONCES DEBE convertirlos en riesgos, preguntas abiertas o criterios de detención.

SI el prompt final autoriza crear una abstracción nueva, ENTONCES DEBE explicar por qué no es duplicación, god object, manager genérico ni utils prematuro.

### R6 — Dependencias externas, documentación oficial y fuente externa

SI una task depende del comportamiento de un paquete externo, framework, SDK o runtime, ENTONCES el agente DEBE determinar si ese comportamiento es task-critical.

SI una dependencia no es task-critical para la hoja ejecutable, ENTONCES el agente NO DEBE leerla ni obtenerla como fuente externa.

SI una task depende de comportamiento task-critical de una dependencia externa, ENTONCES el agente DEBE buscar primero si la versión, tipos, generated code, wrapper o fuente relevante ya existe en el repo.

SI el código fuente relevante ya existe en el repo, ENTONCES el agente DEBE leerlo antes de planificar o implementar.

SI los tipos, generated code o wrappers locales resuelven el comportamiento task-critical, ENTONCES el agente DEBE usarlos como evidencia local antes de recurrir a fuente externa.

SI documentación oficial es suficiente para resolver la semántica task-critical, ENTONCES el agente PUEDE usar documentación oficial como evidencia, citando la fuente revisada.

SI la versión de la dependencia task-critical no está verificada, ENTONCES el agente DEBE reportar `STOP-EXTERNAL-SOURCE-VERSION-UNKNOWN`.

SI documentación oficial, tipos/generated locales y source presente en repo no bastan para resolver comportamiento task-critical, ENTONCES el agente DEBE reportar `STOP-EXTERNAL-SOURCE-REQUIRED`.

SI el código fuente relevante no existe en el repo, ENTONCES el agente NO DEBE obtenerlo con opensrc sin autorización humana explícita.

SI se requiere fuente externa y no existe autorización humana explícita, ENTONCES el agente DEBE reportar `STOP-EXTERNAL-SOURCE-AUTH-REQUIRED`.

SI el humano autoriza obtener fuente externa, ENTONCES el agente DEBE usar `https://github.com/vercel-labs/opensrc`.

SI una dependencia fue obtenida con opensrc, ENTONCES el agente DEBE documentar paquete, versión o commit, fuente incorporada, archivos leídos y decisión que justificó la descarga.

SI una dependencia no es relevante para la task, ENTONCES el agente NO DEBE obtenerla y DEBE reportar `STOP-EXTERNAL-SOURCE-NOT-RELEVANT` si otro artefacto la exige sin justificación.

### R7 — Harness por rol

SI una instancia sólo clasifica, planifica, lee, sintetiza o crea prompts, ENTONCES NO DEBE usar un harness con permisos de edición de código salvo autorización humana explícita.

SI una instancia sólo clasifica, planifica, lee, sintetiza o crea prompts, ENTONCES DEBE usar el harness mínimo suficiente para leer, citar y producir evidencia.

SI una instancia implementa código, ENTONCES PUEDE usar Codex u otro harness con permisos de edición.

SI una instancia usa un harness con permisos superiores a su rol, ENTONCES DEBE detenerse y reportar `HARNESS_SCOPE_EXCEEDED`.

SI el harness disponible no permite cumplir el rol asignado, ENTONCES la instancia DEBE reportar `HARNESS_INSUFFICIENT` y detenerse.

SI el planificador o lector usa Pi Agent como harness, ENTONCES DEBE operar en modo read-only con allowlist mínima.

SI W efectiva de Pi Agent no está medida, ENTONCES el planificador DEBE operar en modo conservador y usar sólo el umbral previo de emisión por proxy de 8% de W.

### R8 — Capa de decisiones arquitectónicas

SI una hoja ejecutable introduce, modifica o desplaza comportamiento, ENTONCES el planificador de lectura DEBE determinar si existen decisiones arquitectónicas necesarias antes de emitir el árbol de lectura.

SI una hoja ejecutable toca boundaries entre UI, estado, repositorio, base de datos, backend, sync, auth, ownership, validación, side effects o dependencias externas, ENTONCES el planificador DEBE crear lectura arquitectónica suficiente para identificar el boundary afectado.

SI una decisión arquitectónica requerida no puede verificarse mediante código, evidence packet o decisión humana explícita, ENTONCES la instancia DEBE reportar `STOP-ARCHITECTURE-DECISION-MISSING`.

SI una recomendación arquitectónica es sólo INFERIDA, ENTONCES NO DEBE convertirse en instrucción obligatoria para el implementador.

SI la arquitectura actual del repo contradice una recomendación genérica, ENTONCES debe priorizarse la arquitectura verificada del repo salvo decisión humana explícita.

SI implementar requiere introducir microservicios, Clean Architecture completa, DDD completo, Hexagonal estricta, DI containers o repository pattern no existente, ENTONCES la instancia DEBE detenerse salvo evidencia fuerte o decisión humana explícita.

### R9 — Reuso, anti-duplicación y anti-god-object

SI una hoja ejecutable introduce o modifica comportamiento, ENTONCES el árbol de lectura DEBE incluir al menos una hoja de lectura anti-duplicación.

SI una hoja ejecutable introduce o modifica comportamiento y no existe reuse scan, ENTONCES el creador de prompt NO DEBE construir el prompt final y DEBE reportar `STOP-REUSE-SCAN-MISSING`.

SI el reuse scan encuentra una implementación existente equivalente, ENTONCES el prompt final DEBE ordenar reutilizarla o detenerse si reutilizarla excede el alcance aprobado.

SI el reuse scan encuentra una implementación parcialmente equivalente, ENTONCES el prompt final DEBE ordenar extenderla o justificar explícitamente por qué no se reutiliza.

SI el reuse scan no encuentra candidatos dentro del allowed scope, ENTONCES el evidence packet DEBE registrar símbolos, rutas y búsquedas revisadas.

SI el implementador descubre una implementación existente no considerada por el prompt final, ENTONCES DEBE detenerse y reportar `STOP-DUPLICATION-RISK`.

SI una nueva función, helper, mapper, DAO, service, repository, widget, provider o validator duplica comportamiento existente verificado, ENTONCES NO DEBE implementarse como solución paralela.

SI una solución concentra validación, auth, ownership, reglas de negocio, persistencia y side effects en una sola función u objeto, ENTONCES la instancia DEBE reportar `STOP-GOD-OBJECT-PATH`.

SI una mutation, handler, service, manager o helper acumula responsabilidades de varios casos de uso independientes, ENTONCES NO DEBE ampliarse sin decisión humana explícita o nueva división de hoja ejecutable.

SI una propuesta crea un manager, service, utils, helpers, common o shared genérico sin ownership claro y sin reutilización verificada, ENTONCES la instancia DEBE reportar `STOP-PREMATURE-ABSTRACTION`.

SI una abstracción nueva sólo tiene un consumidor verificado, ENTONCES NO DEBE crearse como utilidad genérica salvo que una decisión humana explícita lo autorice.

SI dividir una responsabilidad reduce riesgo pero excede diff seguro bajo R2, ENTONCES la instancia DEBE dividir la hoja ejecutable antes de implementar.

### R10 — Reglas específicas para Convex

SI el backend stack es Convex, ENTONCES la arquitectura backend por defecto DEBE tratarse como monolito modular serverless organizado por feature o bounded context.

SI una función Convex es pública, ENTONCES DEBE tratarse como boundary público de aplicación.

SI una función pública Convex recibe input externo, ENTONCES DEBE validar argumentos en el boundary.

SI una función pública Convex toca datos de usuario, ENTONCES DEBE autenticar al caller y verificar ownership o autorización antes de leer o mutar datos.

SI el path de auth, ownership o autorización no está verificado para una función pública que toca datos de usuario, ENTONCES la instancia DEBE reportar `STOP-SECURITY-BOUNDARY-UNVERIFIED`.

SI una función Convex sólo debe ser llamada por backend, ENTONCES DEBE implementarse como función interna.

SI una operación Convex escribe estado y requiere side effects externos, ENTONCES la mutation DEBE registrar intención y programar una internal action o flujo interno equivalente.

SI una query o mutation puede resolver la tarea, ENTONCES NO DEBE introducirse una action.

SI el repo Convex actual usa `ctx.db` directo con helpers locales, ENTONCES NO DEBE introducirse repository pattern sobre `ctx.db` sin precedente verificado, necesidad trazable o decisión humana explícita.

SI un handler Convex contiene lógica de negocio reutilizable, branching complejo o invariantes, ENTONCES DEBE delegar esa lógica a helper/use-case ligero dentro del módulo dueño.

SI el cambio toca `convex/_generated` u otro código generado, ENTONCES el implementador NO DEBE editarlo manualmente como superficie primaria de implementación.

SI el cambio requiere regenerar código generado, ENTONCES la regeneración DEBE estar explícitamente autorizada por el prompt final.

SI una task cambia schema de Convex, contrato HTTP/OpenAPI, generated API/types o integración externa, ENTONCES DEBE producir evidencia de contrato y migración/regeneración antes de implementar.

SI el contrato de datos afectado por Convex no está verificado, ENTONCES la instancia DEBE reportar `STOP-DATA-CONTRACT-UNVERIFIED`.

---

## 16. Capa de decisiones arquitectónicas

La capa de decisiones arquitectónicas existe para impedir que el implementador tome decisiones estructurales improvisadas.

No toda hoja ejecutable necesita un `architecture_decision_packet` completo. Sí se necesita cuando la hoja:

- introduce o modifica comportamiento relevante;
- cruza boundaries;
- toca auth, ownership o validación;
- toca persistencia, schema, generated code o contratos;
- requiere decidir entre reutilizar, extender o crear nuevo módulo;
- podría crear god object, manager genérico o utils prematuro;
- depende de un paquete, framework, SDK o runtime externo;
- requiere elegir patrón arquitectónico.

### 16.1 Preguntas arquitectónicas típicas

Una hoja de lectura arquitectónica PUEDE preguntar:

- ¿Cuál es el boundary dueño de este comportamiento?
- ¿Existe helper/use-case equivalente?
- ¿Existe patrón local para auth/ownership?
- ¿El repo usa `ctx.db` directo o repository pattern?
- ¿El cambio toca schema o generated code?
- ¿Dónde vive actualmente la validación?
- ¿Qué función pública expone este comportamiento?
- ¿Este side effect debe ser action, internal action o mutation programada?
- ¿El cambio crea una abstracción genérica innecesaria?

### 16.2 Salida esperada

La evidencia arquitectónica se produce primero como evidence packet normal. Luego el creador de prompt o una instancia sintetizadora autorizada PUEDE construir `architecture_decision_packet`.

SI el architecture decision packet contiene movimientos permitidos, ENTONCES cada movimiento DEBE trazarse a evidence packet, claim o decisión humana.

SI el architecture decision packet contiene movimientos prohibidos, ENTONCES DEBE incluir razón y etiqueta de detención aplicable.

---

## 17. Lectura anti-duplicación y reuse scan

AI Rules exige lectura anti-duplicación para evitar que la IA cree soluciones paralelas.

### 17.1 Cuándo se requiere

SI una hoja ejecutable introduce o modifica comportamiento, ENTONCES requiere reuse scan.

Esto incluye:

- nueva función;
- nuevo helper;
- nuevo service;
- nuevo validator;
- nueva mutation/query/action;
- nuevo mapper;
- nuevo flujo de auth/ownership;
- nueva integración;
- nuevo contrato de datos.

### 17.2 Qué busca

El reuse scan busca:

- implementación equivalente;
- implementación parcialmente equivalente;
- helpers reutilizables;
- validators existentes;
- tests existentes;
- patrones locales;
- funciones generadas o API refs relevantes;
- duplicación peligrosa;
- duplicación aceptable por bounded context distinto.

### 17.3 Resultados posibles

Un reuse scan puede recomendar:

- `reuse_existing`: reutilizar tal cual;
- `extend_existing`: extender candidato existente;
- `create_new`: crear algo nuevo porque no hay candidato dentro del scope;
- `blocked`: no continuar por riesgo de duplicación o scope insuficiente.

### 17.4 Reglas de uso

SI se recomienda `create_new`, ENTONCES el reuse scan DEBE demostrar qué se buscó y dónde.

SI se recomienda no reutilizar un candidato parcial, ENTONCES DEBE explicar riesgo de reutilizarlo y riesgo de no reutilizarlo.

SI el implementador descubre candidato existente no revisado, ENTONCES DEBE detenerse y reportar `STOP-DUPLICATION-RISK`.

---

## 18. Anti-god-object, anti-manager y anti-utils prematuro

AI Rules no sólo limita líneas; también limita concentración de responsabilidades.

### 18.1 God object path

Una solución entra en god object path cuando concentra responsabilidades independientes como:

- parsing/validación;
- auth;
- ownership;
- reglas de negocio;
- persistencia;
- side effects externos;
- mapping/DTO;
- sync o scheduling;
- logging/observabilidad;
- manejo de errores de múltiples capas.

SI una función u objeto mezcla demasiadas responsabilidades, ENTONCES la respuesta correcta no es necesariamente extraer un mega-service. La respuesta puede ser:

- dividir la hoja ejecutable;
- delegar a helpers específicos;
- mantener boundary thin;
- pedir decisión humana;
- detenerse por `STOP-GOD-OBJECT-PATH`.

### 18.2 Manager genérico

Un manager genérico es sospechoso cuando:

- no tiene bounded context claro;
- orquesta operaciones no relacionadas;
- crece como punto central de conveniencia;
- contiene reglas de varias features;
- se crea para evitar entender módulos existentes.

SI una solución propone un manager genérico sin evidencia, ENTONCES DEBE reportar `STOP-PREMATURE-ABSTRACTION`.

### 18.3 Utils prematuro

Un utils prematuro es sospechoso cuando:

- sólo tiene un consumidor;
- tiene nombre amplio (`utils`, `helpers`, `common`, `shared`);
- no tiene ownership;
- mezcla dominios;
- abstrae similitud superficial sin invariant compartido.

SI dos fragmentos se parecen pero pertenecen a contextos con significado, ownership o cadence distinta, ENTONCES la duplicación PUEDE ser aceptable y NO DEBE extraerse automáticamente.

---

## 19. Reglas específicas para Convex

AI Rules trata Convex como un backend serverless opinado, no como backend tradicional genérico.

### 19.1 Arquitectura por defecto

Para Convex, el default es:

- monolito modular serverless;
- organización por feature o bounded context;
- public functions como boundaries;
- validators en boundaries públicos;
- auth/ownership temprano;
- helpers/use-case ligeros cuando hay lógica reutilizable;
- funciones puras sólo cuando hay reglas de negocio reales;
- internal functions para flujos backend no públicos;
- actions para I/O externo, no para lo que query/mutation puede resolver;
- no repository pattern sobre `ctx.db` por defecto.

### 19.2 Qué NO es default

En Convex, AI Rules NO DEBE introducir por defecto:

- microservicios;
- Clean Architecture completa;
- Hexagonal estricta;
- DDD completo;
- DI containers;
- repository wrappers sobre `ctx.db`;
- managers genéricos;
- actions públicas para workflows de escritura si mutation + internal action es suficiente.

Estas opciones sólo PUEDE introducirse con:

- patrón verificado en el repo;
- necesidad trazable;
- evidence packet suficiente;
- decisión humana explícita.

### 19.3 Queries, mutations y actions

SI el cambio es lectura reactiva o consulta de datos, ENTONCES debe modelarse como query salvo patrón verificado contrario.

SI el cambio es escritura transaccional, ENTONCES debe modelarse como mutation salvo patrón verificado contrario.

SI el cambio requiere I/O externo, ENTONCES PUEDE requerir action o internal action.

SI la action sería invocada directamente por cliente para workflow de escritura, ENTONCES debe tratarse como riesgo arquitectónico y verificarse contra patrón local.

### 19.4 Generated code

SI se toca `convex/_generated` u otro generated code, ENTONCES el implementador NO DEBE editarlo manualmente como implementación primaria.

SI el cambio requiere actualizar generated code, ENTONCES el prompt final DEBE autorizar regeneración y el diff_result DEBE separar diff generado cuando sea posible.

---

## 20. Dependencias externas, documentación oficial y opensrc

AI Rules distingue cuatro niveles de evidencia para dependencias externas:

1. código y wrappers locales del repo;
2. tipos/generated code locales;
3. documentación oficial;
4. fuente externa obtenida con autorización humana.

### 20.1 Matriz de decisión

| Situación | Acción |
|---|---|
| La dependencia no afecta la hoja | NO DEBE leerse ni obtenerse. |
| La semántica se resuelve con tipos/generated locales | DEBE usarse evidencia local. |
| La semántica se resuelve con docs oficiales | PUEDE usarse docs oficiales citadas. |
| La fuente relevante ya existe en repo | DEBE leerse antes de planificar o implementar. |
| La fuente no existe y es task-critical | DEBE detenerse por autorización si se requiere opensrc. |
| La versión es desconocida y afecta comportamiento | DEBE reportarse `STOP-EXTERNAL-SOURCE-VERSION-UNKNOWN`. |

### 20.2 opensrc

SI se requiere fuente externa y no hay autorización humana explícita, ENTONCES NO DEBE usarse opensrc.

SI el humano autoriza fuente externa, ENTONCES DEBE usarse:

```text
https://github.com/vercel-labs/opensrc
```

SI se usa opensrc, ENTONCES el artefacto debe registrar:

- paquete;
- versión o commit;
- fuente;
- archivos incorporados o leídos;
- razón task-critical;
- relación con la hoja ejecutable.

---

## 21. Prompt final para GPT-5.5/Codex

El prompt final es el contrato de implementación.

Debe ser:

- outcome-first;
- breve pero suficiente;
- trazable;
- limitado por alcance;
- compatible con GPT-5.5/Codex;
- basado en evidencia VERIFICADA y decisiones humanas explícitas.

### 21.1 Debe incluir

Cada prompt final DEBE incluir:

- objetivo;
- hoja ejecutable original;
- diff_estimate `pre_prompt` committed;
- árbol de lectura usado;
- evidence packets usados;
- decisiones humanas usadas;
- architecture decision packet, si aplica;
- reuse scan, si aplica;
- external source decision, si aplica;
- alcance permitido;
- archivos confirmados;
- cambios permitidos;
- cambios prohibidos;
- evidencia usada;
- instrucciones de implementación;
- comandos de verificación;
- riesgos;
- desconocidos;
- inferencias no obligatorias;
- criterios de detención;
- reporte final requerido, incluyendo `diff_result`.

### 21.2 No debe incluir

El prompt final NO DEBE incluir:

- instrucciones no trazables;
- claims DESCONOCIDOS como órdenes;
- inferencias como hechos;
- arquitectura genérica no verificada;
- permisos implícitos para ampliar scope;
- permiso para editar generated code manualmente;
- permiso para usar opensrc sin autorización humana explícita;
- permiso para superar R2;
- instrucciones para crear managers/utils genéricos sin evidencia.

### 21.3 Codex y planeación

SI se usa Codex para implementar, ENTONCES la estimación y la implementación DEBEN estar separadas en pasos distintos.

SI se requiere plan, ENTONCES debe existir como artefacto o modo autorizado, no como improvisación conversacional dentro del mismo rollout.

---

## 22. Catálogo de etiquetas de detención

| Etiqueta | Significado | Quién la reporta | Cuándo se usa |
|---|---|---|---|
| `ROLE_BLOCKED_BY_SCOPE` | La siguiente acción requerida no pertenece al rol asignado. | Cualquier instancia | Cuando continuar rompería separación de roles. |
| `STOP-ROOT-REQUIREMENT-MISSING` | No existe Paquete de Requisitos Raíz antes de crear árbol de features. | Clasificador | Antes de feature tree. |
| `STOP-EXPECTED-BEHAVIOR-MISSING` | Falta comportamiento esperado. | Clasificador | Root packet incompleto. |
| `STOP-ACCEPTANCE-CRITERIA-MISSING` | Faltan criterios de aceptación. | Clasificador | Root packet incompleto. |
| `STOP-FEATURE-NOT-TRACEABLE` | Feature node no se traza al root packet. | Clasificador | División inválida. |
| `STOP-REQUIREMENT-INVENTED` | Una instancia convirtió inferencia en requisito. | Clasificador / creador de prompt | Requisito no respaldado. |
| `HARNESS_SCOPE_EXCEEDED` | Harness tiene permisos superiores al rol. | Cualquier instancia | Permisos excesivos. |
| `HARNESS_INSUFFICIENT` | Harness no permite cumplir el rol. | Cualquier instancia | Herramientas insuficientes. |
| `DIFF_ESTIMATE_UNCERTAIN` | No se puede estimar diff con confianza razonable. | Clasificador | Estimación temprana incierta. |
| `STOP-DIFF-ESTIMATE-MISSING` | Falta estimación requerida. | Clasificador / creador de prompt | No existe `diff_estimate` obligatorio. |
| `STOP-DIFF-ESTIMATE-UNCERTAIN` | Incertidumbre demasiado alta para rango creíble. | Creador de prompt / implementador | No se puede comprometer hoja segura. |
| `STOP-DIFF-HIGH-RANGE-EXCEEDS-R2` | El rango conservador supera R2. | Creador de prompt | `uncertainty_range.high > 600`. |
| `STOP-DIFF-CALIBRATION-MISSING` | No se aplicó política de calibración requerida. | Auditor / sistema | Falta revisión de historial cuando aplica. |
| `STOP-DIFF-RESULT-MISSING` | Falta medición posterior. | Implementador / auditor | No existe `diff_result`. |
| `STOP-DIFF-LEDGER-UPDATE-MISSING` | Resultado no fue registrado para calibración. | Auditor / sistema | Falta ledger entry. |
| `STOP-DIFF-UNDERPREDICTION-RISK` | La realidad del repo invalida la predicción committed. | Implementador / creador de prompt | Scope oculto o riesgo material. |
| `READ_PLAN_UNCERTAIN` | El planificador no puede determinar qué leer. | Planificador | Lectura no segura. |
| `TASK_NOT_EXECUTABLE` | La task no puede ejecutarse bajo reglas actuales. | Lector / creador de prompt | Evidencia bloqueante. |
| `R2_EXCEEDED` | El diff real supera 600 líneas. | Implementador | Después de medir diff. |
| `IMPLEMENTATION_SCOPE_EXCEEDED` | Implementador necesita cambiar alcance. | Implementador | Scope insuficiente. |
| `UNVERIFIED_CLAIM` | Conclusión o instrucción sin evidencia. | Cualquier instancia | Claim no soportado. |
| `CONTEXT_LIMIT_EXCEEDED` | Hoja supera límite duro definido por AI Rules. | Planificador / lector | Presupuesto de contexto excedido. |
| `STOP-CONTEXT-WINDOW-EXCEEDED` | Modelo, harness o sistema reportó desborde real de ventana. | Lector / harness | Desborde real. |
| `STOP-W-BASE` | W no está medida y se usa base conservadora. | Planificador | W desconocida. |
| `STOP-BUDGET-NOT-COMPUTED` | No se calcularon presupuestos de hoja. | Planificador | Hoja inválida. |
| `STOP-UNVERIFIED-SOURCE` | Hoja nombra fuente no confirmada. | Planificador / lector | Fuente no descubierta. |
| `STOP-SOURCE-NOT-FOUND` | Selector no existe en inventario real. | Lector | Fuente no encontrada. |
| `STOP-EXACT-COUNT-SKIPPED` | Había conteo exacto posible y no se hizo. | Planificador / lector | Conteo omitido. |
| `STOP-PROXY-GATE-EXCEEDED` | Estimación proxy supera 8% de W. | Planificador | Hoja sobredimensionada. |
| `STOP-EXACT-TARGET-EXCEEDED` | Conteo exacto supera 10% de W. | Planificador / lector | Hoja excedida. |
| `STOP-HARD-CAP-RISK` | Escenario razonable supera 15% de W. | Planificador | Riesgo duro. |
| `STOP-LARGE-FILE` | Archivo/documento demasiado grande como unidad. | Planificador / lector | Debe dividirse. |
| `STOP-TEST-SCOPE-TOO-WIDE` | Selección de tests demasiado amplia. | Planificador | Output o contexto excesivo. |
| `STOP-UNBOUNDED-OUTPUT` | Comando puede generar output sin cota clara. | Planificador / lector | Riesgo de contexto. |
| `STOP-DEPENDENCY-UNVERIFIED` | No existe paquete, versión o fuente verificable. | Planificador / lector | Dependencia no verificada. |
| `STOP-MIXED-SCOPE` | Hoja mezcla demasiadas modalidades. | Planificador | Hoja no atómica. |
| `STOP-ACTUAL-PREFLIGHT-EXCEEDED` | Request real rebasa presupuesto previo. | Lector | Preflight excedido. |
| `STOP-INSUFFICIENT-EVIDENCE` | Hoja no produjo evidencia suficiente. | Lector | Faltó evidencia dentro del scope. |
| `STOP-EPISTEMIC-MIX` | Evidence packet mezcla inferencia con verificación. | Lector / creador de prompt | Error epistemológico. |
| `STOP-HUMAN-DECISION-REQUIRED` | Falta decisión humana necesaria. | Cualquier instancia | Decisión no inferible. |
| `STOP-SCOPE-ESCALATION-REQUIRED` | Lector necesita salir del allowed_scope. | Lector | Scope insuficiente. |
| `STOP-MISSING-DECISION-LINK` | Hoja no explica qué decisión desbloquea. | Planificador | Hoja inválida. |
| `STOP-MISSING-SUCCESS-CONDITION` | Hoja no define respuesta suficiente. | Planificador | Hoja inválida. |
| `STOP-MISSING-STOP-CONDITIONS` | Hoja no define parada. | Planificador | Hoja inválida. |
| `STOP-NON-ATOMIC-READING-LEAF` | Hoja contiene más de una pregunta primaria. | Planificador | Debe dividirse. |
| `STOP-VAGUE-READING-LEAF` | Hoja contiene instrucción abierta. | Planificador | Hoja inválida. |
| `STOP-CLAIM-WITHOUT-SUPPORT` | Claim VERIFICADO sin soporte directo. | Lector / creador de prompt | Evidence inválida. |
| `STOP-INFERRED-WITHOUT-REASONING` | Claim INFERIDO sin razonamiento. | Lector | Evidence inválida. |
| `STOP-FINAL-PROMPT-UNTRACEABLE` | Instrucción final no trazable. | Creador de prompt | Prompt inválido. |
| `STOP-ARCHITECTURE-DECISION-MISSING` | Falta decisión arquitectónica necesaria. | Planificador / creador / implementador | Arquitectura no decidida. |
| `STOP-REUSE-SCAN-MISSING` | Falta lectura anti-duplicación obligatoria. | Planificador / creador | No hay reuse scan. |
| `STOP-DUPLICATION-RISK` | Riesgo de duplicar comportamiento existente. | Lector / creador / implementador | Candidato existente no resuelto. |
| `STOP-GOD-OBJECT-PATH` | Solución concentra responsabilidades independientes. | Planificador / creador / implementador | God object risk. |
| `STOP-PREMATURE-ABSTRACTION` | Abstracción genérica sin evidencia. | Planificador / creador / implementador | Manager/utils prematuro. |
| `STOP-EXTERNAL-SOURCE-REQUIRED` | Se necesita fuente externa task-critical. | Planificador / lector / creador | Docs/local no bastan. |
| `STOP-EXTERNAL-SOURCE-AUTH-REQUIRED` | Falta autorización humana para fuente externa. | Planificador / lector / creador | Antes de opensrc. |
| `STOP-EXTERNAL-SOURCE-NOT-RELEVANT` | Dependencia externa irrelevante. | Planificador / lector | No debe obtenerse. |
| `STOP-EXTERNAL-SOURCE-VERSION-UNKNOWN` | Versión task-critical desconocida. | Planificador / lector | Versión afecta comportamiento. |
| `STOP-SECURITY-BOUNDARY-UNVERIFIED` | Auth/ownership/autorización no verificados. | Lector / creador / implementador | Datos de usuario o función pública. |
| `STOP-DATA-CONTRACT-UNVERIFIED` | Contrato o migración no verificados. | Planificador / lector / creador / implementador | Schema/DTO/API/generated/sync. |
| `STOP-GENERATED-CODE-BOUNDARY` | Se intenta editar generated code como implementación primaria. | Creador / implementador | Boundary generado violado. |

---

## 23. Formatos YAML obligatorios

### 23.1 Paquete de Requisitos Raíz

```yaml
root_requirement_packet:
  id:
  title:
  human_objective:
  problem_statement:
  affected_actor:
  expected_behavior:
  functional_requirements:
  non_functional_requirements:
  product_constraints:
  technical_constraints:
  out_of_scope:
  affected_data_or_contracts:
  acceptance_criteria:
  non_regression_criteria:
  expected_verifications:
  known_risks:
  explicit_human_decisions:
  allowed_unknowns:
  blocking_unknowns:
```

### 23.2 Feature node

```yaml
feature_node:
  id:
  parent_id:
  root_requirement_packet_id:
  type: feature | subfeature | task
  title:
  objective:
  requirement_trace:
  expected_behavior_delta:
  included_scope:
  excluded_scope:
  affected_contracts:
  risks:
  unknowns:
  estimated_diff_ref:
  completion_criterion:
  acceptance_criteria:
  non_regression_criteria:
  status: requires_split | executable_leaf | blocked
```

### 23.3 Diff estimate

```yaml
diff_estimate:
  estimate_id:
  artifact_id:
  repo:
  executable_leaf_id:
  root_requirement_packet_id:
  feature_node_id:
  estimated_by_role:
  model:
  harness:
  stage: initial_classification | feature_tree | post_reading | pre_prompt
  estimate_status: provisional | committed | superseded
  predicted_added_lines:
  predicted_deleted_lines:
  predicted_total_diff:
  uncertainty_range:
    low:
    likely:
    high:
  confidence: low | medium | high
  confidence_basis: heuristic | historically_calibrated
  task_size_class: tiny | small | medium | large | oversize
  task_category:
  stack:
  evidence_basis:
    - source_type: root_packet | feature_tree | reading_leaf | evidence_packet | repo_context | human_decision
      source_id:
      claim:
  expected_files_or_areas:
    - path_or_area:
      predicted_added_lines:
      predicted_deleted_lines:
      predicted_total_diff:
      reason:
  work_components:
    production_code:
      added:
      deleted:
    tests:
      added:
      deleted:
    docs:
      added:
      deleted:
    migrations_or_schema:
      added:
      deleted:
    generated_artifacts:
      added:
      deleted:
    refactor_prerequisite:
      added:
      deleted:
  exclusions_or_separate_tracking:
    - generated_artifacts
    - vendored_code
    - lockfiles
  risk_factors:
    - factor:
      severity: low | medium | high
      expected_impact_on_diff:
      rationale:
  underestimation_risk: low | medium | high
  split_recommendation: no_split | split_recommended | split_required
  stop_label_if_uncertain: true | false
  stop_reason:
  calibration_inputs_used:
    local_repo_examples_count:
    similar_task_examples_count:
    category_adjustment_factor:
    notes:
```

### 23.4 Hoja de lectura

```yaml
reading_leaf:
  reading_leaf_id:
  executable_leaf_id:
  mode: read_only

  primary_question:

  decision_link:

  allowed_scope:
    paths:
    symbols:
    tests:
    docs:
    commands:
    dependencies:

  disallowed_scope:
    paths:
    symbols:
    tests:
    docs:
    commands:
    dependencies:

  search_plan:
    - lookup_1:
    - lookup_2:
    - lookup_3_optional:
    - lookup_4_optional:

  success_condition:

  stop_conditions:
    - stop_by_sufficient_evidence:
    - stop_by_not_found:
    - stop_by_contradiction:
    - stop_by_context_limit:
    - stop_by_scope_violation:

  missing_evidence_policy:

  contradiction_policy:

  claim_label_policy:
    allowed_labels:
      - VERIFICADO
      - INFERIDO
      - DESCONOCIDO

  citation_format:
    file_span: "path:start-end"
    tool_output: "tool:<id>"
    test_span: "path:start-end"
    doc_span: "doc:<id>:start-end"

  estimation:
    mode: proxy | exact
    W_used:
    proxy_threshold_8_percent:
    objective_limit_10_percent:
    hard_cap_15_percent:
    conservative_estimate:

  output_schema: evidence_packet_v1

  prohibited_actions:
    - escribir_codigo
    - implementar
    - ampliar_scope_sin_autorizacion
    - inferir_como_verificado
    - adivinar_fuentes
```

### 23.5 Evidence packet

```yaml
evidence_packet:
  packet_id:
  reading_leaf_id:
  executable_leaf_id:
  status: ANSWERED | BLOCKED | NOT_FOUND | CONTRADICTED

  primary_answer:

  claims:
    - claim_id:
      label: VERIFICADO | INFERIDO | DESCONOCIDO
      statement:
      support:
        - source_type:
          citation:
      reasoning_note:

  contradictions:
    - topic:
      sources:
      resolution: UNRESOLVED | CODE_PRIORITIZED | DOC_PRIORITIZED

  not_found:
    - entity_type: file | symbol | test | command | dependency | doc
      name:
      searched_in:

  sources_reviewed:
    files:
    symbols:
    tests:
    commands:
    dependencies:
    docs:

  risks:

  blockers:

  insufficiencies:

  traceability_note:

  recommendation_for_prompt_final:
```

### 23.6 Architecture decision packet

```yaml
architecture_decision_packet:
  packet_id:
  executable_leaf_id:
  source_evidence_packets:
    - packet_id:

  status: COMPLETE | BLOCKED | INSUFFICIENT | CONTRADICTED

  affected_boundaries:
    - boundary_id:
      type: ui | state | repository | database | sync | convex_query | convex_mutation | convex_action | http_api | auth | ownership | validator | external_dependency | generated_code | other
      description:
      verified_sources:
        - citation:

  existing_implementation_candidates:
    - candidate_id:
      symbol:
      path:
      behavior:
      label: VERIFICADO | INFERIDO | DESCONOCIDO
      support:
        - citation:

  reuse_scan_ref:

  duplication_risks:
    - risk_id:
      description:
      severity: low | medium | high
      evidence:
        - citation:

  god_object_risks:
    - risk_id:
      description:
      severity: low | medium | high
      trigger:
      evidence:
        - citation:

  premature_abstraction_risks:
    - risk_id:
      description:
      proposed_abstraction:
      reason_suspicious:
      evidence:
        - citation:

  required_architectural_decisions:
    - decision_id:
      question:
      required_for:
      status: VERIFIED | HUMAN_DECISION_REQUIRED | UNKNOWN
      blocking: true | false

  verified_existing_patterns:
    - pattern:
      scope:
      evidence:
        - citation:

  inferred_existing_patterns:
    - pattern:
      reasoning_note:
      evidence:
        - citation:

  unknown_architectural_facts:
    - fact:
      why_needed:
      possible_source:

  external_dependencies_requiring_source_read:
    - dependency:
      reason:
      external_source_decision_ref:

  allowed_architectural_moves:
    - move:
      trace:
        - packet_id:
        - claim_id:
        - decision_id:

  forbidden_architectural_moves:
    - move:
      reason:
      stop_label:

  recommendation_for_prompt_final:
```

### 23.7 Reuse scan

```yaml
reuse_scan:
  reuse_scan_id:
  executable_leaf_id:
  status: COMPLETE | NOT_FOUND | BLOCKED | CONTRADICTED

  primary_behavior_searched:

  searched_symbols:
    - symbol:
      searched_in:
        - path:

  searched_paths:
    - path:
      reason:

  searched_queries:
    - query:
      scope:
      result_count:

  existing_candidates:
    - candidate_id:
      symbol:
      path:
      behavior_verified:
      support:
        - citation:
      reusable_as_is: true | false | unknown
      reusable_with_extension: true | false | unknown
      reason_not_reused:
      risks_if_reused:
      risks_if_not_reused:

  no_candidate_evidence:
    searched_in:
      - path:
    not_found:
      - symbol_or_behavior:

  duplication_risk:
    level: none | low | medium | high
    explanation:

  recommendation:
    action: reuse_existing | extend_existing | create_new | blocked
    target_candidate_id:
    rationale:
    required_prompt_instruction:
```

### 23.8 External source decision

```yaml
external_source_decision:
  decision_id:
  executable_leaf_id:
  dependency_name:
  dependency_type: package | framework | sdk | runtime | generated_client | external_service | docs
  version:
  version_status: VERIFIED | UNKNOWN | NOT_APPLICABLE

  task_critical_behavior:
    description:
    why_needed:

  local_repo_check:
    source_present_in_repo: true | false | unknown
    checked_paths:
      - path:
    checked_generated_or_types:
      - path:
    result:

  official_docs_check:
    docs_needed: true | false
    docs_sufficient: true | false | unknown
    docs_reviewed:
      - name:
        citation:

  external_source_check:
    source_needed: true | false | unknown
    reason:
    opensrc_required: true | false
    human_authorization_present: true | false

  decision:
    allowed_action: use_local_source | use_generated_types | use_official_docs | request_human_authorization | use_opensrc | do_not_fetch
    stop_label:
    rationale:

  required_documentation_if_fetched:
    package:
    version_or_commit:
    source_url_or_opensrc_record:
    files_read:
```

### 23.9 Prompt final

```markdown
Objetivo:

Hoja ejecutable original:

Diff estimate committed:

Árbol de lectura usado:

Evidence packets usados:

Architecture decision packet usado:

Reuse scan usado:

External source decision usado:

Decisiones humanas usadas:

Alcance permitido:

Archivos confirmados:

Cambios permitidos:

Cambios prohibidos:

Evidencia usada:

Instrucciones de implementación:

Comandos de verificación:

Riesgos:

Desconocidos:

Inferencias no obligatorias:

Criterios de detención:

Reporte final requerido:
```

### 23.10 Diff result

```yaml
diff_result:
  result_id:
  date:
  repo:
  estimate_id:
  executable_leaf_id:
  implemented_by_role: implementer
  implemented_by:
  model:
  harness:
  stage: post_implementation
  git_numstat:
    added_lines:
    deleted_lines:
    total_diff:
    includes_all_tracked_changes: true
  generated_diff:
    attribution_status: reliable | partial | unavailable
    added_lines:
    deleted_lines:
    total_diff:
  human_authored_diff:
    attribution_status: reliable | partial | unavailable
    added_lines:
    deleted_lines:
    total_diff:
  files_changed:
    - path:
      added:
      deleted:
      generated: true | false | unknown
      notes:
  commands_run:
    - command:
      purpose:
      outcome:
  verification_status: passed | passed_with_warnings | failed | not_run
  r2_status: within_limit | exceeded
  canonical_actual_total_diff:
  notes:
    under_or_over_reason:
    unexpected_scope:
    repo_conditions:
```

### 23.11 Diff prediction ledger entry

```yaml
diff_prediction_ledger_entry:
  entry_id:
  date:
  repo:
  executable_leaf_id:
  root_requirement_packet_id:
  feature_node_id:
  task_category:
  stack:
  model:
  harness:
  estimator_role:
  estimate_stage: pre_prompt
  estimate:
    added:
    deleted:
    total:
    low:
    likely:
    high:
    confidence:
    confidence_basis:
    task_size_class:
  result:
    added:
    deleted:
    total:
    generated_total:
    human_authored_total:
    verification_status:
    r2_status:
  error:
    signed_total:
    absolute_total:
    relative_total:
    underestimated: true | false
    overestimated: true | false
    within_range: true | false
  scope:
    files_changed_count:
    expected_files_changed_count:
    architecture_layers_touched:
  drivers:
    - unexpected_file:
    - hidden_complexity:
    - tests_added:
    - schema_or_generated:
    - refactor_needed:
    - duplicated_code_discovered:
    - architecture_decision_changed:
    - external_dependency:
    - auth_or_permissions:
    - shared_contract_change:
  calibration_notes:
  future_adjustment_hint:
```

### 23.12 Reporte de hoja sobredimensionada

```yaml
read_leaf_oversize_report:
  report_type: READ_LEAF_OVERSIZE
  reading_leaf_id:
  actor:
  stage: planning | preflight | runtime
  stop_label:
  W_used:
  proxy_threshold_8_percent:
  objective_limit_10_percent:
  hard_cap_15_percent:
  estimation_mode: exact | proxy
  original_estimate:
  actual_size_detected:
  probable_cause:
  proposed_split:
  human_decision_required: true | false
```

### 23.13 Reporte de hoja insuficiente

```yaml
read_leaf_insufficient_report:
  report_type: READ_LEAF_INSUFFICIENT
  reading_leaf_id:
  actor:
  stop_label: STOP-INSUFFICIENT-EVIDENCE
  primary_question:
  evidence_produced:
  verified_claims:
  inferred_claims:
  unknown_claims:
  why_insufficient:
  additional_sources_needed:
  recommended_reading_leaves:
  human_decision_required: true | false
```

---

## 24. Ejemplos operativos

### Ejemplo 1: petición ejecutable localizada

Petición: cambiar validación de email.

El clasificador produce:

- root requirement packet;
- feature node;
- diff_estimate inicial:
  - low: 20;
  - likely: 60;
  - high: 120;
  - split_recommendation: no_split.

Resultado: puede pasar a planificación de lectura porque high ≤600 y no hay desconocidos bloqueantes.

### Ejemplo 2: petición con high >600

Petición: crear sistema completo de inventario.

El clasificador predice:

- likely: 1,800;
- high: 2,600.

Resultado:

- NO es hoja ejecutable;
- DEBE dividirse en árbol de features;
- si una subfeature queda con high >600, DEBE dividirse otra vez.

### Ejemplo 3: high supera R2 aunque likely no

Hoja candidata: agregar flujo de exportación con validación y tests.

Diff estimate:

- likely: 480;
- high: 720;
- confidence: low;
- risk_factors: contratos compartidos, tests, integración externa.

Resultado:

- DEBE tratarse como riesgo de subestimación;
- DEBE preferirse dividir o reducir scope;
- si no se puede, DEBE reportarse `STOP-DIFF-HIGH-RANGE-EXCEEDS-R2` o pedir decisión humana.

### Ejemplo 4: hoja de lectura buena

```yaml
reading_leaf:
  reading_leaf_id: RL-workout-save-ownership-01
  executable_leaf_id: EL-workout-save-ownership
  mode: read_only

  primary_question: "¿Dónde se verifica actualmente ownership para mutations de workout?"

  decision_link: "Esta respuesta decide si el prompt final debe reutilizar un helper existente o crear un check nuevo."

  allowed_scope:
    paths:
      - "convex/workouts/**"
      - "convex/auth/**"
    symbols:
      - "requireWorkoutOwner"
      - "getAuthenticatedUser"
    tests:
      - "convex/**/workout*.test.*"
    docs: []
    commands:
      - "rg \"requireWorkoutOwner|getAuthenticatedUser|owner\" convex/workouts convex/auth"
    dependencies: []

  disallowed_scope:
    paths:
      - "node_modules/**"
      - "convex/_generated/**"
    symbols: []
    tests: []
    docs: []
    commands: []
    dependencies: []

  search_plan:
    - lookup_1: "Buscar símbolos de ownership en allowed_scope."
    - lookup_2: "Leer sólo archivos con matches relevantes."
    - lookup_3_optional: "Buscar tests relacionados si el flujo no queda claro."

  success_condition: "La hoja queda respondida si identifica helper/patrón de ownership o demuestra NOT_FOUND dentro del allowed_scope."

  stop_conditions:
    - stop_by_sufficient_evidence: "Detenerse al encontrar patrón verificado."
    - stop_by_not_found: "Detenerse si no hay matches tras búsquedas permitidas."
    - stop_by_contradiction: "Reportar contradicción si helpers y tests difieren."
    - stop_by_context_limit: "Detenerse si excede presupuesto."
    - stop_by_scope_violation: "Detenerse si requiere salir del scope."

  missing_evidence_policy: "Hacer lookup mínimo dentro del allowed_scope; si sigue faltando evidencia, marcar DESCONOCIDO."
  contradiction_policy: "Reportar fuentes en conflicto sin mezclarlas."

  claim_label_policy:
    allowed_labels:
      - VERIFICADO
      - INFERIDO
      - DESCONOCIDO

  citation_format:
    file_span: "path:start-end"

  estimation:
    mode: proxy
    W_used: 258400
    proxy_threshold_8_percent: 20672
    objective_limit_10_percent: 25840
    hard_cap_15_percent: 38760
    conservative_estimate: 7000

  output_schema: evidence_packet_v1

  prohibited_actions:
    - escribir_codigo
    - implementar
    - ampliar_scope_sin_autorizacion
    - inferir_como_verificado
    - adivinar_fuentes
```

### Ejemplo 5: reuse scan obligatorio

Hoja ejecutable: agregar validación de ownership a una mutation Convex.

Antes del prompt final:

- el planificador crea hoja anti-duplicación;
- la lectora busca helpers existentes;
- produce reuse scan.

Si existe `requireWorkoutOwner`, el prompt final DEBE ordenar reutilizarlo o extenderlo.

Si no hay reuse scan, el creador de prompt DEBE reportar `STOP-REUSE-SCAN-MISSING`.

### Ejemplo 6: Convex sin repository pattern por defecto

Hoja ejecutable: modificar una mutation Convex existente para guardar un campo adicional.

La lectura encuentra que el repo usa `ctx.db` directo con helpers locales y no existe repository backend.

Resultado:

- el prompt final NO DEBE ordenar crear `WorkoutRepository` sobre `ctx.db`;
- DEBE seguir el patrón verificado del repo;
- si el implementador intenta crear repository sin autorización, DEBE reportar `IMPLEMENTATION_SCOPE_EXCEEDED` o `STOP-PREMATURE-ABSTRACTION`.

### Ejemplo 7: bloqueo de opensrc sin autorización

Hoja ejecutable: corregir comportamiento dependiente de un SDK externo.

La lectura confirma que:

- la dependencia es task-critical;
- la versión está verificada;
- docs oficiales y tipos locales no bastan;
- source no existe en repo.

Resultado:

- la instancia DEBE reportar `STOP-EXTERNAL-SOURCE-AUTH-REQUIRED`;
- NO PUEDE usar opensrc hasta recibir autorización humana explícita;
- si el humano autoriza, DEBE usar `https://github.com/vercel-labs/opensrc` y documentar paquete, versión o commit, fuente y archivos leídos.

### Ejemplo 8: diff result y ledger

Hoja implementada con diff estimate committed:

```yaml
predicted_total_diff: 180
uncertainty_range:
  low: 100
  likely: 180
  high: 260
```

Después de implementar:

```yaml
git_numstat:
  added_lines: 210
  deleted_lines: 30
  total_diff: 240
```

Cálculo:

```text
actual_total_diff = 240
absolute_error = abs(180 - 240) = 60
relative_error = 60 / 240 = 0.25
within_range = true
underestimated = true
```

Resultado:

- R2 está dentro de límite;
- ledger debe registrar underestimation pero within_range true;
- futuras tareas similares pueden usar esta evidencia para calibrar.

---

## 25. Checklists de uso rápido

### 25.1 Checklist del clasificador

- [ ] Existe petición humana clara.
- [ ] Existe Paquete de Requisitos Raíz.
- [ ] El comportamiento esperado está definido.
- [ ] Los criterios de aceptación están definidos.
- [ ] Los criterios de no regresión están definidos.
- [ ] Cada feature node se traza al root packet.
- [ ] Ningún requisito fue inventado desde inferencia.
- [ ] Cada hoja candidata tiene una sola responsabilidad.
- [ ] Cada hoja candidata tiene `diff_estimate` inicial.
- [ ] `uncertainty_range.high <= 600` para hojas ejecutables.
- [ ] Si high >600, se dividió, redujo o bloqueó.
- [ ] Si no puede estimar, reportó `DIFF_ESTIMATE_UNCERTAIN` o `STOP-DIFF-ESTIMATE-UNCERTAIN`.

### 25.2 Checklist del planificador de lectura

- [ ] Cada hoja de lectura tiene una pregunta primaria.
- [ ] Cada hoja tiene decision_link.
- [ ] Cada hoja tiene allowed_scope.
- [ ] Cada hoja tiene disallowed_scope.
- [ ] Cada hoja tiene success_condition.
- [ ] Cada hoja tiene stop_conditions.
- [ ] Cada hoja tiene presupuesto de contexto.
- [ ] Ninguna hoja dice “lee todo” o equivalente.
- [ ] Si la hoja modifica comportamiento, existe hoja anti-duplicación.
- [ ] Si hay riesgo arquitectónico, existe hoja arquitectónica.
- [ ] Si hay dependencia externa task-critical, existe hoja o decisión de dependencia.
- [ ] Si no sabe qué leer, reportó `READ_PLAN_UNCERTAIN`.

### 25.3 Checklist de la instancia lectora

- [ ] Leyó sólo allowed_scope.
- [ ] No escribió código.
- [ ] No implementó.
- [ ] No amplió scope.
- [ ] Produjo evidence packet.
- [ ] Cada claim tiene label.
- [ ] Los claims VERIFICADOS tienen soporte directo.
- [ ] Los claims INFERIDOS tienen reasoning_note.
- [ ] Los claims DESCONOCIDOS explican qué falta.
- [ ] Contradicciones fueron reportadas, no promediadas.
- [ ] Si faltó scope, reportó `STOP-SCOPE-ESCALATION-REQUIRED`.

### 25.4 Checklist del creador de prompt

- [ ] Leyó la guía de prompting del modelo implementador.
- [ ] Todos los evidence packets requeridos existen.
- [ ] Claims INFERIDOS/DESCONOCIDOS no fueron convertidos en VERIFICADOS.
- [ ] Existe reuse scan si la hoja modifica comportamiento.
- [ ] Existe architecture decision packet si hay decisión arquitectónica.
- [ ] Existe external source decision si hay dependencia externa task-critical.
- [ ] Existe `diff_estimate` pre_prompt committed.
- [ ] Si high >600, no envió al implementador sin dividir/reducir/bloquear.
- [ ] Cada instrucción crítica se traza a evidencia o decisión humana.
- [ ] El prompt final incluye criterios de detención.
- [ ] El prompt final exige `diff_result`.

### 25.5 Checklist del implementador

- [ ] Opera sólo desde prompt final aprobado.
- [ ] No amplía scope.
- [ ] No usa claims DESCONOCIDOS como instrucciones.
- [ ] No introduce arquitectura no autorizada.
- [ ] No duplica comportamiento existente verificado.
- [ ] No crea manager/utils genérico sin autorización.
- [ ] No edita generated code manualmente como implementación primaria.
- [ ] Se detiene si encuentra contradicción prompt vs repo.
- [ ] Se detiene si descubre riesgo de subestimación material.
- [ ] Ejecuta verificaciones indicadas.
- [ ] Mide `git diff --numstat`.
- [ ] Produce `diff_result`.
- [ ] Reporta R2 si se excede.

### 25.6 Checklist del auditor de cierre / ledger

- [ ] Existe diff_estimate committed.
- [ ] Existe diff_result.
- [ ] Calculó actual_total_diff.
- [ ] Calculó signed_error.
- [ ] Calculó absolute_error.
- [ ] Calculó relative_error.
- [ ] Calculó within_range.
- [ ] Registró underestimation/overestimation.
- [ ] Separó diff generado/humano si fue atribuible.
- [ ] Registró drivers de error.
- [ ] Actualizó diff_prediction_ledger.
- [ ] Marcó future_adjustment_hint si aplica.

---

## 26. Errores frecuentes que AI Rules busca prevenir

### 26.1 “Esto parece pequeño” sin diff estimate

Error: clasificar por intuición.  
Prevención: `diff_estimate` con rango low/likely/high y risk factors.

### 26.2 “Sólo agrego un helper nuevo” sin reuse scan

Error: duplicar lógica existente.  
Prevención: lectura anti-duplicación obligatoria.

### 26.3 Crear repository sobre `ctx.db` en Convex porque parece limpio

Error: imponer arquitectura backend tradicional sobre Convex sin evidencia.  
Prevención: R10 prohíbe repository pattern sobre `ctx.db` salvo patrón verificado, necesidad trazable o decisión humana.

### 26.4 Meter auth, validation, negocio y side effects en una mutation gigante

Error: god object path.  
Prevención: `STOP-GOD-OBJECT-PATH` y helpers/use-case ligeros.

### 26.5 Crear `utils.ts` para un solo uso

Error: abstracción prematura.  
Prevención: `STOP-PREMATURE-ABSTRACTION`.

### 26.6 Leer dependencia externa innecesaria

Error: gastar contexto y traer ruido.  
Prevención: R6 exige determinar si es task-critical.

### 26.7 Usar opensrc sin autorización

Error: obtener fuente externa sin permiso humano.  
Prevención: `STOP-EXTERNAL-SOURCE-AUTH-REQUIRED`.

### 26.8 Convertir inferencia en instrucción

Error: tratar “parece que” como hecho.  
Prevención: R4 y R5 separan VERIFICADO, INFERIDO y DESCONOCIDO.

### 26.9 Implementar aunque high >600

Error: aceptar una hoja riesgosa por likely bajo.  
Prevención: si high >600, dividir, reducir o bloquear.

### 26.10 No registrar error de estimación

Error: repetir mala calibración.  
Prevención: diff_prediction_ledger.

---

## 27. Guía de evolución del documento

AI Rules debe evolucionar con evidencia, no con preferencias estéticas.

### 27.1 Cambios permitidos

Un cambio al documento PUEDE aceptarse si:

- resuelve un fallo observado;
- reduce ambigüedad;
- mejora trazabilidad;
- mejora calibración;
- elimina duplicación normativa;
- clarifica ownership;
- mantiene separación de roles;
- no convierte cambios triviales en burocracia pesada.

### 27.2 Cambios que requieren cuidado

Requieren revisión explícita:

- agregar nuevos roles;
- agregar nuevos artefactos obligatorios;
- cambiar R2;
- cambiar W base;
- cambiar límites 8/10/15%;
- modificar reglas de Convex;
- modificar política opensrc;
- hacer obligatorio un patrón arquitectónico.

### 27.3 Uso del ledger para evolución

SI el ledger muestra subestimación repetida por categoría, ENTONCES AI Rules DEBE ajustar predicción, división o prompts de estimación.

SI el ledger muestra que una regla genera burocracia sin mejorar resultado, ENTONCES PUEDE revisarse como candidata a simplificación.

SI una etiqueta de detención aparece frecuentemente, ENTONCES DEBE investigarse si falta una regla previa, una plantilla mejor o una decisión humana recurrente.

### 27.4 Principio de compatibilidad

Las nuevas versiones de AI Rules DEBEN conservar compatibilidad conceptual con:

- separación de roles;
- evidencia antes que implementación;
- diff predicho vs diff real;
- hojas de lectura atómicas;
- prompt final trazable;
- detención segura ante incertidumbre.

---

## Cierre

AI Rules existe para que GPT-5.5/Codex y otras instancias de IA trabajen en codebases reales con disciplina de ingeniería: requisitos claros, lectura limitada, evidencia verificable, arquitectura trazable, predicción de tamaño, implementación acotada, medición real y aprendizaje histórico.

La regla cultural más importante es:

> Si no está definido, leído, evidenciado, trazado, dimensionado y autorizado, no debe implementarse como si lo estuviera.
