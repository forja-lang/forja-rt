# forja-rt

Stub de ejecución autónomo de **Forja (fa)**.

[![CI](https://github.com/forja-lang/forja-rt/actions/workflows/ci.yml/badge.svg)](https://github.com/forja-lang/forja-rt/actions/workflows/ci.yml)

## Qué es

`forja-rt` es el binario mínimo que se distribuye junto al compilador `forja`.
No interpreta archivos `.fa` por sí solo: su única responsabilidad es llamar a
`forja::selfrun::try_selfrun()`, que ejecuta el bytecode **incrustado** en el
propio ejecutable por el empaquetador de Forja.

```text
forja-rt (binario) → forja::selfrun::try_selfrun() → bytecode embebido (FBC)
forja-rt-gui       → forja::selfrun::try_selfrun() → fuente Forja embebido (FBG)
                                                      → forja-gui-rt (xilem)
                   ↘ sin payload: mensaje de error y exit(1)
```

El payload de una app GUI es el **fuente Forja**: el runtime gráfico necesita el
AST y lo evalúa `forja-gui-rt` (`gui_nativa::construir_y_ejecutar`). Así una app
GUI se distribuye como `.exe` sin Rust ni Xilem compilados por app.

Se usa para generar ejecutables autocontenidos: el usuario final no necesita
Rust ni el compilador instalado.

## Compilar

```bash
# Runtime sin GUI
cargo build -p forja-rt --release --locked

# Runtime con GUI (forja-gui-rt / xilem) — perfil `rt`: thin LTO, ~3m40 en frío
# (release con LTO fat tarda ~8m y no aporta acá: el peso lo pone wgpu/vello).
cargo build -p forja-rt --profile rt --features gui --locked
```

El binario resultante es `target/release/forja-rt` (o `target/rt/forja-rt` con el
perfil `rt`). El workflow de release del repo padre lo publica como `forja-rt` y
`forja-rt-gui`; renombrá la copia con GUI a `forja-rt-gui` al lado de `forja.exe`
para que `forja compilar` la encuentre.

> **Windows + sccache.** Con `sccache` como `RUSTC_WRAPPER`, compilar
> `forja-gui-rt` falla con «El nombre del archivo o la extensión es demasiado
> largo» (os error 206): la línea de comandos de rustc supera el límite de rutas.
> Compilá con el wrapper desactivado: `set RUSTC_WRAPPER=` (o
> `RUSTC_WRAPPER= cargo build …`).

## Tests

El crate es un binario de 9 líneas sin lógica propia (todo vive en `forja::selfrun`); no tiene tests unitarios.
El CI compila el crate dentro del workspace Forja (`cargo test -p forja-rt`), lo
que verifica que la integración con `forja::selfrun` sigue compilando.

```bash
cargo test -p forja-rt --locked
```

## Estado

Versión `0.10.3`; es un artefacto de distribución
del repo [forja-lang/forja](https://github.com/forja-lang/forja).

## Licencia

GPL-3.0-or-later. Ver `LICENSE.md` en [forja-lang/forja](https://github.com/forja-lang/forja).
