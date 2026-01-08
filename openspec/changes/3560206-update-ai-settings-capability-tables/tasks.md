## 1. Update Layout to Table Structure
- [x] 1.1 Modify `AiSettingsForm::buildForm()` to render capabilities in a `#type => 'table'` element instead of nested fieldsets
- [x] 1.2 Create table columns: Capability (label + description), Provider (select), Model (select), Info (placeholder)
- [x] 1.3 Preserve AJAX callback functionality for model loading within table row context
- [x] 1.4 Preserve non-JS fallback buttons within table structure
- [x] 1.5 Verify form submission and validation still work correctly with new structure

**QA Checkpoint 1**: Manual verification of table layout, AJAX behavior, and form functionality

---

## 2. Add Info Links Column
- [x] 2.1 Create central provider registry YAML file (`modules/ai/config/ai_provider_registry.yml`) with provider metadata (label, project_url, info_url, updated). Reference `web/modules/contrib/ai_provider_openai` and other installed providers to populate initial registry.
- [x] 2.2 Implement metadata loader that loads registry defaults and merges with optional provider overrides from `[provider_module]/ai_provider_info.yml`
- [x] 2.3 Implement metadata loader to read provider YAML files and extract URLs
- [x] 2.4 Populate "Info" column with external link icons sourced from metadata
- [x] 2.5 Handle cases where provider has no metadata file (show blank - graceful degradation)
- [x] 2.6 Ensure links open in new tab with appropriate accessibility attributes (`target="_blank"`, `rel="noopener"`, aria-label using pattern `"<Model Name> Pricing"` wrapped in `t()`)

**QA Checkpoint 2**: Manual verification of info links, accessibility attributes, and graceful degradation

---

## 3. Implement Sectioned Page Organization
- [x] 3.1 Create "AI Capabilities from Installed Providers" section with heading and help text
- [x] 3.2 Create "Additional AI Capabilities" section showing capabilities without installed providers
- [x] 3.3 Create "Vector Data Capabilities" section by moving embeddings capability and VDB provider dropdown from main list
- [x] 3.4 Implement logic to categorize capabilities into appropriate sections based on provider availability
- [x] 3.5 Add descriptive help text to each section explaining its purpose
- [x] 3.6 Apply table layout to each of the three sections

**QA Checkpoint 3**: Manual verification of section organization, help text, and capability categorization

---

## 4. Add Automated Tests
- [x] 4.1 Write PHPUnit test verifying table structure is rendered correctly
- [x] 4.2 Write functional test for AJAX model dropdown updates
- [x] 4.3 Write unit test for metadata loader (registry + override merge logic)
- [x] 4.4 Write test for capability categorization logic (installed vs additional vs vector data)
- [x] 4.5 Verify existing form element names unchanged (backward compatibility check)

**Final Validation**: Run all tests, then proceed to manual QA checkpoints
