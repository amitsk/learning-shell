# Build Systems: Make and Ninja

`make` is the build file you can read. `ninja` is the one a generator writes when the project gets large. A recipe is indented with a tab.

**About an evening. Open this when a course says `make`.** Done when `make -n` prints the commands and `make` builds the example.

[← Two-hour path](../README.md#if-you-are-new) | [Modern tools, 15 minutes](modern_tools.md)

---

## What are Build Systems?

A build system is a to-do list the computer agrees to re-run. You write "compile these, then link those, then maybe run tests." Tomorrow you type `make` instead of retyping `gcc`.

What you get for the trouble:

- It tracks which files depend on which
- It only rebuilds what changed (the whole point)
- It remembers the compile flags you would otherwise forget
- It can run tests, linters, and "please copy this into `/usr/local/bin`" as named targets

Typing `gcc` by hand works once. A Makefile works the next morning.

---

## Installing Make

`build-essential` is GCC and friends in one apt line. `make --version` is how you know it worked.

### Ubuntu/Debian

```sh
# Install build-essential package (includes make, gcc, and other build tools)
sudo apt update
sudo apt install build-essential

# Or install just make (a separate package)
sudo apt install make

# Verify installation
make --version
```

`*** missing separator` means those leading spaces were supposed to be a tab. Run `make -n` (dry run) before `make`.

### Other Systems

```sh
# Fedora and current RHEL-based systems
sudo dnf install make
# `yum` may still be available as a compatibility command on older systems.

# macOS (with Homebrew; the GNU executable may be named `gmake`)
brew install make

# Arch Linux
sudo pacman -S make
```

---

## Understanding Make

Make looks for a file named `Makefile` (or `makefile`) in the current directory. Each rule is a target, the files it needs, and the shell commands that produce it.

### Basic Makefile Syntax

```makefile
target: dependencies
	command
	command
```

**Important**: Recipe lines must start with a real **TAB**, not spaces. This has ended more friendships than the editor war it came from. Markdown linters will complain about the tabs in the examples below. Make will complain if you "fix" them.

### Simple Example

```makefile
# Basic Makefile
hello: hello.c
	gcc -o hello hello.c

clean:
	rm -f hello
```

Usage:

```sh
make hello    # Builds the hello target
make clean    # Runs the clean target
make          # Builds the first target by default
```

---

## C Programming with Make

This looks like a lot. It is the same `gcc` you would type, written down, with variables so you type the flags once. `$@` is the target, `$<` is the first dependency — Make's idea of pronouns.

### Project Structure

```text
project/
├── Makefile
├── src/
│   ├── main.c
│   ├── utils.c
│   └── utils.h
└── build/
```

### Advanced C Makefile

```makefile
# Compiler and flags
CC = gcc
CFLAGS = -Wall -Wextra -std=c99 -O2 -MMD -MP
LDFLAGS = 
SRCDIR = src
BUILDDIR = build

# Source files
SOURCES = $(wildcard $(SRCDIR)/*.c)
OBJECTS = $(SOURCES:$(SRCDIR)/%.c=$(BUILDDIR)/%.o)
DEPS = $(OBJECTS:.o=.d)
TARGET = $(BUILDDIR)/myprogram

# Default target
all: $(TARGET)

# Build the main program
$(TARGET): $(OBJECTS) | $(BUILDDIR)
	$(CC) $(OBJECTS) -o $@ $(LDFLAGS)

# Compile source files to object files
# -MMD -MP writes a .d file listing the headers each .o needs
$(BUILDDIR)/%.o: $(SRCDIR)/%.c | $(BUILDDIR)
	$(CC) $(CFLAGS) -c $< -o $@

# Create build directory
$(BUILDDIR):
	mkdir -p $(BUILDDIR)

# Compile target
compile: $(OBJECTS)

# Link target (already handled by $(TARGET))
link: $(TARGET)

# Lint with static analysis
lint:
	cppcheck --enable=all $(SRCDIR)/*.c

# Run the program
run: $(TARGET)
	./$(TARGET)

# Clean build artifacts
clean:
	rm -rf $(BUILDDIR)

# Install (example)
install: $(TARGET)
	cp $(TARGET) /usr/local/bin/

# Phony targets (not files)
.PHONY: all compile link lint run clean install

# Pull in the header dependencies the compiler wrote
-include $(DEPS)
```

`-MMD -MP` is the part beginners leave out. Without it, editing `utils.h` does not rebuild `main.o`, and you spend twenty minutes debugging a binary that is older than your code. With it, the compiler writes `build/main.d` on every compile, and `-include` reads those files back as Make dependencies. Touch a header, and everything that includes it rebuilds. That is the whole trick.

### Usage Examples

```sh
# Build everything
make

# Just compile (create object files)
make compile

# Run linting
make lint

# Clean and rebuild
make clean && make

# Install the program
sudo make install
```

---

## Rust Programming with Make

[Cargo](https://doc.rust-lang.org/cargo/) already *is* a build system. Wrapping it in a tiny `Makefile` is just muscle memory — or a CI entry point so the C folks and the Rust folks both type `make test`:

```makefile
CARGO = cargo

.PHONY: build release test lint fmt clean

build:
	$(CARGO) build

release:
	$(CARGO) build --release

test:
	$(CARGO) test

lint:
	$(CARGO) clippy -- -D warnings

fmt:
	$(CARGO) fmt

clean:
	$(CARGO) clean
```

Need cross-compilation, benchmarks, or audits? Call Cargo directly (see [The Cargo Book](https://doc.rust-lang.org/cargo/commands/index.html)) — a Makefile is a front door, not a clone of `cargo --help`.

---

## Ninja Build System

You do not write Ninja files for fun. CMake (or Meson) writes them. You run `ninja` and go get coffee, except you cannot, because it finished.

### Key Differences: Make vs Ninja

| Feature | Make | Ninja |
|---------|------|-------|
| **Speed** | Slower for large projects | Very fast |
| **Syntax** | Complex, flexible | Simple, restricted |
| **Usage** | Hand-written Makefiles | Generated build files |
| **Features** | Rich built-in functions | Minimal feature set |
| **Parallelism** | Good | Excellent |
| **Best for** | Small-medium projects, custom builds | Large projects, generated builds |

### Installing Ninja

```sh
# Ubuntu/Debian
sudo apt install ninja-build

# CentOS/RHEL/Fedora
sudo dnf install ninja-build

# macOS
brew install ninja

# Arch Linux
sudo pacman -S ninja
```

### Basic Ninja Syntax

A `build.ninja` file looks generated because it usually is. Rules first, then `build` lines that use them.

```ninja
# build.ninja file
rule compile
  command = gcc -c $in -o $out
  description = Compiling $in

rule link
  command = gcc $in -o $out
  description = Linking $out

build main.o: compile main.c
build utils.o: compile utils.c
build program: link main.o utils.o
```

### Using Ninja

`ninja` with no arguments reads `build.ninja` and builds everything. `-t` is the toolbox: clean, graph, and other tricks.

```sh
# Build (reads build.ninja by default)
ninja

# Build specific target
ninja program

# Clean
ninja -t clean

# Show build graph
ninja -t graph | dot -Tpng > build.png
```

### Integration with CMake

This is the usual path: CMake writes the Ninja file, Ninja does the compile. You edit `CMakeLists.txt`, not `build.ninja`.

```sh
# Configure: write the build files into build/, not into the source tree
cmake -G Ninja -S . -B build

# Build (CMake drives Ninja for you)
cmake --build build

# Or step into the build directory and call Ninja directly
cd build
ninja
```

`-S .` is "source tree is here." `-B build` is "put the generated files there." That second flag is the out-of-source build the Best Practices section asks for, and it means `rm -rf build` is a clean reset instead of an archaeology project. `cmake -G Ninja .` also works, and it litters your source directory with `CMakeCache.txt` and `CMakeFiles/`. Do the version with `-B`.

---

## When to Use Which (and Four Rules That Save Time)

If a human reads and edits the build file on a bad morning, use **Make**. If [CMake](https://cmake.org/documentation/) or [Meson](https://mesonbuild.com/) generates it and the project is large, let it output **Ninja** (many teams use Make locally and Ninja in CI).

Four habits worth keeping:

- **Mark non-file targets `.PHONY`.** Without it, a file named `clean` turns `make clean` into a silent no-op — a free prank from the filesystem.
- **Build out-of-source (`-B build/`).** Keeping generated files in `build/` means `rm -rf build` is a clean reset instead of an excavation.
- **Track header deps (`-MMD -MP`)** and use automatic variables (`$@` target, `$<` first prerequisite, `$^` all prerequisites) instead of repeating filenames.
- **Use parallel builds** (`make -j$(nproc)`; Ninja parallelizes automatically) and dry-run unfamiliar targets with `make -n`.

```makefile
# Handy self-documenting target
help:
	@echo "Targets: all | clean | test | lint"
```

---

## References

### Make Documentation

- [GNU Make Manual](https://www.gnu.org/software/make/manual/make.html)
- [Make Tutorial](https://makefiletutorial.com/)
- [Advanced Make Techniques](https://www.gnu.org/software/make/manual/html_node/Advanced.html)

### Ninja Documentation

- [Ninja Build System](https://ninja-build.org/)
- [Ninja Manual](https://ninja-build.org/manual.html)
- [Ninja Performance Guide](https://ninja-build.org/manual.html#_performance)

### Related Tools

- [CMake Documentation](https://cmake.org/documentation/)
- [Meson Build System](https://mesonbuild.com/)
- [Bazel Build System](https://bazel.build/)

---

## Next Steps

After `make` stops feeling like a spell:

- **Package managers** (`apt`, `dnf`, `cargo`, `npm`)
- **Continuous Integration** (GitHub Actions, Jenkins)
- **Container builds** (Docker, Podman)
- **Cross-compilation** techniques

[← Two-hour path](../README.md#if-you-are-new) | [Modern tools table →](modern_tools.md)
