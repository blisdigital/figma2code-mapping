# figma2code — repo-context voor Claude

Wanneer Claude in deze repo werkt, gelden de volgende meta-regels (los van wat in `SKILL.md` staat — dat zijn regels voor het *toepassen* van de skill in een project; deze zijn voor het *bewerken* van de skill zelf).

## Context

Deze repo is de **source of truth** voor de figma-to-code skill. De skill wordt door ontwikkelaars geïnstalleerd via een symlink:

```
~/.claude/skills/figma-to-code → ~/Github/figma2code
```

Elke wijziging in `SKILL.md` of in `templates/` wordt na `git pull` direct actief in elk project dat de symlink gebruikt. **Wijzigingen propageren breed** — er is geen "test in één repo eerst".

## Edit-regels

1. **Branch + PR verplicht.** Direct push naar `main` wordt door hooks geblokkeerd. Werk altijd op een feature-branch met beschrijvende naam (`skill-<wat>`, bijvoorbeeld `skill-asset-handling-validation-boundary`).
2. **Eén type wijziging per PR.** Splits feature-additions, leesbaarheid-restructuur, en template-aanpassingen in afzonderlijke PRs. Mengen maakt review en rollback moeilijk.
3. **Lessons-learned format strict.** Max 5 regels per entry, exact velden: `Situatie`, `Wat werkte (of niet)`, `Voorstel`. Datum + type (correctie | bevestiging) in de header. Geen vrije tekst eromheen.
4. **Versienummer in frontmatter ophogen** bij elke significante wijziging in `SKILL.md`. Patch (2.0 → 2.1) voor verfijningen, minor (2.x → 3.0) bij gedragswijziging die bestaande mappings raakt.
5. **Templates niet schrijven naar projects.** Wijzigingen in `templates/` raken alléén nieuwe projecten via `/figma-to-code setup`. Bestaande mapping-docs in projecten blijven onaangeraakt — bewust, om migratie-pijn te vermijden.

## Skill vs project mapping

- `SKILL.md` = werkwijze + regels voor het *toepassen* van de skill in een project (in elk project geladen)
- `CLAUDE.md` = edit-regels voor *deze repo* (alleen geladen wanneer Claude in deze repo werkt)
- `templates/` = startpunt voor projecten die de skill installeren
- `README.md` = developer-facing setup-gids op GitHub

Bij twijfel of iets in `SKILL.md` of `CLAUDE.md` hoort: **gaat het over het maken van een mapping in een project? → SKILL.md. Gaat het over deze repo onderhouden? → CLAUDE.md.**

## Lessons-learned schrijven

Bij elke significante les (zowel correctie als bevestiging) één entry onderaan `SKILL.md § Lessons learned`:

```
[LESSON — YYYY-MM-DD] [type: correctie | bevestiging]
Situatie: <wat gebeurde, 1 regel>
Wat werkte (of niet): <observatie, 1-2 regels>
Voorstel: <regel aanpassen of houden, 1 regel>
```

Niet langer. Niet vager. Bij overschrijding: splitsen in twee entries of de les is niet scherp genoeg geformuleerd.

## Reference

- Symlink wordt aangemaakt door eindgebruiker (zie `README.md`); bij debug-sessies kun je `ls -la ~/.claude/skills/figma-to-code` gebruiken om te verifiëren dat de symlink bestaat.
- Veranderingen testen: `git pull` in je eigen `~/Github/figma2code/`, dan in een test-project Claude triggeren met `/figma-to-code map <component>` op een nieuw component.
