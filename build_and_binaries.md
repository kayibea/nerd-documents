# Build & Binary Tools Cheatsheet

---

## 1. make — Build Automation

### Basic Usage
```bash
make            # build default target
make all        # build 'all' target
make clean      # clean build artifacts
make -j4        # parallel build with 4 threads
```

### Common Pattern
```bash
./configure     # optional: generate Makefile
make            # compile
sudo make install  # install binaries
```

---

## 2. cmake — Modern Build System

### Basic Usage
```bash
mkdir build && cd build
cmake ..          # generate Makefiles or Ninja build
make              # compile
sudo make install # install
```

### Configure Options
```bash
cmake -DCMAKE_BUILD_TYPE=Release ..
cmake -DCMAKE_INSTALL_PREFIX=/opt/mypkg ..
```
- Can generate for different generators: `-G Ninja`
- Works cross-platform

---

## 3. pkg-config — Compiler/Linker Flags

### Basic Usage
```bash
pkg-config --cflags gtk+-3.0   # compiler flags
pkg-config --libs gtk+-3.0     # linker flags
```

### Compile Example
```bash
gcc myprog.c $(pkg-config --cflags --libs gtk+-3.0) -o myprog
```

---

## 4. ldd / readelf / objdump — Inspect Binaries

### ldd — Shared Library Dependencies
```bash
ldd /usr/bin/ls
```
- Lists dynamic libraries required by a binary
- WARNING: don't run on untrusted binaries (it executes code!)

### readelf — ELF Header & Symbols
```bash
readelf -h /usr/bin/ls     # ELF header
readelf -s /usr/bin/ls     # symbols
readelf -d /usr/bin/ls     # dynamic section
```

### objdump — Disassemble / Inspect
```bash
objdump -d /usr/bin/ls      # disassemble
objdump -x /usr/bin/ls      # extended info
```
- Useful for debugging, reverse engineering, or learning

---

## 5. patchelf — Modify ELF Binaries

### Change Interpreter (Dynamic Loader)
```bash
patchelf --set-interpreter /lib64/ld-linux-x86-64.so.2 ./mybinary
```

### Change rpath (Library Search Path)
```bash
patchelf --set-rpath /opt/mylibs ./mybinary
```

### View ELF Info
```bash
patchelf --print-interpreter ./mybinary
patchelf --print-rpath ./mybinary
```

- Critical when deploying binaries in non-standard environments or fixing broken libraries.

---

## Recommended Workflow

1. **Compile source code** → `make` or `cmake`
2. **Resolve dependencies** → `pkg-config`
3. **Inspect binaries** → `ldd`, `readelf`, `objdump`
4. **Fix deployment issues** → `patchelf`

---

## References / Docs
```bash
man make
man cmake
man pkg-config
man ldd
man readelf
man objdump
man patchelf
```

