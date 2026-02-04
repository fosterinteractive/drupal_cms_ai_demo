## 1. Route and Controller Setup

- [ ] 1.1 Create `AiOverviewController` class in `modules/ai/src/Controller/AiOverviewController.php` with an `overview()` method that returns an empty render array initially
- [ ] 1.2 Update `ai.routing.yml` to change the `ai.settings.menu` route controller from `SystemController::systemAdminMenuBlockPage` to `AiOverviewController::overview`
- [ ] 1.3 Verify the route change works — navigating to `/admin/config/ai` renders the new controller without errors

## 2. Item-to-Category Mapping

- [ ] 2.1 Discover all route names for mapped items by searching `*.routing.yml` files across AI ecosystem modules (ai, ai_chatbot, ai_search, ai_canvas, ai_seo, ai_agents, ai_assistants, ai_automations, ai_translate, ai_image_alt, ai_observability, ai_theme_assistant, ai_content_recommendations, etc.)
- [ ] 2.2 Define the hardcoded mapping as a method in `AiOverviewController` — each entry maps a route name to section ID, optional subcategory ID, display label, and benefit-oriented description
- [ ] 2.3 Define the section hierarchy (9 sections in fixed order: AI Infrastructure, Vector Search Infrastructure, Tools & Automation, Content Creation & Support, People Accounts & Users, Safety & Compliance, Search & Discovery, Site Building & Design, Miscellaneous) as structured data in the controller

## 3. Menu Tree Loading and Access Filtering

- [ ] 3.1 Inject `MenuLinkTreeInterface` and `MenuActiveTrailInterface` services into `AiOverviewController`
- [ ] 3.2 Implement menu tree loading in `overview()` — load child links of `ai.admin_settings` using `MenuTreeParameters`, apply `checkAccess` and `generateIndexAndSort` manipulators
- [ ] 3.3 Match loaded menu links against the item-to-category mapping by route name, collecting accessible items into their assigned sections/subcategories. Place unmapped items into the Miscellaneous section using their menu link title and description
- [ ] 3.4 Sort items alphabetically by display label within each section/subcategory
- [ ] 3.5 Filter out empty sections and subcategories (zero accessible items) from the render data

## 4. Twig Template

- [ ] 4.1 Register the `ai_overview_page` theme hook in `ai.module` via `hook_theme()` with the `sections` variable
- [ ] 4.2 Create `modules/ai/templates/ai-overview-page.html.twig` — render all sections uniformly with `<h2>` headings and `<h3>` item labels in a grid
- [ ] 4.3 Render each item as a card with linked label (`<a>` wrapping heading text) and `<p>` description using appropriate CSS classes (`ai-overview__item`, `ai-overview__item-label`, `ai-overview__item-description`)
- [ ] 4.4 Wrap the page in `.ai-overview` container and each section's items in `.ai-overview__grid` for container query layout

## 5. Controller Render Array

- [ ] 5.1 Build the render array in `overview()` using `#theme => 'ai_overview_page'` with the categorized sections data
- [ ] 5.2 Attach the `ai/ai_global` library to the render array
- [ ] 5.3 Set the page title to "AI" (preserving existing title)

## 6. CSS Styles

- [ ] 6.1 Add `.ai-overview` container query declaration (`container-type: inline-size`) to `ai_global.css`
- [ ] 6.2 Add `.ai-overview__grid` with single-column default (`grid-template-columns: 1fr`) and 2-column `@container (min-width: 600px)` rule
- [ ] 6.3 Add `.ai-overview__section` spacing and `.ai-overview__section-title` styling with visual separator (bottom border)
- [ ] 6.4 Add `.ai-overview__subcategory-title` styling (visually smaller than section title)
- [ ] 6.5 Add `.ai-overview__item` card styling, `.ai-overview__item-label` link with hover/focus states, and `.ai-overview__item-description` muted text styling

## 7. Verification

- [ ] 7.1 Verify all nine sections render in correct fixed order with `<h2>` headings
- [ ] 7.2 Verify item labels use `<h3>` headings consistently across all sections
- [ ] 7.3 Verify items are alphabetized within each section/subcategory
- [ ] 7.4 Verify empty sections/subcategories are hidden when modules are not installed
- [ ] 7.5 Verify unmapped contrib module items appear in the Miscellaneous section with their menu link title and description
- [ ] 7.5 Verify container query responsive behavior — single column below 600px, two columns at 600px+
- [ ] 7.6 Verify item links navigate to correct target routes
- [ ] 7.7 Verify permission-based visibility — items for inaccessible routes are hidden
