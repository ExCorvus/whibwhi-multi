# whibwhi-multi

A fork of whibwhi with the premise of running the same PC shell for other console games as well. The parent
repository stays the instance for one specific game.

A shell that hosts an original console-game executable (PE64) on a PC. Original work by Rena
([ExCorvus](https://github.com/ExCorvus)); written with AI assistance.

The hosted executable is the source; the runtime here is our own code. You need your own copy of whatever you
host — this repository contains no game data, no keys and no executable, and nothing here decrypts or bypasses
any protection.

## Files

```
shell/                       our runtime (C++17, MSVC)
  image.hpp / image.cpp      PE64 image mapper — parses a hosted exe and maps it flat
  imports.hpp / imports.cpp  import table reader — the libraries and functions the image imports
  loader.hpp / loader.cpp    image loader — places the image in memory, connects its imports, gives it
                             thread-local storage, sets page protections, registers its exception table;
                             does not start it
  thread_block.h / .cpp      whiwhi_tls.dll — the per-thread storage block the loader lends to the image
  host_libraries.hpp / .cpp  connects an import to the same-named library on the PC, when there is one
  xmem.h / xmem.cpp          xmem.dll — the console's memory library, answered on a PC: 64 KiB physical
                             pages, ranges they are mapped into, committed memory
  console.hpp / console.cpp  where an import of a console image is connected: the shell's own library, or
                             the PC's
  hosting.hpp / hosting.cpp  flat hosting model — one GPU backing over the image's flat VA space,
                             uniform heap_base shift, real pages bound on demand
  shader_isa.hpp / .cpp      reads a console (RDNA2-class) shader's instruction words
  shader_translate.hpp/.cpp  on-demand console-shader -> SPIR-V translator, in-memory cache
  spirv.hpp / spirv.cpp      our own small SPIR-V writer
  tools/                     list_imports / describe_image / connect_imports — inspect an image without starting it
  tests/                     unit tests (no game data)
  CMakeLists.txt             build definition (shell + helper libraries + tools + tests, CTest)
  build.cmd                  build + test helper
scripts/
  check_no_game_data.py      no-game-data gate (run before every commit)
docs/                        notes inherited from the parent instance (exe maps, shader plan, build-and-run)
FINDINGS.md                  living findings log
AGENTS.md                    guide for coding agents
CONTRIBUTING.md              safety and code conventions
ATTRIBUTION.md / NOTICE.md   authorship and credit
LICENSE                      GPLv3-or-later (our own code)
```

## Build

```
shell\build.cmd
```

Builds the shell, the helper libraries and the tools, and runs the tests via CTest. No game data or GPU needed.

## Status

Today the shell places a hosted console executable in memory and connects most of its imports (558 of 587 on the
reference image so far); the console-only libraries are being provided one by one. It does not start a game yet.

## License

Copyright (C) 2026 Rena (ExCorvus). Licensed under the GNU General Public License, version 3 or later — see
[LICENSE](LICENSE) and [NOTICE.md](NOTICE.md).
