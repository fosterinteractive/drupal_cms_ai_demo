## Why

The `/admin/config/ai` overview page currently renders a flat, unorganized list of links using Drupal's default `systemAdminMenuBlockPage` controller. This mixes foundational infrastructure settings with high-level tools and end-user features, making it difficult for site builders to navigate and understand the AI ecosystem hierarchy. Reorganizing into a categorized layout communicates the relationship between layers: infrastructure enables tools, tools execute features, features deliver value.

Reference: https://www.drupal.org/project/ai_initiative/issues/3560199

## What Changes

- Replace the default `systemAdminMenuBlockPage` rendering of `/admin/config/ai` with a custom controller that groups items into a fixed 4-section hierarchy: AI Infrastructure, Vector Search Infrastructure, Tools & Automation, Features
- Within the Features section, items are further grouped into 5 fixed-order subcategories: Content Creation & Support, People Accounts & Users, Safety & Compliance, Search & Discovery, Site Building & Design
- Each item displays as a card with a linked label and benefit-oriented description text
- Items within each section/subcategory are alphabetized by display label
- Desktop layout uses a 2-column grid per section; mobile stacks to single column
- Sections/subcategories with zero visible items are hidden entirely (no empty states)
- Item visibility respects existing route permissions — no new permissions created
- Each item has a hardcoded mapping to its category, description, and route target

## Capabilities

### New Capabilities
- `ai-overview-page`: Categorized layout for the `/admin/config/ai` overview page with 4-section hierarchy, item-to-category mapping, card-based rendering, responsive grid, and permission-based visibility

### Modified Capabilities
- `ai-global-ui`: Additional CSS classes/styles needed for the overview page card layout, section headers, and responsive 2-column grid. Use CSS container queries for this as the admin theme will change the width of the outer container and we don't know how wide it will be.

## Impact

- **Routing**: The `/admin/config/ai` route changes from `SystemController::systemAdminMenuBlockPage` to a custom controller
- **Menu links**: Existing `ai.links.menu.yml` entries remain but the overview page no longer relies on Drupal's default menu block rendering
- **Templates**: New Twig template(s) for the categorized overview layout
- **CSS**: New styles in `ai-global.css` or a new library for section/card/grid layout
- **Dependencies**: No new module dependencies — uses existing Drupal core patterns (Claro/Gin compatible)
- **Existing specs**: `ai-settings-ui` (the `/admin/config/ai/settings` page) is unaffected — this change only touches the parent overview page
