# Lint Chronicle

---

## The Extraction — 3 March 2026

Lint was born from a reckoning inside Carson.

Carson 2.12.0 had added MegaLinter integration — embedding lint policy directly into the governance runtime. It felt natural: if Carson manages `.github/` files, why not manage lint configs too? Four versions later, the answer was obvious: because lint is not governance. Lint is opinion.

What counts as a lint violation in a Rails app is irrelevant to a Python data pipeline. RuboCop's style cops enforce conventions that only make sense in the context of a specific team's coding philosophy. Biome's formatting rules reflect a particular stance on indentation, quotes, and line width. These are not universal truths. They are choices.

Carson should not make those choices. Carson carries envelopes. It does not write the letters inside them.

So the lint configs were extracted. On the morning of 3 March 2026, eight commits across seventy-two minutes created a standalone repository: the central source of truth for lint policy across every project.

## The Consolidation

The first commit seeded RuboCop, ERB Lint, and placeholder configs. Then came a rapid simplification:

ESLint was added and immediately removed. Stylelint was added and immediately removed. HTMLHint was added and immediately removed. Each one required Node.js as a dependency — a runtime that most of the projects did not otherwise need.

**Biome** replaced all three. One tool. No Node dependency. JavaScript, TypeScript, CSS, HTML — all covered by a single binary with a single config file. The consolidation took three commits and thirty minutes.

**Ruff** covered Python. Another single-binary tool, another single config file.

The final stack: four tools, four config files, zero unnecessary dependencies.

- `.rubocop.yml` — Ruby, with `rubocop-performance`. Tab indentation. Spaces inside brackets and braces. Double quotes. Generous metrics thresholds. Many style cops disabled in favour of project conventions.
- `.erb-lint.yml` — ERB templates, delegating to RuboCop for inline Ruby.
- `biome.json` — JavaScript, TypeScript, CSS, HTML. Tab indentation. Recommended rules. No Node ecosystem required.
- `ruff.toml` — Python. Tab indentation. Import sorting. Standard error and warning classes.

## The Philosophy in the Config

Every disabled cop tells a story. Every threshold tells a story. The RuboCop config is not a default — it is a document of conviction.

`Layout/IndentationStyle: tabs` — tabs, not spaces. This is a project-wide rule that overrides the Ruby community's convention. It applies to every language, every file, every project. The only exception is YAML, where tabs are a syntax error.

`Style/Documentation: Enabled: false` — do not require class-level doc comments. Code should be self-documenting through good naming and clear structure. Forced documentation produces noise, not clarity.

`Style/FrozenStringLiteralComment: Enabled: false` — do not require the frozen string literal magic comment. Ruby 4.0 handles this differently; the pragma is legacy.

`Metrics/MethodLength: Enabled: false` — do not gate CI on method size. A method should be as long as it needs to be. Arbitrary line counts force artificial extraction that harms readability.

`Metrics/AbcSize: Max: 70` — generous, but not infinite. Complex methods are allowed when the domain demands it, but there is a ceiling.

## The Distribution

Lint does not enforce anything by itself. It is a library, not a runtime. The configs sit in this repository as the source of truth. Carson's `template.canonical` mechanism reads them and distributes them to every governed repository through the template pipeline.

When a lint rule changes here, `carson refresh --all` propagates the change everywhere. No manual copying. No drift. One source, many destinations.

Carson carries the envelope. Lint writes the letter. The separation is clean.
