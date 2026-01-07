## 1. Update Layout to Table Structure
- [ ] 1.1 Modify `AiSettingsForm::buildForm()` to render capabilities in a `#type => 'table'` element instead of nested fieldsets
- [ ] 1.2 Create table columns: Capability (label + description), Provider (select), Model (select), Info (placeholder)
- [ ] 1.3 Preserve AJAX callback functionality for model loading within table row context
- [ ] 1.4 Preserve non-JS fallback buttons within table structure
- [ ] 1.5 Verify form submission and validation still work correctly with new structure

**QA Checkpoint 1**: Manual verification of table layout, AJAX behavior, and form functionality

---

## 2. Add Info Links Column
- [ ] 2.1 Define YAML metadata schema for provider pricing/docs URLs (e.g., `ai_provider_info.yml`). Reference `web/modules/contrib/ai_provider_openai` as an example to find the least disruptive pattern for adding metadata to the many existing AI provider modules.
- [ ] 2.2 Implement metadata loader to read provider YAML files and extract URLs
- [ ] 2.3 Populate "Info" column with external link icons sourced from metadata
- [ ] 2.4 Handle cases where provider has no metadata file (show blank - graceful degradation)
- [ ] 2.5 Ensure links open in new tab with appropriate accessibility attributes (`target="_blank"`, `rel="noopener"`, aria-label using pattern `"<Model Name> Pricing"` wrapped in `t()`)

**QA Checkpoint 2**: Manual verification of info links, accessibility attributes, and graceful degradation

---

## 3. Implement Sectioned Page Organization
- [ ] 3.1 Create "AI Capabilities from Installed Providers" section with heading and help text
- [ ] 3.2 Create "Additional AI Capabilities" section showing capabilities without installed providers
- [ ] 3.3 Create "Vector Data Capabilities" section separating embeddings and VDB config from inference
- [ ] 3.4 Implement logic to categorize capabilities into appropriate sections based on provider availability
- [ ] 3.5 Add descriptive help text to each section explaining its purpose
- [ ] 3.6 Apply table layout to each of the three sections

**QA Checkpoint 3**: Manual verification of section organization, help text, and capability categorization
