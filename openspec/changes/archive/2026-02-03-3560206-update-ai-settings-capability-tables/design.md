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

*(Capabilities sorted alphabetically by label)*

| Capability | Provider | Model | Info |
|------------|----------|-------|------|
| **Chat** | `[OpenAI ▼]` | `[gpt-4o ▼]` | [i] |
| *Have back-and-forth conversations with AI* | | | |
| **Chat with Complex JSON** | `[OpenAI ▼]` | `[gpt-4o ▼]` | [i] |
| *Get AI responses formatted as valid JSON* | | | |
| **Chat with Image Vision** | `[OpenAI ▼]` | `[gpt-4o ▼]` | [i] |
| *Send images to AI and analyze them* | | | |
| **Moderation** | `[OpenAI ▼]` | `[omni-moderation-latest ▼]` | [i] |
| *Detect and filter harmful content* | | | |
| **Speech to Text** | `[OpenAI ▼]` | `[whisper-1 ▼]` | [i] |
| *Transcribe spoken audio into written text* | | | |
| **Text to Speech** | `[OpenAI ▼]` | `[tts-1-hd ▼]` | [i] |
| *Convert written text into spoken audio* | | | |

---

### Additional AI Capabilities

*These capabilities require additional provider modules to be installed.*

*(Capabilities sorted alphabetically by label)*

| Capability | Available Providers |
|------------|---------------------|
| **Audio to Audio** | Install: (check AI module homepage) |
| *Transform audio by changing voice or quality* | |
| **Image Classification** | Install: `ai_provider_huggingface` |
| *Identify and categorize image content* | |
| **Image to Image** | Install: `ai_provider_dreamstudio` |
| *Transform images with AI-powered edits* | |
| **Text to Image** | Install: `ai_provider_openai`, `ai_provider_dreamstudio` |
| *Generate images from text prompts* | |

---

### Vector Data Capabilities

*Embedding providers allow text & media to be converted to a vector format, which is stored in a vector database (Eg Pinecone, Milvus). This enables AI tools to more easily understand your website's content, which is useful for features such as semantic search, chatbots, or AI content reviews.*

#### Embedding Providers

**When no embedding providers installed:**
> Install: OpenAI, Azure, Ollama, AWS Bedrock, Huggingface, Vertex AI, Fireworks AI, LMStudio
*(Each name links to drupal.org project page)*

**When embedding providers installed:**

| AI Model Capability | Provider | Default Model | Info |
|---------------------|----------|---------------|------|
| **Embeddings** | `[OpenAI ▼]` | `[text-embedding-3-small ▼]` | [i] |

#### Vector Database (VDB) Providers

**When no VDB providers installed:**
> Install: Milvus, Pinecone, PostgreSQL, SQLite
*(Each name links to drupal.org project page)*

**When VDB providers installed:**

| AI Model Capability | VDB Provider | Database | Info |
|---------------------|--------------|----------|------|
| **Vector Database** | `[Pinecone ▼]` | Pinecone | [i] |

---

**Legend:**
- `[▼]` = Drupal select dropdown
- `[i]` = Info icon linking to provider pricing/docs (opens new tab)
- *Italic text* = Capability/section description (smaller/muted helper text)

## Decisions

- **Table over fieldsets**: Tables provide better visual alignment and scannability for comparing provider/model selections across capabilities
- **Alphabetical sorting**: Capabilities within each section are sorted alphabetically by label for consistent, predictable ordering
- **Info column**: External link icon only (no text label) to keep table compact; tooltip or aria-label provides context
- **Description placement**: Below capability name in same cell, styled as helper text (consistent with Drupal form patterns)
- **Empty info links**: Simply omit the icon when provider has no URL configured (graceful degradation)

## Provider Metadata Schema

### Registry-Based Architecture

The AI module maintains a **central provider registry** YAML file listing all known AI provider modules with default metadata. Contrib provider modules can optionally override this with their own metadata file.

**Registry file**: `modules/ai/config/ai_provider_registry.yml`

**Example registry**:
```yaml
# ai_provider_registry.yml - Central registry in AI module

providers:
  ai_provider_openai:
    label: 'OpenAI'
    project_url: 'https://www.drupal.org/project/ai_provider_openai'
    info_url: 'https://platform.openai.com/docs/pricing'
    updated: '2025-01-07'
    
  ai_provider_anthropic:
    label: 'Anthropic'
    project_url: 'https://www.drupal.org/project/ai_provider_anthropic'
    info_url: 'https://www.anthropic.com/pricing'
    updated: '2025-01-07'
    
  ai_provider_elevenlabs:
    label: 'ElevenLabs'
    project_url: 'https://www.drupal.org/project/ai_provider_elevenlabs'
    info_url: 'https://elevenlabs.io/pricing'
    updated: '2025-01-07'
```

**Override file** (optional): `[provider_module]/ai_provider_info.yml`

**Example override** (in contrib module):
```yaml
# ai_provider_openai/ai_provider_info.yml - Provider-specific overrides

# Override default info URL
info_url: 'https://platform.openai.com/docs/pricing'

# Update timestamp
updated: '2025-01-07'

# Model-specific metadata
models:
  gpt-4o:
    info_url: 'https://platform.openai.com/docs/models/gpt-4o'
  gpt-4o-mini:
    info_url: 'https://platform.openai.com/docs/models/gpt-4o-mini'
  gpt-4-turbo:
    info_url: 'https://platform.openai.com/docs/models/gpt-4-turbo'
    eol: '2025-12-31'  # Optional: end-of-life date
  text-embedding-ada-002:
    eol: '2025-06-01'  # Model being deprecated
```

### Schema Fields

**Registry level** (`ai_provider_registry.yml`):
| Field | Required | Description |
|-------|----------|-------------|
| `label` | Yes | Human-readable provider name |
| `project_url` | Yes | Drupal.org project page (for "Additional Capabilities" links) |
| `info_url` | Optional | Default pricing/docs URL for all models |
| `updated` | Recommended | ISO 8601 date when metadata was last verified |

**Provider override level** (`ai_provider_info.yml`):
| Field | Required | Description |
|-------|----------|-------------|
| `info_url` | Optional | Override default pricing/docs URL |
| `updated` | Optional | Override update timestamp |
| `models` | Optional | Per-model metadata overrides |

**Model level** (within `models`):
| Field | Required | Description |
|-------|----------|-------------|
| `info_url` | Optional | Model-specific pricing/docs URL |
| `eol` | Optional | ISO 8601 date when model will stop working |

### Merge Behavior

1. **Load registry defaults** from `ai_provider_registry.yml`
2. **Check for provider override** at `[provider_module]/ai_provider_info.yml`
3. **Merge strategy**:
   - Provider-level fields: Override replaces default
   - Model-level fields: Override is additive (registry doesn't define models)
4. **Fallback chain**:
   - Model-specific `info_url` → Provider `info_url` → Registry `info_url` → blank

### "Additional Capabilities" Section

When rendering uninstalled capabilities, use `project_url` from registry to create installation links:

| Capability | Available Providers |
|------------|---------------------|
| **Text to Image** | Install: [ai_provider_openai](link), [ai_provider_stability](link) |

This approach:
- **Centralized defaults**: AI module maintains registry without depending on contrib
- **Incremental overrides**: Contrib modules only add metadata when needed
- **"Additional Capabilities" support**: Registry provides `project_url` for uninstalled providers
- **No breaking changes**: Provider plugin interface unchanged
- **Future-proof**: EOL warnings can be added later without schema changes

## Implementation Notes

### "Additional Capabilities" Rendering

Capabilities without installed providers display in a simplified table showing which provider modules could be installed:

| Capability | Available Providers |
|------------|---------------------|
| **Text to Image** | Install: [ai_provider_openai](drupal.org link), [ai_provider_stability](drupal.org link) |

- Links use `project_url` from registry (`ai_provider_registry.yml`)
- Links open in new tab with appropriate aria-labels
- No Provider/Model/Info columns (capabilities aren't configurable until provider installed)

### Capability Categorization Logic

Capabilities are categorized into sections based on provider availability:

1. **Check installed providers**: Use `ProviderPluginManager::getDefinitions()` to get installed providers
2. **For each capability type**: Check if `isUsable()` returns true for any installed provider
3. **Categorize**:
   - If **embeddings** capability → "Vector Data Capabilities" section
   - If **has installed provider** → "AI Capabilities from Installed Providers" section  
   - If **no installed provider** → "Additional AI Capabilities" section (use registry to show available providers)

### Vector Data Capabilities Section

Currently, embeddings and VDB provider are in the main capabilities list. This change **moves** them to a separate "Vector Data Capabilities" section with two subsections:

**Section structure**:
- **Embedding Providers** (h2 subsection)
  - Shows install links when no embedding-capable providers installed
  - Shows capability table with Provider/Default Model/Info columns when providers available
- **Vector Database (VDB) Providers** (h2 subsection)
  - Shows install links when no VDB providers installed
  - Shows capability table with VDB Provider/Database/Info columns when providers available

**Empty state behavior**:
- Tables are **hidden** when no providers installed (not shown with "n/a" values)
- Install links list specific providers that support the capability
- Each provider name links to its drupal.org project page

**Rationale**: Vector databases are infrastructure (storage) distinct from AI inference (computation). Separating them reduces cognitive load and groups related concerns. The empty states guide users to install appropriate modules.

### Empty State Provider Lists

When sections have no installed providers, they display curated lists of available providers with links:

**Embedding Providers** (providers supporting `embeddings` capability):
- OpenAI (`ai_provider_openai`)
- Azure (`ai_provider_azure`)
- Ollama (`ai_provider_ollama`)
- AWS Bedrock (`ai_provider_aws_bedrock`)
- Huggingface (`ai_provider_huggingface`)
- Vertex AI (`ai_provider_google_vertex`)
- Fireworks AI (`fireworksai`)
- LMStudio (`ai_provider_lmstudio`)

**VDB Providers**:
- Milvus (`ai_vdb_provider_milvus`)
- Pinecone (`ai_vdb_provider_pinecone`)
- PostgreSQL (`ai_vdb_provider_postgres`)
- SQLite (`ai_vdb_provider_sqlite`)

These lists are hardcoded in `AiSettingsForm` methods `buildEmbeddingProviderLinks()` and `buildVdbProviderLinks()`. Links open in new tabs with `rel="noopener noreferrer"`.

### Provider Registry ID Corrections

The central registry (`ai_provider_registry.yml`) was updated to use correct drupal.org project IDs:

| Old ID | Correct ID |
|--------|------------|
| `ai_provider_elevenlabs` | `elevenlabs` |
| `ai_provider_google` | `gemini_provider` |
| `ai_provider_stability` | `ai_provider_dreamstudio` |
| `ai_provider_aws` | `ai_provider_aws_bedrock` |
| `ai_provider_replicate` | `replicate` |
| `ai_vdb_provider_postgresql` | `ai_vdb_provider_postgres` |

The `ai_vdb_provider_azure` entry was removed (project does not exist).

### Form Element Naming

Form element names/IDs will **remain unchanged** to maintain backward compatibility:
- `operation__embeddings` (still exists, just rendered in different section)
- `model__embeddings` (still exists)
- `default_vdb_provider` (still exists)

This ensures existing form alters, JavaScript, and automated tests continue to work.

### EOL Warnings

The `eol` field is included in the schema but **deferred for future implementation**:
- Metadata files can include `eol` dates now
- UI will ignore them (no warnings displayed)
- Future enhancement can add visual indicators without schema changes

### Testing Strategy

Automated tests will be added for:
- **Form structure**: PHPUnit test verifying table elements exist
- **AJAX callbacks**: Functional test for model dropdown updates
- **Metadata loading**: Unit test for registry merge logic
- **Section categorization**: Test capability routing to correct sections

Manual QA checkpoints verify visual/UX aspects not covered by automated tests.

### Dynamic Model Info Links (Client-Side JavaScript)

When a provider is selected via AJAX, the model dropdown is dynamically loaded. However, Drupal's Form API `#ajax` handlers are not automatically re-attached to elements replaced via `ReplaceCommand`. This means model selection changes would not trigger server-side AJAX callbacks to update the info links.

**Solution**: A client-side JavaScript behavior handles model dropdown changes and dynamically updates info links.

**Implementation files**:
- `modules/ai/assets/js/ai_settings_form.js` - Drupal behavior for model selection handling
- `modules/ai/ai.libraries.yml` - Library definition (`ai/ai_settings_form`)

**How it works**:

1. **Data attribute**: When a provider is selected, the AJAX callback adds a `data-model-url-pattern` attribute to the info cell containing the URL pattern (e.g., `https://platform.openai.com/docs/models/{model}`)

2. **JavaScript behavior**: Uses `Drupal.behaviors` with `once()` pattern to attach change listeners to model dropdowns

3. **Dynamic URL construction**: When model selection changes, JavaScript replaces `{model}` placeholder with the selected model ID

4. **DOM manipulation**: Creates/updates the model info link with proper accessibility attributes (`target="_blank"`, `rel="noopener noreferrer"`, `aria-label`)

**Code pattern** (follows Drupal best practices):
```javascript
(function (Drupal, once) {
  'use strict';

  Drupal.behaviors.aiSettingsModelSelect = {
    attach: function (context) {
      once('ai-settings-model', 'select[name^="model__"]', context).forEach(function (select) {
        select.addEventListener('change', function () {
          // Get model URL pattern from data attribute
          // Construct URL and update/create info link
        });
      });
    }
  };
})(Drupal, once);
```

**Why this approach**:
- Follows Drupal's recommended `Drupal.behaviors` + `once()` pattern for AJAX content
- Works with dynamically replaced form elements
- Avoids complex server-side workarounds
- Provides immediate user feedback without additional server round-trips

**New service method**: `AiProviderMetadataLoader::getModelInfoUrlPattern(string $providerId): ?string`
- Extracts URL pattern from provider's model metadata
- Returns pattern like `https://platform.openai.com/docs/models/{model}` or NULL

## Non-Goals

- Custom styling or theming (use Drupal Claro/Gin admin defaults)
