## Context

The AI module's AiSettingsForm currently renders capability tables with info links using text-only labels ("Provider Info", "Model Info") and incorrectly applies `.form-item__description` for styling small text. This class is semantically intended for form field helper text, not general typography in tables.

The form already outputs `ai-*` prefixed CSS classes (`.ai-capabilities-table`, `.ai-capability-cell`, `.ai-info-link`, etc.) but no corresponding CSS file exists to style them - they rely on default Drupal table styling.

Drupal's Claro and Gin admin themes provide CSS custom properties for typography (`--font-size-xs`, `--font-size-xxs`, etc.) that we can leverage for consistent styling across admin themes.

## Goals / Non-Goals

**Goals:**
- Create a shared CSS library (`ai-global.css`) for AI module UI components
- Provide typography utility classes that use Drupal core CSS variables
- Replace text-only info links with accessible icon-based links
- Establish an extensible icon system for future AI module needs
- Follow Drupal accessibility standards for icon-only interactive elements

**Non-Goals:**
- Full component library (buttons, modals, etc.) - focus on immediate table/link needs
- Tooltip system - deferred to future change
- JavaScript framework or custom elements - pure CSS + minimal JS updates
- Styling for non-AI-settings pages - focus on AiSettingsForm only

## Decisions

### 1. Icon Library: Phosphor Icons

**Decision**: Use Phosphor Icons (MIT licensed) for SVG icons.

**Alternatives considered**:
- Drupal CMS Design System icons - not yet stable/published
- Heroicons - limited icon variety for our use case
- Custom SVGs - maintenance burden

**Rationale**: Phosphor provides a comprehensive, consistent icon set with MIT license. Icons use `fill="currentColor"` allowing CSS color control. The `circles-three-plus` and `sphere` icons match our semantic needs (provider = multiple sources, model = contained/complete entity).

### 2. CSS Architecture: Utility Classes + Drupal Variables

**Decision**: Create utility classes that reference Drupal core CSS custom properties. Class names match the CSS variable names for consistency.

```css
.ai-font-size-xxs { font-size: var(--font-size-xxs); }
.ai-font-size-xs { font-size: var(--font-size-xs); }
.ai-font-size-s { font-size: var(--font-size-s); }
.ai-description {
  font-size: var(--font-size-xs);
  color: var(--color-text-light);
}
```

**Variable priority**:
1. Drupal core CSS variables (highest priority)
2. Claro theme variables (if no core equivalent)
3. Avoid Gin-specific variables (would require fallbacks for non-Gin users)

**Naming convention**: Class names use the same suffix as Drupal's CSS variables (`xxs`, `xs`, `s`) rather than full words (`extra-small`). This matches the established pattern in Drupal core and makes the mapping obvious.

**Alternatives considered**:
- Defining our own variables - duplication, theme conflicts
- Inline styles - no caching, harder to maintain
- SCSS/preprocessing - adds build complexity
- Full word names (`.ai-font-size-extra-small`) - doesn't match CSS var names

**Rationale**: Using Drupal's existing CSS variables ensures consistency with both Claro and Gin themes without maintaining separate values. The `ai-` prefix prevents conflicts. Matching variable name suffixes reduces cognitive load.

### 3. Accessible Icon Pattern: Visually Hidden Text

**Decision**: Use Drupal's `.visually-hidden` pattern for screen reader text.

```html
<a href="..." class="ai-info-link ai-info-link--provider">
  <span class="visually-hidden">OpenAI Provider Information</span>
</a>
```

**Alternatives considered**:
- `aria-label` only - inconsistent screen reader support
- Title attribute - not announced by all screen readers
- Icon + visible text - user requested icon-only

**Rationale**: Drupal core's `.visually-hidden` class is the established pattern, works across all assistive technologies, and allows contextual labels (provider name, model name) for better UX.

### 4. Icon Rendering: CSS Background Images

**Decision**: Render icons via CSS `background-image` with SVG data URIs or file references.

```css
.ai-info-link--provider::before {
  background-image: url('../icons/circles-three-plus.svg');
}
```

**Alternatives considered**:
- Inline SVG in PHP - bloats HTML, harder to cache
- Icon fonts - accessibility issues, rendering inconsistencies
- `<img>` tags - can't style with CSS color

**Rationale**: CSS background images allow state-based styling (hover, focus, active), work with `currentColor` via SVG, and keep icons as static assets that can be cached.

### 5. File Structure

**Decision**: Place assets in existing `ai/assets/` structure.

```
ai/
├── assets/
│   ├── css/
│   │   └── ai-global.css      (new)
│   ├── js/
│   │   └── ai_settings_form.js (existing, modify)
│   └── icons/                  (new directory)
│       ├── circles-three-plus.svg
│       └── sphere.svg
```

**Rationale**: Follows existing module structure. Icons in dedicated directory allows easy addition of future icons.

### 6. Icon Naming Convention

**Decision**: Use two naming strategies based on icon source:

1. **Phosphor Icons**: Keep original Phosphor names (e.g., `circles-three-plus.svg`, `globe.svg`)
2. **Custom/Gin Icons**: Use semantic names describing the icon's visual appearance (e.g., `checkmark.svg`, `close.svg`, `warning.svg`)

**Future consolidation from ai_dashboard**:

When icons from `ai_dashboard` are consolidated into `ai/assets/icons/`, rename from usage-based to semantic:

| ai_dashboard name | Consolidated name | Visual description |
|-------------------|-------------------|-------------------|
| `supported.svg` | `checkmark.svg` | Checkmark/tick |
| `not-supported.svg` | `close.svg` | X/close |
| `warning.svg` | `warning.svg` | Triangle with exclamation |
| `gear.svg` | `gear.svg` | Gear/cog |
| `external.svg` | `external.svg` | Arrow pointing out |

**Rationale**:
- Phosphor icons have established, recognizable names in their library - keeping them aids discoverability and updates.
- Custom icons should use semantic names describing what they look like, not how they're used. This allows reuse (a checkmark can indicate "supported", "complete", "valid", etc.).

## Risks / Trade-offs

**[Risk] CSS variable availability** → Mitigation: Variables are defined in Claro (Drupal core default) and Gin (common contrib). Fallback values can be added if needed.

**[Risk] Icon file requests** → Trade-off: Separate files = extra HTTP requests but easier to manage/swap. Could inline as data URIs later if performance is a concern.

**[Risk] Breaking existing styling** → Mitigation: We're replacing `.form-item__description` (misused) with new classes. The form already outputs `ai-*` classes that have no CSS - we're adding styling, not changing it.

**[Risk] Icon color inheritance** → Mitigation: Phosphor SVGs use `fill="currentColor"`, allowing CSS color control via `color` property on parent.

## Open Questions

- Should we add a hover tooltip showing the full link text? (Deferred - requires tooltip decision from parent ticket #3567389)
- Icon size: 1rem (16px) vs 1.25rem (20px) for better touch targets? (Leaning 1.25rem, matches ai_dashboard's 20px icons)

## Future Work

- **ai_dashboard consolidation**: Migrate icons from `ai_dashboard/images/` to `ai/assets/icons/` and update ai_dashboard to use the shared library
- **Additional icon classes**: Add `.ai-icon--checkmark`, `.ai-icon--close`, `.ai-icon--warning`, `.ai-icon--gear` when consolidating
- **Tooltip system**: Implement tooltips for icon-only links (blocked on #3567389 tooltip decision)
