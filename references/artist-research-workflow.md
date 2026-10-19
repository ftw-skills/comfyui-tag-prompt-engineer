# Artist Research and Recommendation Workflow

Read this reference when a prompt task needs content-matched artist recommendations, when an artist tag must be resolved to a real person, or when the response should include platform accounts and representative/recent work links.

Build an evidence chain:

```text
visual needs -> candidate -> identity -> Danbooru artist tag
-> model-specific @artist tag -> platform accounts -> representative + recent work
```

Never let one source prove unrelated claims. Danbooru naming does not prove model support; a model sample does not prove real-world identity; a matching account name does not prove account ownership; an image-board post does not prove authorship or permission.

## 1. Extract visual needs

Summarize the requested subject/genre, apparent age treatment, line quality, coloring/rendering, palette/lighting, composition/camera, motion, detail density, mood, and desired/avoided traits. Tie each recommendation reason to these dimensions. If there is no concrete image brief, research the artist record but set `style_match.status: not_evaluated`.

## 2. Discover candidates

Search current web sources because accounts, credits, and recent works change. Normally return three candidates; return fewer when evidence is weak and up to five only for meaningfully different directions.

Use this source priority:

1. artist-owned website, portfolio, or directory;
2. official publisher, studio, game, animation, gallery, or event credit;
3. artist-owned platform profile or work post;
4. Danbooru artist profile and registered URLs for identity/tag resolution;
5. reputable reports, interviews, and archives as secondary corroboration;
6. aggregators and reposts for discovery only.

Do not use search-result pages, raw image/CDN URLs, booru posts, scraped reposts, or unsourced fan uploads as final work links.

## 3. Resolve identity

Record the canonical public name, aliases, legal name only when explicitly published, and ambiguity. Strong identity evidence includes an artist-owned site linking an account, an account linking back, a rights-holder credit linking the artist, or agreement between self-linked accounts and Danbooru registered URLs. Names, avatars, and apparent style alone are insufficient.

## 4. Resolve Danbooru mapping

Open the artist profile, not merely tagged posts. Record numeric artist ID, canonical tag, aliases, registered URLs, URL agreement with primary evidence, and collision risks. Keep character/self-insert, copyright, and general tags separate from the artist tag. Preserve the canonical Danbooru spelling in the research record.

## 5. Resolve the model tag separately

For Anima, first verify the general `@artist name` syntax from model documentation. Then resolve the active environment's `paths.artist_prompt_samples` and inspect the actual JSON for the exact candidate spelling.

Use these statuses:

- `model_sample_verified`: exact tag occurs in the configured model sample metadata;
- `index_verified`: exact tag occurs in the applicable model-specific artist index;
- `inferred`: plausible conversion from a confirmed Danbooru tag but absent from model evidence;
- `unverified`: spelling or model support cannot be established.

Record whether the occurrence is unweighted, weighted, single-artist, or blended with other artists. Configured sample presence verifies local model-prompt usage, not the artist's identity, isolated visual effect, or upstream provenance. Do not silently normalize spaces, underscores, parentheses, capitalization, or aliases.

Only verified tags may be described as verified and ready to paste. An inferred/unverified candidate may still be shown as visual reference with the limitation visible.

## 6. Verify platform accounts

For every account record platform, handle or numeric ID, profile URL, verification evidence, and one status:

- `self_linked`: linked by an artist-owned site or confirmed account;
- `rights_holder_linked`: linked by an official credited client/publisher;
- `cross_verified`: supported by multiple independent consistent sources;
- `directory_only`: present only in a community database;
- `uncertain`: unresolved.

Prefer stable numeric IDs in the record when available, while still returning readable profile URLs.

## 7. Select works

### Representative

Use `representative_work` only when a strong source explicitly supports that status. Otherwise use `style_representative_example` and record the basis: prominent artist-portfolio listing, credited commercial key visual, repeated artist/rights-holder highlighting, or clear relevance to the requested visual dimensions. Popularity alone is insufficient.

### Recent

At response time inspect the confirmed official account. `Recent` defaults to the last 12 months. Select the latest verifiable original artwork, excluding reposts, old-work reposts, and non-art announcements. Record the visible publication date and research date.

Prefer a canonical post/work URL. If only a reliable archive entry is accessible, set `direct_work_url: null`, include the archive URL and a precise page locator, and use `link_status: secondary_archive_only`. Never invent a platform status ID.

## 8. Grade claims independently

| Claim | Allowed statuses |
| --- | --- |
| Identity | `confirmed`, `probable`, `unresolved` |
| Danbooru mapping | `confirmed`, `probable`, `unresolved` |
| Model tag | `model_sample_verified`, `index_verified`, `inferred`, `unverified` |
| Account | `self_linked`, `rights_holder_linked`, `cross_verified`, `directory_only`, `uncertain` |
| Work attribution | `primary_source`, `secondary_source`, `probable`, `unresolved` |
| Work link | `direct`, `portfolio_listing`, `secondary_archive_only`, `missing` |

## 9. Output and stopping rules

For recommendations, compare artist, content-match reason, Danbooru tag, model-tag status, official account, representative work, recent work, and limitations. State the preferred direction and suggest same-seed comparisons where useful.

Stop and downgrade instead of guessing when identity cannot be disambiguated, registered URLs conflict, exact model spelling is absent, attribution rests only on reposts, a canonical work link is unavailable, or a page is deleted/private/inaccessible. Links are for attribution and viewing only; do not reproduce artwork, imply reuse permission, or describe generated output as the artist's work.

## YAML record

Use `artist-research-example-rucarachi.yaml` as the populated structural example. A record should contain:

```yaml
schema_version: 1
researched_at: YYYY-MM-DD
research_context:
  requested_artist: ""
  target_model: ""
  content_brief: null
  style_match: {status: not_evaluated, reason: ""}
identity: {canonical_public_name: "", aliases: [], status: unresolved, evidence: []}
danbooru: {artist_id: null, canonical_tag: "", status: unresolved, evidence: []}
model_artist_tag: {exact_tag: "", status: unverified, syntax_verified: false, evidence: []}
accounts: []
works:
  representative: {}
  recent: {}
limitations: []
```
