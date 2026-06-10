# Starter Configuration

Use this as the default shape for `literature-today.config.json`. Replace user-specific values before running.

```json
{
  "recipient_email": "user@example.com",
  "crossref_mailto": "user@example.com",
  "pubmed_email": "user@example.com",
  "language": "en",
  "timezone": "America/New_York",
  "schedule_time": "10:00",
  "output_dir": "literature-today-digests",
  "include_arxiv": true,
  "include_pubmed": true,
  "rows": 50,
  "arxiv_rows": 25,
  "pubmed_rows": 25,
  "max_papers": 30,
  "high_impact_only": true,
  "accept_preprints": true,
  "relevant_only": true,
  "minimum_relevance_score": 2,
  "require_direct_keyword_match": true,
  "require_abstract": true,
  "high_impact_journals": [
    "Nature",
    "Science",
    "Cell",
    "The Lancet",
    "The New England Journal of Medicine",
    "JAMA",
    "The BMJ",
    "Proceedings of the National Academy of Sciences",
    "PNAS",
    "PNAS Nexus",
    "Nature Medicine",
    "Nature Microbiology",
    "Nature Communications",
    "Nature Human Behaviour",
    "Nature Computational Science",
    "Science Translational Medicine",
    "Science Advances",
    "Cell Host & Microbe",
    "The Lancet Infectious Diseases",
    "The Lancet Public Health",
    "The Lancet Digital Health",
    "The Lancet Microbe",
    "Clinical Infectious Diseases",
    "The Journal of Infectious Diseases",
    "Emerging Infectious Diseases",
    "Eurosurveillance",
    "International Journal of Epidemiology",
    "American Journal of Epidemiology",
    "Epidemiology",
    "PLOS Medicine",
    "PLOS Pathogens",
    "PLOS Computational Biology",
    "eLife"
  ],
  "high_impact_journal_prefixes": [
    "Nature Reviews",
    "The Lancet Regional Health"
  ],
  "topic_keyword_groups": [
    {
      "label": "infectious disease modeling + neural networks",
      "topic_label": "infectious disease modeling",
      "topic_terms": [
        "infectious disease",
        "infectious disease modeling",
        "infectious disease transmission",
        "epidemic modeling",
        "epidemiological modeling",
        "disease transmission",
        "outbreak analysis",
        "transmission dynamics"
      ],
      "keyword_label": "neural networks",
      "keyword_terms": [
        "neural network",
        "neural networks",
        "deep learning",
        "machine learning",
        "graph neural network",
        "graph neural networks",
        "artificial intelligence"
      ]
    }
  ],
  "keyword_groups": [
    {
      "label": "infectious disease modeling",
      "terms": [
        "infectious disease modeling",
        "infectious disease modelling",
        "infectious disease model",
        "infectious disease transmission",
        "epidemic modeling",
        "epidemic modelling",
        "epidemic model",
        "epidemiological modeling",
        "epidemiological modelling",
        "mathematical modeling infectious disease",
        "disease transmission model"
      ]
    },
    {
      "label": "measles outbreak analysis",
      "terms": [
        "measles outbreak analysis",
        "measles outbreak",
        "measles transmission",
        "measles epidemic",
        "measles resurgence",
        "measles elimination",
        "measles vaccination coverage",
        "measles susceptibility",
        "measles importation",
        "measles reproduction number"
      ]
    },
    {
      "label": "respiratory disease analysis",
      "terms": [
        "respiratory disease analysis",
        "respiratory disease",
        "respiratory infection",
        "respiratory virus",
        "respiratory pathogen",
        "respiratory transmission",
        "influenza transmission",
        "RSV transmission",
        "SARS-CoV-2 transmission",
        "COVID-19 transmission",
        "acute respiratory infection"
      ]
    },
    {
      "label": "vaccination strategy",
      "terms": [
        "vaccination strategy",
        "vaccination strategies",
        "vaccine strategy",
        "immunization strategy",
        "immunisation strategy",
        "vaccine allocation",
        "vaccine prioritization",
        "vaccine prioritisation",
        "vaccination coverage",
        "booster strategy",
        "catch-up vaccination",
        "mass vaccination"
      ]
    },
    {
      "label": "contact behavior",
      "terms": [
        "contact behavior",
        "contact behaviour",
        "contact patterns",
        "social contact patterns",
        "human contact behavior",
        "human contact behaviour",
        "mixing patterns",
        "contact mixing",
        "contact matrix",
        "social mixing",
        "mobility patterns",
        "behavioral response",
        "behavioural response"
      ]
    }
  ]
}
```

Notes:

- Keep `crossref_mailto` as the recipient or another user-controlled email for polite Crossref/OpenAlex API use.
- Keep `pubmed_email` as a user-controlled email for NCBI E-utilities requests. Add `ncbi_api_key` only when the user explicitly wants to use an NCBI API key.
- Set `language` to `en` for English or `zh-CN` for Simplified Chinese. When `zh-CN` is used, write the Markdown archive and email body in Chinese while preserving source metadata such as titles, authors, journal names, DOI/URL, and arXiv IDs.
- Tune `rows`, `arxiv_rows`, and topic-keyword combinations carefully; every expanded combination adds API calls.
- Use `include_pubmed: true` for biomedical, clinical, epidemiology, and biostatistics/statistics literature indexed in PubMed/MEDLINE.
- Use `include_arxiv: true` plus `accept_preprints: true` to query arXiv and allow relevant preprints to pass the venue filter.
- Use `high_impact_only: true` to filter journal articles to the whitelist while still allowing accepted preprints.
