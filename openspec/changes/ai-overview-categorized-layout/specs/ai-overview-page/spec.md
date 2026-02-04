## ADDED Requirements

### Requirement: Categorized section hierarchy

The `/admin/config/ai` overview page SHALL display items grouped into sections in the following fixed order:

1. **AI Infrastructure** (id: `ai_infrastructure`)
2. **Vector Search Infrastructure** (id: `vector_search_infrastructure`)
3. **Tools & Automation** (id: `tools_automation`)
4. **Content Creation & Support** (id: `content_creation`)
5. **People, Accounts & Users** (id: `people_accounts`)
6. **Safety & Compliance** (id: `safety_compliance`)
7. **Search & Discovery** (id: `search_discovery`)
8. **Site Building & Design** (id: `site_building`)
9. **Miscellaneous** (id: `miscellaneous`)

All sections are rendered at the same level. There is no parent "Features" heading — the feature categories (4–8) are top-level sections alongside infrastructure and tools sections.

Sections SHALL NOT be alphabetized. The order SHALL be fixed as listed above.
The Miscellaneous section SHALL always appear last.

#### Scenario: Overview page displays sections in fixed order
- **WHEN** an administrator navigates to `/admin/config/ai`
- **THEN** they SHALL see sections in this exact order: AI Infrastructure, Vector Search Infrastructure, Tools & Automation, Content Creation & Support, People Accounts & Users, Safety & Compliance, Search & Discovery, Site Building & Design, Miscellaneous
- **AND** only sections with visible items SHALL be rendered

#### Scenario: Section order is not alphabetical
- **WHEN** the overview page is rendered
- **THEN** the sections SHALL appear in the fixed hierarchy order regardless of the alphabetical order of their names

---

### Requirement: Item alphabetical ordering within sections

Items within each section or subcategory SHALL be sorted alphabetically by their display label.

#### Scenario: Items within AI Infrastructure are alphabetized
- **WHEN** the AI Infrastructure section contains "Prompt Library", "AI Platform Providers", and "Default Models for AI Operations"
- **THEN** they SHALL appear in this order: AI Platform Providers, Default Models for AI Operations, Prompt Library

#### Scenario: Items within a feature subcategory are alphabetized
- **WHEN** the Site Building & Design subcategory contains "AI Theme Assistant" and "AI Canvas Page Builder"
- **THEN** they SHALL appear in this order: AI Canvas Page Builder, AI Theme Assistant

---

### Requirement: Item card display

Each item SHALL be rendered as a card containing a linked label and a description.

The label SHALL be rendered as a heading element wrapped in an `<a>` link to the item's target route.
The description SHALL be rendered as a `<p>` element with muted/secondary text styling.

#### Scenario: Item displays label and description
- **WHEN** an item is rendered on the overview page
- **THEN** it SHALL display the item label as a linked heading
- **AND** it SHALL display the item description as secondary text below the label

#### Scenario: Item label links to target route
- **WHEN** a user clicks on an item label (e.g., "AI Platform Providers")
- **THEN** they SHALL be navigated to that item's configuration page

---

### Requirement: Section headers

All section headings SHALL use `<h2>` heading elements.
All item labels SHALL use `<h3>` heading elements.
Each section heading SHALL have a visual separator (border or spacing) below it.

#### Scenario: Section heading uses h2
- **WHEN** any section heading is rendered (e.g., "AI Infrastructure", "Content Creation & Support")
- **THEN** it SHALL be an `<h2>` element

#### Scenario: Item label uses h3
- **WHEN** any item label is rendered within a section
- **THEN** it SHALL be an `<h3>` element

---

### Requirement: Custom controller

The `/admin/config/ai` route SHALL use a custom `AiOverviewController::overview` method instead of `SystemController::systemAdminMenuBlockPage`.

The controller SHALL:
- Load child menu links of `ai.admin_settings` via the menu tree system
- Check access permissions using `menu.default_tree_manipulators:checkAccess`
- Match each link's route name against the hardcoded item-to-category mapping
- Build a render array with the categorized structure
- Attach the `ai/ai_global` library

#### Scenario: Route uses custom controller
- **WHEN** a user navigates to `/admin/config/ai`
- **THEN** the page SHALL be rendered by `AiOverviewController::overview`
- **AND** the page title SHALL remain "AI"

#### Scenario: Controller uses menu tree for access checking
- **WHEN** the controller builds the overview page
- **THEN** it SHALL load menu links using `MenuTreeParameters`
- **AND** it SHALL apply `checkAccess` manipulator to respect existing permissions

---

### Requirement: Custom Twig template

The overview page SHALL use a custom `ai-overview-page.html.twig` template registered via `hook_theme()`.

The template SHALL receive a `sections` variable containing the categorized structure.

#### Scenario: Template is registered
- **WHEN** Drupal's theme registry is rebuilt
- **THEN** the `ai_overview_page` theme hook SHALL be registered with the `ai` module

#### Scenario: Template renders sections
- **WHEN** the template receives the sections data
- **THEN** it SHALL render each section with its heading, items grid, and any subcategories

---

### Requirement: Item-to-category mapping

The controller SHALL maintain a hardcoded mapping of route names to categories. The mapping SHALL include the following items:

**AI Infrastructure:**
| Label | Route |
|---|---|
| AI Platform Providers | discover from codebase |
| Default Models for AI Operations | discover from codebase |
| Prompt Library | discover from codebase |

**Vector Search Infrastructure:**
| Label | Route |
|---|---|
| Vector Database Configuration | discover from codebase |
| Embedding Service | discover from codebase |

**Tools & Automation:**
| Label | Route |
|---|---|
| AI Agents | discover from codebase |
| AI Assistants | discover from codebase |
| AI Automation Workflows | discover from codebase |
| AI Moderation Guardrails | discover from codebase |

**Content Creation & Support:**
| Label | Route |
|---|---|
| AI Image Alt Text | discover from codebase |
| AI SEO Tools | discover from codebase |
| AI Translation | discover from codebase |

**People, Accounts & Users:**
| Label | Route |
|---|---|
| AI Chatbot | discover from codebase |

**Safety & Compliance:**
| Label | Route |
|---|---|
| AI Observability | discover from codebase |

**Search & Discovery:**
| Label | Route |
|---|---|
| AI Related Content | discover from codebase |

**Site Building & Design:**
| Label | Route |
|---|---|
| AI Canvas Page Builder | discover from codebase |
| AI Theme Assistant | discover from codebase |

Each mapping entry SHALL include a benefit-oriented description string.

Items whose route is not present in the loaded menu tree (module not installed) SHALL be omitted.
Items not present in the mapping SHALL be placed in the Miscellaneous section using their menu link title and description.

#### Scenario: Installed module item appears in correct section
- **WHEN** the `ai_chatbot` module is installed and its route is accessible
- **THEN** "AI Chatbot" SHALL appear in the People, Accounts & Users section

#### Scenario: Uninstalled module item is omitted
- **WHEN** a module providing a mapped route is not installed
- **THEN** that item SHALL NOT appear on the overview page
- **AND** no error SHALL be generated

#### Scenario: Unmapped contrib module appears in Miscellaneous
- **WHEN** a menu link exists under `ai.admin_settings` but its route is not in the hardcoded mapping
- **THEN** that item SHALL appear in the Miscellaneous section
- **AND** the item label SHALL use the menu link's title
- **AND** the item description SHALL use the menu link's description

#### Scenario: Miscellaneous items are alphabetized
- **WHEN** multiple unmapped items exist
- **THEN** they SHALL be sorted alphabetically by their menu link title within the Miscellaneous section

---

### Requirement: Empty section hiding

If a section has zero visible items after access filtering, the entire section SHALL be hidden.

The page SHALL NOT display:
- Empty section headers
- "No items available" messages
- Install prompts

#### Scenario: Section with no accessible items is hidden
- **WHEN** no items in the "Vector Search Infrastructure" section are accessible to the current user
- **THEN** the entire "Vector Search Infrastructure" section SHALL NOT be rendered

#### Scenario: Feature category section with no items is hidden
- **WHEN** no items in "Safety & Compliance" are accessible to the current user
- **THEN** the "Safety & Compliance" section SHALL NOT be rendered
- **AND** other sections with items SHALL still be rendered

---

### Requirement: Permission-based visibility

Item visibility SHALL respect existing route permissions. No new permissions SHALL be created.

Each item's visibility SHALL be determined by the user's access to that item's target route, as checked by Drupal's menu tree access system.

#### Scenario: User without permission does not see item
- **WHEN** a user does not have permission to access a specific item's route
- **THEN** that item SHALL NOT appear on the overview page

#### Scenario: Admin user sees all installed items
- **WHEN** a user with the `administer ai` permission views the overview page
- **AND** all AI modules are installed
- **THEN** all mapped items SHALL be visible
