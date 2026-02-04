## ADDED Requirements

### Requirement: Overview page container query layout

The `ai_global.css` file SHALL provide CSS classes for the overview page responsive grid using CSS container queries.

The `.ai-overview` wrapper element SHALL declare `container-type: inline-size`.

The `.ai-overview__grid` element SHALL:
- Default to a single-column grid layout (`grid-template-columns: 1fr`)
- Switch to a 2-column grid (`grid-template-columns: 1fr 1fr`) when the container width is `≥600px` via `@container (min-width: 600px)`

#### Scenario: Single column below 600px container width
- **WHEN** the `.ai-overview` container is narrower than 600px
- **THEN** `.ai-overview__grid` SHALL display items in a single column

#### Scenario: Two columns at or above 600px container width
- **WHEN** the `.ai-overview` container is 600px or wider
- **THEN** `.ai-overview__grid` SHALL display items in two equal columns

#### Scenario: Layout adapts to Claro theme sidebar
- **WHEN** the Claro admin theme renders the overview page with a sidebar that constrains available width below 600px
- **THEN** the grid SHALL fall back to single-column layout

#### Scenario: Layout adapts to Gin theme
- **WHEN** the Gin admin theme renders the overview page with a wider content area
- **THEN** the grid SHALL use the 2-column layout if the container is ≥600px

---

### Requirement: Overview page section styling

The `ai_global.css` file SHALL provide CSS classes for overview page sections and headings.

The following classes SHALL be available:
- `.ai-overview__section` — section wrapper with bottom margin/spacing
- `.ai-overview__section-title` — styling for `<h2>` section headings

Section headings SHALL include a visual separator (bottom border or spacing).

#### Scenario: Section title has visual separator
- **WHEN** a section heading is rendered with `.ai-overview__section-title`
- **THEN** it SHALL have a visible bottom border or spacing separating it from the items below

---

### Requirement: Overview page item card styling

The `ai_global.css` file SHALL provide CSS classes for overview page item cards.

The following classes SHALL be available:
- `.ai-overview__item` — card wrapper
- `.ai-overview__item-label` — linked heading styling
- `.ai-overview__item-description` — description text with muted color

The item label link SHALL have hover and focus states that indicate interactivity.

#### Scenario: Item card displays as a distinct block
- **WHEN** an item is rendered with `.ai-overview__item`
- **THEN** it SHALL be visually distinct as a card or block element

#### Scenario: Item label has hover state
- **WHEN** a user hovers over an `.ai-overview__item-label` link
- **THEN** the link SHALL provide visual feedback (color change or underline)

#### Scenario: Item label has focus state
- **WHEN** a user focuses an `.ai-overview__item-label` link via keyboard
- **THEN** the link SHALL display a visible focus indicator meeting WCAG 2.1 requirements

#### Scenario: Item description uses muted styling
- **WHEN** an item description is rendered with `.ai-overview__item-description`
- **THEN** it SHALL use a muted text color and smaller font size

---

## MODIFIED Requirements

### Requirement: Library definition

The system SHALL define an `ai/ai_global` library in `ai.libraries.yml` that includes the shared CSS.

#### Scenario: Library loads ai-global.css
- **WHEN** a page attaches the `ai/ai_global` library
- **THEN** the `ai-global.css` file SHALL be loaded

#### Scenario: Settings form uses global library
- **WHEN** AiSettingsForm is rendered
- **THEN** the `ai/ai_global` library SHALL be attached

#### Scenario: Overview page uses global library
- **WHEN** the AI overview page at `/admin/config/ai` is rendered
- **THEN** the `ai/ai_global` library SHALL be attached
