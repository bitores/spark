# build-lod

Convert PLY/SPZ/SPLAT files to RAD/SPZ format with LOD (Level of Detail) support.

## Installation

### Download Pre-built Binary

Download the latest release from [GitHub Releases](https://github.com/sparkjs-dev/spark/releases).

| Platform | File |
|----------|------|
| Windows x64 | `build-lod-x86_64-pc-windows-msvc.zip` |
| macOS x64 | `build-lod-x86_64-apple-darwin.tar.gz` |
| macOS ARM | `build-lod-aarch64-apple-darwin.tar.gz` |
| Linux x64 | `build-lod-x86_64-unknown-linux-gnu.tar.gz` |
| Linux ARM | `build-lod-aarch64-unknown-linux-gnu.tar.gz` |

### Build from Source

```bash
# Clone the repository
git clone https://github.com/sparkjs-dev/spark.git
cd spark

# Build
cargo build --release --manifest-path rust/build-lod/Cargo.toml

# The binary will be at rust/build-lod/target/release/build-lod
```

## Usage

```bash
build-lod <input-file> [options]
```

### Input Formats

- `.ply` - Standard PLY format
- `.compressed.ply` - Compressed PLY (SuperSplat format)
- `.spz` - SPZ format
- `.splat` - SPLAT format
- `.ksplat` - KSPLAT format
- `.sog` - SOG format
- `.rad` - RAD format

### Output Formats

By default outputs `.rad` format.

```bash
# Output RAD (default)
build-lod input.ply

# Output RAD with chunked files
build-lod --rad-chunked input.ply

# Output SPZ
build-lod --spz input.ply
```

### Options

| Option | Description |
|--------|-------------|
| `--quick` | Quick LOD method (tiny-lod, base 1.5) |
| `--quality` | Quality LOD method (bhatt-lod, base 1.75) (default) |
| `--tiny-lod[=<base>]` | Use tiny-lod with custom base |
| `--bhatt-lod[=<base>]` | Use bhatt-lod with custom base |
| `--max-sh=<degree>` | Maximum SH degree (0-3, default 3) |
| `--rad` | Output RAD format (default) |
| `--rad-chunked` | Output chunked RAD files |
| `--spz` | Output SPZ format |
| `--spz-chunked` | Output chunked SPZ files |
| `--min-box=<x>,<y>,<z>` | Crop to minimum bounding box |
| `--max-box=<x>,<y>,<z>` | Crop to maximum bounding box |
| `--within-dist=<x>,<y>,<z>,<radius>` | Crop to within radius of point |
| `--skip-validate` | Skip input validation |
| `--inflate` | Inflate scales for normal opacity range |
| `--cluster-sh[=<iterations>]` | Cluster SH coefficients (default 10 iterations) |
| `--csplat` | Use compact splat encoding |
| `--gsplat` | Use higher-precision splat encoding (default) |
| `--unlod` | Remove LOD nodes (reverse operation) |

### Examples

```bash
# Basic conversion
build-lod scene.ply

# Quick conversion with max SH degree 1
build-lod --quick --max-sh=1 scene.ply

# Quality conversion with custom LOD base
build-lod --bhatt-lod=2.0 scene.ply

# Crop to bounding box
build-lod --min-box=0,0,0 --max-box=100,100,100 scene.ply

# Cluster SH coefficients for smaller file size
build-lod --cluster-sh=20 scene.ply
```

## Output

The output file will be named `<input>-lod.rad` or `<input>-lod.spz` depending on the format.

For chunked output, additional `.radc` or `.spz` files will be generated:
- `scene-lod.rad` - Main metadata file
- `scene-lod-0.radc`, `scene-lod-1.radc`, ... - Chunk files

## License

Proprietary - World Labs Technologies