# Change: Reorganize AI Settings into Capability-Based Table Layout

## Why
The current AI Settings page (`/admin/config/ai/settings`) presents configuration options as a long vertical list of nested fieldsets. As more AI providers are installed, this becomes difficult to navigate and compare settings across providers. A table-based layout would improve scannability and allow administrators to quickly see capability-provider-model relationships at a glance.

Reference: https://www.drupal.org/project/ai_initiative/issues/3560206

## What Changes
- Restructure the Default Providers section from vertical fieldsets to a table layout with columns for Capability, Provider, Model, and Info
- Add an "Info" column with links to provider pricing/documentation pages for each capability row (blank if not provided)
- Introduce optional YAML metadata schema for contrib providers to specify model pricing/documentation URLs
- Maintain existing functionality (AJAX model loading, non-JS fallback) within the new layout
- Add client-side JavaScript behavior to dynamically update model info links when selecting models on AJAX-loaded dropdowns (follows Drupal `Drupal.behaviors` + `once()` pattern)

## Impact
- Affected specs: ai-settings-ui (new)
- Affected code:
  - `modules/ai/src/Form/AiSettingsForm.php`
  - `modules/ai/src/Service/AiProviderMetadataLoader.php` (new method: `getModelInfoUrlPattern()`)
  - `modules/ai/assets/js/ai_settings_form.js` (new file)
  - `modules/ai/ai.libraries.yml` (new library: `ai_settings_form`)
  - `modules/ai/config/ai_provider_registry.yml`
- **External module impact**: Contrib AI provider modules may optionally add a YAML metadata file to populate info links. This is not a breaking change - the Info column gracefully shows blank when metadata is missing.
- No breaking changes to stored configuration
- No API changes
