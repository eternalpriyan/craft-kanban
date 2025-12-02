# Craft – Daily Notes API for API for All Daily Notes

**Version:** 1.0.0

## Overview
The Craft Daily Notes API provides programmatic access to your daily notes with blocks, tasks, and collections. Daily notes are date-based documents that can contain structured content, tasks, and time-based data.

## Recommended Usage
This API is best utilized when building automation, task management integrations, or daily note workflows.

## Development Tip
- Use relative date formats ('today', 'tomorrow', 'yesterday') for easier date handling
- Tasks are automatically organized into inbox, active, upcoming, and logbook scopes

## Note for AI
When implementing functionality using this API, always make actual calls to these endpoints and verify the responses. Do not simulate or mock the API interactions or use hard-coded values on the client-side - use the real endpoints to ensure proper functionality and data handling.

**IMPORTANT: This is a production server connected to real user data.** Only perform testing operations that can be safely rolled back:

- Safe: Reading data (`GET` requests), creating test content that you delete immediately after
- Safe: Modifying content if you can restore it to its original state
- Safe: Moving blocks if you can move them back to their original position
- Unsafe: Permanent deletions, modifications without backup, or any changes you cannot reverse

Always verify rollback operations work before considering a test complete.

## Servers

- https://connect.craft.do/links/4LRilONEs5e/api/v1
  API Server for API for All Daily Notes Daily Notes

---

# Endpoints

# Fetch Blocks

`GET /blocks`

Fetches content from daily notes. By default returns blocks from today's daily note. Use 'date' parameter to fetch from other dates.

Use `Accept` header `application/json` for structured data, `text/markdown` for rendered content.

**Content Rendering:** Text blocks contain markdown formatting. When displaying content, consider rendering markdown as formatted text or cleaning up the syntax for plain text display.

**Scope Filtering:** Block links in markdown and collections, as well as relations are filtered to daily notes scope (includes all daily notes, task inbox, and task logbook). Block links and date links are returned as `block://` and `date://` URLs.

**Tip:** Start by calling GET /documents to list available documents, then use their documentId values as the 'id' parameter to fetch each document's root content.

## Parameters

- **date** (query): string
  Fetches the root page of a Daily Note for the specified date. Accepts ISO format YYYY-MM-DD or relative dates: 'today', 'tomorrow', 'yesterday'.
Defaults to 'today' if both 'date' and 'id' not provided. Mutually exclusive with 'id' - use this to fetch a Daily Note's root page, or use 'id' to fetch a specific block.
- **id** (query): string
  Fetches a specific page block by its ID. Use this when you want to retrieve a particular block directly, regardless of which Daily Note it belongs to.
Mutually exclusive with 'date' - omit 'date' entirely when using this parameter.
- **maxDepth** (query): number
  The maximum depth of blocks to fetch. Default is -1 (all descendants). With a depth of 0, only the specified block is fetched. With a depth of 1, only direct children are returned.
- **fetchMetadata** (query): boolean
  Whether to fetch metadata (comments, createdBy, lastModifiedBy, lastModifiedAt, createdAt) for the blocks. Default is false.

## Responses

### 200
Array of fetched blocks

**Content-Type:** `application/json`

```json
{
  "id": "0",
  "type": "page",
  "textStyle": "page",
  "markdown": "<page>2025.01.15</page>",
  "content": [
    {
      "id": "1",
      "type": "text",
      "textStyle": "h1",
      "markdown": "# Today's Goals"
    },
    {
      "id": "2",
      "type": "text",
      "markdown": "- Complete project planning"
    },
    {
      "id": "3",
      "type": "text",
      "markdown": "- Review pull requests"
    }
  ]
}
```

**Content-Type:** `text/markdown`

```markdown
# Today's Goals

- Complete project planning
- Review pull requests
```

---

# Insert Blocks

`POST /blocks`

Insert content into a daily note. This single endpoint handles both structured blocks and markdown insertion via Content-Type header negotiation.

**Content-Type: application/json** - Insert structured block objects with position in request body
**Content-Type: text/markdown** - Insert raw markdown text with position specified via query parameter (`?position={"position":"end","date":"today"}`)

Returns the inserted blocks with their assigned block IDs for later reference.

## Request Body

**Content-Type:** `application/json`

```json
{
  "blocks": [
    {
      "type": "text",
      "markdown": "## Meeting Notes\n\n- Discussed Q1 goals\n- Action items assigned"
    }
  ],
  "position": {
    "position": "end",
    "date": "today"
  }
}
```

## Responses

### 200
Array of inserted blocks with assigned IDs

**Content-Type:** `application/json`

```json
{
  "items": [
    {
      "type": "text",
      "id": "string",
      "textStyle": "card",
      "textAlignment": "left",
      "font": "system",
      "cardLayout": "small",
      "markdown": "string",
      "indentationLevel": 0,
      "listStyle": "none",
      "decorations": [
        "callout"
      ],
      "color": "string",
      "taskInfo": {
        "state": "todo",
        "scheduleDate": "2024-01-01",
        "deadlineDate": "2024-01-01"
      },
      "metadata": {
        "lastModifiedAt": "2024-01-01",
        "createdAt": "2024-01-01",
        "lastModifiedBy": "string",
        "createdBy": "string",
        "comments": [
          {
            "id": "string",
            "author": "string",
            "content": "string",
            "createdAt": "2024-01-01"
          }
        ]
      }
    }
  ]
}
```

---

# Delete Blocks

`DELETE /blocks`

Delete content from daily notes. Removes specified blocks by their IDs.

## Request Body

**Content-Type:** `application/json`

```json
{
  "blockIds": [
    "2",
    "3"
  ]
}
```

## Responses

### 200
Array of deleted block IDs

**Content-Type:** `application/json`

```json
{
  "items": [
    {
      "id": "string"
    }
  ]
}
```

---

# Update Blocks

`PUT /blocks`

Update content in daily notes. For text blocks, provide updated markdown content. Only the fields that are provided will be updated.

## Request Body

**Content-Type:** `application/json`

```json
{
  "blocks": [
    {
      "id": "2",
      "markdown": "- ✅ Complete project planning"
    }
  ]
}
```

## Responses

### 200
Array of updated blocks

**Content-Type:** `application/json`

```json
{
  "items": [
    {
      "type": "text",
      "id": "string",
      "textStyle": "card",
      "textAlignment": "left",
      "font": "system",
      "cardLayout": "small",
      "markdown": "string",
      "indentationLevel": 0,
      "listStyle": "none",
      "decorations": [
        "callout"
      ],
      "color": "string",
      "taskInfo": {
        "state": "todo",
        "scheduleDate": "2024-01-01",
        "deadlineDate": "2024-01-01"
      },
      "metadata": {
        "lastModifiedAt": "2024-01-01",
        "createdAt": "2024-01-01",
        "lastModifiedBy": "string",
        "createdBy": "string",
        "comments": [
          {
            "id": "string",
            "author": "string",
            "content": "string",
            "createdAt": "2024-01-01"
          }
        ]
      }
    }
  ]
}
```

---

# Move Blocks

`PUT /blocks/move`

Move blocks to reorder them or move them to a different daily note. Returns the moved block IDs.

## Request Body

**Content-Type:** `application/json`

```json
{
  "blockIds": [
    "2",
    "3"
  ],
  "position": {
    "position": "end",
    "date": "today"
  }
}
```

## Responses

### 200
Array of moved block IDs

**Content-Type:** `application/json`

```json
{
  "items": [
    {
      "id": "string"
    }
  ]
}
```

---

# Search in Document

`GET /blocks/search`

Search content within a specific daily note. Supports regex patterns for flexible searching. Use the 'date' query parameter to specify which daily note to search (defaults to 'today').

## Parameters

- **date** (required) (query): string
  The Daily Note date to search within. Accepts ISO format YYYY-MM-DD or relative dates: 'today', 'tomorrow', 'yesterday'.
- **pattern** (required) (query): string
  The search patterns to look for. Supports NodeJS regular expressions.
- **caseSensitive** (query): boolean
  Whether the search should be case sensitive. Default is false.
- **beforeBlockCount** (query): number
  The number of blocks to include before the matched block.
- **afterBlockCount** (query): number
  The number of blocks to include after the matched block.

## Responses

### 200
Array of search results with context

**Content-Type:** `application/json`

```json
{
  "items": [
    {
      "dailyNoteDate": "2025-01-15",
      "markdown": "Team meeting at 2pm"
    }
  ]
}
```

---

# Search across Daily Notes

`GET /daily-notes/search`

Search content across multiple daily notes using relevance-based ranking. This endpoint uses FlexiSpaceSearch to find matches across your daily notes within an optional date range.

**Key Features:**
- Search across multiple daily notes (vs /blocks/search which searches a single daily note)
- Include term filtering
- Optional date range filtering (startDate/endDate)
- Relevance-based ranking (top 20 results)
- Context blocks before/after each match
- Supports relative dates: 'today', 'tomorrow', 'yesterday'

**Example Use Cases:**
- Find all mentions of a project across the last month
- Search for meeting notes from a specific time period
- Locate tasks or action items across multiple days

## Parameters

- **include** (required) (query): string
  Search terms to include in the search. Can be a single string or array of strings.
- **startDate** (query): string
  The start date for filtering daily notes. Accepts ISO format YYYY-MM-DD or relative dates: 'today', 'tomorrow', 'yesterday'. Only daily notes on or after this date will be included in the search.
- **endDate** (query): string
  The end date for filtering daily notes. Accepts ISO format YYYY-MM-DD or relative dates: 'today', 'tomorrow', 'yesterday'. Only daily notes on or before this date will be included in the search.

## Responses

### 200
Array of search results grouped by daily note with context blocks

**Content-Type:** `application/json`


**Example: basicSearch**

Search for 'meeting' across daily notes

```json
{
  "items": [
    {
      "dailyNoteDate": "2025-01-15",
      "markdown": "Team meeting at 2pm"
    },
    {
      "dailyNoteDate": "2025-01-12",
      "markdown": "Sprint planning meeting"
    }
  ]
}
```

**Example: dateRangeSearch**

Search within date range

```json
{
  "items": [
    {
      "dailyNoteDate": "2025-01-10",
      "markdown": "Working on project Alpha"
    }
  ]
}
```

---

# Get Tasks

`GET /tasks`

Retrieve tasks filtered by scope. Tasks are automatically organized into inbox, active, upcoming, and logbook categories.

## Parameters

- **scope** (required) (query): string
  Filter tasks by scope: - 'active': Active tasks from inbox and other documents (tasks due before now that are not completed/cancelled) - 'upcoming': Upcoming tasks from inbox and other documents (tasks scheduled after now) - 'inbox': Only tasks in the task inbox - 'logbook': Only tasks in the task logbook (completed and cancelled tasks)

## Responses

### 200
Array of tasks

**Content-Type:** `application/json`

```json
{
  "items": [
    {
      "id": "1",
      "markdown": "Review project proposal",
      "state": "todo",
      "scheduleDate": "2025-01-15"
    }
  ]
}
```

---

# Add Tasks

`POST /tasks`

Create new tasks in inbox or daily notes. Tasks can include schedule dates and deadlines.

## Request Body

**Content-Type:** `application/json`

```json
{
  "tasks": [
    {
      "markdown": "Prepare presentation slides",
      "taskInfo": {
        "scheduleDate": "tomorrow"
      },
      "location": {
        "type": "inbox"
      }
    }
  ]
}
```

## Responses

### 200
Task creation result

**Content-Type:** `application/json`

```json
{
  "items": [
    {
      "id": "string",
      "markdown": "string",
      "taskInfo": {
        "state": "todo",
        "scheduleDate": "2024-01-01",
        "deadlineDate": "2024-01-01"
      },
      "location": {
        "type": "inbox"
      }
    }
  ]
}
```

---

# Delete Tasks

`DELETE /tasks`

Delete tasks by their IDs. Only tasks in inbox, logbook, or daily notes can be deleted.

## Request Body

**Content-Type:** `application/json`

```json
{
  "idsToDelete": [
    "1",
    "2"
  ]
}
```

## Responses

### 200
Task deletion result

**Content-Type:** `application/json`

```json
{
  "items": [
    {
      "id": "string"
    }
  ]
}
```

---

# Update Tasks

`PUT /tasks`

Update existing tasks. Can modify task content, state, schedule dates, and deadlines. Marking tasks as done/canceled moves them to logbook.

## Request Body

**Content-Type:** `application/json`

```json
{
  "tasksToUpdate": [
    {
      "id": "1",
      "taskInfo": {
        "state": "done"
      }
    }
  ]
}
```

## Responses

### 200
Task update result

**Content-Type:** `application/json`

```json
{
  "items": [
    {
      "id": "string",
      "markdown": "string",
      "taskInfo": {
        "state": "todo",
        "scheduleDate": "2024-01-01",
        "deadlineDate": "2024-01-01"
      }
    }
  ]
}
```

---

# List Collections

`GET /collections`

Retrieve all collections across daily notes. Collections are data structures that organize items with properties (like a database table).

Use optional `startDate` and `endDate` query parameters to filter collections by daily note date range. Dates can be ISO format (YYYY-MM-DD) or relative ('today', 'tomorrow', 'yesterday').

Each collection has a unique ID that can be used with the `/collections/{collectionId}/items` endpoint to access its items.

## Parameters

- **startDate** (query): string
  The start date for filtering daily notes. Accepts ISO format YYYY-MM-DD or relative dates: 'today', 'tomorrow', 'yesterday'. Only collections in daily notes on or after this date will be included.
- **endDate** (query): string
  The end date for filtering daily notes. Accepts ISO format YYYY-MM-DD or relative dates: 'today', 'tomorrow', 'yesterday'. Only collections in daily notes on or before this date will be included.

## Responses

### 200
List of collections with metadata

**Content-Type:** `application/json`


**Example: allCollections**

Get all collections

```json
{
  "items": [
    {
      "id": "41",
      "name": "Project Tasks",
      "itemCount": 5,
      "dailyNoteDate": "2025-01-15"
    },
    {
      "id": "52",
      "name": "Meeting Notes",
      "itemCount": 3,
      "dailyNoteDate": "2025-01-14"
    }
  ]
}
```

**Example: dateFiltered**

Get collections within date range

```json
{
  "items": [
    {
      "id": "41",
      "name": "Project Tasks",
      "itemCount": 5,
      "dailyNoteDate": "2025-01-15"
    }
  ]
}
```

---

# Get Collection Schema

`GET /collections/{collectionId}/schema`

Get the schema for a collection by its ID.

**Format Options** (via `format` query parameter):
- `json-schema-items` (default): Returns JSON Schema for validating collection items (use with add/update endpoints)
- `schema`: Returns the collection's schema structure (name, properties, types)

Use the collection ID from the `/collections` endpoint.

## Parameters

- **format** (query): string
  The format to return the schema in. Default: json-schema-items. - 'schema': Returns the collection schema structure that can be edited - 'json-schema-items': Returns JSON Schema for addCollectionItems/updateCollectionItems validation
- **collectionId** (required) (path): string
  The unique ID of the collection (obtained from /collections endpoint)

## Responses

### 200
Collection schema in requested format

**Content-Type:** `application/json`


**Example: jsonSchemaFormat**

JSON Schema format (format=json-schema-items, default)

```json
{
  "type": "object",
  "properties": {
    "items": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "title": {
            "type": "string"
          },
          "properties": {
            "type": "object"
          }
        },
        "required": [
          "title"
        ]
      }
    }
  },
  "required": [
    "items"
  ]
}
```

**Example: schemaFormat**

Schema structure format (format=schema)

```json
{
  "name": "Project Tasks",
  "properties": [
    {
      "name": "Status",
      "type": "singleSelect",
      "options": [
        {
          "name": "To Do"
        },
        {
          "name": "In Progress"
        },
        {
          "name": "Done"
        }
      ]
    },
    {
      "name": "Due Date",
      "type": "date"
    }
  ]
}
```

---

# Get Collection Items

`GET /collections/{collectionId}/items`

Retrieve all items from a specific collection. Use the collection ID from the `/collections` endpoint.

Each item includes its properties, nested content blocks, and a unique item ID. Backend document changes may cause collections to be deleted (404 errors) - handle gracefully.

## Parameters

- **maxDepth** (query): number
  The maximum depth of nested content to fetch for each collection item. Default is -1 (all descendants). With a depth of 0, only the item properties are fetched without nested content.
- **collectionId** (required) (path): string
  The unique ID of the collection (obtained from /collections endpoint)

## Responses

### 200
Collection items retrieved successfully

**Content-Type:** `application/json`

```json
{
  "items": [
    {
      "id": "56",
      "title": "Example Item",
      "properties": {
        "status": "Active",
        "priority": "High",
        "relation": {
          "relations": [
            {
              "blockId": "58",
              "title": "Related Item"
            }
          ]
        },
        "linkToBlock": {
          "title": "Linked Block Title",
          "reference": {
            "blockId": "60"
          }
        }
      },
      "content": [
        {
          "id": "57",
          "type": "text",
          "markdown": "Item description and details"
        }
      ]
    }
  ]
}
```

---

# Add Collection Items

`POST /collections/{collectionId}/items`

Add new items to a specific collection. The item structure depends on the collection's schema (properties vary by collection).

Use the `/collections` endpoint to discover available collections and their schemas.

**Note:** Property types and requirements are determined by the collection schema. Consult the schema from the list endpoint to understand required fields. Schema changes may cause validation errors.

## Parameters

- **collectionId** (required) (path): string
  The unique ID of the collection (obtained from /collections endpoint)

## Request Body

**Content-Type:** `application/json`

```json
{
  "items": [
    {
      "title": "New Item",
      "properties": {
        "status": "Active",
        "priority": "High",
        "relation": {
          "relations": [
            {
              "blockId": "58",
              "title": "Related Item"
            }
          ]
        },
        "linkToBlock": {
          "title": "Link Title",
          "reference": {
            "blockId": "60"
          }
        }
      }
    }
  ]
}
```

## Responses

### 200
Items added successfully

**Content-Type:** `application/json`

```json
{
  "items": [
    {
      "id": "58",
      "title": "New Item",
      "properties": {
        "status": "Active",
        "priority": "High"
      }
    }
  ]
}
```

---

# Delete Collection Items

`DELETE /collections/{collectionId}/items`

Delete items from a specific collection by their IDs.

Provide an array of item IDs to delete. Use the GET endpoint to retrieve current item IDs. Non-existent item IDs will be silently skipped.

## Parameters

- **collectionId** (required) (path): string
  The unique ID of the collection (obtained from /collections endpoint)

## Request Body

**Content-Type:** `application/json`

```json
{
  "idsToDelete": [
    "56",
    "57"
  ]
}
```

## Responses

### 200
Items deleted successfully

**Content-Type:** `application/json`

```json
{
  "items": [
    {
      "id": "56"
    },
    {
      "id": "57"
    }
  ]
}
```

---

# Update Collection Items

`PUT /collections/{collectionId}/items`

Update existing items in a specific collection. Only provided fields will be updated (partial updates supported)a.

Each item must include an `id` field to identify which item to update. Use the GET endpoint to retrieve current item IDs. Schema changes may cause validation errors.

## Parameters

- **collectionId** (required) (path): string
  The unique ID of the collection (obtained from /collections endpoint)

## Request Body

**Content-Type:** `application/json`

```json
{
  "itemsToUpdate": [
    {
      "id": "56",
      "title": "Updated Item Title",
      "properties": {
        "status": "Completed"
      }
    }
  ]
}
```

## Responses

### 200
Items updated successfully

**Content-Type:** `application/json`

```json
{
  "items": [
    {
      "id": "56",
      "title": "Updated Item Title",
      "properties": {
        "status": "Completed"
      }
    }
  ]
}
```

---
