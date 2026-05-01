# Claude-md snippet voor projectrepo

Wanneer een gebruiker `/figma-to-code init-claude-md` aanroept, toon je het volgende
markdown-blok plus instructies. **Schrijf zelf niets naar CLAUDE.md.** De gebruiker
plakt het zelf.

---

## Wat te tonen aan de gebruiker

Een korte introductie:

> Hier is het markdown-blok dat je in `CLAUDE.md` van je projectrepo (root) kunt plakken.
> Daarna triggert de figma-to-code skill automatisch bij elke chat in dit project —
> zonder dat je `/figma-to-code` hoeft te typen.
>
> **Plaatsing:**
> - Als `CLAUDE.md` nog niet bestaat in projectrepo-root, maak hem aan met dit blok als inhoud
> - Als `CLAUDE.md` al bestaat, voeg dit blok toe aan het einde

Daarna het blok in een markdown code-fence:

````markdown
## Figma-to-code werkwijze

Dit project gebruikt de figma-to-code skill voor mapping tussen Figma-designs en code.
Bij elk werk aan componenten, tokens, of Figma-mapping: gebruik de figma-to-code skill.

**Werkwijze in het kort:**
- Code is source of truth, Figma is intent
- Drie documenten in `docs/` houden de mapping: `tokens.md`, `components.md`, `components/<naam>.md`
- Lezen voor schrijven, geen improvisatie bij gaten, organisms eerst bij selectie
- Drift wordt kort gemarkeerd, niet uitvoerig gerapporteerd

**Volledige werkwijze:** `~/.claude/skills/figma-to-code/SKILL.md`

**Slash-commando's:**
- `/figma-to-code map <component>` — documenteer een component
- `/figma-to-code setup` — maak `docs/`-structuur aan (alleen eerste keer)
````

## Wat de gebruiker daarna doet

1. Kopieert het blok
2. Opent `CLAUDE.md` in projectrepo-root (of maakt hem aan)
3. Plakt het blok aan het einde
4. Commit naar git zodat het team-breed werkt
5. Bij volgende chat in dit project triggert de skill automatisch

## Wat je NIET doet

- Niet zelf naar `CLAUDE.md` schrijven in de projectrepo
- Niet `git add` of `git commit` uitvoeren
- Geen aanname doen over of `CLAUDE.md` al bestaat — laat de gebruiker dat zien
