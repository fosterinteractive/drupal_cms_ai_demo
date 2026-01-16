## ADDED Requirements

### Requirement: Sectioned Page Organization
The AI Settings page SHALL be organized into distinct sections with headings and help text, separating capabilities by availability and architectural role.

#### Scenario: Page displays three organized sections
- **WHEN** an administrator navigates to `/admin/config/ai/settings`
- **THEN** they see three distinct sections:
  1. "AI Capabilities from Installed Providers" - capabilities ready to configure
  2. "Additional AI Capabilities" - capabilities available if more providers are installed
  3. "Vector Data Capabilities" - embeddings and vector database configuration
- **AND** each section has a heading and descriptive help text explaining its purpose

#### Scenario: Installed capabilities section shows configurable capabilities
- **WHEN** the administrator views the "AI Capabilities from Installed Providers" section
- **THEN** it displays only capabilities that have at least one installed provider available
- **AND** provider/model dropdowns are enabled for selection

#### Scenario: Additional capabilities section shows unavailable capabilities
- **WHEN** the administrator views the "Additional AI Capabilities" section
- **THEN** it displays capabilities that have no installed providers
- **AND** indicates which provider modules could be installed to enable each capability

#### Scenario: Vector data section is architecturally separate
- **WHEN** the administrator views the "Vector Data Capabilities" section
- **THEN** it displays embeddings provider configuration separate from inference capabilities
- **AND** it displays vector database provider configuration
- **AND** help text explains that vector databases are infrastructure distinct from AI inference

### Requirement: Table-Based Capability Layout
Each section SHALL display its capabilities in a table format with columns for Capability, Provider, Model, and Info, replacing the current vertical fieldset layout. Capabilities SHALL be sorted alphabetically by label within each section.

#### Scenario: Each section displays capabilities in table format
- **WHEN** an administrator views any capability section
- **THEN** they see a table with columns: Capability, Provider, Model, Info
- **AND** each row shows the capability name/description, provider dropdown, model dropdown, and info link
- **AND** capabilities are sorted alphabetically by their label (e.g., "Chat" before "Chat with Complex JSON" before "Moderation")

#### Scenario: Table maintains AJAX model loading
- **WHEN** an administrator changes the provider selection in a table row
- **THEN** the model dropdown in that same row updates via AJAX to show available models for the selected provider

#### Scenario: Table supports non-JavaScript fallback
- **WHEN** JavaScript is disabled
- **THEN** a "Choose Model" button appears in each row to trigger a form rebuild and load models for the selected provider

### Requirement: Provider Info Links
The AI Settings capabilities table SHALL include an "Info" column displaying links to provider documentation or pricing pages. URLs are sourced from optional YAML metadata files provided by contrib AI provider modules.

#### Scenario: Provider has info URL in metadata
- **WHEN** a provider is selected that has a `pricing_url` or `documentation_url` defined in its YAML metadata file
- **THEN** the Info column displays a clickable link icon that opens the URL in a new tab

#### Scenario: Provider has no metadata file
- **WHEN** a provider is selected that has no YAML metadata file or no URL defined
- **THEN** the Info column displays blank (graceful degradation)

#### Scenario: Info link accessibility
- **WHEN** an info link is rendered
- **THEN** it includes `target="_blank"` and `rel="noopener noreferrer"` attributes
- **AND** includes an aria-label using the pattern `"<Model Name> Pricing"` or `"<Model Name> Info"` (e.g., "gpt-4o Pricing")
- **AND** the aria-label text is wrapped in Drupal's translation function `t()` for internationalization
