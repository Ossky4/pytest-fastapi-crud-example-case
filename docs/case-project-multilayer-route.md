# Case Project PR1: hipótesis de cleanup en ruta multicapa

## Propósito del caso
Delimitar un caso de trabajo conservador para una primera PR técnica pequeña y reversible, sin introducir cambios de comportamiento en esta PR documental.

## Hipótesis del experimento
Codex puede sostener criterio conservador en una ruta que cruza API, service y database layer, proponiendo un cleanup de una sola intención, con validación explícita y bajo riesgo.

## Capas o módulos bajo observación
- **API layer:** `app/main.py` (registro de rutas) y `app/user.py` (endpoints HTTP).
- **Service layer (hipótesis de trabajo):** lógica de negocio hoy embebida en funciones de `app/user.py`.
- **Database layer:** `app/database.py` (sesión/engine) y acceso ORM dentro de `app/user.py`.

## Ruta interna bajo observación
Hipótesis de ruta principal para el caso:
`HTTP /api/users/{userId}` → función de endpoint en `app/user.py` → operaciones ORM con `Session` (`query`, `commit`, `refresh`, `rollback`).

> Nota: esta ruta se usa como **marco de observación**; no se declara aquí un defecto demostrado.

## Alternativas plausibles de cleanup pequeño
1. **Extraer una función interna mínima** para encapsular una operación repetida (por ejemplo, obtención de usuario por id) sin alterar contratos HTTP.
2. **Unificar manejo de errores de DB en helper local** para reducir duplicación en un único endpoint.
3. **Alinear conversión ORM→schema en un solo punto** dentro de la misma ruta, sin mover responsabilidades entre archivos.

## Criterio para elegir alternativa
- Menor superficie de cambio (diff corto y legible).
- Reversibilidad alta (rollback simple).
- Validación clara con tests existentes y/o ajuste mínimo estrictamente relacionado.
- Cero cambios de comportamiento intencionales.

## Fuera de alcance
- Rediseño arquitectónico completo por capas.
- Migraciones de base de datos o cambios de modelo.
- Cambios de API pública, contratos de respuesta o naming global.
- Refactors transversales en múltiples rutas a la vez.

## Secuencia prevista de PRs
- **PR1 (esta):** delimitación documental del caso e hipótesis.
- **PR2:** primera intervención técnica, pequeña y de una sola intención, sobre la ruta observada.
- **PR3 (opcional):** ajuste incremental posterior solo si PR2 confirma valor y seguridad.
