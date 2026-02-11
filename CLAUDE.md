# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Shopify theme based on Shopify's Ritual theme (v3.3.1). It follows Shopify's standard theme architecture with Liquid templating, JSON-based page templates, and a modular section/block system.

## Theme Architecture

### Core Structure

- **Sections** (`sections/`): ~41 major page components that can be added/removed via the theme editor
  - JSON config files (`header-group.json`, `footer-group.json`) define section groups
  - Liquid files define the section markup and schema
  - Sections contain blocks (defined inline) that users can add/customize

- **Blocks** (`blocks/`): ~94 reusable components used within sections
  - Prefixed with `_` are internal/compositional blocks (e.g., `_content.liquid`, `_media.liquid`)
  - Non-prefixed are user-facing blocks available in the theme editor

- **Snippets** (`snippets/`): ~94 reusable Liquid partials for shared functionality
  - Rendered via `{% render 'snippet-name' %}`
  - Examples: `product-card.liquid`, `cart-products.liquid`, `button.liquid`

- **Templates** (`templates/`): Page-level JSON templates
  - Define which sections appear on each page type (product, collection, etc.)
  - `gift_card.liquid` is the only non-JSON template

- **Layout** (`layout/`): Base HTML structure
  - `theme.liquid`: Main layout wrapping all pages
  - `password.liquid`: Layout for password-protected stores

### Configuration

- **Settings Schema** (`config/settings_schema.json`):
  - Defines all theme settings in the Shopify admin
  - Color schemes, typography, buttons, badges, animations, etc.
  - ~2,300 lines of theme configuration options

- **Settings Data** (`config/settings_data.json`):
  - Stores actual theme setting values (gitignored to avoid conflicts)
  - Managed through Shopify admin, not directly edited

### Styling System

- **Color Schemes**: Defined in settings schema with roles (text, background, buttons, etc.)
  - Applied via `color_scheme` settings on sections
  - CSS variables generated from scheme settings

- **Typography**: Four font families (body, subheading, heading, accent)
  - Configurable size, line-height, letter-spacing, and text-case for H1-H6

- **Design Tokens**: Border radius, shadows, spacing controlled via settings

### JavaScript Architecture

All JS files are in `assets/` directory:
- **Component-based**: Files like `component.js`, `component-cart-items.js`
- **Feature modules**: `cart-drawer.js`, `product-card.js`, `variant-picker.js`
- **No build step**: Vanilla JavaScript, no bundler

## Development Workflow

### Local Development

This theme uses Shopify CLI for development:

```bash
# Install Shopify CLI if needed
npm install -g @shopify/cli @shopify/theme

# Authenticate with Shopify
shopify auth login

# Start development server (live preview with hot reload)
shopify theme dev

# Pull theme from store
shopify theme pull

# Push theme to store
shopify theme push
```

### File Editing

- **Liquid files**: Edit directly in theme files
- **Settings**: Modify `config/settings_schema.json` for new settings
- **Translations**: Update locale files in `locales/` (40+ languages supported)
- **Styles**: Main CSS in `assets/base.css` (~120KB)

### Testing Changes

1. Use `shopify theme dev` to preview changes locally
2. Test in Shopify's theme editor (accessible via dev server)
3. Verify responsive behavior (mobile, tablet, desktop)
4. Check across different sections/blocks combinations

## Important Patterns

### Liquid Patterns

**Rendering snippets with parameters:**
```liquid
{% render 'product-card', product: product, show_vendor: true %}
```

**Section settings access:**
```liquid
{{ section.settings.heading }}
{{ block.settings.image }}
```

**Global settings:**
```liquid
{{ settings.logo }}
{{ settings.type_body_font }}
```

### Block Architecture

Blocks within sections follow a compositional pattern:
- Public blocks (e.g., `button.liquid`) can be added by users
- Private blocks (e.g., `_content.liquid`) are composables used internally
- Blocks define their own schema within the section file

### Color Scheme Application

Sections typically include a `color_scheme` setting that applies CSS classes:
```liquid
<div class="color-scheme-{{ section.settings.color_scheme }}">
```

## Key Files Reference

### Critical Sections
- `sections/header.liquid`: Main navigation (~40KB, complex)
- `sections/footer.liquid`: Site footer
- `sections/hero.liquid`: Hero/banner component (~44KB)
- `sections/product-information.liquid`: Product page details

### Important Snippets
- `snippets/product-card.liquid`: Product grid cards
- `snippets/cart-products.liquid`: Cart item list (~25KB)
- `snippets/variant-picker.liquid`: Product variant selection
- `snippets/color-schemes.liquid`: CSS variable generation

### Templates
All templates in `templates/` are JSON except `gift_card.liquid`

## Localization

- 40+ language files in `locales/`
- Each has a `.json` file (translations) and `.schema.json` file (translation keys)
- Reference translations: `{{ 'general.search.search' | t }}`
- English is the default: `locales/en.default.json`

## Common Customizations

### Adding a New Section
1. Create `sections/my-section.liquid`
2. Define schema at bottom of file with settings and blocks
3. Section will appear in theme editor automatically

### Modifying Theme Settings
1. Edit `config/settings_schema.json`
2. Add new setting objects with type, id, label, default
3. Access via `{{ settings.my_setting_id }}`

### Creating Reusable Components
- For user-facing: Create in `blocks/`
- For internal use: Create in `snippets/` or prefix with `_` in `blocks/`

## Notes

- **No build process**: This is a standard Shopify theme without webpack/bundlers
- **Settings data**: Never commit `config/settings_data.json` (gitignored)
- **Theme editor**: Changes made in Shopify admin don't auto-sync to local files
- **Version control**: Use `shopify theme pull` to sync remote changes before committing
