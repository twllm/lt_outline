# Scratchpad for repo analysis

## Repository summary
Outline is an open-source knowledge base built with Node.js/React. The repo has a monorepo structure:
- `server/`: backend services including routes, models, utils, jobs
- `shared/`: common utilities and types used on client and server
- `app/`: frontend React app
- `plugins/`: optional features (e.g., Notion integration)
There are ~153 test files scattered across these directories.

Interesting domain areas include permission checks (`server/utils/permissions.ts`), background tasks (`server/queues/tasks`), file parsing & conversions (`server/utils/DocumentConverter.ts`, `plugins/notion/...`), and data models (`server/models`).

## Candidate snippet ideas
- `server/utils/permissions.ts` – logic to determine document permissions
- `server/utils/DocumentConverter.ts` – convert various file types to markdown; includes csv parsing
- `server/queues/tasks/DocumentPublishedNotificationsTask.ts` – notifications for document publish event
- `shared/editor/lib/filterExcessSeparators.ts` – filter separators from menu items
- `server/models/Document.ts` – hooks and queries for updateCollectionStructure and findByPk etc
- `server/models/User.ts` – updateMembershipPermissions and getCounts
- `plugins/notion/server/utils/NotionConverter.ts` – mapping Notion blocks to Outline format
- `server/storage/redis.ts` – custom redis adapter parsing base64 encoded options

