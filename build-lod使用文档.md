# build-lod 使用文档

`build-lod` 是一个用于转换 Gaussian Splatting 文件格式并生成 LOD（Level of Detail，细节层次）树的命令行工具。

## 目录

- [功能概述](#功能概述)
- [下载安装](#下载安装)
- [基本用法](#基本用法)
- [支持的格式](#支持的格式)
- [命令行选项](#命令行选项)
- [使用示例](#使用示例)
- [最佳实践](#最佳实践)

---

## 功能概述

`build-lod` 主要功能：

1. **格式转换** - 在多种 Gaussian Splatting 格式之间转换
2. **LOD 生成** - 为大规模场景生成细节层次结构，支持流式加载
3. **数据优化** - 裁剪、压缩、SH 聚类等优化处理

---

## 下载安装

从 [GitHub Releases](https://github.com/sparkjs-dev/spark/releases) 下载对应平台的可执行文件：

| 平台 | 文件名 |
|------|--------|
| Windows x64 | `build-lod-x86_64-pc-windows-msvc.exe` |
| macOS Intel | `build-lod-x86_64-apple-darwin.dmg` |
| macOS ARM (M1/M2/M3) | `build-lod-aarch64-apple-darwin.dmg` |
| Linux x64 | `build-lod-x86_64-unknown-linux-gnu.deb` |

### 安装步骤

#### Windows

```bash
# 1. 下载 build-lod-x86_64-pc-windows-msvc.exe
# 2. 重命名为 build-lod.exe（可选）
# 3. 直接运行或在命令行中使用
build-lod.exe scene.ply
```

#### macOS

```bash
# 1. 下载 .dmg 文件
# 2. 双击安装或将可执行文件复制到 /usr/local/bin
# 3. 运行
build-lod scene.ply
```

#### Linux

```bash
# 1. 下载 .deb 文件
# 2. 安装
sudo dpkg -i build-lod-x86_64-unknown-linux-gnu.deb

# 3. 运行
build-lod scene.ply
```

---

## 基本用法

```bash
build-lod <input-file> [options]
```

---

## 支持的格式

### 输入格式

| 格式 | 扩展名 | 说明 |
|------|--------|------|
| PLY | `.ply` | 标准 PLY 格式 |
| Compressed PLY | `.compressed.ply` | SuperSplat 压缩格式 |
| SPZ | `.spz` | SPZ 格式 |
| SPLAT | `.splat` | SPLAT 格式 |
| KSPLAT | `.ksplat` | KSPLAT 格式 |
| SOG | `.sog` | SOG 格式 |
| RAD | `.rad` | RAD 格式 |

### 输出格式

| 选项 | 说明 |
|------|------|
| `--rad` | 输出 RAD 格式（默认） |
| `--rad-chunked` | 输出分块 RAD 文件（用于流式加载） |
| `--spz` | 输出 SPZ 格式 |
| `--spz-chunked` | 输出分块 SPZ 文件 |

---

## 命令行选项

### 输出格式选项

| 选项 | 说明 |
|------|------|
| `--rad` | 输出 RAD 格式（默认） |
| `--rad-chunked` | 输出分块 RAD 文件，支持流式加载 |
| `--spz` | 输出 SPZ 格式 |
| `--spz-chunked` | 输出分块 SPZ 文件 |

### LOD 方法选项

| 选项 | 说明 |
|------|------|
| `--quick` | 快速 LOD 方法（tiny-lod，base 1.5） |
| `--quality` | 高质量 LOD 方法（bhatt-lod，base 1.75）**默认** |
| `--tiny-lod[=<base>]` | 使用 tiny-lod 方法，可自定义 base 值 |
| `--bhatt-lod[=<base>]` | 使用 bhatt-lod 方法，可自定义 base 值 |
| `--unlod` | 移除 LOD 结构（反向操作） |

### 数据处理选项

| 选项 | 说明 |
|------|------|
| `--max-sh=<degree>` | 最大 SH（球谐函数）阶数，0-3，默认 3 |
| `--min-box=<x>,<y>,<z>` | 裁剪最小边界坐标 |
| `--max-box=<x>,<y>,<z>` | 裁剪最大边界坐标 |
| `--within-dist=<x>,<y>,<z>,<radius>` | 裁剪到指定点的半径范围内 |
| `--skip-validate` | 跳过输入验证 |
| `--inflate` | 膨胀缩放以获得正常不透明度范围 |

### 编码选项

| 选项 | 说明 |
|------|------|
| `--gsplat` | 使用高精度 splat 编码（默认） |
| `--csplat` | 使用紧凑 splat 编码 |

### SH 聚类选项

| 选项 | 说明 |
|------|------|
| `--cluster-sh[=<iterations>]` | 聚类 SH 系数以减小文件大小，默认 10 次迭代 |
| `--cluster-sh-cpu[=<iterations>]` | 使用 CPU 进行 SH 聚类 |
| `--cluster-sh-f16=<option>` | SH float16 模式：`auto`/`true`/`false` |

---

## 使用示例

### 基础转换

```bash
# PLY 转 RAD（默认）
build-lod scene.ply

# SPLAT 转 RAD
build-lod scene.splat

# SPZ 转 RAD
build-lod scene.spz

# 输出 SPZ 格式
build-lod --spz scene.ply
```

### LOD 质量设置

```bash
# 快速模式（适合预览）
build-lod --quick scene.ply

# 高质量模式（推荐用于最终输出）
build-lod --quality scene.ply

# 自定义 LOD base 值
build-lod --bhatt-lod=2.0 scene.ply
```

### SH 球谐处理

```bash
# 限制最大 SH 阶数为 1（减小文件大小）
build-lod --max-sh=1 scene.ply

# 不保留 SH 数据
build-lod --max-sh=0 scene.ply

# 聚类 SH 系数以减小文件大小
build-lod --cluster-sh=20 scene.ply
```

### 空间裁剪

```bash
# 裁剪到指定边界框
build-lod --min-box=0,0,0 --max-box=100,100,100 scene.ply

# 裁剪到指定点半径范围内
build-lod --within-dist=50,50,50,25 scene.ply
```

### 流式加载输出

```bash
# 输出分块 RAD 文件用于流式加载
build-lod --rad-chunked scene.ply

# 输出文件：
# scene-lod.rad      - 主元数据文件
# scene-lod-0.radc   - 数据块 0
# scene-lod-1.radc   - 数据块 1
# ...
```

### 在 Spark 中加载

```javascript
// 加载预构建的 LOD 文件
const splats = new SplatMesh({ url: "./scene-lod.rad" });
scene.add(splats);

// 启用流式加载（即时显示）
const splats = new SplatMesh({
  url: "./scene-lod.rad",
  paged: true
});
scene.add(splats);
```

### 批量处理

```bash
# 多文件处理
build-lod scene1.ply scene2.splat scene3.spz --quality

# 处理目录下所有文件（Windows PowerShell）
Get-ChildItem *.ply | ForEach-Object { build-lod $_.Name --quality }

# 处理目录下所有文件（Linux/macOS）
for f in *.ply; do build-lod "$f" --quality; done
```

### 反向操作

```bash
# 移除 LOD 结构
build-lod --unlod scene-lod.spz
```

---

## 最佳实践

### 1. 选择合适的 LOD 方法

| 场景 | 推荐方法 |
|------|----------|
| 快速预览 | `--quick` |
| 离线构建 / 流式加载 | `--quality` |
| 超大规模场景 | `--tiny-lod=2.0` |

### 2. 优化文件大小

```bash
# 限制 SH 阶数 + 聚类
build-lod --quality --max-sh=1 --cluster-sh scene.ply
```

### 3. 处理大坐标场景

如果场景坐标值很大（超过 1000 单位），在加载时使用扩展精度：

```javascript
new SplatMesh({ url: "./scene-lod.rad", extSplats: true });
```

### 4. 流式加载最佳配置

```bash
# 构建流式加载文件
build-lod --quality --rad-chunked scene.ply
```

```javascript
// 加载时启用分页
const splats = new SplatMesh({
  url: "./scene-lod.rad",
  paged: true
});
```

### 5. 性能参考

| 操作 | 大致耗时 |
|------|----------|
| 快速模式 | ~0.5 秒 / 百万 splats |
| 高质量模式 | ~2 秒 / 百万 splats |

---

## 输出说明

### 普通输出

输入 `scene.ply` → 输出 `scene-lod.rad`

### 分块输出

输入 `scene.ply` → 输出：
- `scene-lod.rad` - 元数据文件（小）
- `scene-lod-0.radc` - 数据块 0
- `scene-lod-1.radc` - 数据块 1
- ...

---

## 常见问题

### Q: `--quick` 和 `--quality` 有什么区别？

`--quick` 使用 tiny-lod 算法，速度更快但质量略低。`--quality` 使用 bhatt-lod 算法，质量更高但耗时更长。推荐离线构建时使用 `--quality`。

### Q: 如何减小输出文件大小？

1. 使用 `--max-sh=0` 或 `--max-sh=1` 限制 SH 阶数
2. 使用 `--cluster-sh` 聚类 SH 系数
3. 使用 `--csplat` 紧凑编码（会损失精度）

### Q: 输出文件名规则是什么？

输出文件会在原文件名后添加 `-lod` 后缀，例如：
- `scene.ply` → `scene-lod.rad`
- `model.spz` → `model-lod.rad`

### Q: 如何处理无效 splat 数据？

默认会检测无效数据并停止处理。可以使用 `--skip-validate` 跳过验证继续处理。

---

## 相关链接

- [Spark 官方文档](https://sparkjs.dev)
- [GitHub 仓库](https://github.com/sparkjs-dev/spark)
- [LOD 入门指南](./lod-getting-started.md)
