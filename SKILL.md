---
name: literature-today
description: Create, update, run, or troubleshoot a reusable daily literature digest that expands research topics and keywords, searches Crossref/OpenAlex/arXiv, filters relevant high-impact journal articles plus accepted preprints, archives Markdown or optional DOCX digests, and sends daily email summaries through Gmail. Use when the user asks for daily or scheduled literature monitoring, keyword/topic paper alerts, high-impact paper digests, arXiv/preprint-inclusive research updates, topic-plus-keyword search combinations, or automated Gmail literature summaries.
---

# Literature Today

## Overview

Use this skill to build a user's daily research literature digest. The bundled fetch script gathers open metadata and abstracts only; Codex writes the analysis, saves the archive, sends email through Gmail when connected, and creates or updates the recurring automation.

Do not read paywalled full text or auto-login to publisher, university, or library sites during unattended runs. Full-text follow-up is a separate explicit task using PDFs supplied by the user or an active browser session the user already opened.

## Core Workflow

1. Confirm or infer settings:
   - Recipient email.
   - Digest language.
   - Timezone and daily schedule.
   - Workspace path and Python command.
   - Sources: Crossref/OpenAlex by default; arXiv when `include_arxiv` and `accept_preprints` are true.
   - Journal/preprint policy: high-impact journal whitelist plus accepted preprints by default.
   - Keyword groups and optional topic-keyword combination groups.
2. Copy `scripts/literature_today.py` into the user's workspace.
3. Copy `scripts/markdown_to_docx.py` only when DOCX output is requested.
4. Create `daily-literature-digest.config.json` in the workspace. Use `references/starter-config.md` as the starting point and replace the example user values.
5. Run a validation fetch:
   ```bash
   python scripts/literature_today.py --config daily-literature-digest.config.json fetch --include-seen
   ```
6. Read the printed JSON path and write the Markdown archive to `daily-literature-digests/YYYY-MM-DD.md`.
7. If records lack abstracts and need manual follow-up, write `daily-literature-digests/fulltext-inbox/to-download-YYYY-MM-DD.md` with DOI/URL and a note that no abstract/full text was read.
8. Send email through Gmail when available. If Gmail is unavailable, do not ask for SMTP credentials; record `not-configured`.
9. Mark success only after the Markdown archive exists:
   ```bash
   python scripts/literature_today.py --config daily-literature-digest.config.json mark-success --data-file <JSON_PATH> --digest-file <DIGEST_PATH> --email-status <sent|failed|not-configured>
   ```
10. Create or update a Codex cron automation at the user's local time.

## Search Rules

Use two complementary search modes:

- **Standalone expanded keywords**: Search every term in `keyword_groups[].terms` separately.
- **Topic-plus-keyword combinations**: For each `topic_keyword_groups[]`, search every `topic_terms[]` and `keyword_terms[]` pair separately. The script represents internal paired terms as `topic term && keyword term`; Crossref receives the pair as a combined bibliographic query and arXiv receives an `AND` query.

For topic-keyword groups, keep a result only when title, abstract, or subject metadata visibly contains evidence for both the topic side and the keyword side.

Example:

```json
{
  "label": "infectious disease modeling + neural networks",
  "topic_label": "infectious disease modeling",
  "topic_terms": ["infectious disease", "infectious disease transmission", "epidemic modeling"],
  "keyword_label": "neural networks",
  "keyword_terms": ["neural network", "deep learning", "machine learning"]
}
```

This produces searches like `infectious disease && neural network`, `infectious disease transmission && deep learning`, and `epidemic modeling && machine learning`.

## Selection Rules

Treat matching as inclusive during fetch, but strict during final selection.

Include a paper in the main digest only when it passes the configured policy:

- `high_impact_only`: journal articles must match `high_impact_journals` or `high_impact_journal_prefixes`.
- `accept_preprints`: arXiv/preprint records can pass the venue filter when enabled.
- `relevant_only`: remove query-only, title-only, weakly matched, and off-topic records.
- `require_direct_keyword_match`: require title, abstract, or subject metadata evidence.
- `require_abstract`: require an abstract for unattended summary.
- `minimum_relevance_score`: enforce the configured score threshold.

If no papers pass, still write and send a concise no-results digest. Do not relax the filters silently.

## Summary Rules

Use only title, abstract, keywords, subject tags, DOI, journal/source, authors, publisher, source metadata, and open metadata in the JSON. Mark arXiv records clearly as preprints.

For each included paper, report:

- Title.
- Source/publisher and journal/preprint source.
- Date, authors, DOI/URL, and PDF/open URL when available.
- Matched keyword group or topic-keyword group.
- Priority and relevance.
- Research goal, method, main result, relevance to the user, and next action.

For no-abstract/title-only records, do not infer research goal, method, or result. State: `No abstract/full text was available; this is a title-level judgment only.`

Mention Crossref, OpenAlex, or arXiv API errors in the digest and summarize any successfully fetched results.

## Automation Prompt Requirements

The automation prompt must include:

- Exact workspace path and Python command.
- Exact config path.
- Exact fetch command using `--config`.
- Recipient email, language, schedule time, timezone, and output directory.
- Original user keywords and a note that expanded terms live in `keyword_groups`.
- Topic-keyword combination instructions when `topic_keyword_groups` is configured.
- Instruction to summarize open metadata/abstracts only.
- Instruction to use Gmail connector if available.
- Instruction to call `mark-success` with `sent`, `failed`, or `not-configured`.
- Warning that local Codex automations may not run if the computer is asleep or the local runner is not active.

For daily 10:00:

```text
FREQ=DAILY;BYHOUR=10;BYMINUTE=0;BYSECOND=0
```

## Full-Text Follow-Up

When the user says they have logged in to ScienceDirect, a university library, or another publisher site:

- Do not ask for passwords.
- Use only the current active browser/session or PDFs downloaded into `daily-literature-digests/fulltext-inbox`.
- Process only the explicit batch/list requested by the user.
- Read accessible PDFs or article pages only when allowed by the active session.
- Save summaries to `daily-literature-digests/fulltext-summaries/YYYY-MM-DD-fulltext.md`.
- Do not create unattended publisher-download automation.

## Resources

- `scripts/literature_today.py`: deterministic fetch/state script for Crossref, OpenAlex, and arXiv.
- `scripts/markdown_to_docx.py`: optional Markdown-to-DOCX converter.
- `references/starter-config.md`: reusable starter config with high-impact journal, accepted-preprint, expanded-keyword, and topic-keyword examples.
