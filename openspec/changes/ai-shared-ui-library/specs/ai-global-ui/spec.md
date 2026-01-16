## ADDED Requirements

### Requirement: Typography utility classes

The system SHALL provide CSS utility classes for typography that reference Drupal core CSS custom properties.

The following classes SHALL be available:
- `.ai-font-size-s` - maps to `var(--font-size-s)` (~14px)
- `.ai-font-size-xs` - maps to `var(--font-size-xs)` (~13px)
- `.ai-font-size-xxs` - maps to `var(--font-size-xxs)` (~11px)

#### Scenario: Typography class applies correct font size
- **WHEN** an element has class `ai-font-size-xxs`
- **THEN** the element's font-size SHALL be `var(--font-size-xxs)`

#### Scenario: Typography works with Claro theme
- **WHEN** the Claro admin theme is active
- **THEN** typography classes SHALL resolve to Claro's CSS variable values

#### Scenario: Typography works with Gin theme
- **WHEN** the Gin admin theme is active
- **THEN** typography classes SHALL resolve to Gin's CSS variable values

---

### Requirement: Semantic description class

The system SHALL provide a semantic `.ai-description` class that combines typography and color styling for descriptive text.

The `.ai-description` class SHALL:
- Set font-size to `var(--font-size-xs)`
- Set color to `var(--color-text-light)` or equivalent muted text color

#### Scenario: Description class applies combined styles
- **WHEN** an element has class `ai-description`
- **THEN** the element SHALL have reduced font size and muted text color

#### Scenario: Capability descriptions use ai-description class
- **WHEN** AiSettingsForm renders a capability description
- **THEN** the description text SHALL use the `ai-description` class instead of `form-item__description`

---

### Requirement: Icon-based info links

The system SHALL render provider and model info links as icon-only links with accessible labels.

Provider links SHALL display the `circles-three-plus` Phosphor icon.
Model links SHALL display the `sphere` Phosphor icon.

#### Scenario: Provider info link displays icon
- **WHEN** a provider info link is rendered in the capabilities table
- **THEN** the link SHALL display the circles-three-plus icon
- **AND** the link text "Provider Info" SHALL be visually hidden but accessible to screen readers

#### Scenario: Model info link displays icon
- **WHEN** a model info link is rendered in the capabilities table
- **THEN** the link SHALL display the sphere icon
- **AND** the link text "Model Info" SHALL be visually hidden but accessible to screen readers

#### Scenario: Icons inherit color from CSS
- **WHEN** an icon link is rendered
- **THEN** the icon color SHALL be controlled by CSS (using `currentColor`)

---

### Requirement: Accessible icon implementation

All icon-only links SHALL be accessible to users of assistive technologies.

Each icon link SHALL:
- Contain a `<span class="visually-hidden">` element with descriptive text
- Include the provider name or model name in the hidden text for context
- Have sufficient color contrast in all states

#### Scenario: Screen reader announces provider link
- **WHEN** a screen reader user focuses on a provider info link for "OpenAI"
- **THEN** the screen reader SHALL announce "OpenAI Provider Information" or equivalent contextual text

#### Scenario: Screen reader announces model link
- **WHEN** a screen reader user focuses on a model info link for "gpt-4"
- **THEN** the screen reader SHALL announce "gpt-4 Model Information" or equivalent contextual text

---

### Requirement: Icon link interactive states

Icon links SHALL have distinct visual states for user interaction feedback.

The following states SHALL be visually distinct:
- Default state
- Hover state (mouse over)
- Focus state (keyboard navigation)
- Active state (being clicked/pressed)

#### Scenario: Hover state changes icon appearance
- **WHEN** user hovers over an icon link
- **THEN** the icon color SHALL change to indicate interactivity

#### Scenario: Focus state shows visible indicator
- **WHEN** user focuses an icon link via keyboard
- **THEN** the link SHALL display a visible focus indicator (outline or ring)
- **AND** the focus indicator SHALL meet WCAG 2.1 focus visibility requirements

#### Scenario: Active state provides feedback
- **WHEN** user clicks/presses an icon link
- **THEN** the icon SHALL provide visual feedback (color change or scale)

---

### Requirement: Icon asset files

The system SHALL store icon SVG files in `ai/assets/icons/` directory.

Each icon file SHALL:
- Be a valid SVG file
- Use `fill="currentColor"` to allow CSS color control
- Use a 256x256 viewBox for consistent scaling
- Be sourced from Phosphor Icons (MIT license)

#### Scenario: Provider icon file exists
- **WHEN** the ai-global.css is loaded
- **THEN** the file `ai/assets/icons/circles-three-plus.svg` SHALL exist and be valid SVG

#### Scenario: Model icon file exists
- **WHEN** the ai-global.css is loaded
- **THEN** the file `ai/assets/icons/sphere.svg` SHALL exist and be valid SVG

---

### Requirement: Library definition

The system SHALL define an `ai/ai_global` library in `ai.libraries.yml` that includes the shared CSS.

#### Scenario: Library loads ai-global.css
- **WHEN** a page attaches the `ai/ai_global` library
- **THEN** the `ai-global.css` file SHALL be loaded

#### Scenario: Settings form uses global library
- **WHEN** AiSettingsForm is rendered
- **THEN** the `ai/ai_global` library SHALL be attached

---

### Requirement: Extensible icon system

The icon system SHALL support adding new icons without modifying existing CSS structure.

New icons SHALL be added by:
1. Adding SVG file to `ai/assets/icons/`
2. Adding CSS class (`.ai-info-link--<type>`) with background-image reference

#### Scenario: Adding a new icon type
- **WHEN** a developer needs to add a "settings" icon
- **THEN** they SHALL add `settings.svg` to icons directory
- **AND** add `.ai-info-link--settings` class to ai-global.css
- **AND** no changes to existing icon classes SHALL be required

---

### Requirement: Dynamic link creation in JavaScript

The `ai_settings_form.js` file SHALL create icon-based links dynamically when model selection changes.

Dynamically created links SHALL:
- Use the same HTML structure as server-rendered links
- Include visually-hidden accessible text
- Apply appropriate icon class based on link type

#### Scenario: JavaScript creates accessible model link
- **WHEN** user selects a model from dropdown
- **AND** JavaScript creates a model info link dynamically
- **THEN** the link SHALL include `<span class="visually-hidden">` with model name
- **AND** the link SHALL have class `ai-info-link ai-info-link--model`
