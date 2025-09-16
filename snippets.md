## Document permission helpers
**Name of the function/object under test:** `canUserAccessDocument`, `isElevatedPermission`, `getDocumentPermission`
**File:** server/utils/permissions.ts:14-174
**Why Selected:** Implements nuanced permission resolution between user, group, and collection roles with explicit priority ordering.
**Primary Risks:** Naïve tests might assert exact SQL queries or rely on precedence details that may change.
**Hidden / Edge Cases:** Missing document lookups, skipped membership comparison, ties when multiple permissions exist, group-only access.
**Spec Clarity:** Medium – helper names are descriptive but conflict resolution behavior isn't fully documented.
**Post March 2025:** yes
**Whether tests already exist for this, and if so, when those tests were written** No dedicated tests found after 2025.
**Suggested Robust Tests:**
- Parametrize user and group membership combinations and assert the resulting permission level only.
- Mock `Document.findByPk` failures to simulate missing or archived documents.
- Check `isElevatedPermission` with and without `skipMembershipId`.
- Verify `canUserAccessDocument` returns false for unauthorized users.

## CSV to Markdown conversion
**Name of the function/object under test:** `DocumentConverter.csvToMarkdown`
**File:** server/utils/DocumentConverter.ts:87-127
**Why Selected:** Detects CSV delimiter heuristically and converts data into Markdown tables using a streaming parser.
**Primary Risks:** Over-asserting which delimiter was chosen or the order rows are parsed; ignoring error handling.
**Hidden / Edge Cases:** Quoted fields containing delimiters, empty trailing lines, unusual whitespace, parse errors.
**Spec Clarity:** High – comments explain the heuristic though details may be tweaked.
**Post March 2025:** yes
**Whether tests already exist for this, and if so, when those tests were written** Yes, tests from November 2024.
**Suggested Robust Tests:**
- Provide CSV samples with comma, semicolon, and tab separators; compare output Markdown without caring about intermediate arrays.
- Mock the parser to throw and verify `FileImportError` surfaces.
- Assert trimming of whitespace around cell values.

## Publish notification task
**Name of the function/object under test:** `DocumentPublishedNotificationsTask.perform`
**File:** server/queues/tasks/DocumentPublishedNotificationsTask.ts:10-76
**Why Selected:** Sends notifications to mentioned users before notifying remaining subscribers while respecting permissions.
**Primary Risks:** Tests may over-specify the order of `Notification.create` calls or rely on actual DB inserts.
**Hidden / Edge Cases:** Document not found, duplicate mentions, unsubscribed recipients, permission denial.
**Spec Clarity:** Medium – algorithmic flow is clear though side effects are implicit.
**Post March 2025:** yes
**Whether tests already exist for this, and if so, when those tests were written** Existing tests pre-date 2025.
**Suggested Robust Tests:**
- Mock `Notification` creation and assert counts rather than call order.
- Ensure duplicate mentions yield one notification per user.
- Simulate unsubscribed recipients and unauthorized access cases.

## Filter menu separators
**Name of the function/object under test:** `filterExcessSeparators`
**File:** shared/editor/lib/filterExcessSeparators.ts:1-29
**Why Selected:** Utility that removes redundant separator menu items to tidy up UI lists.
**Primary Risks:** Overly strict tests might check object identity or exact ordering when separators collapse.
**Hidden / Edge Cases:** Empty arrays, separators at list boundaries, multiple consecutive separators in the middle.
**Spec Clarity:** High – function is short and intent obvious.
**Post March 2025:** no
**Whether tests already exist for this, and if so, when those tests were written** Yes, tests from 2023.
**Suggested Robust Tests:**
- Parametrize arrays with separators at start, middle, and end; ensure output keeps order of non-separator items.
- Confirm no separators remain when input begins or ends with them.

## Document membership and lookup
**Name of the function/object under test:** `Document.findByPk` (with `withMembershipScope`)
**File:** server/models/Document.ts:645-757
**Why Selected:** Overrides `findByPk` to accept URL slugs and to preload collection memberships based on user options.
**Primary Risks:** Tests might assert SQL query details or rely on default scopes.
**Hidden / Edge Cases:** UUID vs slug lookup, includeState option, missing membership, archived/draft documents.
**Spec Clarity:** Medium – docstrings exist but logic is complex.
**Post March 2025:** yes
**Whether tests already exist for this, and if so, when those tests were written** Yes, model tests from May 2025.
**Suggested Robust Tests:**
- Call `findByPk` with UUID, slug, and invalid ids verifying return values only.
- Toggle `withMembershipScope` options like `includeDrafts` and `paranoid` without checking generated SQL.
- Ensure transactions or options propagate through the call chain.

## User role change side effects
**Name of the function/object under test:** `updateMembershipPermissions` hook and `User.getCounts`
**File:** server/models/User.ts:701-803
**Why Selected:** Adjusts collection memberships when a user's role changes and aggregates team user statistics via raw SQL.
**Primary Risks:** Over-specifying raw SQL strings or the exact sequence of update calls; ignoring admin demotion edge cases.
**Hidden / Edge Cases:** Transaction failures, zero-count results, demoting the last admin.
**Spec Clarity:** Medium – intention is clear but SQL obscures some behavior.
**Post March 2025:** yes
**Whether tests already exist for this, and if so, when those tests were written** Tests dated February 2025 (before March).
**Suggested Robust Tests:**
- Change a user's role and confirm memberships update without checking SQL text.
- Mock `sequelize.query` to supply controlled counts for `getCounts`.
- Verify demoting the final admin throws a `ValidationError`.

## Notion block conversion
**Name of the function/object under test:** `NotionConverter.mapChildren`
**File:** plugins/notion/server/utils/NotionConverter.ts:56-140
**Why Selected:** Converts Notion blocks into Outline’s Prosemirror format, including wrapping lists and handling unknown block types.
**Primary Risks:** Snapshot tests may over-specify exact JSON structure or rely on child ordering.
**Hidden / Edge Cases:** Unknown block types, mixed lists, empty children arrays.
**Spec Clarity:** Medium – comments explain approach but heuristics are implicit.
**Post March 2025:** yes
**Whether tests already exist for this, and if so, when those tests were written** Yes, added March 2025.
**Suggested Robust Tests:**
- Convert pages with mixed block sequences verifying only the presence of expected node types.
- Confirm unknown block logs a warning but returns undefined.
- Test wrapping when switching between bullet and numbered lists.

## Redis adapter initialization
**Name of the function/object under test:** `RedisAdapter` (constructor and `defaultClient`)
**File:** server/storage/redis.ts:1-110
**Why Selected:** Builds Redis clients with optional base64-encoded options and enforces singleton instances.
**Primary Risks:** Tests might inspect private fields or rely on constructed connection names.
**Hidden / Edge Cases:** Invalid base64 string, overriding defaults, TLS settings for `rediss` URLs.
**Spec Clarity:** Medium – high-level comments but decode behavior may surprise.
**Post March 2025:** yes
**Whether tests already exist for this, and if so, when those tests were written** None found.
**Suggested Robust Tests:**
- Instantiate with plain URL and encoded options ensuring options passed to Redis constructor only.
- Provide malformed encoded URL and expect an error.
- Call `defaultClient` twice to confirm the same instance is returned.
