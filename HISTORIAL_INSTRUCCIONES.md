# Instrucciones para implementar el historial (snapshots + navegación)

## Objetivo
Cambiar la navegación del historial para que NO re-ejecute comandos, sino que restaure el estado de la caché justo después de cada comando. Además, corregir el render del textarea para que solo haya un ">" móvil.

## Estado actual (checkpoint)
En este checkpoint:
- Se eliminó `window.onload` que auto-generaba el sistema.
- Se quitó el `onclick` inline del botón "Generate System" y se deja al JS asignarlo.
- Se corrigieron errores de sintaxis del modo Explain.
- El historial actual usa `addToHistory()` concatenando al textarea con `\n` y `navPrev/Next` llaman a `executeCmd()` (re-ejecutando).

## Comportamiento deseado

### 1) Historial con snapshots
- Solo los accesos **normales** (sin checkbox "Explain" marcado para la operación) deben guardar entradas navegables.
- Los accesos hechos en modo "Explain" NO deben entrar en el historial navegable.
- Por cada comando normal (`Read`/`Write`) guardar un snapshot del estado tras ejecutarlo: 
  - `cache` (copia profunda por set/línea: `valid`, `dirty`, `tag`, `data` clonado, `lruCounter`, `fifoOrder`, `timestamp`)
  - contadores: `hits`, `misses`, `compulsoryMisses`, `conflictMisses`, `memoryReads`, `memoryWrites`
- Guardar también: `historyBaseText[]` = `["r 0x08", "w 0x00 0xFF", ...]`, `historyResults[]` = `["H", "M", ...]` en el mismo orden.

### 2) Textarea - un solo ">" móvil
El textarea NO debe empezar con línea vacía. Debe tener **exactamente un ">"**.
Formato:
```text
r 0x08 = H
w 0x00 0xFF = ?
>
```
Reglas:
- Construir desde `historyBaseText`. Nunca hacer `textarea.value = "> " + ...` ni concatenar con `>`.
- Para cada i: si `i < historyViewPos` mostrar `cmd = H/M` (conocido). Si `i >= historyViewPos` mostrar `cmd = ?` (incógnito).
- Añadir **una sola línea** `>` al final. Ese `>` se mueve con la navegación (cambia posición visual según `historyViewPos`).
- Al ejecutar un comando nuevo, `historyViewPos = historyBaseText.length` (">" queda justo después del último comando).
- Al pulsar Load y quedarse "parado", **el punto donde se queda debe dejar en incógnita M/H** y **de ahí para abajo todo pasa a `?`**. El ">" marca esa posición.

### 3) Navegación sin re-ejecutar
`navPrev()` / `navNext()` NO deben llamar a `executeCmd()`, `handleRead()`, `handleWrite()`.
- Actualizar `historyViewPos` según la dirección.
- Si hay snapshot en la posición correspondiente, restaurar:
  - `state.cache` (deep copy inversa desde snapshot)
  - todos los contadores guardados
- **NO** restaurar valores de inputs (`readAddr`/`writeAddr`/`writeByte`).
- Llamar a `renderCache()`, `renderPhysicalMemory()`, `updateStatsDisplay()`.
- Llamar a `renderHistoryView()` para actualizar el ">" y los `?`.

### 4) Botón Load
Modificar `loadHistory()`:
1. Limpiar: `historyBaseText = []`, `historyResults = []`, `historySnapshots = []`, `historyViewPos = -1`
2. Llamar a `onSystemAction()` para resetear sistema
3. Parsear textarea ignorando líneas que empiecen con `>` y quedarse solo con `r/w` válidos
4. Ejecutar cada comando por el camino normal (`handleRead()`/`handleWrite()`) para que **se generen snapshots automáticamente**
5. Tras reproducir toda la traza, dejar `historyViewPos` en la posición que corresponda para que se muestre con `?` desde el punto donde "se queda parado". El ">" debe quedar en esa línea siguiente indicando la posición.
6. Sincronizar navegación

## Campos a añadir en `state`
```js
historyBaseText: [],
historyResults: [],
historySnapshots: [],
historyViewPos: -1
```

## Funciones necesarias
- `cloneCacheSnapshot()` - copia profunda de `state.cache`
- `restoreCacheSnapshot(snap)` - restaura `state.cache` desde copia
- `saveHistorySnapshot(cmd, isHit)` - guarda snapshot, añade a arrays, actualiza `historyViewPos`, llama `renderHistoryView()`
- `renderHistoryView()` - pinta textarea con **único ">"** móvil según reglas

## Puntos importantes
- **Deep copy obligatorio**: clonar arrays `data[]` y objetos de línea al guardar/restaurar.
- **Solo modo normal**: integrar `saveHistorySnapshot()` en `handleRead()`/`handleWrite()` **después** de aplicar cambios, **solo si NO hay explain activado**.
- **Explain aislado**: `renderExplainStep()` NO debe tocar el historial navegable.
- **Sin re-ejecución**: navegación puramente visual/restaurativa.
- **Una sola línea con ">"**: eliminar cualquier concatenación que añada ">" desde otros lados.
