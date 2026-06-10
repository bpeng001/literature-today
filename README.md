# Literature Today

A Codex skill for creating a reusable daily literature digest. It monitors Crossref, OpenAlex, and arXiv for papers matching expanded research keywords and optional topic-plus-keyword combinations, filters for relevant high-impact journal articles and preprints, saves a local Markdown archive, and sends a daily Gmail digest.

This repository is inspired by the structure of [`xuezheng627/daily-literature-digest-skill`](https://github.com/xuezheng627/daily-literature-digest-skill), with added support for stricter high-impact filtering, arXiv/preprint records, expanded search terms, and topic-keyword combination searches.

## What It Does

- Runs a daily literature digest at your chosen local time.
- Searches Crossref/OpenAlex and, when enabled, arXiv.
- Expands broad keyword groups automatically, such as turning `measles outbreak analysis` into related terms like `measles outbreak`, `measles transmission`, `measles epidemic`, and `measles vaccination coverage`.
- Supports topic-plus-keyword combination searches, such as `infectious disease && neural network` or `epidemic modeling && machine learning`.
- Filters journal articles to a configurable high-impact journal whitelist.
- Allows arXiv/preprint records when `include_arxiv` and `accept_preprints` are enabled.
- Summarizes only open metadata and abstracts during unattended runs.
- Writes local archives under `daily-literature-digests/`.
- Sends the concise digest through the Codex Gmail connector when Gmail is connected.
- Creates follow-up lists for records that need manual full-text review.

It does not store passwords, log in to publisher websites, bypass paywalls, or create unattended publisher-download workflows.

## Install

Clone or download this repository, then copy the skill folder into your Codex skills directory.

macOS/Linux:

```bash
mkdir -p ~/.codex/skills
cp -R ./literature-today ~/.codex/skills/literature-today
```

Windows PowerShell:

```powershell
$skills = "$env:USERPROFILE\.codex\skills"
New-Item -ItemType Directory -Path $skills -Force | Out-Null
Copy-Item -Recurse -Force .\literature-today "$skills\literature-today"
```

Restart Codex if it does not immediately discover the new skill.

## Connect Gmail

In Codex, connect the Gmail app/connector for the account that should send the digest. This skill uses Gmail connector tools only; it does not ask for SMTP credentials, Gmail app passwords, or account passwords.

## How To Use

After installing the skill and connecting Gmail, ask Codex something like:

```text
Use $literature-today to create a daily literature digest.
Send it to me@example.com every day at 10:00.
Use English.
Only include relevant papers from high-impact journals, plus relevant arXiv/preprints.
My topics and keywords are:
- Topic: infectious disease modeling
- Keywords: neural networks, machine learning
- Keywords: measles outbreak analysis
- Keywords: respiratory disease analysis
- Keywords: vaccination strategy
- Keywords: contact behavior
```

Codex will:

1. Copy `scripts/literature_today.py` into your workspace.
2. Create `daily-literature-digest.config.json`.
3. Expand standalone keywords and any configured topic-keyword combinations.
4. Run an initial fetch.
5. Write the first Markdown digest.
6. Send the digest by Gmail if connected.
7. Create the recurring local Codex automation.

## Search Modes

### Standalone Expanded Keywords

Each term in each `keyword_groups[].terms` list is searched separately. For example, a group labeled `measles outbreak analysis` can include:

```json
[
  "measles outbreak analysis",
  "measles outbreak",
  "measles transmission",
  "measles epidemic",
  "measles vaccination coverage"
]
```

### Topic-Plus-Keyword Combinations

Each topic term is crossed with each keyword term in `topic_keyword_groups`. For example:

```json
{
  "label": "infectious disease modeling + neural networks",
  "topic_terms": ["infectious disease", "infectious disease transmission", "epidemic modeling"],
  "keyword_terms": ["neural network", "deep learning", "machine learning"]
}
```

This generates paired searches such as:

- `infectious disease && neural network`
- `infectious disease transmission && deep learning`
- `epidemic modeling && machine learning`

Paired-search results should show evidence for both sides of the pair in the title, abstract, or subject metadata.

## Generated Workspace Files

The skill creates runtime files in the user's own workspace, not inside this repository:

```text
daily-literature-digest.config.json
daily-literature-digests/data/YYYY-MM-DDTHHMMSSZ.json
daily-literature-digests/YYYY-MM-DD.md
daily-literature-digests/fulltext-inbox/to-download-YYYY-MM-DD.md
daily-literature-digests/fulltext-summaries/
daily-literature-digests/state.json
```

Do not commit generated configs, state files, emails, downloaded PDFs, or full-text notes if they reveal private research interests, email addresses, institutional access traces, or unpublished work.

## Configuration

Use [references/starter-config.md](references/starter-config.md) as the starter shape for `daily-literature-digest.config.json`.

Important settings:

- `include_arxiv`: query arXiv.
- `accept_preprints`: allow arXiv/preprint records to pass the venue filter.
- `high_impact_only`: restrict journal articles to the configured high-impact whitelist.
- `relevant_only`: remove query-only, title-only, weakly matched, and off-topic records.
- `require_direct_keyword_match`: require evidence in title, abstract, or subject metadata.
- `require_abstract`: summarize only records with abstracts during unattended runs.
- `topic_keyword_groups`: define topic-plus-keyword combination searches.

## Automation Caveat

Codex local automations depend on your local Codex runner/environment. If your computer is asleep, shut down, offline, or the local automation runner is not active at the scheduled time, the digest may not run until the environment is available again.

## Full-Text Follow-Up

The unattended daily digest is intentionally abstract/open-metadata based. For records with no abstract or restricted full text:

1. Open the follow-up list under `daily-literature-digests/fulltext-inbox/`.
2. Log in to your university or publisher access yourself in the active browser, or provide downloaded PDFs.
3. Ask Codex to process that explicit batch.

Codex can then summarize accessible pages or PDFs you provide, but it should not store passwords, cookies, or create unattended download automation.

## License

MIT. See [LICENSE](LICENSE).
