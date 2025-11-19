# Minimal GRC Engineering Landing Page Design

**Date**: 2025-11-18
**Author**: Joseph LeRoy
**Status**: Approved

## Overview

Redesign the GRC Engineering landing page to be minimal and low-maintenance, with automatic listing of all GRC Engineering posts.

## Design Decision

The current GRC Engineering landing page (`/grc-engineering/`) is too heavy with static content that requires manual updates. The new design will be minimal with automatic post listing.

## Landing Page Structure

### 1. Header & Introduction

**Page title**: "GRC Engineering"

**Brief introduction** (2-3 sentences):
- What: Automating AWS compliance controls
- Why: Reduce audit friction while improving security
- How: Practical tutorials with working Infrastructure-as-Code

### 2. Post List

**Display format**:
- Chronological order (newest first)
- Automatically populated from all posts with category "GRC Engineering"
- Each entry shows:
  - Post title (linked to the post)
  - Publication date (e.g., "Nov 18, 2025")
  - Series badge (e.g., "AWS Foundations for Compliance")

**Design principles**:
- Clean, scannable format
- Automatically updates when new posts are published
- No manual maintenance required

## Content Removed

The following sections from the current landing page will be removed:

- "About This Content" section
- "Tutorial Series" with subsections and "Coming soon" items
- "Frameworks Covered" section
- "Code Repository" section with GitHub link
- "Questions or Improvements?" footer
- "New tutorials published bi-weekly" note

**Rationale**: This information can live in individual posts or be discovered through the post list itself. The landing page should be an index, not documentation.

## Implementation Approach

### Hugo Configuration

**Option 1: Simple Approach**
- Update `content/grc-engineering.md` with minimal intro
- Use Hugo shortcode or built-in list functionality to pull posts

**Option 2: Custom Layout** (Recommended)
- Create custom Hugo layout template for the GRC Engineering page
- Template automatically:
  - Renders intro text
  - Queries posts with category="GRC Engineering"
  - Displays chronologically with title, date, and series
  - Updates automatically when new posts published

### Post Frontmatter Requirements

Each GRC Engineering post in `/content/posts/` must include:

```yaml
---
title: "Post Title Here"
date: 2025-11-18
categories: ["GRC Engineering"]
series: ["Series Name Here"]
tags: ["AWS", "OpenTofu", "Compliance", ...]
---
```

**Key fields**:
- `categories`: Must include "GRC Engineering" for the post to appear on landing page
- `series`: Used to display the series badge (e.g., "AWS Foundations for Compliance")
- `tags`: Optional, for additional categorization and discoverability

## Benefits

1. **Low maintenance**: Landing page updates automatically when posts are published
2. **Simplicity**: Clear, focused purpose as an index
3. **Scalability**: Works well with 2 posts or 200 posts
4. **Consistency**: Single source of truth (posts) drives the landing page
5. **No stale content**: No "Coming soon" lists or manual updates needed

## Next Steps

1. Implement Hugo template or shortcode for automatic post listing
2. Update `content/grc-engineering.md` with minimal intro
3. Test with existing posts (if any)
4. Verify automatic updates work when publishing new posts
5. Ensure series badges display correctly
6. Commit changes to git
