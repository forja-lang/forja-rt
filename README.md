# forja-rt

Stub de ejecución autónomo de **Forja (fa)**.

[![CI](https://github.com/forja-lang/forja-rt/actions/workflows/ci.yml/badge.svg)](https://github.com/forja-lang/forja-rt/actions/workflows/ci.yml)

## Qué es

`forja-rt` es el binario mínimo que se distribuye junto al compilador `forja`.
No interpreta archivos `.fa` por sí solo: su única responsabilidad es llamar a
`forja::selfrun::try_selfrun()`, que ejecuta el bytecode **incrustado** en el
propio ejecutable por el empaquetador de Forja.

```text
forja-rt (binario) → forja::selfrun::try_selfrun() → bytecode embebido
                   ↘ sin bytecode: mensaje de error y exit(1)
```

Se usa para generar ejecutables autocontenidos: el usuario final no necesita
Rust ni el compilador instalado.

## Compilar

```bash
# Runtime sin GUI
cargo build -p forja-rt --release --locked

# Runtime con GUI (forja-gui-rt / xilem)
cargo build -p forja-rt --release --features gui --locked
```

El binario resultante es `target/release/forja-rt`. El workflow de release del
repo padre lo publica como `forja-rt` y `forja-rt-gui`.

## Tests

El crate es un binario de 9 líneas sin lógica propia; no tiene tests unitarios.
El CI compila el crate dentro del workspace Forja (`cargo test -p forja-rt`), lo
que verifica que la integración con `forja::selfrun` sigue compilando.

```bash
cargo test -p forja-rt --locked
```

## Estado

Versión `0.9.5`; es un artefacto de distribución
del repo [forja-lang/forja](https://github.com/forja-lang/forja).

## Licencia

GPL-3.0-or-later. Ver `LICENSE.md` en [forja-lang/forja](https://github.com/forja-lang/forja).
