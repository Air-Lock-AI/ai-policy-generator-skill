# ai-policy-generator

A Claude Code / Cowork skill that generates a polished three-page **AI Use Policy** for any company. Aligned to **EU AI Act Articles 4, 5 and 50**, with optional **Annex III** high-risk addendum for regulated sectors.

The skill researches the company, asks 12 short questions across four batches, drafts the full policy in chat for the user to validate, and only then writes the final files.

## What you get

When the skill runs to completion, it writes two files to the user's folder:

- `ai-use-policy-{slug}-v{version}.html` — print-ready three-page A4 policy (cover, the rules, the duties)
- `ai-use-policy-{slug}-inputs.json` — sidecar with every answer, used to re-run the skill with minimal questions next time

The user opens the HTML, hits the floating **Save as PDF** button (or Cmd/Ctrl+P), prints with Margins: None, and gets a clean signed PDF.

## Quick start

### Option A — Native Claude (drag-and-drop, one click)

Grab the latest `ai-policy-generator.skill` from the [Releases page](https://github.com/Air-Lock-AI/ai-policy-generator-skill/releases) and drop it into Cowork or Claude Code. Then trigger with: *"create an AI policy"*, *"draft an AI use policy"*, or *"AI policy generator"*.

No Releases yet? Build locally:

```bash
git clone https://github.com/Air-Lock-AI/ai-policy-generator-skill.git
cd ai-policy-generator-skill
./build.sh   # produces ai-policy-generator.skill in the parent directory
```

### Option B — Use directly from a clone (Claude Code / Cowork)

Symlink the checkout into your skills folder:

```bash
ln -s "$(pwd)" ~/.claude/skills/ai-policy-generator
# or for Cowork:
ln -s "$(pwd)" "$HOME/Library/Application Support/Claude/skills/ai-policy-generator"
```

The skill reads `template.html` and `i18n/{lang}.json` at runtime.

### Option C — ChatGPT, Gemini, or Microsoft Copilot

The same source files run inside any client that accepts attached project files plus a system prompt. See **[INSTALL-OTHER-CLIENTS.md](INSTALL-OTHER-CLIENTS.md)** for the full per-vendor setup (Projects, Gems, Agents). Short version: clone the repo, attach `SKILL.md` + `template.html` + the `i18n/` and `examples/` folders to a Project, paste the instructions snippet from the install guide, then say *"Generate our AI policy."*

## What's in this repo

```
ai-policy-generator-skill/
├── README.md                       # this file
├── INSTALL-OTHER-CLIENTS.md        # ChatGPT / Gemini / Microsoft Copilot setup
├── LICENSE                         # MIT
├── CHANGELOG.md                    # version history
├── build.sh                        # zips the directory into ai-policy-generator.skill
├── SKILL.md                        # the skill instructions (read by Claude at runtime)
├── template.html                   # the policy template with {{PLACEHOLDER}} markers
├── i18n/
│   ├── en.json                     # English string pack
│   ├── nl.json                     # Dutch
│   ├── fr.json                     # French
│   └── de.json                     # German
└── examples/
    ├── sample-vandermeer-inputs.json   # fictional filled example (inputs)
    └── sample-vandermeer.html          # fictional filled example (rendered)
```

Built artifacts (the `.skill` bundle) are published on the [Releases page](https://github.com/Air-Lock-AI/ai-policy-generator-skill/releases), not committed to source.

## The flow

1. **Greet** and set expectation: 12 questions across 4 batches, full draft in chat, you approve, then HTML.
2. **Context question** — paste anything about the company (website, sector, customers, data, country).
3. **Research** — WebSearch confirms legal entity, CEO, regulator, Annex III sector flags. The skill never invents names.
4. **Structural questions** across 2 batches:
   - Language, governance proxy (airlock or other or none), scope (company-wide or department)
   - AI clients (chat tools), embedded AI (Copilot, Einstein, Notion AI, Slack AI, etc.), customer-facing AI (chatbot, in-product, voice)
5. **Sector specifics + enforcement** — Annex III enumeration for regulated sectors, breach detection method
6. **People & internals** — CEO, AI Officer (name + email + Slack), DPO + channel, tool access path, live register URL, disciplinary code name
7. **Full draft in chat with zero placeholders** — the user reads the policy as a faithful preview of what the HTML will render, word for word
8. **Iterate until approved** — no file generation before explicit user approval
9. **Generate** the HTML + JSON sidecar
10. **Print instructions** — Margins: None, Scale: Default, Background graphics: ON, Headers and footers: OFF, A4

## Output structure

Three A4 pages by default:

- **Cover** — dark indigo hero, company identity, version, effective and review dates, classification
- **The rules** — Purpose & scope, Three duties (Train / Stay on list / Report), Banned full stop (Article 5 plus house rules), Data tier matrix, Approved tools register, optional Annex III addendum
- **The duties** — Transparency, Accountability, Confidentiality and IP, Incidents same business day, Sanctions, Where to ask, Acknowledgement and review

Content can grow beyond three pages. A JS splitter in the template automatically divides long sheets into discrete A4 cards at band boundaries, with `part N of M` page numbering rewritten to match the actual count.

## Languages

English, Dutch, French, German. The static policy text lives in `i18n/{lang}.json`. Each language pack covers all clause headings, fixed clause text, the Article 5 prohibition list, and the default house rules. Variables (company name, contacts, tools register, addendum content) are substituted at render time.

## Print

The output HTML uses CSS `@page` rules to set A4 size with `14mm` content margins on every physical page (the cover stays full-bleed via a named `@page cover-page`). Empty `@page` margin boxes claim the page edges so Chrome's default browser headers and footers don't appear. A JS splitter divides long content sheets at band boundaries.

For the cleanest PDF, the in-page **Save as PDF** button alerts the user to set Margins to None, Scale to Default, Background graphics ON, and Headers and footers OFF before the print dialog opens.

## Aligned to

- **EU AI Act, Article 4** — AI literacy (clause 2, the three duties)
- **EU AI Act, Article 5** — prohibited uses (clause 3, the five fixed bullets)
- **EU AI Act, Article 50** — transparency obligations (clause 6)
- **EU AI Act, Annex III** — high-risk uses (optional sector addendum clause)
- **GDPR Article 37** — DPO informer note for regulated sectors when DPO is `[ to appoint ]`

The skill never quotes a regulator it didn't verify in the research step. Unknown contacts default to `[ to confirm ]` placeholders that the user resolves before the draft is even shown.

## Status

Version 1.3.1. Stable for production use.

## License

MIT. See [LICENSE](LICENSE).

## Related

Built with [airlock](https://air-lock.ai), the AI governance proxy. The skill's tools register positions airlock as the default proxy connector when the user selects "Yes — airlock" in Q2, but the policy works equally well without any proxy.
