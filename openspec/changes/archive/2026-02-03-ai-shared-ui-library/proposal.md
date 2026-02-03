## Why

Multiple AI submodules are independently solving identical UI challenges, resulting in inconsistent component implementations, duplicated development effort, and potential CSS conflicts with admin themes. The AiSettingsForm currently misuses `.form-item__description` for styling and uses text-only info links that lack visual affordance.

## What Changes

- Create `ai/assets/css/ai-global.css` with:
  - Typography utility classes (`.ai-font-size-s`, `.ai-font-size-xs`, `.ai-font-size-xxs`)
  - Semantic helper class (`.ai-description`) combining font-size and muted color
  - Table styling for capability tables (`.ai-capabilities-table`, etc.)
  - Icon-based link styling with hover/focus/active states
- Create `ai/assets/icons/` directory with Phosphor SVG icons:
  - `circles-three-plus.svg` for provider links
  - `sphere.svg` for model links
- Refactor `AiSettingsForm.php`:
  - Replace `.form-item__description` with `.ai-description`
  - Update `buildInfoLink()` to render icon-only links with accessible labels
- Update `ai_settings_form.js`:
  - Modify dynamic link creation to use icon pattern with visually-hidden text
- Update `ai.libraries.yml`:
  - Add `ai/ai_global` library referencing new CSS

## Capabilities

### New Capabilities

- `ai-global-ui`: Shared CSS library and icon system for AI module UI components. Provides typography utilities, table styling, and accessible icon-based links using Drupal core CSS variables.

### Modified Capabilities

<!-- No existing capabilities are being modified at the spec level -->

## Impact

- **Files modified**:
  - `modules/ai/src/Form/AiSettingsForm.php` - link rendering changes
  - `modules/ai/assets/js/ai_settings_form.js` - dynamic link creation
  - `modules/ai/ai.libraries.yml` - new library definition
- **Files created**:
  - `modules/ai/assets/css/ai-global.css`
  - `modules/ai/assets/icons/circles-three-plus.svg`
  - `modules/ai/assets/icons/sphere.svg`
- **Dependencies**: Phosphor Icons (MIT license) for SVG assets
- **Accessibility**: Links must include visually-hidden text for screen readers per Drupal standards
