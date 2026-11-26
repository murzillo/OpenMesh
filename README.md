[README.md](https://github.com/user-attachments/files/35468488/README.md)
# OpenMesh 0.1.0

OpenMesh compiles a text model (`.om`) into a 3D solid or a 2D sketch and exports GLB, OBJ, STL, 3MF, SVG, or DXF.
A model is one text file: no binary project, no hidden state, no GUI required. The same source and compiler version
produce byte-identical output.

This release is the Windows x64 command-line compiler. There is no installer, no runtime, and no administrator rights.

## Install

1. Download `openmesh-0.1.0-windows-x64.zip` and verify it against `SHA256SUMS.txt`.
2. Extract the archive, for example to `%LOCALAPPDATA%\OpenMesh`.
3. Optionally add that folder to `PATH` to use the short command.

```powershell
New-Item -ItemType Directory -Force "$env:LOCALAPPDATA\OpenMesh" | Out-Null
Expand-Archive .\openmesh-0.1.0-windows-x64.zip -DestinationPath "$env:LOCALAPPDATA\OpenMesh" -Force
& "$env:LOCALAPPDATA\OpenMesh\openmesh.exe" version
```

Expected output:

```text
openmesh 0.1.0 (oml 1.0)
```

Keep the `docs/` folder next to `openmesh.exe`. The manual, `openmesh docs`, and the MCP documentation tools read it
from disk.

## First model

`cube.om`:

```om
// @om 1
@param size = 40mm { min = 10mm, max = 100mm, step = 1mm }

export(box([size, size, size]), "cube.glb");
```

```powershell
openmesh build cube.om
openmesh render cube.om -o cube.png
```

`build` writes `cube.glb`; `render` writes a 2x2 contact sheet. Import the GLB into Blender: the file stores metres,
so a 40 mm cube measures 0.04 m, which is the correct size.

## Commands

| Command | What it does |
|---|---|
| `openmesh build model.om` | compile and write the exports declared in the file |
| `openmesh check model.om` | parse and typecheck without geometry |
| `openmesh render model.om -o out.png` | deterministic 2x2 contact sheet |
| `openmesh preview model.om -o out.png` | one shaded view |
| `openmesh params model.om` | print parameters, ranges, and presets |
| `openmesh docs --list` | list the bundled manual |
| `openmesh mcp --root .` | start the MCP server for an AI client |
| `openmesh cache info` | show the build cache |

## Use it from an AI client (MCP)

The executable is also an MCP server over stdio:

```powershell
openmesh mcp --root C:\models
```

The model gets eight tools: search the manual, read a symbol reference, validate, compile, preview, query geometry,
export, and read examples. Configuration for Claude Desktop and OpenCode is in `docs/mcp/02-setup.md`.

## Documentation

`docs/` is the complete manual. `openmesh docs --list` lists every page and
`openmesh docs --print docs/start/02-first-model.md` prints one.

## Limits

- Parametric and technical modeling only: no sculpting, NURBS, rigging, animation, or textures.
- `fillet` is faceted and skips concave edges; `chamfer` works on convex edges.
- A boolean that only touches along an edge is rejected with `E0307`.
- 0.1.0 is pre-1.0: the language and the CLI may change.

## License

MIT. See `LICENSE`.
