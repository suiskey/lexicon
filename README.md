# suiskey/lexicons

Neutral community identity schemas for the Atmosphere. Published under [CC0 1.0](LICENSE) — no conditions, public domain.

---

## What is Suiskey?

Suiskey is a namespace for AT Protocol lexicons that describe how people participate in communities. The goal is shared vocabulary that any community-building app on ATProto can adopt — without being owned by any single one of them.

The name is a loose reference to *sui generis* ("of its own kind") and a key that fits many locks.

---

## Current Schema

### `com.suiskey.community.profile`

A community identity record. Stores what a person practices, how they want to collaborate, and where their work can be found.

**Design relationship to `app.bsky.actor.profile`**: this record is additive, not a replacement. Fields already well-covered by the base Bluesky profile (bio/description, pronouns, website, pinnedPost) are intentionally absent. Apps should read both records and composite them.

```
app.bsky.actor.profile    →  bio, pronouns, website URL, one pinned post
com.suiskey.community.profile  →  focus areas, platform links, collaboration status,
                                  featured work (links + AT URIs), location
```

**Record key**: `self` (singleton — one per DID)

#### Fields

| Field | Type | Description |
|-------|------|-------------|
| `focus` | `string[]` (max 20) | What you actively practice or work in. Free-form — no controlled vocabulary enforced. |
| `platformLinks` | `platformLink[]` (max 20) | Identity across platforms — each entry has `platform`, `url`, and optional `label`. |
| `collaborationStatus` | `string` (enum) | `open` \| `closed` \| `activelySeeking` |
| `collaborationInterests` | `string[]` (max 20) | Focus areas or skills wanted in collaborators. |
| `featuredLinks` | `featuredLink[]` (max 10) | External work or project URLs with optional labels. |
| `featuredItems` | `at-uri[]` (max 10) | Native Atmosphere objects — posts, feeds, collections, etc. Supplements `pinnedPost`. |
| `location` | `string` (max 64 graphemes) | Optional freeform location. |

No fields are required except `$type`. All are optional.

#### Sub-types

**`platformLink`**
```json
{
  "platform": "GitHub",
  "url": "https://github.com/example",
  "label": "example"
}
```

**`featuredLink`**
```json
{
  "url": "https://example.bandcamp.com",
  "label": "My Bandcamp"
}
```

---

## Design Principles

**Additive, not replacing.** This schema complements `app.bsky.actor.profile`. Apps should read both.

**No required fields.** Partial records are valid. Apps should handle any field being absent.

**No controlled vocabulary in the schema.** `focus` and `collaborationInterests` are free-form string arrays. Vocabulary coordination (if desired) belongs in app-layer conventions or a separate vocabulary lexicon, not the base schema.

**lowerCamelCase field names** per ATProto style guide.

**Reasonable array limits** to prevent abuse: 20 items on focus/platformLinks/collaborationInterests, 10 on featured content.

**CC0.** No attribution required. Fork it, adopt it, extend it.

---

## How to Contribute

Open an issue or pull request. Contributions should follow the same principles above:

- Don't duplicate fields available in `app.bsky.actor.profile`
- Prefer additive schemas over modifications to existing ones (lexicons are append-only once published)
- Field names lowerCamelCase
- No required fields
- All arrays bounded

If you are building an app that implements `com.suiskey.community.profile`, add it to the list below.

---

## Reference Implementations

- **[built.blue](https://built.blue)** — community labeler and feed generator for Atmosphere creators

---

## Future Directions

**Label vocabulary layer.** A companion schema (`com.suiskey.community.vocab` or similar) could define controlled term lists for `focus` — allowing apps to normalize "oil painting" and "oil paint" to the same concept without baking vocabulary into the base record schema.

**Verification layer.** A separate attestation schema for third-party confirmation of `platformLinks` — proving that a Bluesky DID owns a given GitHub or Bandcamp URL without storing the proof in the profile record itself.

**Community membership.** A record type for explicit opt-in to named communities, complementing label-based membership as used by built.blue.

These are not committed roadmap items — they are signals of where the namespace could grow.

---

## License

[CC0 1.0 Universal](LICENSE) — public domain dedication. No rights reserved.
