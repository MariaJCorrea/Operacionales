# Continuación del Proyecto - Cache Simulator

## Estado actual
Se implementaron las secciones 1, 2 y 3 del plan original. Las secciones 4, 5 y 6 están pendientes.

## Archivo principal
`cache_simulatorv2.html` - simulador de caché en HTML/JavaScript puro.

## Cambios realizados (secciones 1-3)

### Sección 1: Clasificación de misses (Compulsory / Conflict / Invalid)
- Agregado `state.loadedBlocks` (Set) para rastrear bloques cargados
- Función `classifyMiss(setLines, blockStartAddr, wasInvalid)`:
  - Invalid: línea con valid=false al reemplazar
  - Compulsory: bloque nunca cargado antes
  - Conflict: bloque cargado pero comparte set con otro
- Contadores `state.compulsoryMisses` y `state.conflictMisses` se actualizan
- Consola muestra: `Cache MISS (Compulsory) for address 0x08`

### Sección 2: Políticas de reemplazo (LRU / FIFO / Random)
- Función `getReplacementLine(setLines, index)`:
  - Primero busca líneas inválidas libres
  - Si todas ocupadas, usa `state.replacement`:
    - LRU: línea con menor `lruCounter`
    - FIFO: línea con menor `fifoOrder`
    - Random: línea aleatoria
- Contadores `lruCounter` y `fifoOrder` actualizados en cada acceso

### Sección 3: Write-back con dirty bit
- Función `writeBackLine(line, setIdx)`: escribe línea dirty a RAM antes de sobrescribirla
- `handleFlush()`: recorre líneas dirty y las escribe a RAM antes de limpiar
- Contador `state.memoryWrites` actualizado correctamente

## Secciones pendientes (NO implementar todavía, esperar aprobación)

### Sección 4: Explicaciones interactivas (nuevo botón Explain)
- Agregar botón "Explain" en la sección de Manual Memory Access (junto a Read/Write/Flush)
- Al presionarlo, mostrar en el panel de explicación (explanationPanel):
  - Descomposición de la dirección (Tag, Index, Offset)
  - Si fue hit o miss, y tipo de miss
  - Qué pasó con los datos (carga desde RAM, escritura a RAM, etc.)
  - Estado de los bits V, D, T de la línea afectada

### Sección 5: Navegación por historial (NavPrev / NavNext)
- Corregir la variable global `navIdx` que no se sincroniza después de usar Load
- Asegurar que la navegación por flechas funcione correctamente

### Sección 6: Mejoras de visualización
- Mostrar significados de los bits V (Valid), D (Dirty), T (Tag) en la tabla de caché
- Actualizar consola para muestre detalles de la operación

## Estructura del state (objeto global)
```javascript
state = {
  initialized, addrWidth, cacheSize, blockSize, associativity,
  writeHit, writeMiss, replacement, numSets, linesPerSet,
  bitsOffset, bitsIndex, bitsTag,
  hits, misses, compulsoryMisses, conflictMisses,
  memoryReads, memoryWrites,
  ram, cache, fifoQueues, loadedBlocks, accessHistory,
  timestamp, explainStep
}
```

## Funciones clave existentes
- `readParameters()` - lee los valores del HTML
- `initRAM()` - genera memoria RAM con semilla 'cse351'
- `initCache()` - inicializa la caché (líneas con valid=false, dirty=false)
- `onSystemAction()` - botón Generate System / Reset System
- `renderExplanationStep()` - pasos guiados iniciales (1, 2, 3)
- `renderCache()` - muestra la tabla de caché
- `renderPhysicalMemory()` - muestra la memoria RAM
- `decomposeAddress(addr)` - calcula Tag, Index, Offset
- `getReplacementLine(setLines, index)` - selecciona línea según política
- `classifyMiss(...)` - clasifica el tipo de miss
- `writeBackLine(line, setIdx)` - escribe línea dirty a RAM
- `handleRead()` - procesa lectura
- `handleWrite()` - procesa escritura
- `handleFlush()` - limpia caché (escribe dirty a RAM primero)
- `loadHistory()` - carga y ejecuta historial
- `navPrev()`, `navNext()`, `executeCmd()` - navegación por historial

## Parámetros configurables (UI)
- Address width: 4, 6, 8, 10, 12 bits
- Cache size: 8, 16, 32, 64, 128, 256 bytes
- Block size: 2, 4, 8 bytes (radio buttons)
- Associativity: 1, 2, 4 ways (radio buttons)
- Write Hit: Write back / Write through (select)
- Write Miss: Write-allocate / No-write-allocate (select)
- Replacement: LRU / FIFO / Random (select)
- Checkbox "Explain" - activa pasos guiados al generar

## Notas importantes
- No calcular Miss Rate ni AMAT (no se incluye en el alcance)
- No hacer recuentos adicionales de estadísticas
- Los datos de RAM se generan con PRNG determinista (semilla 'cse351')
- La caché asume que cache size, block size y numSets son potencias de 2
- El archivo Python `cache_simulator.py` NO fue modificado (es una implementación separa)

## Para continuar
1. Leer este archivo para entender el estado actual
2. Revisar `cache_simulatorv2.html` para ver el código existente
3. Solicitar aprobación para cada sección antes de implementar
4. Implementar sección por sección (4, luego 5, luego 6)
5. Validar después de cada cambio con `node -e` para verificar sintaxis