## Context

The AI Settings page (`/admin/config/ai/settings`) needs a layout reorganization to improve scannability as the number of AI capabilities grows. This is a layout change only - use standard Drupal admin UI patterns (tables, dropdowns, icons).

Reference issue: https://www.drupal.org/project/ai_initiative/issues/3560206

## Before

Current layout uses nested fieldsets with vertical stacking:

![Current AI Settings page](https://www.drupal.org/files/issues/2025-11-26/AI%20Settings%20%28Before%29.png)

## After (Layout Mockup)

Proposed sectioned layout with tables:

---

### AI Capabilities from Installed Providers

*Configure default providers and models for AI capabilities. These providers are installed and ready to use.*

| Capability | Provider | Model | Info |
|------------|----------|-------|------|
| **Chat** | `[OpenAI ▼]` | `[gpt-4o ▼]` | [i] |
| *Generate conversational responses* | | | |
| **Chat with Image Vision** | `[OpenAI ▼]` | `[gpt-4o ▼]` | [i] |
| *Analyze images within conversation* | | | |
| **Chat with Complex JSON** | `[OpenAI ▼]` | `[gpt-4o ▼]` | [i] |
| *Produce structured JSON outputs* | | | |
| **Text to Speech** | `[ElevenLabs ▼]` | `[eleven_multilingual_v2 ▼]` | [i] |
| *Convert text to audio* | | | |
| **Speech to Text** | `[OpenAI ▼]` | `[whisper-1 ▼]` | [i] |
| *Transcribe audio to text* | | | |

---

### Additional AI Capabilities

*These capabilities require additional provider modules to be installed.*

| Capability | Available Providers |
|------------|---------------------|
| **Text to Image** | Install: `ai_provider_openai`, `ai_provider_stability` |
| *Generate images from text prompts* | |
| **Moderation** | Install: `ai_provider_openai` |
| *Content moderation and safety filtering* | |

---

### Vector Data Capabilities

*Vector databases store embeddings for semantic search and retrieval. This is infrastructure configuration separate from AI inference.*

| Capability | Provider | Model | Info |
|------------|----------|-------|------|
| **Embeddings** | `[OpenAI ▼]` | `[text-embedding-3-small ▼]` | [i] |
| *Generate vector representations for text* | | | |

**Vector Database Provider**

| Provider | Status |
|----------|--------|
| `[Pinecone ▼]` | Connected |

---

**Legend:**
- `[▼]` = Drupal select dropdown
- `[i]` = Info icon linking to provider pricing/docs (opens new tab)
- *Italic text* = Capability/section description (smaller/muted helper text)

## Decisions

- **Table over fieldsets**: Tables provide better visual alignment and scannability for comparing provider/model selections across capabilities
- **Info column**: External link icon only (no text label) to keep table compact; tooltip or aria-label provides context
- **Description placement**: Below capability name in same cell, styled as helper text (consistent with Drupal form patterns)
- **Empty info links**: Simply omit the icon when provider has no URL configured (graceful degradation)

## Provider Metadata Schema

Contrib AI provider modules can optionally provide model pricing/documentation URLs via a YAML metadata file. The AI Settings form reads this metadata to populate the Info column.

**Suggested file**: `[provider_module]/config/ai_provider_info.yml` (or similar convention)

**Example schema**:
```yaml
# ai_provider_info.yml

# Date this metadata was last verified/updated (ISO 8601)
updated: '2025-01-07'

# Default info URL for all models (provider pricing/docs page)
info_url: 'https://platform.openai.com/docs/pricing'

# Model-specific overrides (optional - use when model has dedicated docs page)
models:
  gpt-4o:
    info_url: 'https://platform.openai.com/docs/models/gpt-4o'
  gpt-4o-mini:
    info_url: 'https://platform.openai.com/docs/models/gpt-4o-mini'
  gpt-4-turbo:
    info_url: 'https://platform.openai.com/docs/models/gpt-4-turbo'
    eol: '2025-12-31'  # Optional: end-of-life date when model stops working
  text-embedding-ada-002:
    eol: '2025-06-01'  # Model being deprecated
```

**Schema fields**:
| Field | Level | Required | Description |
|-------|-------|----------|-------------|
| `updated` | Provider | Recommended | ISO 8601 date when metadata was last verified |
| `info_url` | Provider | Optional | Default pricing/docs URL for all models |
| `info_url` | Model | Optional | Override URL for specific model |
| `eol` | Model | Optional | ISO 8601 date when model will stop working |

**Behavior**:
- If provider has no metadata file: Info column shows blank
- If provider has top-level `info_url`: Use as default for all models
- If specific model has `info_url`: Override the provider default
- If model has `eol` date: Display warning indicator in UI (future enhancement)
- The `updated` date helps administrators assess if pricing info may be stale

This approach:
- Requires no breaking changes to provider plugin interface
- Allows incremental adoption by contrib modules
- Keeps pricing/docs URLs maintainable separate from PHP code
- Provides transparency on data freshness via `updated` field
- Enables proactive model deprecation warnings via `eol` field

## Non-Goals

- Custom styling or theming (use Drupal Claro/Gin admin defaults)
