# Project Context

## Purpose
Drupal CMS AI Demo - A ready-to-use platform built on Drupal CMS with AI integration. This project demonstrates Drupal CMS capabilities combined with AI-powered features, enabling content creators to leverage AI assistance for content generation, analysis, and other intelligent workflows.

## Tech Stack
- **CMS Framework**: Drupal 11.x (drupal/core ^11.2.4)
- **Language**: PHP 8.3
- **Database**: MySQL 8.0
- **Web Server**: Apache with FPM
- **Package Manager**: Composer 2
- **Local Development**: DDEV (with DevPanel compatibility)
- **CLI**: Drush ^13.6.1
- **AI Integration**: drupal/ai module with LiteLLM provider support

### Key Drupal Recipes
- drupal_cms_starter (core starter kit)
- drupal_cms_blog, drupal_cms_news, drupal_cms_events (content types)
- drupal_cms_page, drupal_cms_person, drupal_cms_project, drupal_cms_case_study
- drupal_cms_seo_tools, drupal_cms_accessibility_tools
- drupal_cms_forms (webform integration)
- drupal_cms_google_analytics
- drupal_cms_ai (AI features)

## Project Conventions

### Code Style
- **Formatting**: EditorConfig enforced
  - 2-space indentation (4 spaces for composer.json/lock)
  - LF line endings
  - UTF-8 charset
  - Trim trailing whitespace
  - Insert final newline
- **PHP Standards**: Drupal and DrupalPractice coding standards via PHPCS
- **Static Analysis**: PHPStan level 7
- **JavaScript**: ESLint extending Drupal core rules
- **File Extensions**: .module, .inc, .install, .php, .theme, .yml

### Architecture Patterns
- **Recipe-Based Architecture**: Features are packaged as Drupal recipes in `recipes/`
- **Module Structure**:
  - Contrib modules: `web/modules/contrib/`
  - Custom modules: `web/modules/custom/`
  - Project-level modules: `modules/` (AI module lives here as a submodule)
- **Configuration Management**: Exported to `config/sync/`
- **Web Root**: `web/` directory (Drupal docroot)
- **Libraries**: External JS/CSS libraries in `web/libraries/`
- **Private Files**: `private/` for sensitive file storage

### Testing Strategy
- **PHPUnit**: Configuration at `modules/ai/phpunit.ai.xml.dist`
- **Test Directory**: `modules/ai/tests/`
- **Static Analysis**: PHPStan with custom configuration in `phpstan.neon`
- **Pre-commit**: GrumPHP integration available (`grumphp.yml`)

### Git Workflow
- **Primary Branch**: `main`
- **Feature Branches**: Use descriptive names (e.g., `3560206-capabilties-ux`)
- **Commit Messages**: Short, imperative descriptions (e.g., "Add command to install ai module")
- **Issue Tracking**: Branch names may include issue numbers from Drupal.org

## Domain Context
- **Drupal Concepts**: Understand nodes, entities, fields, views, recipes, modules, themes
- **AI Module**: The `modules/ai/` directory contains the AI integration framework providing:
  - AI provider abstraction layer
  - LiteLLM integration for model access
  - Agent/assistant API capabilities
  - Content generation features
- **Recipe System**: Drupal recipes are YAML-based configuration packages that can be applied to quickly set up features
- **DevPanel**: This project supports DevPanel hosting platform in addition to DDEV

## Important Constraints
- **Drupal 11 Compatibility**: All code must be compatible with Drupal 11.x
- **PHP Version**: Minimum PHP 8.3 required
- **License**: GPL-2.0-or-later (Drupal license requirements)
- **Coding Standards**: Must pass Drupal/DrupalPractice phpcs checks
- **PHPStan Level 7**: Static analysis must pass at level 7
- **Security**: Follow Drupal security best practices; no hardcoded credentials
- **AI Provider**: Requires valid AI provider configuration (LiteLLM) for AI features

## External Dependencies
- **AI Provider**: LiteLLM proxy for AI model access (configured via `DP_AI_VIRTUAL_KEY`)
- **Drupal.org Packages**: drupal/core and contrib modules from packages.drupal.org
- **Frontend Libraries**: Multiple jQuery plugins and UI libraries (select2, timepicker, inputmask, etc.)
- **Key Module**: Required for secure credential storage
- **Documentation**: MkDocs used for AI module documentation at `/docs/`
