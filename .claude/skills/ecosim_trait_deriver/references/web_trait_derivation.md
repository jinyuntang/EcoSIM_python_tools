# Web Trait Derivation

Use this reference when the task is to derive EcoSIM parameters for a named plant from web evidence.

## Decision sequence

1. Resolve the accepted scientific name.
2. Decide whether the plant maps best to the tree anchor `ndlf43` or the grass anchor `gr3s43`.
3. Extract the anchor traits from the template `.desc`.
4. Search for plant-specific evidence.
5. Update the anchor values only where evidence supports a change.

## Recommended source stack

### Identity and growth form

- Kew POWO
- USDA PLANTS or USDA Forest Service

Use these to determine:

- accepted scientific name
- family
- life form
- perennial vs annual
- woody vs herbaceous
- broad climate or habitat context

### Trees and forestry species

- USDA Forest Service Silvics pages
- USDA Fire Effects Information System
- peer-reviewed forestry ecology papers

Useful for:

- height and growth form
- drought tolerance
- shade tolerance
- regeneration strategy
- root habit
- phenology
- habitat and climate range

### Grasses and crops

- FAO ECOCROP
- USDA crop resources
- peer-reviewed crop physiology papers

Useful for:

- life span
- habit
- photoperiod
- growing cycle
- climate requirements
- crop physiology and nutrient traits

## Trait mapping heuristics

### Safe direct mappings

These can often be mapped directly from source descriptions:

- `ISTYP`
- `IDTYP`
- `IWTYP`
- `IPTYP`
- `MY`
- `ZTYPI`
- `WDLF`
- `GRMX`
- `GRDM`
- `WTSTDI`

### Usually inference-heavy

These should be changed only with stronger support:

- `VCMX`
- `VOMX`
- `ETMX`
- `RUBP`
- `UPMXZH`
- `UPMXZO`
- `UPMXPO`
- `RCS`
- `RSMX`

### Often template-retained

Unless high-quality quantitative data are found, these often remain close to the anchor:

- `OPTICAL PROPERTIES`
- many `ROOT UPTAKE PARAMETERS`
- many `ORGAN GROWTH YIELDS`

## Output expectations

For each changed trait, record:

- trait code
- derived value
- rationale
- evidence source
- provenance label

For unchanged traits, prefer:

- keep the anchor value
- mark it `template-retained`

## Examples

### Limber Pine

- likely anchor: `ndlf43`
- evidence to look for:
  - evergreen conifer
  - long-lived, slow-growing tree
  - drought and cold tolerance
  - deep roots / woody roots
  - ectomycorrhizal association

### Maize

- likely anchor: `gr3s43`
- evidence to look for:
  - annual C4 grass
  - crop cycle and photoperiod response
  - high nutrient demand
  - shallow to intermediate fibrous root system
  - crop physiology measurements from agronomy literature
