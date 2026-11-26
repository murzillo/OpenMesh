# OpenMesh

> **Status: pre-alpha implementation.** The compiler described by `docs/` is implemented in this tree in Rust.
> Language, CLI, MCP, and output contracts may still change before 0.1.0. See
> [Changelog](docs/reference/changelog.md) and [DEVELOPING.md](DEVELOPING.md).

OpenMesh compiles an OML text file (`*.om`) into a 2D sketch or a 3D solid and exports glTF 2.0 binary (`.glb`),
OBJ, STL, 3MF, SVG, or DXF. A model is one text file: no binary project, no hidden state, no GUI required. The same
source and the same compiler version produce byte-identical output.

## Build from source

```powershell
cargo build --release
.\target\release\openmesh.exe version
.\target\release\openmesh.exe build examples\cube.om
```

The release binary embeds [the OpenMesh icon](assets/icon.png) as a Windows resource. A portable archive can be
produced with `scripts\package.ps1`; it contains the binary, `docs/`, `LICENSE`, `README.md`, and the icon.

## Install

> No release archive is published yet. `scripts\package.ps1` produces the same layout for a development build.
> This block defines the intended install flow for 0.1.0 and becomes valid when the first release is published.

Portable archive, no runtime, no installer:

```powershell
# Windows x64
New-Item -ItemType Directory -Force "$env:LOCALAPPDATA\OpenMesh" | Out-Null
Expand-Archive openmesh-0.1.0-windows-x64.zip -DestinationPath "$env:LOCALAPPDATA\OpenMesh" -Force
& "$env:LOCALAPPDATA\OpenMesh\openmesh.exe" version
```

```bash
# Linux x64
mkdir -p "$HOME/.local/openmesh"
tar -xzf openmesh-0.1.0-linux-x64.tar.gz -C "$HOME/.local/openmesh" --strip-components=1
"$HOME/.local/openmesh/openmesh" version
```

Expected output: `openmesh 0.1.0 (oml 1.0)`. Add the binary directory to `PATH` to use the short
form `openmesh` in the commands below. The archive contains the binary, `docs/`, `LICENSE`, and this file.

## Three commands

```bash
openmesh build model.om -o model.glb    # compile and export
openmesh check model.om --json          # parse and typecheck, no geometry
openmesh mcp --root .                   # MCP server over stdio for LLM agents
```

Minimal model, `model.om`:

```om
// @om 1
@param size = 40mm { min = 10mm, max = 100mm, step = 1mm, desc = "Cube edge" }

export(box([size, size, size]), "cube.glb");
```

## Start here

| Reader | Entry point | Time |
|---|---|---|
| First model | [Install](docs/start/01-install.md) → [Build](docs/start/02-first-model.md) | 10 min |
| Engineer | [OML reference](docs/language/00-reference.md) | 20 min |
| Developer | [CLI](docs/cli/01-usage.md) → [CI](docs/cli/02-build-and-ci.md) | 10 min |
| LLM agent | [Context pack](docs/ai/00-context-pack.md) → [MCP](docs/mcp/03-tools.md) | 5 min |

Full map:

- [Start](docs/start/01-install.md): install, first model, Blender import, concepts and glossary.
- [Preview](docs/start/05-preview.md): live viewport, last-good mesh, quality levels, display modes.
- [Language](docs/language/00-reference.md): syntax, types, primitives, booleans, modifiers, selectors, errors.
- [CLI](docs/cli/01-usage.md) → [Render](docs/cli/04-render.md): commands, flags, exit codes, headless render.
- [MCP](docs/mcp/01-overview.md): server setup, tool schemas, error contract, recipes.
- [AI](docs/ai/00-context-pack.md): context pack, prompting guide, common failures.
- [Recipes](docs/recipes/printable-bracket.md): bracket, laser panel, enclosure, logo extrusion.
- [Reference](docs/reference/architecture.md): grammar, API index, changelog, compiler architecture.
- [llms.txt](docs/llms.txt): flat machine-readable index of every page.

## Capabilities

| Area | Supported |
|---|---|
| 3D primitives | `box`, `sphere`, `cylinder`, `cone`, `torus`, `polyhedron` |
| 2D primitives | `rect`, `circle`, `polygon` (with holes), `path` |
| Booleans | `union(list)`, `difference`, `intersection`, `hull`, `minkowski` |
| 2D to 3D | `extrude` (twist, slices, taper), `revolve`, `sweep`, `offset` |
| Modifiers | `fillet`, `chamfer`, `shell`, `array`, `subdivide` |
| Selectors | `edges` / `faces` with presets (`"vertical"`, `"top"`) and predicates |
| Preview | live viewport, last-good mesh, contact sheet, section, wall thickness |
| Appearance | `Color` per exported solid, written to glTF, OBJ/MTL, 3MF, SVG |
| Parameters | `@param` sliders with ranges and steps, `@preset` named configurations |
| Output | `.glb`, `.obj`, `.stl`, `.3mf`, `.svg`, `.dxf` |

## Not supported

OpenMesh does not sculpt, rig, animate, produce cinematic renders, or simulate. It has no NURBS or class-A surface
modeling, no brush texturing, and no GUI sketching with mouse constraints. `fillet` rounds convex edges and skips
concave or knife edges. Boolean operations can produce a non-manifold result when two solids touch only along an
edge or a face; the compiler reports `E0307` instead of writing a broken mesh. The full list is in
[Limits](docs/language/13-limits.md).

## Text-first rationale

A model that is text can be reviewed as a diff, built in CI, cached by content hash, and generated by a model that
has no filesystem access. OML has no I/O, network, clock, environment variables, or reflection; `import` reads only
`.om` files inside the project root. Running generated code is bounded by the same limits as any other build:
10 s CPU, 512 MiB RAM, 2 million triangles by default.


## Next

- [Install OpenMesh](docs/start/01-install.md)
- [OML reference](docs/language/00-reference.md)
- [AI context pack](docs/ai/00-context-pack.md)
