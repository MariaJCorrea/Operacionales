## Universidad Industrial de Santander Práctica 3. Memoria Caché Sistemas Operacionales Ingeniería de Sistemas

## 1. Objetivos

- Reconocer las características principales de la memoria caché.

- Distinguir entre las distintas configuraciones de la memoria caché.

- Identificar los tipos de mapeo en memoria caché: directo, asociativo y asociativo por conjuntos.

## 2. Materiales

- CS351 Cache Simulator

- Simulador propio

## 3. Descripción

El simulador CS351, es una herramienta muy usada en cursos de Sistemas Operativos para visualizar cómo se comporta la memoria caché bajo distintas configuraciones (mapeo directo, asociativo, N-way, tamaños de bloque, etc.). El simulador permite experimentar con: Tamaño de caché, Tamaño de bloque, Política de reemplazo (LRU, FIFO, Aleatoria), Política de escritura (Write-through, Write-back), Grado de asociatividad y Secuencia de direcciones de acceso.

## 4. Actividad

Diseñe un simulador que imite las características del simulador CS351 usando las distintas configuraciones (mapeo directo, asociativo, N-way, tamaños de bloque, etc.). El simulador debe permitir configurar: Tamaño de caché, Tamaño de bloque, Política de reemplazo (LRU, FIFO, Aleatoria), Política de escritura (Write-through, Write-back), Grado de asociatividad y Secuencia de direcciones de acceso.

## 5. Pruebas de validación

## 5.1. Configuración de memoria caché:

- 1. Simule diferentes tamaños de la memoria principal (MM) usando el bus de direcciones (Address width). Por ejemplo, pruebe con los valores 4, 6, 8 y 12 bits.

- 2. Mantenga el bus de direcciones (Address width) en 8 bits y configure el tamaño del bloque (Block size) como 4 bytes. Cambie la capacidad de la caché a 8, 16, 32 ,64, 128 y 256 bytes. Para cada caso calcule el número de conjuntos (sets).

- 3. Configure un bus de direcciones en 8 bits, la capacidad de la caché en 16 bytes, el tamaño del bloque en 4. Posteriormente modifique el grado de asociación en 1, 2 y 4 vías (ways). Para cada caso determine el número de conjuntos (sets).

- 4. Identifique a que corresponden los parámetros m, C, K y E visibles en el simulador.

## 5.2. Procesos de lectura y escritura:

- 1. Configure la caché con los valores m=8, C=16, K=4 y E=1. Explique esta configuración.

- 2. Realice un acceso manual de lectura para la dirección 0x08. Explique los campos Tag, Index y Offset (TIO) generados por el simulador. ¿Qué tamaño tiene cada campo?, ¿por qué?

- 3. Manualmente calcule el set y byte al cual mapean las siguientes direcciones 0x08, 0x40, 0xa0, y 0xe8. Lea (Read) estas lecturas en el simulador y valide su respuesta (recuerde mantener la casilla Explain activa.

- 4. Use el botón escribir (Write) para actualizar el valor almacenado en la dirección 0x00 de la memoria principal con el valor (byte=0xFF). Note los campos VDT visibles en la caché y explique el significado de cada sigla. ¿Por qué, el dato no se actualiza en la memoria principal? ¿Cuándo se actualiza? Use el botón vaciar (Flush).


- 5. Habilite el campo (Write Miss:No write-allocate) y repita el ejercicio anterior (recuerde reiniciar el sistema para aplicar el cambio). Note los valores VDT y estado de la memoria principal. ¿Cuál es el efecto de este parametro?

- 6. Habilite el campo (Write Hit: Write through), recuerde reiniciar el sistema. Lea la posición 0x00 de memoria y posteriormente actualice su valor a 0xFF (escriba el byte 0xFF). Note los valores VDT y estado de la memoria principal. ¿Cuál es el efecto de este parámetro?

- 7. Determine las ventajas y desventajas para cada una de las opciones disponibles en Write Hit y Write Miss.

## 5.3. Mapeo directo:

- 1. Configure la caché con los valores m=8, C=16, K=4, E=1, Write Hit: Write back y Write Miss: Write-allocate. Explique esta configuración.

- 2. Complete la tabla para los siguientes accesos de memoria (recuerde mantener la casilla Explain activa).

| Dirección (en decimal) | HIT (Si/No) | Tipo de Miss (Conflict/Compulsory/Invalid) |
| --- | --- | --- |
| 8 |   |   |
| 124 |   |   |
| 48 |   |   |
| 165 |   |   |
| 80 |   |   |
| 226 |   |   |
| 58 |   |   |
| 96 |   |   |
| 212 |   |   |
| 56 |   |   |

- 3. Para el ejercicio anterior calcule el Miss rate. Asuma que el tiempo de acceso a cache es de 5ns y el de memoria principal es de 100 ns y calcule el AMAT.

- 4. Use el botón escribir (Write) para actualizar los valores en la memoria principal de acuerdo con la siguiente tabla (recuerde mantener la casilla Explain activa):

| Dirección (en decimal) | Valor (Byte) | Hit (Si/No) | Tipo de Miss (Conflict/Compulsory/Invalid) |
| --- | --- | --- | --- |
| 19 | 45 |   |   |
| 150 | 62 |   |   |
| 220 | 85 |   |   |
| 101 | 35 |   |   |
| 28 | 9 |   |   |
| 50 | 65 |   |   |
| 249 | 23 |   |   |

- 5. Para el ejercicio anterior calcule el Miss rate. Asuma que el tiempo de acceso a cache es de 5ns y el de memoria principal es de 100 ns y calcule el AMAT.

## 5.4. Mapeo asociativo(N=2):

- 1. Configure la caché con los valores m=8, C=16, K=4, E=2, Write Hit: Write back y Write Miss: Write-allocate. Explique esta configuración.

- 2. Complete la tabla para los siguientes accesos de memoria (recuerde mantener la casilla Explain activa).

| Dirección | HIT | Tipo de Miss |
| --- | --- | --- |


| (en decimal) | (Si/No) | (Conflict/Compulsory/Invalid) |
| --- | --- | --- |
| 8 |   |   |
| 124 |   |   |
| 48 |   |   |
| 165 |   |   |
| 80 |   |   |
| 226 |   |   |
| 58 |   |   |
| 96 |   |   |
| 212 |   |   |
| 56 |   |   |

- 3. Para el ejercicio anterior calcule el Miss rate. Asuma que el tiempo de acceso a cache es de 5ns y el de memoria principal es de 100 ns y calcule el AMAT.

- 4. Use el botón escribir (Write) para actualizar los valores en la memoria principal de acuerdo con la siguiente tabla (recuerde mantener la casilla Explain activa).

| Dirección (en decimal) | Valor (Byte) | Hit (Si/No) | Tipo de Miss (Conflict/Compulsory/Invalid) |
| --- | --- | --- | --- |
| 19 | 45 |   |   |
| 150 | 62 |   |   |
| 220 | 85 |   |   |
| 101 | 35 |   |   |
| 28 | 9 |   |   |
| 50 | 65 |   |   |
| 249 | 23 |   |   |

- 5. Para el ejercicio anterior calcule el Miss rate. Asuma que el tiempo de acceso a cache es de 5ns y el de memoria principal es de 100 ns y calcule el AMAT.

## 5.5. Mapeo asociativo(N=4):

- 1. Configure la caché con los valores m=8, C=16, K=4, E=4, Write Hit: Write back y Write Miss: Write-allocate. Explique esta configuración.

- 2. Complete la tabla para los siguientes accesos de memoria (recuerde mantener la casilla Explain activa).

| Dirección (en decimal) | HIT (Si/No) | Tipo de Miss (Conflict/Compulsory/Invalid) |
| --- | --- | --- |
| 8 |   |   |
| 124 |   |   |
| 48 |   |   |
| 165 |   |   |
| 80 |   |   |
| 226 |   |   |
| 58 |   |   |
| 96 |   |   |
| 212 |   |   |
| 56 |   |   |

- 3. Para el ejercicio anterior calcule el Miss rate. Asuma que el tiempo de acceso a cache es de 5ns y el de memoria principal es de 100 ns y calcule el AMAT.


- 4. Use el botón escribir (Write) para actualizar los valores en la memoria principal de acuerdo con la siguiente tabla (recuerde mantener la casilla Explain activa).

| Dirección (en decimal) | Valor (Byte) | Hit (Si/No) | Tipo de Miss (Conflict/Compulsory/Invalid) |
| --- | --- | --- | --- |
| 19 | 45 |   |   |
| 150 | 62 |   |   |
| 220 | 85 |   |   |
| 101 | 35 |   |   |
| 28 | 9 |   |   |
| 50 | 65 |   |   |
| 249 | 23 |   |   |

- 5. Para el ejercicio anterior calcule el Miss rate. Asuma que el tiempo de acceso a cache es de 5ns y el de memoria principal es de 100 ns y calcule el AMAT.

## 5.6. Políticas de remplazo (N=4):

- 1. Configure la caché con los valores m=8, C=32, K=4, E=4, Write Hit: Write back y Write Miss: Write-allocate.

- 2. Use las siguientes políticas de remplazo. LRU, FIFO y Random

- 3. Complete la tabla para los siguientes accesos de memoria.

| Dirección | HIT |   | # Remplazos |   |
| --- | --- | --- | --- | --- |
| (en decimal) | (Si/No) | LRU | FIFO | Random |
| 8 |   |   |   |   |
| 16 |   |   |   |   |
| 24 |   |   |   |   |
| 32 |   |   |   |   |
| 40 |   |   |   |   |
| 48 |   |   |   |   |
| 56 |   |   |   |   |
| 240 |   |   |   |   |
| 245 |   |   |   |   |
| 125 |   |   |   |   |
| 85 |   |   |   |   |
| 101 |   |   |   |   |
| 21 |   |   |   |   |
| 43 |   |   |   |   |
| 201 |   |   |   |   |
| 95 |   |   |   |   |

- 4. Explique cuál es la mejor política de remplazo y por qué.
