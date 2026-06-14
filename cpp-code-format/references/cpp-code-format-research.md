# C++ Code Format Research

This reference serves the `cpp-code-format` skill.

Within the scope of this skill and its related documents, C++20 is the baseline for language and style guidance (`/std:c++20`, extensions OFF). Code shared with DeformSim must also compile as C++17. This statement does not by itself update the C++ version baseline of the workspace as a whole.

Rule sources, in order of weight:
1. **ShapeReconstruction's own dominant conventions** (surveyed 2026-06-12 across all 54 files, 8,874 lines) — the canonical form is the majority form already in the code; this skill codifies, it does not import a foreign preset.
2. **Intel oneAPI engineering practice** (oneDNN, oneMath/oneMKL-interfaces, oneTBB — the user-designated exemplar): process discipline and the rules where the repo had no established form.
3. **C++ Core Guidelines (CG), Google C++ Style Guide (G), LLVM Coding Standards (L)** — cited per rule; disagreements recorded in the register at the end.

---

## Language Baseline Rationale

1. **C++20, pinned, extensions OFF.** On the only targeted toolchain (MSVC VS2022) C++20 is fully implemented under the stable, ABI-committed `/std:c++20` switch (every core-language row "supported" in Microsoft's conformance table, last DRs at 17.14). ShapeReconstruction already builds at this pin.

2. **C++23 is not eligible yet.** Roughly 10 core papers are unimplemented in VS2022; several landed only in MSVC 14.50 (VS 2026); there is no stable `/std:c++23` switch — only `/std:c++23preview`, which Microsoft warns is not ABI-stable across releases. Half of production codebases still forbid C++23 (2025 ISO C++ Developer Survey: 50.6% not allowed).

3. **The oneAPI exemplar's discipline, applied.** Intel pins each library's floor at the *oldest* standard that delivers what it needs (C++11 for oneTBB/oneDNN, C++17 where SYCL requires it), pins extensions OFF where verified (oneTBB), and lets consumers raise the standard. Applied here: C++20 is the oldest standard that is both complete on MSVC and already in use; do not chase C++23, do not drop to C++17 (no cross-toolchain SYCL interop is planned). DeformSim (links MKL's classic API, which imposes no standard requirement) keeps its own C++17 pin.

4. **Dependencies impose only floors.** CGAL 6.1 requires "C++17 or later" and supports VS2022 17.14; yaml-cpp and TetGen impose no documented standard requirement relevant here. C++20 is compatible headroom, not risk.

5. **Modules: out of baseline.** CMake supports named modules since 3.28, but header units are unsupported, `import std` is experimental, and no dependency ships module interfaces. Revisit after a stable `/std:c++23` and non-experimental `import std`.

---

## Line Length

1. Soft limit **100 characters** (the repo's established habit; matches oneMath's `ColumnLimit: 100`; G/L prescribe 80, recorded as a documented local deviation).

2. Allowed overflows, sparingly: `#include` lines, string literals that must stay greppable as one unit (error/test messages), URLs in comments.

3. Do not wrap by breaking a quoted message string mid-sentence if that destroys grep-ability; prefer restructuring the line (shorter context prefix, hoist a variable).

---

## Indentation

1. **4 spaces. Never tabs. Never mix.** (Repo: 0 tabs in 8,874 lines; oneDNN/oneMath: `IndentWidth: 4`, `UseTab: Never`.)

2. `switch`/`case` labels at the **same indent** as the `switch` keyword; the statements under a label one level deeper.

```cpp
switch (mode) {
case SurfaceMode::DirectSurface:
    return build_direct(parsed);
case SurfaceMode::AdaptiveRemesh:
    return build_adaptive(parsed, options);
}
```

3. Continuation lines: either one parameter per line at +4, or aligned under the opening parenthesis. Both exist in the repo; pick whichever the surrounding file already uses, and do not convert between them in unrelated edits.

4. Namespace bodies are **not** indented (repo-universal; `NamespaceIndentation: None` in every oneAPI config).

---

## Braces

1. **Attached (K&R) everywhere — functions, control flow, structs, enums, namespaces.** `) {` on the same line as the declaration tail. (Repo-dominant; oneDNN fully attached; G/L attach. CG NL.17's function-brace-on-own-line variant is rejected here — see disagreement register.)

```cpp
Vec3 vsub(const Vec3& a, const Vec3& b) {
    return {a.x - b.x, a.y - b.y, a.z - b.z};
}
```

2. Multi-line bodies always take braces. A brace-less single statement is acceptable only when the entire statement fits on the `if`'s own line or the immediately following line and there is no `else`:

```cpp
if (faces.empty()) {
    return {};
}
// also acceptable for short guards:
if (!on_top) continue;
```

3. Never a bare `;` as a loop body; use `{}` or `continue;` (G).

4. Prefer early exits; do not write `else` after a branch that ends in `return`/`break`/`continue`/`throw` (L).

---

## Spaces and Blank Lines

1. Space after control keywords (`if (`, `for (`, `while (`, `switch (`); none after function names. No spaces inside parentheses. Spaces around binary operators and `=`. Range-for colon spaced on both sides (`for (const Vec3& v : vertices)`). (G; CG NL.15.)

2. Two spaces before an end-of-line comment: `box.valid = true;  // empty input stays invalid` (G; repo namespace closers already follow it).

3. **No decorative vertical alignment** of `=`, fields, or trailing comments across lines — it is a maintenance burden (oneAPI configs: `AlignConsecutiveAssignments`/`AlignConsecutiveDeclarations` and `AlignTrailingComments` false across oneDNN/oneMath/oneTBB; oneTBB's examples config does enable `AlignConsecutiveMacros`, the lone exception). Do not add new alignment; do not destroy existing alignment (e.g. `src/backends/cgal/cgal_mesh.cpp:28-33`) in edits that are not otherwise touching those lines.

4. At most one consecutive blank line (`MaxEmptyLinesToKeep: 1` in all oneAPI configs). No blank line at the start or end of a block (G). One blank line between include groups (G) and between function definitions (house rule).

---

## Naming

Canonical table (repo-dominant forms; per-guide attribution in the register):

| Identifier kind | Canonical form | Example |
|---|---|---|
| Types (class/struct/enum/alias) | `PascalCase` | `ParsedMvr`, `MeshBoundingBox`, `CgalKernel` |
| Enumerators | `PascalCase` | `SurfaceMode::DirectSurface` |
| Functions (free and member) | `snake_case` | `parse_mvr()`, `boundary_faces_from_tets()` |
| Variables, parameters | `snake_case` | `total_top_area`, `n_vertices` |
| Data members | `snake_case`, no `m_` prefix, no trailing `_` | `bounding_box` |
| Compile-time constants | `kCamelCase` | `kEpsilon`, `kMaxSdfGridResolution` |
| Out-parameters | `snake_case` + `_out` suffix | `points_out`, `mesh_out` |
| Namespaces | short, all-lowercase | `mvrmesh`, `detail` |
| Macros (avoid; last resort) | `ALL_CAPS` with project prefix | `MVRMESH_FIXTURE_DIR` |
| Test functions | `test_` + snake_case scenario | `test_boundary_faces_single_tet()` |
| Files | `snake_case.h` / `snake_case.cpp` | `mesh_postprocess.cpp` |

Rules:

1. Names are descriptive English; no Hungarian notation, no type encoding (G; CG NL.5). Single letters only for counters/indices (`i`, `j`, `k`), coordinates in math kernels (`x`, `y`, `z`, `a`, `b`, `c` for vertices), and conventional exceptions.

2. Never use `__` anywhere in a name, nor `_` followed by an uppercase letter — reserved for the implementation (CG NL.8).

3. `ALL_CAPS` is for macros **only** (CG NL.9; oneAPI-universal). A `const`/`constexpr` value is `kCamelCase`, not `ALL_CAPS` — `KIB`-style constants are the noncanonical outlier.

4. Name length proportional to scope (CG NL.7): a 3-line lambda may use `v`; a header-level constant gets a full descriptive name.

Note: the `PascalCase` types + `snake_case` functions hybrid follows the repo (and Google's types / CG's functions). Pure-snake_case types (the oneMath/oneDNN/stdlib scheme) would require a mass rename and is rejected for this codebase.

---

## Headers and Includes

1. **`#pragma once`**, first line of every header. (Repo-universal. Documented deviation: G/CG SF.8/L all prescribe classic include guards; `#pragma once` is non-standard but uniformly supported on the single targeted toolchain, and mixing schemes is worse than either.)

2. **Headers are self-contained**: a header compiles on its own and includes everything it needs (G; L; CG SF.11).

3. **Include order** in a `.cpp` (own-header-first is repo-universal; group order is the canonical fix for the repo's one unstable point):
   1. Own header (`#include "mvrmesh/core/topology.h"` first in `topology.cpp`) — makes a non-self-contained header break its own build first (G; CG SF.5).
   2. C++ standard library, `<angle>` form, alphabetized.
   3. Third-party libraries (`<CGAL/...>`, `<yaml-cpp/...>`, `"tetgen.h"`).
   4. Project headers, `"quoted"` full-path form (`"mvrmesh/core/geometry.h"`), alphabetized.
   One blank line between groups.

4. Quoted form for project headers and vendored third-party headers without an install tree (`"tetgen.h"`); angle brackets for standard library and installed external libraries (G; CG SF.12).

5. **Include what you use**: if a file refers to a symbol, it includes the header that provides it; never rely on transitive includes (G; CG SF.10).

6. Avoid forward declarations; include the header instead (G; deviation from L recorded in the register). Never forward-declare anything from `std::`.

7. Never `using namespace` at global scope in a header (CG SF.7); avoid `using namespace` directives entirely in implementation files (G). A scoped namespace alias is fine (`namespace PMP = CGAL::Polygon_mesh_processing;`).

---

## Namespaces and File-Local Linkage

1. All library code lives in a single top-level `namespace mvrmesh { ... }`; test helpers in `mvrmesh::test` using C++17 nested syntax (`namespace mvrmesh::test {`).

2. File-local helpers go in an **anonymous namespace placed inside `mvrmesh`**, never file-scope `static` (repo: 0 file-scope statics; CG SF.22; deviation from L's prefer-static recorded in the register). The anonymous namespace sits near the top of the `.cpp`, after includes.

3. Internal seams intentionally exposed for tests live in `namespace detail` inside `mvrmesh`.

4. Close every namespace with the comment idiom, two spaces before `//`:

```cpp
}  // namespace mvrmesh
```

---

## Comments

1. `//` for all prose comments; `/* */` only as inline argument annotations at call sites: `run_case(/*flip=*/true)` (repo; G and L both use the `/*name=*/` idiom).

2. Comments state **why**, not what: rationale, constraints the code cannot show, units, invariants (CG NL.1/NL.2). Do not restate the code, do not narrate edits, do not address a reviewer.

3. Comment density: meaningful decisions and non-obvious geometry/numerics deserve a comment; trivial accessors and obvious control flow do not (G).

4. Doc comments above declarations use plain `//` (repo-dominant). Doxygen `///` is not the house style; do not introduce it into new headers (the lone `///` header, `mesh_postprocess.h`, is grandfathered).

5. TODO format: `// TODO: <identifier or owner> - <action>.` with the string TODO in caps (G).

6. Section dividers, where a long file genuinely needs them, use the existing dash-padded idiom: `// -- helpers ----------------------------------------------------------`.

7. All comments in English; no emoji (workspace rule).

---

## Error Handling

The codebase is exception-based (CG E.2 throw-by-default; Google's exception ban is a self-described legacy artifact and does not bind here — see register).

1. **Exception types**: `std::invalid_argument` for argument/configuration validation failures; `std::runtime_error` for runtime, I/O, and data failures (repo-consistent split). Throw by value, catch by `const` reference (CG E.15).

2. Build messages with `std::ostringstream`, then throw. A message embeds: the context (what was being done), the offending value, and the expectation or remediation hint.

```cpp
std::ostringstream oss;
oss << context << ": face index out of range: " << idx
    << ", n_vertices=" << vertices.size();
throw std::runtime_error(oss.str());
```

3. **Message wording** (L's grep-ability rules; repo-consistent): start lowercase, no trailing period, embed actual-vs-expected values (`size is 10 when it should be 20`), keep a stable greppable prefix.

4. Dedicated `[[noreturn]]` thrower helpers for repeated failure shapes (`throw_field_error`, `throw_usage_error`).

5. CLI `main()` is a thin `try { run(); } catch (const std::exception& e) { std::cerr << "[error] " << e.what() << "\n"; return 1; }` wrapper.

6. At library boundaries that must not propagate (e.g. the TetGen pre-flight), convert exceptions — including foreign throw styles like TetGen's `catch (int code)` — into a result struct with `success=false` and a `diagnostic` string.

7. Programming invariants (not input errors) use `assert` with a message: `assert(i < count && "node index out of range");` (L). Never assert on user input or file content.

8. New functions whose return value encodes success/failure should be `[[nodiscard]]` (CG E.27 note). Do not retrofit the attribute across existing code outside the task's scope.

9. Destructors, deallocation, and `swap` never fail (CG E.16). Constructors that cannot establish the invariant throw (CG E.5).

---

## Logging and Runtime Messages

1. iostream only; no `printf`-family logging (`std::snprintf` is acceptable for pure number formatting into a buffer).

2. Tag prefixes and stream routing (canonical — normalizes the one `[warn]`-to-`cout` outlier):

| Tag | Stream | Use |
|---|---|---|
| `[info]`, `[ok]` | `std::cout` | progress, success |
| `[warn]` | `std::cerr` | recoverable anomaly, fallback taken |
| `[error]`, `[fail]` | `std::cerr` | failure |

3. Payload format: `[tag] context: key=value key2=value2` — machine-greppable, values inline.

```cpp
std::cerr << "[warn] config: unknown key ignored: name=" << key
          << " file=" << path << "\n";
```

4. End messages with `"\n"`, never `std::endl` (no gratuitous flushes).

5. Messages must precisely match the real condition — never guess a cause the code did not establish; report what failed and with which values, not an interpretation.

---

## Modern C++ Idiom (C++20 Baseline)

1. **`auto`**: conservative. Use it for factory-call results whose type is obvious from the call, iterators, property maps, and lambdas; prefer explicit types in core numeric code (G clarity rule; CG ES.11; L). Beware unnecessary copies: `auto&` / `const auto&` / `auto*` where applicable.

2. **const placement: west const** (`const Vec3& a`), repo-universal; CG NL.26 enforces conventional notation. Never `Vec3 const&` in this codebase.

3. **Pointer/reference attach to the type**: `char* argv0`, `const std::string& path` — and therefore one declaration per line for pointer/reference variables (G).

4. `nullptr`, never `0` or `NULL` (CG ES.47). `'\0'` for the char.

5. **`enum class`** for all new enumerations (CG Enum.3); enumerators `PascalCase`, never `ALL_CAPS` (CG Enum.5).

6. In-class default member initializers for struct/class fields (`double x = 0.0;`) — repo-universal.

7. `constexpr` (or `inline constexpr` in headers) `kCamelCase` constants instead of macros or unnamed enums (CG Enum.6; G). Digit separators for large literals: `1'000'000`.

8. `std::move` locals into aggregate returns (`result.vertices = std::move(vertices);`); never `return std::move(local)` — it blocks copy elision (CG F.48).

9. Liberal `const` on locals that do not change — established repo habit; keep it.

10. `std::optional` for genuinely-absent values and state tracking, not as an error channel (errors throw; boundary structs carry diagnostics).

11. `std::string_view`/`std::span` are currently absent from the codebase. Introduce them only with a concrete reason (hot path, avoiding copies at an API boundary), with lifetime discipline (CG F.24: non-owning, caller must outlive the call), not as a blanket modernization.

12. C++20 features in good standing: range-for (universal), abbreviated function templates (`void require_throws(auto fn, ...)` — already in tests), structured bindings where they clarify, designated initializers for option structs, concepts where a template constraint reads better as one. Do not use modules (out of baseline), do not adopt `std::format` while the codebase logs through iostream — consistency first.

---

## Known ShapeReconstruction Inconsistencies (normalize on touch)

When a task edits a region containing one of these, normalize to the canonical form as part of the edit. Do not launch a dedicated reformat commit for them.

| # | Inconsistency | Canonical form | Noncanonical sites |
|---|---|---|---|
| 1 | Constant naming 3-way | `kCamelCase` | `k_zero_tolerance` (`src/core/reconstruction.cpp:40-44`), `KIB/MIB/GIB` (`src/pressure/pressure_metrics.cpp:85-87`) |
| 2 | Function brace on own line | attached `) {` | `src/pressure/pressure_metrics.cpp:26,60,113` |
| 3 | `[warn]` stream | `std::cerr` | `src/core/io.cpp:259-268` (goes to cout) |
| 4 | Out-param naming | `_out` suffix | `out_vertices`, `out_faces` (`src/core/io.cpp:303-306`) |
| 5 | Include-block order | own / std / third-party / project | `src/backends/cgal/cgal_mesh.cpp:1-22` (project before std), `src/core/reconstruction.cpp:20-26` (CGAL after project), `src/pressure/pressure_evaluator.cpp:15-17` (`tetgen.h` inside project block) |
| 6 | Anonymous-namespace placement | inside `namespace mvrmesh` | `src/cli/cli_common.cpp:14-118`, `src/config/pressure_config.cpp:9-22` (global scope) |
| 7 | Doc-comment marker | plain `//` | `include/mvrmesh/core/mesh_postprocess.h` (`///`, grandfathered — do not spread) |
| 8 | Top-of-file path comment | omit in new files | present in config/cli/pressure files only |
| 9 | Test main style | either is acceptable; within one file, stay consistent | sequential (`smoke_tests.cpp`) vs table-driven `run_tests` (`config_tests.cpp`) |

Line numbers reflect the 2026-06-12 survey; re-verify before editing.

---

## The Intel oneAPI Exemplar

What "Intel oneAPI practice" concretely is (verified from oneDNN, oneMath, oneTBB repos):

1. **snake_case identifiers everywhere, `ALL_CAPS` macros only** — Intel libraries deliberately read like the standard library. Adopted for functions/variables/namespaces; types stay `PascalCase` per the repo (documented divergence).
2. **4-space indent, never tabs, attached braces, no namespace indentation** — adopted verbatim.
3. **No decorative alignment** (`AlignConsecutiveAssignments`/`Declarations` and `AlignTrailingComments` false in every config, `MaxEmptyLinesToKeep: 1`) — adopted.
4. **A checked-in, fully explicit format config with no `BasedOnStyle`** — the config is the spec, immune to tool-default drift. Noted for the future tooling task; this skill carries the rules prose-first.
5. **Mechanical enforcement with a pinned formatter version in CI** (oneDNN: clang-format-18 diff on changed files; oneMath: pre-commit-pinned v19.1.0) — out of this skill's boundary; the pattern to copy when tooling rollout is requested.
6. **Conservative standard floor (extensions OFF verified in oneTBB)** — applied as the C++20 pin rationale above.
7. Where Intel splits internally, this skill chose: **100 columns** and **left/type-attached pointers** (the oneMath side), because they match the repo's existing habit; oneDNN's 80-col/right-pointer side is rejected.

---

## Cross-Guide Disagreement Register

Where the major guides conflict, the canonical choice and its basis:

| Topic | G | L | CG | oneAPI | **Canonical here** |
|---|---|---|---|---|---|
| Function naming | `PascalCase` | `camelBack` | snake_case | snake_case | **snake_case** (repo + CG + oneAPI) |
| Type naming | `PascalCase` | `PascalCase` | snake_case | snake_case (`_t`) | **PascalCase** (repo) |
| Constants | `kCamelCase` | — | snake_case | snake_case | **kCamelCase** (repo majority) |
| Line length | 80 | 80 | none | 80 or 100 | **100 soft** (repo + oneMath) |
| Function braces | attach | attach | own line (NL.17) | attach | **attach** |
| Header guards | named guards | named guards | named guards (SF.8) | guards | **`#pragma once`** (repo-universal, single toolchain) |
| Exceptions | banned (legacy rationale) | assert-centric | throw by default (E.2) | — | **exceptions** (repo + CG) |
| System-include position | after own header | last | — | varies | **after own header** (G order) |
| Forward declarations | avoid | accept | — | — | **avoid** (G) |
| File-local linkage | unnamed namespace | `static` | unnamed namespace (SF.22) | — | **unnamed namespace inside `mvrmesh`** |
| const position | west (encouraged) | — | west (NL.26) | — | **west** |
| Pointer alignment | `T* p` | `T *p` | `T* p` (NL.18) | both | **`T* p`** |

---

## Sources

- ShapeReconstruction style survey, 2026-06-12 (54 files; file:line evidence recorded in the skill-creation session)
- Microsoft C/C++ language conformance table — https://learn.microsoft.com/en-us/cpp/overview/visual-cpp-language-conformance
- MSVC `/std` switch documentation — https://learn.microsoft.com/en-us/cpp/build/reference/std-specify-language-standard-version?view=msvc-170
- CGAL 6.1 third-party requirements — https://doc.cgal.org/6.1/Manual/thirdparty.html
- SYCL 2020 specification (C++17 core language) — https://registry.khronos.org/SYCL/specs/sycl-2020/html/sycl-2020.html
- Intel oneMKL DPC++ developer reference (C++17 requirement) — https://www.intel.com/content/www/us/en/docs/onemkl/developer-reference-dpcpp/2023-0/overview.html
- 2025 ISO C++ Developer Survey results — https://isocpp.org/files/papers/CppDevSurvey-2025-summary.pdf
- cmake-cxxmodules(7) — https://cmake.org/cmake/help/latest/manual/cmake-cxxmodules.7.html
- Google C++ Style Guide — https://google.github.io/styleguide/cppguide.html
- C++ Core Guidelines — https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines
- LLVM Coding Standards — https://llvm.org/docs/CodingStandards.html
- clang-format style options — https://clang.llvm.org/docs/ClangFormatStyleOptions.html
- clang-tidy checks — https://clang.llvm.org/extra/clang-tidy/checks/list.html
- oneDNN `.clang-format` / `.clang-tidy` / CODING_STANDARDS.md — https://github.com/uxlfoundation/oneDNN
- oneMath (formerly oneMKL interfaces) `_clang-format` / CONTRIBUTING.md — https://github.com/uxlfoundation/oneMath
- oneTBB repository and CONTRIBUTING.md — https://github.com/uxlfoundation/oneTBB
