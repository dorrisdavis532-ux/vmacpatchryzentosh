# Mini vMac for macOS (No OpenGL Required)

!! this was made by AI and wont be updated, forgot to say that only for hackintoshes that has small VRAM !!

Pre-built Mini vMac binaries for macOS, patched to work **without OpenGL** — uses pure Core Graphics rendering instead. Works on Hackintosh and machines where OpenGL drivers are broken.

## Downloads

| Model | ROM Required | Description |
|-------|-------------|-------------|
| `minivmac-128k.app` | `Mac128K.ROM` | Macintosh 128K (1984) |
| `minivmac-ii.app` | `vMac.ROM` | Macintosh II (1987) |
| `minivmac-plus.app` | `vMac.ROM` | Macintosh Plus (1986) |

## Setup

1. Download the appropriate `.app` for the model you want
2. Get the matching ROM file (see below)
3. Rename the ROM to match the expected name and place it **next to the app**:
   - `minivmac-128k.app` → expects `Mac128K.ROM`
   - `minivmac-ii.app` → expects `vMac.ROM`
   - `minivmac-plus.app` → expects `vMac.ROM`
4. Launch the app

## ROM Files

ROM files are copyrighted by Apple and cannot be distributed. You can dump ROM from your own vintage Mac or find them online. Search for "Mini vMac ROM" to find sources.

## What Was Changed

The original Mini vMac uses OpenGL (`glDrawPixels`) for screen rendering, which crashes on macOS with AMD GPUs or Hackintosh setups. These builds:

- Switched from OpenGL to **Core Graphics** (`CGContextDrawImage`) for all rendering
- Removed all OpenGL context creation and GPU driver dependencies
- Uses `CALayer`-backed views instead of OpenGL-backed views
- Built with the native Cocoa backend (`-t mc64`)

Tested on macOS Tahoe (26.x) with AMD Radeon.

## Building From Source

```bash
# Extract source
tar xzf minivmac-36.04.src.tgz
cd minivmac

# Generate build config (default = 128K)
./setup_t -t mc64 > setup.sh
# Or for Mac II: ./setup_t -t mc64 -m II > setup.sh
# Or for Mac Plus: ./setup_t -t mc64 -m Plus > setup.sh

bash setup.sh
make
```

The patch applied to `src/OSGLUCCO.m`:
- Set `#define UseCGContextDrawImage 1`
- Changed `kCGImageAlphaNoneSkipFirst` → `kCGImageAlphaNoneSkipLast`
- Bypassed `GetOpnGLCntxt()` when using CGContext
- Added `[MyNSview setWantsLayer: YES]` for layer-backed rendering
- `drawRect:` now uses `CGContextDrawImage` instead of `glDrawPixels`
- `SDL_UpdateRect` calls `[MyNSview setNeedsDisplay:YES]`

## Credits

- [Mini vMac](https://www.gryphel.com/) by Paul C. Pratt
- Source version: 36.04

## License

Mini vMac is distributed under the MIT License. See source for details.
