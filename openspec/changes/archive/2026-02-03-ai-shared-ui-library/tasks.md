## 1. Asset Setup

- [x] 1.1 Create `ai/assets/icons/` directory
- [x] 1.2 Add `circles-three-plus.svg` (Phosphor icon for provider links)
- [x] 1.3 Add `sphere.svg` (Phosphor icon for model links)
- [x] 1.4 Create `ai/assets/css/ai-global.css` file

## 2. CSS Typography & Utilities

- [x] 2.1 Add typography utility classes (`.ai-font-size-s`, `.ai-font-size-xs`, `.ai-font-size-xxs`)
- [x] 2.2 Add semantic `.ai-description` class (font-size + muted color)
- [x] 2.3 Add color utility `.ai-text-muted` if needed for standalone use

## 3. CSS Icon Link Styling

- [x] 3.1 Add base `.ai-info-link` class with icon sizing and positioning
- [x] 3.2 Add `.ai-info-link--provider` class with circles-three-plus background
- [x] 3.3 Add `.ai-info-link--model` class with sphere background
- [x] 3.4 Add hover state styles for icon links
- [x] 3.5 Add focus state styles with visible focus ring
- [x] 3.6 Add active state styles for click feedback

## 4. PHP Form Updates

- [x] 4.1 Update `buildInfoLink()` to render icon-only links with visually-hidden text
- [x] 4.2 Update provider link to use `.ai-info-link--provider` class
- [x] 4.3 Update model link to use `.ai-info-link--model` class
- [x] 4.4 Replace `.form-item__description` with `.ai-description` in capability markup (line ~280)
- [x] 4.5 Replace `.form-item__description` with `.ai-description` in additional capabilities (line ~501)
- [x] 4.6 Remove wrapper span with `form-item__description` from info links (lines ~465-470)

## 5. JavaScript Updates

- [x] 5.1 Update dynamic link creation to use icon-only pattern
- [x] 5.2 Add visually-hidden span with model name to dynamically created links
- [x] 5.3 Apply `.ai-info-link--model` class to dynamically created model links
- [x] 5.4 Remove text content from dynamically created links (icon-only)

## 6. Library Integration

- [x] 6.1 Add `ai/ai_global` library definition to `ai.libraries.yml`
- [x] 6.2 Ensure AiSettingsForm attaches `ai/ai_global` library
- [x] 6.3 Verify `ai/ai_settings_form` library depends on or includes `ai/ai_global`

## 7. Verification

Screenshots saved to: `openspec/changes/ai-shared-ui-library/screenshots/`

### Claro Theme Testing
- [x] 7.1 Switch to Claro admin theme
- [x] 7.2 Test icon links display correctly (default, hover, focus, active states)
- [x] 7.3 Capture screenshot: `claro-capabilities-table.png`
- [x] 7.4 Capture screenshot: `claro-icon-hover-state.png` (N/A - hover doesn't change SVG appearance)
- [x] 7.5 Capture screenshot: `claro-icon-focus-state.png`

### Gin Theme Testing
- [x] 7.6 Switch to Gin admin theme
- [x] 7.7 Test icon links display correctly (default, hover, focus, active states)
- [x] 7.8 Capture screenshot: `gin-capabilities-table.png`
- [x] 7.9 Capture screenshot: `gin-icon-hover-state.png`
- [x] 7.10 Capture screenshot: `gin-icon-focus-state.png`

### Accessibility & Functionality
- [x] 7.11 Verify screen reader announces accessible text for both link types (visually-hidden spans verified in HTML)
- [x] 7.12 Test dynamic link creation when selecting different models (JavaScript verified)
- [x] 7.13 Verify typography classes render correctly in both themes
