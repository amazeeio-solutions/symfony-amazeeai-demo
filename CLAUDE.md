# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Symfony 8.0 demo project for **amazee.ai Private AI Provider**. It demonstrates RAG (Retrieval-Augmented Generation) using the Symfony AI Bundle with amazee.ai's LiteLLM-compatible API, PostgreSQL for vector storage, and the titan-embed-text-v2:0 embedding model.

## Common Commands

### Setup
```bash
composer install
php bin/console ai:amazee:configure user@example.com   # Configure VDB and LLM credentials
```

### Blog Demo (Index & Retrieve)
```bash
symfony console ai:store:index blog -vv                  # Index RSS blog posts into vector store
symfony console ai:store:retrieve blog "Week of Symfony"  # Retrieve similar documents
```

### Development
```bash
php bin/console cache:clear          # Clear Symfony cache
php bin/console debug:router         # List all routes
php bin/console make:controller      # Scaffold a controller
php bin/console doctrine:migrations:migrate  # Run database migrations
```

### Testing
```bash
php bin/phpunit                      # Run full test suite
php bin/phpunit tests/Path/To/TestFile.php           # Run a single test file
php bin/phpunit --filter testMethodName               # Run a single test method
```

### Docker Services
```bash
docker compose up -d                 # Start PostgreSQL and MailPit
```
PostgreSQL runs on port 5432, MailPit web UI on port 8025 (SMTP on 1025).

## Architecture

### AI Integration Pipeline

The core architecture revolves around the Symfony AI Bundle configured in `config/packages/ai.yaml`:

1. **Platform** (`ai.platform.generic.amazeeai`): Generic platform connecting to amazee.ai's LiteLLM API via `AmazeeAiModelCatalog` which discovers models from the `/model/info` endpoint and maps them to `CompletionsModel` or `EmbeddingsModel`.

2. **Store** (`ai.store.postgres.symfonycon`): PostgreSQL vector store using cosine distance similarity on table `symfony_blog`.

3. **Vectorizer** (`ai.vectorizer.amazeeai`): Uses the amazeeai platform with `titan-embed-text-v2:0` model to generate embeddings.

4. **Indexer** (`blog`): Loads documents from an RSS feed, filters for "Week of Symfony" posts, applies text split/trim transformers, vectorizes, and stores in PostgreSQL.

5. **Retriever** (`blog`): Queries the vector store using the same vectorizer for semantic search.

### Key Custom Code

- `src/Platform/AmazeeAiModelCatalog.php` — Implements `ModelCatalogInterface` to auto-discover amazee.ai models and their capabilities (embeddings, completions, streaming, tool-calling, image/audio support). This is the main custom integration point.

### Environment Variables

amazee.ai credentials are set by the `ai:amazee:configure` command and stored in `.env.local`:
- `AMAZEEAI_LLM_KEY`, `AMAZEEAI_LLM_API_URL` — LLM API access
- `AMAZEEAI_VDB_HOST`, `AMAZEEAI_VDB_PORT`, `AMAZEEAI_VDB_NAME`, `AMAZEEAI_VDB_USER`, `AMAZEEAI_VDB_PASSWORD`, `AMAZEEAI_VDB_DSN` — Vector database connection

### Stack

- **PHP >= 8.4**, **Symfony 8.0**
- **Doctrine ORM 3.6** with PostgreSQL 16
- **Symfony AI Bundle** (^0.3.2) with generic platform, postgres store
- **Asset Mapper** with Stimulus.js and Turbo (no webpack/encore)
- **Messenger** with Doctrine transport for async processing
