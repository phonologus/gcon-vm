# gcon-vm

**Status: ARCHIVED.** Development has moved to [gcon/vm](https://github.com/phonologus/gcon)

The virtual machine for gcon (Go-based Icon), in C. gcon's toolchain, in
Go, translates Icon 9.5 programs to gcon's bytecode; this VM runs that
bytecode. It is the counterpart of iconx to gcon's icont, as
[gcon-graphics](https://github.com/phonologus/gcon-graphics) is gcon's
display server: C builds are kept out of the Go repository.

**Status: running programs.** `gcon-vm prog args...` runs a program gcon
has built (`gcon build`), with the same results as gcon's Go VM on
gcon's program tests and the reference tests; `gcon-vm -dis prog` lists
one. Memory is managed by a precise mark-and-sweep collector. See
[docs/design.md](docs/design.md) for why the VM is in C and how it is built, and `experiments/spike` for the
measurement behind it.

## Building

    ./build.sh

needs only a C compiler (C11, with the computed-goto extension that GCC
and Clang provide), and leaves `bin/gcon-vm`. `$CC` and `$CFLAGS` are
used if set.

## Using it with gcon

    tools/install.sh [gcon-root]

copies `bin/gcon-vm` to the gcon root's `libexec` (by default `../gcon`,
a checkout of gcon being a root), where `gcon run`, and so every program
gcon builds, finds it and hands programs to it. `GCONVM` overrides the
choice (a path to a gcon-vm, or `go` for gcon's own VM); `gcon env`
shows it. gcon-vm can also be run directly on a built program:

    gcon-vm [-progname name] program [argument ...]

## Testing

The tests compare gcon-vm with gcon, so they need Go and gcon's source
(`$GCONSRC`, by default `../gcon` beside this repository); building
gcon-vm needs neither.

    tests/dis.sh tests/values.icn ../icon/tests/general ../icon/ipl/progs

builds each program with gcon and checks that gcon-vm's listing is
identical to gcon's.

    tests/damage.sh tests/values.icn

checks every truncation and every changed byte of a built program:
gcon-vm, built with the address and undefined-behaviour sanitizers,
must report each exactly as gcon does.

    tests/programs.sh

runs gcon's program tests (expected results recorded from iconx).

    tests/vs-go.sh ../icon/tests/general ../icon/ipl/progs

runs each program on gcon's Go VM and on gcon-vm and compares output,
messages and exit status. `tests/bench.sh` times gcon's VM
micro-benchmarks on both.

`src/gen_tables.c` is generated from gcon's sources by
`tools/gentables.sh`: the names of gcon's builtins, and which characters
are printable (for images).

## Licence

MIT; see [LICENSE](LICENSE). Code taken from the reference
implementation of Icon (public domain) is marked where it is used.
