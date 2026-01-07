# Blog API — Database Schema & Domain Model

## Overview

This document describes the core domain entities, relationships, and database schema
for the Blog API. The schema is designed to be:

- REST & GraphQL friendly
- Scalable and normalized
- Compatible with Laravel 12 (2026 best practices)
- Suitable for public-read / authenticated-write use cases

---

## Core Domain Entities

### User
Represents an authenticated author or commenter.

> Provided by Laravel default authentication scaffolding.

---

### Post
Represents a blog article written by a user.

**Responsibilities**
- Store publishable content
- Track publication state
- Own comments and tags

---

### Comment
Represents user-generated feedback on a post.

**Responsibilities**
- Belongs to a post
- Belongs to a user
- Publicly readable

---

### Tag
Represents a categorization label for posts.

**Responsibilities**
- Used for filtering and discovery
- Shared across posts

---

## Entity Relationships

| Entity | Relationship | Entity |
|------|-------------|--------|
| User | has many | Posts |
| Post | belongs to | User |
| Post | has many | Comments |
| Comment | belongs to | Post |
| Comment | belongs to | User |
| Post | belongs to many | Tags |
| Tag | belongs to many | Posts |

---

## Database Tables

### users
(Provided by Laravel)

| Column | Type | Notes |
|------|-----|------|
| id | bigint | Primary key |
| name | string | |
| email | string | Unique |
| password | string | Hashed |
| timestamps | | |

---

### posts

| Column | Type | Notes |
|------|-----|------|
| id | bigint | Primary key |
| user_id | bigint | Author |
| title | string | |
| slug | string | URL identifier |
| content | longText | Markdown or HTML |
| status | enum | draft / published |
| published_at | timestamp | Nullable |
| timestamps | | |
| deleted_at | timestamp | Soft deletes |

**Indexes**
- `slug` (unique)
- `user_id`

---

### comments

| Column | Type | Notes |
|------|-----|------|
| id | bigint | Primary key |
| post_id | bigint | |
| user_id | bigint | |
| content | text | |
| timestamps | | |

**Indexes**
- `post_id`
- `user_id`

---

### tags

| Column | Type | Notes |
|------|-----|------|
| id | bigint | Primary key |
| name | string | |
| slug | string | Unique |
| timestamps | | |

---

### post_tag (pivot)

| Column | Type | Notes |
|------|-----|------|
| post_id | bigint | |
| tag_id | bigint | |

**Composite Key**
- (`post_id`, `tag_id`)

---

## Design Decisions

- Soft deletes are enabled for posts to support content recovery
- Slugs are used for public URLs instead of numeric IDs
- Status-based publishing allows drafts without access control hacks
- Many-to-many tags allow flexible categorization
- Schema supports future extensions (reactions, revisions, SEO metadata)

---

## Next Steps

1. Create migrations based on this schema
2. Define API routes and contracts
3. Implement policies for write access
4. Add API resources for response shaping
5. Introduce feature tests

---
