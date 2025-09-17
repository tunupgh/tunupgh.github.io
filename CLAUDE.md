# CLAUDE.md

This file provides guidance to Claude Code when working with code in this repository.

## Project Overview

This is **TunnelsUP**, a technical knowledge base website focused on network security, firewalls, and Cisco configurations. The site is built with Jekyll and contains 200+ technical articles spanning from 2010-2018.

## Site Architecture

### Technology Stack
- **Framework**: Jekyll 3.10.0
- **Theme**: Minima 2.0
- **Markdown**: Kramdown with GFM parser
- **Plugins**: jekyll-feed, jekyll-paginate

### Site Structure
- **Posts**: 200+ technical articles in `_posts/` (2010-2018)
- **Tools**: Interactive network security tools in `/tools/` directory
- **Content Types**: Cisco configs, VPN tutorials, security guides, Linux commands
- **URL Format**: `/:title/` (clean URLs without dates)
- **Pagination**: `/tup/page/:num/` (10 posts per page)

## Development Commands

### Local Development
```bash
bundle exec jekyll serve              # Start dev server (localhost:4000)
bundle exec jekyll serve --trace      # Debug mode with detailed errors
bundle exec jekyll build              # Build site to _site/
```

### Content Management
- Posts are in markdown format with YAML frontmatter
- Images stored in `/images/` directory
- Tools are standalone HTML/JS pages in `/tools/` subdirectories

## Content Focus Areas

The site specializes in:
- **Cisco ASA/PIX**: Firewall configurations, troubleshooting, VPN setup
- **Network Security**: VPN tunnels, NAT, routing, packet analysis
- **Security Tools**: Custom calculators and analyzers for network admins
- **Linux/CLI**: Command line guides and system administration
- **Professional Tutorials**: Step-by-step technical guides

## Key Features

### Interactive Tools
Located in `/tools/` with individual subdirectories:
- **Hash Analyzer**: Identify hash types
- **Subnet Calculator**: IPv4/IPv6 network calculations
- **Config Cleanup**: Cisco ASA configuration analysis
- **Show Connections Analyzer**: ASA connection parsing
- **NAT Tools**: Configuration converters and generators

### Content Organization
- **Technical Posts**: Deep-dive tutorials and configurations
- **Cheat Sheets**: Quick reference guides
- **Troubleshooting**: Problem/solution focused articles
- **Videos**: External video content references

## Site Configuration

### Important Settings
- **Base URL**: https://www.tunnelsup.com
- **Permalink**: `/:title/` (preserves clean URLs)
- **Pagination**: 10 posts per page at `/tup/page/:num/`
- **Feed**: Atom feed via jekyll-feed plugin

### File Structure
```
/
├── _posts/           # 200+ markdown articles (2010-2018)
├── _layouts/         # Jekyll page templates
├── _includes/        # Partial templates
├── _sass/           # Sass stylesheets
├── tools/           # Interactive JavaScript tools
├── images/          # Site images and diagrams
├── assets/          # CSS, JS, and other assets
└── _config.yml      # Jekyll configuration
```

## Development Guidelines

### Code Style
- Follow existing Jekyll/Liquid templating patterns
- Maintain clean, semantic HTML structure
- Use existing CSS classes and styling patterns
- Keep JavaScript tools simple and functional

### Content Guidelines
- Technical accuracy is paramount
- Include practical examples and configurations
- Focus on real-world networking scenarios
- Maintain professional, instructional tone

### Tool Development
- Tools should be self-contained in their directories
- Use vanilla JavaScript when possible
- Include clear instructions and examples
- Test with realistic network data

## Common Tasks

### Adding New Posts
1. Create file in `_posts/` with format: `YYYY-MM-DD-title.markdown`
2. Include proper YAML frontmatter (title, date, layout)
3. Use markdown for content formatting
4. Test locally before committing

### Updating Tools
1. Navigate to specific tool directory in `/tools/`
2. Modify HTML/CSS/JS files as needed
3. Test functionality thoroughly
4. Ensure responsive design works

### Site Maintenance
- Check for broken links periodically
- Update dependencies in Gemfile as needed
- Monitor site performance and load times
- Validate HTML and CSS periodically