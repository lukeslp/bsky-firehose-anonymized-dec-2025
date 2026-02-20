---
license: cc-by-4.0
task_categories:
  - text-classification
  - text-generation
language:
  - en
  - ja
  - ko
tags:
  - bluesky
  - social-media
  - sentiment
  - anonymized
  - firehose
  - at-protocol
pretty_name: "Bluesky Firehose: Anonymized Posts (Dec 2025)"
size_categories:
  - 100K<n<1M
---

# Bluesky Firehose: Anonymized Posts (Dec 2025)

[![License: CC BY 4.0](https://img.shields.io/badge/License-CC_BY_4.0-lightblue.svg)](https://creativecommons.org/licenses/by/4.0/)
[![HuggingFace](https://img.shields.io/badge/HuggingFace-Dataset-FFD21E)](https://huggingface.co/datasets/lukeslp/bsky-firehose-anonymized-dec-2025)
[![GitHub](https://img.shields.io/badge/GitHub-Repo-181717)](https://github.com/lukeslp/bsky-firehose-anonymized-dec-2025)
[![Posts](https://img.shields.io/badge/posts-101,040-blue)]()

101,040 Bluesky posts collected via the AT Protocol firehose, December 2–25, 2025. All author DIDs, post URIs, and thread relationships are SHA-256 hashed. Includes sentiment scores (VADER), language detection, media flags, and thread structure.

## Overview

- **Collection Period**: December 2–25, 2025
- **Total Posts**: 101,040
- **Unique Authors**: 43,998
- **Languages**: 90 detected (60.8% English, 12.5% Japanese, 11.4% unknown)
- **Collection Method**: Bluesky AT Protocol firehose (Jetstream WebSocket)

## Top Languages

| Language | Posts | Percentage |
|----------|-------|-----------|
| en | 61,468 | 60.8% |
| ja | 12,607 | 12.5% |
| unknown | 11,481 | 11.4% |
| en-US | 3,617 | 3.6% |
| ko | 2,406 | 2.4% |

## Schema

| Field | Type | Description |
|-------|------|-------------|
| `text` | string | Post content (original text) |
| `author_did_hash` | string | Anonymized author identifier (SHA-256, 16 chars) |
| `uri_hash` | string | Anonymized post URI (SHA-256, 16 chars) |
| `reply_parent_hash` | string | Anonymized parent post URI if reply (SHA-256, 16 chars) |
| `reply_root_hash` | string | Anonymized root post URI if in thread (SHA-256, 16 chars) |
| `sentiment` | string | Sentiment classification (positive, negative, neutral) |
| `sentiment_score` | float | VADER sentiment compound score (-1.0 to 1.0) |
| `created_at` | string | Post creation timestamp (ISO 8601) |
| `timestamp` | string | Ingestion timestamp (ISO 8601) |
| `language` | string | Detected language code (ISO 639-1) |
| `char_count` | int | Character count |
| `word_count` | int | Word count |
| `has_images` | boolean | Post contains images |
| `has_video` | boolean | Post contains video |
| `has_link` | boolean | Post contains external links |
| `embed_type` | string | Embed type if present |
| `hashtags` | string | JSON array of hashtags |
| `mentions` | string | JSON array of mentions (anonymized) |
| `links` | string | JSON array of external links |

## Anonymization

All personally identifiable fields are hashed using SHA-256:
- **Author DIDs**: Hashed to prevent re-identification while preserving uniqueness for author-level analysis
- **Post URIs**: Hashed to prevent direct post lookup
- **Thread relationships**: Preserved via consistent hashing (same URI always produces same hash)
- **Handles**: Not present in source data (0% populated during collection)

Hashes are truncated to 16 characters for compactness. This provides sufficient uniqueness (2^64 possible values) while preventing reversibility.

## Usage

```python
import pandas as pd
import json

df = pd.read_csv('bluesky_posts.csv')

# Drop trailing blank rows
df = df.dropna(subset=['text'])

# Parse JSON array columns
df['hashtags'] = df['hashtags'].apply(lambda x: json.loads(x) if pd.notna(x) and x != '[]' else [])
df['mentions'] = df['mentions'].apply(lambda x: json.loads(x) if pd.notna(x) and x != '[]' else [])

# Sentiment distribution
print(df['sentiment'].value_counts())

# Language breakdown
print(df['language'].value_counts().head(10))

# Daily posting volume
df['date'] = pd.to_datetime(df['created_at']).dt.date
print(df.groupby('date').size())
```

## Terms of Service Compliance

This dataset complies with Bluesky Developer Guidelines:
1. **Content deletion**: A deletion API endpoint exists for removal requests — contact luke@lukesteuber.com
2. **Privacy protection**: All DIDs and URIs are anonymized via SHA-256 hashing
3. **Jetstream disclaimer**: Data collected via Jetstream API (not formally part of AT Protocol, stability not guaranteed)

## Data Quality

- **Text completeness**: 100%
- **Sentiment analysis**: VADER sentiment on all posts
- **Language detection**: Automated (may contain false positives for short posts)
- **Missing author_handle**: The collection process did not capture Bluesky handles (0% populated)

## Known Limitations

1. **Missing handles**: The `author_handle` field was not captured during collection (0% populated)
2. **Language detection**: Short posts may be misclassified
3. **Temporal snapshot**: Dec 2–25 window is not representative of broader Bluesky activity
4. **Jetstream stability**: Collection API not guaranteed stable long-term

## Suggested Use Cases

- Sentiment analysis and opinion mining
- Language detection and multilingual NLP
- Social network analysis (thread structure via reply hashes)
- Comparative analysis across languages
- Temporal pattern analysis
- Content moderation research

## Distribution

- **GitHub**: [lukeslp/bsky-firehose-anonymized-dec-2025](https://github.com/lukeslp/bsky-firehose-anonymized-dec-2025)
- **HuggingFace**: [lukeslp/bsky-firehose-anonymized-dec-2025](https://huggingface.co/datasets/lukeslp/bsky-firehose-anonymized-dec-2025)

## Citation

```bibtex
@dataset{steuber2025bluesky,
  author    = {Luke Steuber},
  title     = {Bluesky Firehose: Anonymized Posts (Dec 2025)},
  year      = {2025},
  publisher = {HuggingFace},
  url       = {https://huggingface.co/datasets/lukeslp/bsky-firehose-anonymized-dec-2025}
}
```

## Bluesky Ecosystem

More Bluesky tools by Luke Steuber:

- [bluedrop](https://github.com/lukeslp/bluedrop) — browser extension for DMs, Zen Mode, and dark mode
- [skymarshal](https://github.com/lukeslp/skymarshal) — Python CLI for post and comment management
- [skymarshal-js](https://github.com/lukeslp/skymarshal-js) — TypeScript/JS toolkit for AT Protocol

## License

CC-BY-4.0 (Creative Commons Attribution 4.0 International). Free to share and adapt with attribution to Luke Steuber.

## Author

**Luke Steuber**
- Website: [lukesteuber.com](https://lukesteuber.com)
- Bluesky: [@lukesteuber.com](https://bsky.app/profile/lukesteuber.com)
- Contact: luke@lukesteuber.com
