# build-lod

Convert Gaussian Splatting files between various formats with LOD (Level of Detail) support.

## Supported Format Conversion Matrix

| Input Format | → | Output Format | Supported |
|--------------|---|---------------|-----------|
| `.ply` | → | `.rad` | ✅ |
| `.ply` | → | `.spz` | ✅ |
| `.compressed.ply` | → | `.rad` | ✅ |
| `.spz` | → | `.rad` | ✅ |
| `.splat` | → | `.rad` | ✅ |
| `.splat` | → | `.spz` | ✅ |
| `.ksplat` | → | `.rad` | ✅ |
| `.sog` | → | `.rad` | ✅ |
| `.rad` | → | `.rad` (with LOD) | ✅ |
| `.rad` | → | `.spz` | ✅ |

## Installation

### Download Pre-built Binary

Download the latest release from [GitHub Releases](https://github.com/sparkjs-dev/spark/releases).

| Platform | File |
|----------|------|
| Windows x64 | `build-lod-x86_64-pc-windows-msvc.exe` |
| macOS x64 (Intel) | `build-lod-x86_64-apple-darwin.dmg` |
| macOS ARM (M1/M2/M3) | `build-lod-aarch64-apple-darwin.dmg` |
| Linux x64 | `build-lod-x86_64-unknown-linux-gnu.deb` |

### Build from Source

```bash
# Clone the repository
git clone https://github.com/sparkjs-dev/spark.git
cd spark

# Build
cd rust
cargo build --release -p build-lod --no-default-features

# The binary will be at rust/target/release/build-lod
```

## Usage

```bash
build-lod <input-file> [options]
```

### Input Formats

| Format | Description |
|--------|-------------|
| `.ply` | Standard PLY format |
| `.compressed.ply` | Compressed PLY (SuperSplat format) |
| `.spz` | SPZ format |
| `.splat` | SPLAT format |
| `.ksplat` | KSPLAT format |
| `.sog` | SOG format |
| `.rad` | RAD format |

### Output Formats

| Option | Description |
|--------|-------------|
| `--rad` | Output RAD format (default) |
| `--rad-chunked` | Output chunked RAD files (for streaming) |
| `--spz` | Output SPZ format |
| `--spz-chunked` | Output chunked SPZ files |

```bash
# PLY to RAD (default)
build-lod scene.ply

# SPLAT to RAD
build-lod scene.splat

# SPZ to RAD
build-lod scene.spz

# SPZ to SPZ (with LOD processing)
build-lod --spz scene.spz

# SPLAT to SPZ
build-lod --spz scene.splat

# Output chunked for streaming
build-lod --rad-chunked scene.ply
```

### LOD Options

| Option | Description |
|--------|-------------|
| `--quick` | Quick LOD method (tiny-lod, base 1.5) |
| `--quality` | Quality LOD method (bhatt-lod, base 1.75) (default) |
| `--tiny-lod[=<base>]` | Use tiny-lod with custom base |
| `--bhatt-lod[=<base>]` | Use bhatt-lod with custom base |
| `--max-sh=<degree>` | Maximum SH degree (0-3, default 3) |
| `--unlod` | Remove LOD nodes (reverse operation) |

### Processing Options

| Option | Description |
|--------|-------------|
| `--min-box=<x>,<y>,<z>` | Crop to minimum bounding box |
| `--max-box=<x>,<y>,<z>` | Crop to maximum bounding box |
| `--within-dist=<x>,<y>,<z>,<radius>` | Crop to within radius of point |
| `--skip-validate` | Skip input validation |
| `--inflate` | Inflate scales for normal opacity range |
| `--cluster-sh[=<iterations>]` | Cluster SH coefficients (default 10 iterations) |
| `--csplat` | Use compact splat encoding |
| `--gsplat` | Use higher-precision splat encoding (default) |

### Examples

```bash
# Basic PLY to RAD conversion
build-lod scene.ply

# SPLAT to RAD conversion
build-lod scene.splat

# SPZ to RAD conversion  
build-lod scene.spz

# Quick conversion with max SH degree 1
build-lod --quick --max-sh=1 scene.ply

# Quality conversion with custom LOD base
build-lod --bhatt-lod=2.0 scene.splat

# Crop to bounding box
build-lod --min-box=0,0,0 --max-box=100,100,100 scene.spz

# Cluster SH coefficients for smaller file size
build-lod --cluster-sh=20 scene.ply

# Convert SPZ to SPZ with LOD
build-lod --spz --quality scene.spz

# Un-LOD (remove LOD structure)
build-lod --unlod scene-lod.spz
```

## Output

The output file will be named `<input>-lod.rad` or `<input>-lod.spz` depending on the format.

For chunked output, additional `.radc` or `.spz` files will be generated:
- `scene-lod.rad` - Main metadata file
- `scene-lod-0.radc`, `scene-lod-1.radc`, ... - Chunk files

## License

Proprietary - World Labs Technologies