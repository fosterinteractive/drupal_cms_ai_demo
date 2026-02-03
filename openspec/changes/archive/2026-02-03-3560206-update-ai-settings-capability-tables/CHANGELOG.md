# Changelog

## 2026-01-15: Add Alphabetical Sorting to Capability Sections

### Change

Added alphabetical sorting to all three capability sections on the AI Settings page for consistent, predictable ordering.

### Implementation

Added sorting in `categorizeCapabilities()` method after capabilities are categorized into sections:

```php
// Sort each section alphabetically by label.
$sortByLabel = fn($a, $b) => strcasecmp((string) $a['label'], (string) $b['label']);
usort($categorized[self::SECTION_INSTALLED], $sortByLabel);
usort($categorized[self::SECTION_ADDITIONAL], $sortByLabel);
usort($categorized[self::SECTION_VECTOR], $sortByLabel);
```

### Files Changed

- `modules/ai/src/Form/AiSettingsForm.php`
  - Lines 180-184: Added alphabetical sorting for all three sections

### Result

**AI Capabilities from Installed Providers** (sorted):
1. Chat
2. Chat with Complex JSON
3. Chat with Image Vision
4. Chat with Structured Response
5. Chat with Tools/Function Calling
6. Moderation
7. Speech To Text
8. Text To Image
9. Text To Speech
10. Translate Text

**Additional AI Capabilities** (sorted):
1. Audio to Audio
2. Image and Audio to Video
3. Image Classification
4. Image to Image
5. Image to Video
6. Speech to Speech

---

## 2026-01-08: Fix Info Links Not Appearing in Settings Form

### Problem

The "Info" column in the AI Settings form was empty even when a provider like OpenAI was selected. The `[i]` pricing/documentation links were not showing.

### Root Cause

The registry file `ai_provider_registry.yml` used **module names** as keys (e.g., `ai_provider_openai`), but the form passes **plugin IDs** (e.g., `openai`) to the `AiProviderMetadataLoader::getInfoUrl()` method. This mismatch caused the lookup to fail.

### Solution

1. **Changed registry keys from module names to plugin IDs**
   - `ai_provider_openai` → `openai`
   - `ai_provider_anthropic` → `anthropic`
   - etc.

2. **Added `module` field to each registry entry** for override lookup
   - The `loadProviderOverride()` method needs the module name to find `ai_provider_info.yml` files

3. **Updated `AiProviderMetadataLoader::loadProviderOverride()`** to use the new `module` field

### Files Changed

- `modules/ai/config/ai_provider_registry.yml` - Restructured with plugin IDs as keys and added `module` field
- `modules/ai/src/Service/AiProviderMetadataLoader.php` - Updated to use `module` field for override lookup

### Registry Changes

Also updated the registry to match the official provider matrix:

#### Providers Added (5)

| Plugin ID | Label | Module | Info URL |
|-----------|-------|--------|----------|
| `amazeeio` | amazee.ai | ai_provider_amazeeio | https://www.amazee.ai/pricing |
| `docker` | Docker Model Runner | ai_provider_docker | https://docs.docker.com/model-runner/ |
| `xai` | xAI (Grok) | ai_provider_x | https://x.ai/api |
| `auphonic` | Auphonic | auphonic | https://auphonic.com/pricing |
| `deepgram` | Deepgram | deepgram | https://deepgram.com/pricing |

#### Providers Removed (2)

| Provider | Reason |
|----------|--------|
| `ai_provider_cohere` | Not listed in official provider matrix |
| `replicate` | Wrong project - drupal.org/project/replicate is an entity cloning module |

### Note

The `modules/` directory is gitignored in this repository. These changes should be applied to the upstream AI module repository.

---

## 2026-01-07: Update Capability Descriptions and Section Headings

### Changes

#### 1. Updated Section Headings (h4 → h2)

Changed the "Embedding Providers" and "Vector Database (VDB) Providers" headings from `<h4>` to `<h2>` for better visual hierarchy within the Vector Data Capabilities section.

**Files Changed:**
- `modules/ai/src/Form/AiSettingsForm.php`
  - Line 504: Changed `'#tag' => 'h4'` to `'#tag' => 'h2'` for Embedding Providers heading
  - Line 555: Changed `'#tag' => 'h4'` to `'#tag' => 'h2'` for VDB Providers heading

#### 2. Updated Capability Descriptions

Rewrote all capability descriptions to be clearer, more user-friendly, and action-oriented.

**AiSettingsForm.php (hardcoded selections):**
- Chat with Image Vision: "Send images to AI and analyze them. Useful for generating alt text, analyzing screenshots, or describing visual content."
- Chat with Complex JSON: "Get AI responses formatted as valid JSON. Ideal for custom AI workflows, or working with organized data."
- Chat with Structured Response: "Get AI responses in consistent formats like lists, tables, or outlines. Helpful when you need predictable output for content workflows."
- Chat with Tools/Function Calling: "Allow AI to trigger actions like fetching live data, running calculations, or calling external services during a conversation."

**OperationType Interface Files:**
- ChatInterface.php: "Have back-and-forth conversations with AI using plain language."
- ModerationInterface.php: "Detect and filter harmful or inappropriate content in text or images."
- TextToImageInterface.php: "Generate images from text descriptions."
- TextToSpeechInterface.php: "Convert written text into spoken audio using synthetic voices."
- AudioToAudioInterface.php: "Transform audio by changing the voice, improving quality, or applying other modifications."
- ImageAndAudioToVideoInterface.php: "Combine an image and audio track to create a video, such as animating a photo with voiceover."
- ImageClassificationInterface.php: "Automatically identify and categorize image content. Useful for tagging media libraries or organizing uploads."
- ImageToImageInterface.php: "Transform images by applying styles, enhancing quality, or making AI-powered edits." (newly added - was missing)
- ImageToVideoInterface.php: "Animate a still image to produce a short video with movement or effects."
- SpeechToSpeechInterface.php: "Transform spoken audio by translating, changing the voice, or modifying tone."

**Already matching (no change needed):**
- SpeechToTextInterface.php: "Transcribe spoken audio into written text."
- TranslateTextInterface.php: "Translate text from one language to another."

---

## 2026-01-07: Fix AJAX Model Dropdown Field Dependency Bug

### Problem

The AJAX model dropdown had a critical bug where changing the provider selection would break the model field:

1. Select a capability with a provider (e.g., OpenAI for Text To Speech) - model dropdown shows correctly
2. Change provider to "No default" - model dropdown correctly shows disabled "- Select -"
3. Change provider back to "OpenAI" - **model dropdown does NOT reappear** (BUG)

The model cell would remain empty, making it impossible to select a model without refreshing the page.

### Root Causes Identified

Two issues in `AiSettingsForm::loadModels()` AJAX callback:

#### 1. Missing `#attached` Property

The AJAX callback returned render arrays without the `#attached` property:

```php
// BEFORE (broken)
$element = [
  '#type' => 'container',
  '#attributes' => [...],
];
```

This caused a PHP TypeError in Drupal's AjaxRenderer:
```
TypeError: Drupal\Core\Ajax\AjaxResponse::setAttachments(): 
Argument #1 ($attachments) must be of type array, null given
```

#### 2. Missing Explicit `#name` on Provider Select

The `loadModels()` callback extracted the operation type via:
```php
$operation_type = substr($trigger['#name'], 11);  // Expects "operation__xxx"
```

But without explicit `#name` on the provider select, Drupal used the full nested form path:
```
table[text_to_speech][provider][operation__text_to_speech]
```

This caused the callback to parse garbage and fail to locate the model element.

### Solution

#### Fix 1: Add `#attached` to Render Arrays

Added `'#attached' => [],` to both render arrays in `loadModels()`:

```php
// Line ~903 (empty provider case)
$element = [
  '#type' => 'container',
  '#attached' => [],  // ADDED
  '#attributes' => [...],
];

// Line ~958 (success case with models)
$element = [
  '#type' => 'container',
  '#attached' => [],  // ADDED
  '#attributes' => [...],
];
```

#### Fix 2: Add Explicit `#name` to Provider Select

Added explicit `#name` attribute to the provider select element:

```php
// Line ~285
$row['provider']['operation__' . $operation_id] = [
  '#type' => 'select',
  '#name' => 'operation__' . $operation_id,  // ADDED
  '#title' => $this->t('Provider for @capability', [...]),
  // ...
];
```

### Files Changed

- `modules/ai/src/Form/AiSettingsForm.php`
  - Line 285: Added `'#name' => 'operation__' . $operation_id,`
  - Line 903: Added `'#attached' => [],`
  - Line 958: Added `'#attached' => [],`

### Testing Verification

1. Navigate to `/admin/config/ai/settings`
2. Find "Text To Speech" row (has OpenAI selected, model shows tts-1-hd)
3. Change provider to "No default" - model field shows disabled select with "- Select -"
4. Change provider back to "OpenAI" - **model field correctly reappears with tts-1, tts-1-hd options**
5. No PHP errors in `drush watchdog:show`
6. No JavaScript console errors

### Commits

```
647194b3 Issue #3560206: Fix AJAX model dropdown not reappearing when re-selecting provider
```

---

## 2026-01-07: Fix Model Select Not Disabled via AJAX

### Problem

When changing the provider from a valid selection (e.g., OpenAI) to "No default", the model select would show an empty "- Select -" option but was **not disabled**. This was inconsistent with the initial page load behavior where model selects are properly disabled when no provider is selected.

### Root Cause

The `#disabled => TRUE` Form API property was not being rendered when the element was returned from an AJAX callback. While Form API processes `#disabled` during form building, AJAX-replaced elements bypass this processing.

### Solution

Added explicit `disabled` attribute via `#attributes` in addition to `#disabled`:

```php
$element['model__' . $operation_type] = [
  '#type' => 'select',
  '#title' => $this->t('Model'),
  '#title_display' => 'invisible',
  '#options' => ['' => $this->t('- Select -')],
  '#default_value' => '',
  '#disabled' => TRUE,
  '#attributes' => ['disabled' => 'disabled'],  // ADDED
];
```

### Files Changed

- `modules/ai/src/Form/AiSettingsForm.php`
  - Line 918: Added `'#attributes' => ['disabled' => 'disabled'],`

### Testing Verification

1. Navigate to `/admin/config/ai/settings`
2. Find "Text To Speech" row (has OpenAI selected)
3. Change provider to "No default" - model select shows "- Select -" **and is disabled**
4. Change provider back to "OpenAI" - model select becomes enabled with model options

### Commit

```
d9baef26 Issue #3560206: Ensure model select is disabled when no provider selected via AJAX
```

---

## 2026-01-07: Fix Form Submission Values Reset

### Problem

After saving the form, all provider/model selections would reset to "No default" instead of preserving the selected values.

### Root Cause

When `#name` is explicitly set on form elements, Drupal's Form API also needs `#parents` to correctly map submitted values. Without `#parents`, Drupal looks for values at the nested form tree path instead of the flat path specified by `#name`.

For example:
- Element at: `$form['table']['chat']['provider']['operation__chat']`
- With `#name => 'operation__chat'`, HTML name is `operation__chat`
- But without `#parents`, Drupal looks for value at nested path, finds nothing

### Solution

Added `#parents` alongside `#name` on all provider and model select elements:

```php
// Provider select
$row['provider']['operation__' . $operation_id] = [
  '#type' => 'select',
  '#name' => 'operation__' . $operation_id,
  '#parents' => ['operation__' . $operation_id],  // ADDED
  ...
];

// Model select
$row['model']['model__' . $operation_id] = [
  '#type' => 'select',
  '#name' => 'model__' . $operation_id,
  '#parents' => ['model__' . $operation_id],  // ADDED
  ...
];
```

Also updated both AJAX callback render arrays with the same `#name` and `#parents`.

### Files Changed

- `modules/ai/src/Form/AiSettingsForm.php`
  - Line 287: Added `'#parents' => ['operation__' . $operation_id],`
  - Line 354: Added `'#name' => 'model__' . $operation_id,` and `'#parents' => ['model__' . $operation_id],`
  - Lines 916-917: Added `#name` and `#parents` to AJAX empty provider case
  - Lines 976-977: Added `#name` and `#parents` to AJAX success case

### Testing Verification

1. Navigate to `/admin/config/ai/settings`
2. Set "Chat" provider to "OpenAI", model to "gpt-4.1"
3. Click "Save configuration"
4. Verify success message appears
5. Verify Chat row still shows "OpenAI" and "gpt-4.1" after page reload

### Commit

```
a961b059 Issue #3560206: Fix form submission by adding #parents to form elements
```
