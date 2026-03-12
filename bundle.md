---
bundle:
  name: change-advisor
  version: 2.0.0
  description: Multi-lens decision advisor for evaluating changes to your product

includes:
  - bundle: git+https://github.com/microsoft/amplifier-foundation@main
  - bundle: git+https://github.com/microsoft/amplifier-bundle-recipes@main#subdirectory=behaviors/recipes.yaml
  - bundle: change-advisor:behaviors/change-advisor
---

# Change Advisor

@change-advisor:context/change-advisor-instructions.md
