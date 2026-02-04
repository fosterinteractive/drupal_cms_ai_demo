## Context

The `/admin/config/ai` page uses `SystemController::systemAdminMenuBlockPage()`, which delegates to `SystemManager::getBlockContents()`. This loads child menu links from `ai.links.menu.yml`, checks access, and renders them as a flat `admin_block_content` themed list. There are currently ~8 top-level child links (AI Default Settings, Provider Settings, Vector DBs Settings, AI External Moderation, AI Prompts, AI Prompt Types, AI Guardrails, AI Guardrail Sets) plus items contributed by submodules.

The AI module already has two custom controllers (`ToolsLibrary`, `ProviderSetupList`) and a `ai/ai_global` CSS library with shared utility classes. The `ai_global.css` file contains typography, icon, and layout utilities that follow Claro/Gin patterns.

Contrib submodules (e.g., `ai_chatbot`, `ai_search`, `ai_canvas`, `ai_seo`, etc.) each define their own menu links parented under `ai.admin_settings`. The new overview page must discover and categorize these dynamically based on a hardcoded mapping.

## Goals / Non-Goals

**Goals:**
- Replace the flat link list with a 4-section categorized layout
- Provide a hardcoded item-to-category mapping that covers all known AI ecosystem items
- Use CSS container queries (`<600px` = 1 column, `≥600px` = 2 columns) for responsive layout
- Respect existing route permissions for item visibility
- Hide empty sections/subcategories entirely

**Non-Goals:**
- Changing any child page behavior or layout (e.g., `/admin/config/ai/settings` is untouched)
- Creating new permissions
- Dynamic/configurable categorization (mapping is hardcoded for now)
- Adding icons to items (no icons for MVP)
- Modifying the `ai.links.menu.yml` structure

## Decisions

### 1. Custom controller replacing SystemController

**Decision**: Create a new `AiOverviewController` class in `modules/ai/src/Controller/` with a single method that builds the categorized render array.

**Why**: `SystemController::systemAdminMenuBlockPage()` produces a flat list with no hook for grouping. A custom controller gives full control over the structure. This follows the same pattern as the existing `ProviderSetupList` controller.

**Route change**: Update `ai.routing.yml` to point `ai.settings.menu` at `\Drupal\ai\Controller\AiOverviewController::overview`.

**Alternative considered**: Using `hook_menu_alter` or preprocess hooks to restructure the existing output. Rejected because the `admin_block_content` theme is too rigid — it renders a flat `<dl>` with no grouping semantics.

### 2. Hardcoded item-to-category mapping via a data method

**Decision**: Define the mapping as a static method or constant within the controller. Each entry maps a route name to a section ID, optional subcategory ID, display label, and description.

**Why**: The set of AI ecosystem items is relatively stable and controlled by the AI Initiative. A hardcoded mapping avoids the complexity of a plugin/annotation system while keeping the mapping centralized and easy to update. Items not in the mapping are excluded from the overview.

**Structure**:
```php
[
  'ai.settings_form' => [
    'section' => 'ai_infrastructure',
    'label' => 'Default Models for AI Operations',
    'description' => 'Select default providers and models...',
  ],
  'ai_chatbot.settings' => [
    'section' => 'features',
    'subcategory' => 'people_accounts',
    'label' => 'AI Chatbot',
    'description' => 'The chat widget interface...',
  ],
  // ...
]
```

**Fallback**: Items whose route is not in the mapping (e.g., a new contrib module) are placed in a **Miscellaneous** section at the bottom, using their menu link title and description as-is. This ensures contrib modules are never invisible — they appear until a mapping entry is added for them.

**Alternative considered**: Using menu link descriptions from `*.links.menu.yml` instead of hardcoded descriptions. Rejected because the existing descriptions are developer-oriented ("Configure the core AI Settings") rather than benefit-oriented ("Select default providers and models for each AI capability"). Miscellaneous items are the exception — they use the menu link title/description since no hardcoded entry exists.

### 3. Route discovery via menu tree

**Decision**: Use `MenuTreeParameters` to load child links of `ai.admin_settings` (same as `SystemManager::getAdminBlock()`), then match each link's route name against the mapping. This preserves Drupal's permission checking via `menu.default_tree_manipulators:checkAccess`.

**Why**: This reuses Drupal's existing access control without reimplementing permission checks. Items the user cannot access are automatically excluded.

### 4. Custom Twig template

**Decision**: Create `templates/ai-overview-page.html.twig` in the `ai` module with the section/subcategory/item structure. Register via `ai.module` `hook_theme()`.

**Template variables**:
- `sections`: Array of section render data, each containing `id`, `title`, `items`
- Each item: `label`, `description`, `url`

**Why**: A dedicated template keeps presentation separate from the controller logic and allows theme override by site builders. All sections are at the same level (no nested subcategories) — the feature categories are promoted to top-level sections.

### 5. CSS container queries for responsive layout

**Decision**: Add CSS to `ai_global.css` using `container-type: inline-size` on the overview page wrapper and `@container` queries at `600px` breakpoint.

**Why**: The admin theme (Claro, Gin, or custom) controls the outer container width. Media queries would break if the admin theme uses a narrow sidebar layout. Container queries respond to the actual available width.

**Structure**:
```css
.ai-overview {
  container-type: inline-size;
}

.ai-overview__grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: 1rem;
}

@container (min-width: 600px) {
  .ai-overview__grid {
    grid-template-columns: 1fr 1fr;
  }
}
```

### 6. Section and item ordering

**Decision**: Sections follow a fixed order defined by the controller (not alphabetized). Items within each section/subcategory are alphabetized by display label using PHP `usort`.

**Why**: The section order communicates the ecosystem hierarchy (infrastructure → tools → features). Item alphabetization within sections makes individual items easy to find.

### 7. Empty section handling

**Decision**: After filtering items by access, sections/subcategories with zero items are excluded from the render array entirely. No empty state markup is generated.

**Why**: Empty sections with no content add visual noise and confusion. If a user can't access any items in a section, that section isn't relevant to them.

## Risks / Trade-offs

**[Hardcoded mapping becomes stale]** → New contrib submodules won't appear on the overview until the mapping is updated. Mitigation: document the mapping clearly so contrib maintainers can submit patches. Consider a follow-up to support a plugin-based approach.

**[Route names change in contrib]** → If a submodule renames a route, the mapping breaks silently (item disappears). Mitigation: items not found in the menu tree are simply omitted — no errors. Periodic review during releases.

**[Container query browser support]** → CSS container queries are supported in all modern browsers but not IE11. Mitigation: IE11 is not supported by Drupal 10+. The single-column fallback (default grid) provides a usable experience for any edge case.

**[Menu link completeness]** → Every child link under `ai.admin_settings` is rendered: mapped items appear in their assigned section, unmapped items fall into Miscellaneous. No menu links are lost. The existing `ai.links.menu.yml` entries remain for breadcrumb/menu trail purposes as well.
