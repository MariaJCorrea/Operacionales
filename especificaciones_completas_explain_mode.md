# Especificaciones de Animación y Flujo de Explicación (Explain Mode) - Cache Simulator

Este documento define la máquina de estados finitos (FSM) y los micro-pasos requeridos para el modo de explicación interactiva (botón **"Next"**) en el simulador de caché para todas las operaciones de **Lectura (Read)** y **Escritura (Write)**.

---

## 1. Secuencia de Micro-pasos: Lectura con CACHE MISS (Read Miss)

Cuando se ejecuta una operación de lectura (`Read`) y el bloque no se encuentra en la caché (Miss), la simulación requerirá clics sucesivos en el botón **"Next"** a través de 7 micro-pasos:

### PASO 1: Inicio de Lectura y División TIO
* **Simulation Messages**: Mostrar `Read: 0x01` (según la dirección ingresada).
* **Desglose TIO (Tag, Index, Offset)**: Actualizar los valores calculados en los paneles superiores (ej. Tag: `000`, Index: `00`, Offset: `001`).
* **History**: Agregar el comando en ejecución `> R(0x01) = ?`.

### PASO 2: Desglose TIO Confirmado
* **Simulation Messages**:
  ```text
  Read: 0x01
  Split address into TIO breakdown.
  ```
* **Controles**: El campo de entrada de dirección permanece bloqueado mientras avanza la explicación.

### PASO 3: Verificación del Set (Index Match)
* **Simulation Messages**: Agregar a los mensajes previos: `Checking Set 0`.
* **Efecto Visual (Tabla Cache)**: Resaltar con fondo translúcido (rojo/naranja claro) todo el contenedor del conjunto correspondiente (`Set 0`) para indicar que el índice apunta a él.

### PASO 4: Comparación de Tag y Detección de Miss
* **Simulation Messages**: Agregar: `Looking for Tag 0... MISS!`.
* **Estadísticas**: Incrementar en 1 el contador de **Cache Misses** (caja azul).
* **History**: Actualizar la línea del historial a `> R(0x01) = M`.

### PASO 5: Elección de Línea para Reemplazo
* **Simulation Messages**: Agregar: `Invalid Line 0 chosen for replacement.`.
* **Efecto Visual (Tabla Cache)**: Resaltar en rojo el bit de Validez (`V`) de la línea seleccionada dentro del set.

### PASO 6: Carga de Bloque desde Memoria Principal
* **Simulation Messages**: Agregar: `Block read into cache from memory at address 0x0.`.
* **Efecto Visual (Tabla Cache)**:
  * Actualizar el bit de Validez `V` a `1` y Dirty `D` a `0`.
  * Escribir el valor del Tag en el campo `T`.
  * Rellenar los bytes del bloque con los datos obtenidos de la memoria principal.
* **Efecto Visual (Memoria Física)**: Resaltar con color los bytes correspondientes al bloque leído en el panel de Physical Memory.

### PASO 7: Actualización de Estado LRU y Finalización
* **Simulation Messages**: Agregar: `LRU statuses updated.`.
* **Estado Final**: El bloque completo queda cargado en la caché y la interfaz se desbloquea para la siguiente instrucción.

---

## 2. Secuencia de Micro-pasos: Lectura con CACHE HIT (Read Hit)

Cuando se ejecuta una operación de lectura (`Read`) y el bloque ya se encuentra cargado en la caché (Hit), la secuencia consta de 6 micro-pasos:

### PASO 1: Inicio de Lectura
* **Simulation Messages**: Mostrar `Read: 0x01`.
* **Desglose TIO**: Actualizar los paneles TIO con los valores calculados de la dirección.
* **History**: Agregar `> R(0x01) = ?`.

### PASO 2: División TIO Confirmada
* **Simulation Messages**:
  ```text
  Read: 0x01
  Split address into TIO breakdown.
  ```

### PASO 3: Selección del Set
* **Simulation Messages**: Agregar: `Checking Set 0`.
* **Efecto Visual (Tabla Cache)**: Resaltar con fondo suave todo el contenedor del `Set 0` (manteniendo los datos actuales visibles).

### PASO 4: Detección de Cache Hit
* **Simulation Messages**:
  ```text
  Read: 0x01
  Split address into TIO breakdown.
  Checking Set 0
  Looking for Tag 0... HIT in Line 0!
  ```
* **Estadísticas**: Incrementar en 1 el contador de **Cache Hits** (caja azul).
* **History**: Actualizar la línea del historial a `> R(0x01) = H`.
* **Efecto Visual**: Desactivar el resaltado general del set.

### PASO 5: Actualización del Estado LRU
* **Simulation Messages**: Agregar: `LRU statuses updated.`.
* **Efecto Visual (Tabla Cache)**: Destacar brevemente el conjunto para reflejar el acceso reciente.

### PASO 6: Lectura del Byte Específico y Retorno de Dato
* **Simulation Messages**: Agregar el valor del byte leído: `Data: 0xf6`.
* **Efecto Visual (Tabla Cache)**:
  * Quitar el resaltado del conjunto.
  * Resaltar con un borde o fondo distintivo exclusivamente la celda del byte exacto dentro del bloque según su Offset (ej. el byte correspondiente a `f6`).
* **Finalización**: Operación completada con éxito.

---

## 3. Secuencia de Micro-pasos: Escritura con CACHE HIT (Write Hit - Write Back)

Cuando se ejecuta una escritura (`Write`) en un bloque que ya está presente en la caché:

### PASO 1: Inicio de Escritura
* **Simulation Messages**: `Write: 0x01 = 0xAA`.
* **Desglose TIO**: Actualizar campos TIO con la dirección a escribir.
* **History**: Agregar `> W(0x01) = ?`.

### PASO 2: División TIO Confirmada
* **Simulation Messages**:
  ```text
  Write: 0x01 = 0xAA
  Split address into TIO breakdown.
  ```

### PASO 3: Selección del Set
* **Simulation Messages**: `Checking Set 0`.
* **Efecto Visual (Tabla Cache)**: Resaltar la fila/conjunto `Set 0`.

### PASO 4: Detección de Cache Hit
* **Simulation Messages**: `Looking for Tag 0... HIT in Line 0!`.
* **Estadísticas**: Incrementar en 1 el contador **Cache Hits**.
* **History**: Actualizar la línea del historial a `> W(0x01) = H`.

### PASO 5: Escritura en Caché y Marca Dirty (Write Back)
* **Simulation Messages**: `Byte 0x01 updated to 0xAA in cache. Line marked dirty (D=1).`.
* **Efecto Visual (Tabla Cache)**:
  * Reemplazar el byte en la posición indicada por el Offset con el valor escrito (`0xAA`).
  * Cambiar el bit de estado Dirty `D` de `0` a `1`.
  * Resaltar en rojo o color de énfasis la celda modificada y el bit Dirty `D`.

### PASO 6: Actualización LRU y Finalización
* **Simulation Messages**: Agregar: `LRU statuses updated.`.
* **Estado Final**: Los cambios permanecen en la caché (sin escribir a memoria RAM principal aún por ser Write-Back) y la interfaz queda libre para el siguiente comando.

---

## 4. Secuencia de Micro-pasos: Escritura con CACHE MISS (Write Miss - Write Allocate + Write Back)

Cuando se ejecuta una escritura (`Write`) y el bloque de datos no está presente en la caché:

### PASO 1: Inicio de Escritura
* **Simulation Messages**: `Write: 0x01 = 0xAA`.
* **Desglose TIO**: Actualizar campos TIO.
* **History**: Agregar `> W(0x01) = ?`.

### PASO 2: División TIO Confirmada
* **Simulation Messages**:
  ```text
  Write: 0x01 = 0xAA
  Split address into TIO breakdown.
  ```

### PASO 3: Selección del Set
* **Simulation Messages**: `Checking Set 0`.
* **Efecto Visual (Tabla Cache)**: Resaltar `Set 0`.

### PASO 4: Detección de Cache Miss
* **Simulation Messages**: `Looking for Tag 0... MISS!`.
* **Estadísticas**: Incrementar en 1 el contador **Cache Misses**.
* **History**: Actualizar la entrada a `> W(0x01) = M`.

### PASO 5: Elección de Línea
* **Simulation Messages**: `Invalid Line 0 chosen for replacement.`.
* **Efecto Visual**: Destacar la línea inválida o la elegida por la política LRU.

### PASO 6: Traída del Bloque a Caché (Write Allocate)
* **Simulation Messages**: `Block read into cache from memory at address 0x0.`.
* **Efecto Visual**:
  * Traer el bloque completo de 8 bytes desde la Memoria Principal e insertarlo en la línea.
  * Establecer el bit de Validez `V` a `1` y Tag `T` correspondiente.
  * Resaltar los bytes recién cargados en la caché y su bloque de origen en Memoria Física.

### PASO 7: Modificación de Byte y Bit Dirty
* **Simulation Messages**: `Byte 0x01 updated to 0xAA in cache. Line marked dirty (D=1).`.
* **Efecto Visual**:
  * Actualizar el byte exacto según el Offset con el valor `0xAA`.
  * Cambiar el bit Dirty `D` de `0` a `1`.
  * Resaltar la casilla modificada.

### PASO 8: Actualización LRU y Finalización
* **Simulation Messages**: `LRU statuses updated.`.
* **Estado Final**: El bloque está asignado en la caché con la modificación realizada y marcado como sucio (`D=1`). Se concluye el flujo de explicación.