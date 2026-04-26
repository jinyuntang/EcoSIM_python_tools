---
name: "ecosim-trait-deriver"
description: "Use this skill when working with EcoSIM plant trait description files such as `plant_trait.*.desc` and you need to derive trait parameter sets for a named plant from web-sourced evidence, using the `.desc` file as a template and the `ndlf43` tree block or `gr3s43` grass block as the starting archetype."
---

# EcoSIM Trait Deriver

Use this skill for EcoSIM trait derivation tasks where:

- the user gives a plant name such as `Limber Pine` or `maize`
- a local `plant_trait.*.desc` file is available as the EcoSIM template
- you need to derive a species-specific parameter set from web evidence rather than just copying template values

## What this skill does

- Parses the template `.desc` file into functional-type blocks
- Extracts normalized parameter rows from each template block
- Uses `ndlf43` as the default tree archetype
- Uses `gr3s43` as the default grass archetype
- Guides web-based derivation of trait values for a named plant
- Separates values into:
  - directly supported by sources
  - inferred from close biological evidence
  - left at template defaults when no defensible evidence exists

## Required behavior

When the user asks for traits for a named plant, do not treat the template `.desc` values as species truth.

Instead:

1. Identify whether the target plant should start from the tree archetype `ndlf43` or the grass archetype `gr3s43`.
2. Parse the template file with `scripts/extract_trait_profiles.py`.
3. Browse the web for plant-specific evidence.
4. Map the evidence onto EcoSIM trait codes.
5. Keep a clear distinction between:
   - observed or explicitly reported values
   - values inferred from authoritative descriptions
   - values retained from the template because evidence is missing

## Source priority

Use sources in this order whenever possible:

1. Official taxonomic or species-profile sources for identity and life form.
2. Official crop or forestry databases for ecology, growth form, phenology, and environmental tolerances.
3. Peer-reviewed papers for quantitative physiological or morphological trait values.
4. Reputable extension or botanic-garden sources only as a fallback.

Preferred source types:

- Trees:
  - USDA Forest Service species pages
  - Fire Effects Information System
  - Kew POWO for taxonomy and growth form
- Grasses and crops:
  - FAO ECOCROP
  - USDA sources
  - peer-reviewed crop physiology literature
- Cross-cutting plant traits:
  - TRY database metadata and linked literature
  - peer-reviewed trait papers

## Web workflow

Before doing substantial web-based derivation work, read [references/web_trait_derivation.md](references/web_trait_derivation.md).

For a plant like `Limber Pine` or `maize`:

1. Resolve the accepted scientific name and broad life form.
2. Choose the starting archetype:
   - tree, woody conifer, woody broadleaf, shrub-like tree form -> start from `ndlf43`
   - grass, cereal, herbaceous monocot grass form -> start from `gr3s43`
3. Gather evidence for:
   - taxonomy and life history
   - climate and habitat
   - phenology
   - morphology
   - root traits
   - photosynthetic and nutrient traits
4. Update only the traits that have support.
5. If evidence is qualitative rather than numeric, convert only when the mapping is defensible and explain the inference.
6. If no support is found, keep the template value and mark it as template-retained.

## Mapping guidance

Use the template as a scaffold, not as a source of truth.

- `PLANT CLASS INFORMATION`
  - derive from taxonomy, life form, lifespan, growth pattern, phenology, photoperiod, mycorrhizal status
- `PHOTOSYNTHETIC PROPERTIES`
  - prefer peer-reviewed physiology measurements
  - if unavailable, only adjust high-level expectations such as slower evergreen conifer vs faster crop grass
- `OPTICAL PROPERTIES`
  - usually retain template unless a credible species- or functional-type source provides values
- `PHENOLOGICAL PROPERTIES`
  - use flowering time, leafout, senescence, chilling, and photoperiod evidence
- `MORPHOLOGICAL PROPERTIES`
  - use SLA, leaf shape, seed size, canopy architecture, clumping, standing biomass evidence
- `ROOT CHARACTERISTICS`
  - use rooting depth, woody vs non-woody roots, fine-root structure, mycorrhiza, hydraulic traits if available
- `ROOT UPTAKE PARAMETERS`
  - usually infer from functional type or retain template unless species-specific uptake kinetics are available
- `WATER RELATIONS`
  - use drought tolerance and water-stress physiology when supported
- `ORGAN GROWTH YIELDS` and `ORGAN N AND P CONCENTRATIONS`
  - prefer peer-reviewed measurements; otherwise retain template and label as such

## Command

```bash
python3 skills/ecosim-trait-deriver/scripts/extract_trait_profiles.py /absolute/path/to/plant_trait.1930.desc
```

## Output shape

The parser script returns JSON with:

- `source_file`
- `available_functional_types`
- `tree_profile`
- `grass_profile`

These are the template anchor profiles, not the final derived species profile.

Each anchor profile contains:

- `functional_type_code`
- `plant_name`
- `koppen_climate_info`
- `trait_count`
- `traits_by_code`
- `traits_by_section`

When a trait code appears more than once in a block, `traits_by_code[CODE]` becomes a list in file order instead of dropping later entries.

## Expected final deliverable

When the user asks for derivation for a plant name, produce:

- the chosen anchor block: `ndlf43` or `gr3s43`
- the accepted scientific name
- a derived trait table or JSON object
- per-trait provenance labels:
  - `sourced`
  - `inferred`
  - `template-retained`
- source links for the evidence used

If the user asks for a file output, emit CSV, JSON, or an updated `.desc`-style block.

## Notes

- `ndlf43` is the default tree reference block.
- `gr3s43` is the default grass reference block.
- The `.desc` file is a template.
- If the plant does not clearly fit tree or grass, say so and choose the nearest valid EcoSIM archetype explicitly.
- If either template block is missing, fail clearly instead of guessing.
