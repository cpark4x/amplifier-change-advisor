---
bundle:
  name: pr-review
  version: 1.0.0
  description: Structured multi-lens PR review workflow

includes:
  - bundle: git+https://github.com/microsoft/amplifier-foundation@main
  - bundle: git+https://github.com/microsoft/amplifier-bundle-recipes@main#subdirectory=behaviors/recipes.yaml
  - bundle: pr-review:behaviors/pr-review
---

# PR Review

@pr-review:context/pr-review-instructions.md
