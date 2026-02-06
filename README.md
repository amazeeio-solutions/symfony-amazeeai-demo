# Symfony amazee.ai demo

Demo project for **amazee.ai Private AI Provider**.

It uses this [configuration Console command](https://github.com/amazeeio-solutions/symfony-amazeeai-configure).

## Quick start

### Configure VDB and LLM credentials

- Clone this repository
- `composer i`
- `php bin/console ai:amazee:configure user@example.com` or `symfony console ai:amazee:configure user@example.com` with the Symfony CLI.

### Index and retrieve blog demo

This demo is a port of the [Blog example](https://github.com/symfony/ai-demo?tab=readme-ov-file#3-chroma-db-initialization).
Instead of using ChromaDB, we are using PostgreSQL available from the amazee.ai setup.

- Index `symfony console ai:store:index blog -vv`
- Retrieve `symfony console ai:store:retrieve blog "Week of Symfony"`
