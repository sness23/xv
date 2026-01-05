# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

XV is a classic X Window System image viewer and manipulation tool, originally written by John Bradley. The codebase is ~82K lines of C99 code with a modular architecture supporting 20+ image formats.

## Build Commands

```bash
# Standard build (release mode, installs to tmp_cmake/install/)
./build/build

# Debug build with AddressSanitizer and UndefinedBehaviorSanitizer
./build/build -d

# Clean build
./build/build -c

# Strict mode (treat warnings as errors)
./build/build -s

# Use specific compiler
./build/build -p gcc
./build/build -p clang

# Fast build without install step
./build/build -f

# Custom directories
./build/build -b /path/to/build -i /path/to/install
```

The build script wraps CMake. Direct CMake usage:
```bash
cmake -H. -Btmp_cmake -DCMAKE_INSTALL_PREFIX=$INSTALL_DIR
cmake --build tmp_cmake
cmake --build tmp_cmake --target install
```

## CMake Options

| Option | Default | Description |
|--------|---------|-------------|
| XV_ENABLE_JPEG | ON | JPEG support (requires libjpeg) |
| XV_ENABLE_EXIF | ON | EXIF orientation tags (requires libexif) |
| XV_ENABLE_JP2K | ON | JPEG-2000 support (requires JasPer) |
| XV_ENABLE_PNG | ON | PNG support (requires libpng) |
| XV_ENABLE_TIFF | ON | TIFF support (requires libtiff) |
| XV_ENABLE_WEBP | ON | WebP support (requires libwebp) |
| XV_ENABLE_G3 | ON | G3 fax format |
| XV_ENABLE_PDS | ON | PDS format |
| XV_ENABLE_XRANDR | ON | X11 RandR extension |
| XV_STRICT | OFF | Treat warnings as errors |

## Architecture

### Core Modules

- **src/xv.c** - Main entry point, X11 initialization, argument parsing. `main()` at line 158
- **src/xvevent.c** - Central event loop (`EventLoop()`), window management, input handling
- **src/xvimage.c** - Image manipulation (crop, resize, rotate, dither, smooth, pad)
- **src/xvbrowse.c** - Visual file browser/schnauzer, thumbnail generation

### Image Format Modules

Each format follows the pattern `xv<format>.c` with a `Load<Format>File()` function:
- xvjpeg.c, xvpng.c, xvtiff.c, xvwebp.c, xvjp2k.c, xvgif.c, xvbmp.c, etc.
- Format detection uses magic bytes or file extensions
- Inclusion controlled by CMake options

### UI Modules

- **xvbutt.c** - Button widgets
- **xvctrl.c** - Control window (gamma, brightness, colors)
- **xvdial.c** - Dial widgets
- **xvpopup.c** - Popup menus and dialogs
- **xvdir.c** - Directory navigation

### Key Headers

- **src/xv.h** - Master header with all declarations and platform-specific defines
- **src/config.h** - Compile-time feature configuration
- **src/conf.h.in** - CMake configuration template (generates conf.h)

## Dependencies

**Required:**
- X11 libraries (libx11-dev, libxt-dev)
- CMake 3.12+
- C compiler (GCC or Clang)

**Optional (for image formats):**
- libjpeg, libpng, libtiff, libwebp, libjasper, libexif, libxrandr

## CI/CD

GitHub Actions runs on push:
- Platforms: Ubuntu (latest), macOS (latest)
- Compilers: GCC, Clang
- Workflow: `.github/workflows/ci.yml`

Install dependencies before building:
```bash
./build/github/prebuild
```
