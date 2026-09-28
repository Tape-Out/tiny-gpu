# tiny-gpu

Adam Majmudar's [tiny-gpu](https://github.com/adam-maj/tiny-gpu), a minimal GPU written to show how GPUs work, taken as a black box.

![maturity](https://img.shields.io/badge/maturity-planned-lightgrey) ![license](https://img.shields.io/badge/license-MIT%20OR%20Apache--2.0%20OR%20MulanPSL--2.0-blue) ![upstream](https://img.shields.io/badge/upstream-no%20licence%20file-lightgrey)

Part of the [Tape-Out](https://github.com/Tape-Out) IP library, wired up by [`xirang`](https://github.com/Tape-Out/xirang). One submodule, not modified: `third_party/tiny-gpu`.

## What this repository adds

The top is `gpu`: a dispatcher, a device control register, memory controllers for program and data memory, and `numCores` cores of `threadsPerBlock` threads each. Those two are the knobs.

The sources are SystemVerilog that IEEE 1800 does not quite accept: `controller.sv` assigns an integer to an unpacked array, which slang refuses and sv2v and Icarus let through. Upstream builds the same way, so the `sv2v` setup task converts the sources to `build/gpu.v` as upstream's `make compile` does, and the manifest declares that file. Its ports are the converted ones, which are also what upstream's tests drive.

```console
$ ran run tiny-gpu sv2v
$ ran test tiny-gpu
```

## Testing

Upstream's two cocotb tests, matrix addition and matrix multiplication, run as tasks at every point of the matrix, with the two knobs passed to Icarus as parameters. Both compare every result. cocotb does not fail the simulator's exit code when a test fails, so each task checks the summary line instead. They need `sv2v` and `cocotb` 1.9 (the tests use the 1.x `Clock(..., units=)` form).

This is not the GPU fabricated on Tiny Tapeout: tiny-gpu itself was never taped out, and only its ALU went to TT06 as [`adam-maj/tt06`](https://github.com/adam-maj/tt06).

## Limits

Memory widths and channel counts stay at upstream's defaults (8-bit data over 4 channels, 16-bit instructions over 1), because upstream's tests hard-code them. There is no cache (upstream lists it as work in progress), and the threads of a block are assumed to reconverge after every instruction, so a kernel whose threads branch apart runs wrong.

## License

This repository: 任选其一 [MIT](LICENSE-MIT) · [Apache 2.0](LICENSE-APACHE) · [木兰宽松许可证 第2版](LICENSE-MULAN).

`third_party/tiny-gpu` has no licence file. It is referenced as a submodule and neither changed nor copied here.
