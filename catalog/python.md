# Python CI recipe notes

## Default posture

Python projects should keep their existing package manager and test framework.

Do not migrate pip → uv, unittest → pytest, or add Ruff/mypy merely to make CI look uniform.

## Preferred composition

```text
GitHub official runtime setup
→ project dependency install CLI
→ project formatter/lint/typecheck if already part of the engineering baseline
→ project unit tests
→ build if the project produces a buildable artifact
→ repo-specific checks remain local
```

## Capability decisions

| Capability | Default |
| --- | --- |
| Python runtime | `actions/setup-python` |
| pip install | `python -m pip ...` |
| pip cache | setup-python built-in cache when useful |
| uv project | upstream `setup-uv` + project uv CLI |
| Ruff | CLI-first; Action optional |
| unittest | CLI-first |
| pytest | CLI-first |
| mypy | CLI-first when project already has a typing baseline |
| build | project/PyPA CLI when the project actually builds a package/product |
| integration/E2E | add from real complexity/risk, not by default |

## Reusable workflow status

DEFER.

Do not create a generic Python reusable workflow until at least a second real Python consumer demonstrates meaningful stable duplication beyond a few setup lines.
