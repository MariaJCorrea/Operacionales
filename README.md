# Cache Simulator

This is a cache simulator implemented for the Operacionales Lab 3.

## Features

- Simulates a cache with configurable parameters:
  - Address width (m) in bits
  - Cache size (C) in bytes
  - Block size (K) in bytes
  - Associativity (E) (number of ways per set)
  - Replacement policy: LRU, FIFO, Random
  - Write policy: Write-through, Write-back
  - Write allocate policy: Write-allocate, No-write-allocate (associated with write miss)
- Simulates main memory (byte-addressable)
- Processes a sequence of memory accesses (reads and writes)
- For each access, outputs:
  - Hit or miss
  - Type of miss (if miss): Compulsory, Conflict
  - For write accesses, indicates if it resulted in a write to memory (depending on write policy)
- Maintains and displays cache state (valid, dirty, tag, data for each line)
- Maintains and displays main memory state (for accessed addresses)
- Computes miss rate and AMAT (Average Memory Access Time) given cache access time and memory access time

## Usage

The simulator is implemented in `cache_simulator.py`. It can be used as a module or run directly for a simple test.

### As a module

```python
from cache_simulator import Cache

# Create a cache instance
cache = Cache(
    address_width=8,        # m: address width in bits
    cache_size=16,          # C: cache size in bytes
    block_size=4,           # K: block size in bytes
    associativity=1,        # E: associativity (number of ways)
    replacement_policy='LRU',
    write_policy='write-back',
    write_allocate=True
)

# Perform an access
# address: byte address (integer)
# is_write: True for write, False for read
# data: byte value to write (0-255), required if is_write is True
hit, miss_type, data_returned, mem_written = cache.access(address, is_write=False)
# For a read: data_returned is the byte read.
# For a write: data_returned is the byte written (same as input).
# mem_written: True if a write to main memory happened.

# Print statistics
cache.print_stats()

# Print cache state
cache.print_cache_state()
```

### Running the built-in test

```bash
python3 cache_simulator.py
```

This will run a simple test with a direct-mapped cache (m=8, C=16, K=4, E=1) and two read accesses to address 0x08.

## Lab Exercises

To run the lab exercises, you can create a Python script that uses the `Cache` class to simulate the required configurations and access sequences.

For example, to simulate the direct mapping exercise (section 5.3):

```python
from cache_simulator import Cache

# Configuration for section 5.3.1: m=8, C=16, K=4, E=1
cache = Cache(address_width=8, cache_size=16, block_size=4, associativity=1,
              replacement_policy='LRU', write_policy='write-back', write_allocate=True)

# The exercise provides a sequence of memory accesses (in decimal):
# 8, 124, 48, 165, 80, 226, 58, 96, 212, 56
addresses = [8, 124, 48, 165, 80, 226, 58, 96, 212, 56]

for addr in addresses:
    hit, miss_type, data, mem_written = cache.access(addr, is_write=False)
    print(f"Address {addr}: Hit={hit}, Miss type={miss_type}")

# After the sequence, compute miss rate and AMAT
cache.print_stats()
```

You can then modify the script to match the other exercises.

## Notes

- The simulator assumes that the cache size, block size, and number of sets are powers of two. This matches the lab exercises.
- The main memory size is 2^m bytes.
- The miss type classification (Compulsory vs Conflict) is based on whether there is an invalid line in the set at the time of the miss. This is a simplification but works for the given lab exercises.
- The simulator does not distinguish "Invalid" misses as a separate category; they are treated as compulsory misses if an invalid line is available, or conflict otherwise. This matches the lab's definitions for the exercises provided.

## License

This code is for educational purposes.