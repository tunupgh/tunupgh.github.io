# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is the **NEW Jekyll migration** of the TunnelsUP blog. The original Octopress site is located at `../tunnelsup.github.io/` and serves as the source for content, theme, and functionality migration.

**Migration Status**: 🚧 **In Progress**
- ✅ Basic Jekyll site structure created
- ✅ URL preservation configured (`permalink: /:title/`)
- ✅ Site metadata configured (title, description, URLs)
- 🔄 **Currently migrating**: Content and theme from Octopress
- ⏳ **Pending**: Custom tools, tag system, final styling

## Development Commands

### Basic Jekyll Operations
```bash
bundle exec jekyll serve          # Start development server (localhost:4000)
bundle exec jekyll build          # Build static site to _site/
bundle exec jekyll serve --trace  # Run with detailed error output
```

### Content Management
```bash
bundle exec jekyll new-post "Title"  # Create new post (if jekyll-compose added)
```

## Architecture

### Current Structure
- **Standard Jekyll 3.10.0** with minima theme
- **Plugins**: jekyll-feed, jekyll-paginate
- **Markdown**: kramdown
- **URL Structure**: `/:title/` (preserves Octopress URLs)
- **Pagination**: `/tup/page/:num/` (matches original)

### Migration Sources
- **Original Octopress**: `../tunnelsup.github.io/`
- **Content Source**: `../tunnelsup.github.io/source/_posts/` (200+ technical posts)
- **Theme Source**: `../tunnelsup.github.io/sass/` and `../tunnelsup.github.io/source/_includes/`
- **Custom Tools**: Various JavaScript utilities to migrate

## Migration Plan

### Phase 1: ✅ Foundation (Complete)
- Jekyll site creation
- Basic configuration
- URL preservation setup

### Phase 2: 🔄 Content & Theme (In Progress)
- Content migration from `../tunnelsup.github.io/source/_posts/`
- Theme extraction from Octopress Sass files
- Custom sidebar/layout migration

### Phase 3: ⏳ Advanced Features (Pending)
- Custom JavaScript tools (hash analyzer, subnet calculator, config cleanup)
- Tag/category system (recreate custom tag generator functionality)
- Custom includes and partials

### Phase 4: ⏳ Polish (Pending)
- Performance optimization
- Final URL testing and validation
- Deployment setup

## Key Differences from Original

**Simplified Architecture:**
- Uses standard Jekyll instead of heavy Octopress framework
- Modern Ruby/Jekyll versions vs legacy dependencies
- Standard plugin ecosystem vs custom Octopress plugins

**Preserved Elements:**
- Same URL structure (`/:title/`)
- Same pagination paths (`/tup/page/:num/`)
- Same site metadata and branding
- Content and theme styling (when migrated)

**Technical Focus:**
- Network security and Cisco configuration content
- Professional technical knowledge base
- Custom networking tools integration

## Development Notes

### Content Migration Process
1. Copy posts from `../tunnelsup.github.io/source/_posts/`
2. Update frontmatter format for Jekyll
3. Test URL preservation with sample posts
4. Batch migrate remaining content

### Theme Migration Process
1. Extract Sass from `../tunnelsup.github.io/sass/`
2. Convert Octopress includes to Jekyll layouts
3. Migrate custom sidebar system
4. Preserve professional styling

### Tools Migration Process
1. Extract JavaScript from `../tunnelsup.github.io/source/javascripts/`
2. Migrate custom tools to new site structure
3. Update any hardcoded paths or dependencies