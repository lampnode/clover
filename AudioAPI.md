# Audio API Documentation

This document describes the API endpoints for accessing and managing audio content. All endpoints require authentication unless otherwise specified.

## Base URL
`/api/`

## Authentication
- All endpoints (except `/api/stream/`) require the `IsAuthenticated` permission.
- Users must include a valid authentication token in the request headers.

## Endpoints

### 1. List Audio Entities
- **URL**: `/api/list/`
- **Method**: GET
- **Description**: Retrieves a paginated list of audio entities.
- **Query Parameters**:
  - `cat` (optional): Filter by category slug. Supports exact matches and subcategories (e.g., `cat=rock` includes `rock-pop`).
  - `page_size` (optional): Number of items per page (default: 20, max: 100).
- **Permissions**:
  - Authenticated users only.
  - For users in the `KIDS` group, only entities with content levels `TEENAGER` or `CHILDREN` are returned.
- **Response**:
  - **Success (200)**:
    ```json
    [
      {
        "id": <int>,
        "title": <string>,
        "title_alt": <string>,
        "category_slug": <string>,
        "author_slug": <string>,
        "entity_slug": <string>,
        ...
      },
      ...
    ]
    ```
  - **Error (401)**: Unauthorized if not authenticated.
- **Pagination**:
  - Page size: 20 (configurable via `page_size` up to 100).
  - Returns paginated results with standard pagination metadata.

### 2. List Categories
- **URL**: `/api/categories/`
- **Method**: GET
- **Description**: Retrieves a list of parent categories for audio content.
- **Permissions**: Authenticated users only.
- **Response**:
  - **Success (200)**:
    ```json
    [
      {
        "title": <string>,
        "slug": <string>
      },
      ...
    ]
    ```
  - **Error (401)**: Unauthorized if not authenticated.

### 3. Search Audio Entities
- **URL**: `/api/search/`
- **Method**: GET
- **Description**: Searches audio entities by title or alternative title.
- **Query Parameters**:
  - `q` (optional): Search query for filtering by title or `title_alt` (case-insensitive).
  - `page_size` (optional): Number of items per page (default: 20, max: 100).
- **Permissions**:
  - Authenticated users only.
  - For users in the `KIDS` group, only entities with content levels `TEENAGER` or `CHILDREN` are returned.
- **Response**:
  - **Success (200)**:
    ```json
    [
      {
        "id": <int>,
        "title": <string>,
        "title_alt": <string>,
        "category_slug": <string>,
        "author_slug": <string>,
        "entity_slug": <string>,
        ...
      },
      ...
    ]
    ```
  - **Error (401)**: Unauthorized if not authenticated.
- **Pagination**:
  - Page size: 20 (configurable via `page_size` up to 100).
  - Returns paginated results with standard pagination metadata.

### 4. Audio Entity Details
- **URL**: `/api/detail/<cat_slug>/<author_slug>/<entity_slug>/`
- **Method**: GET
- **Description**: Retrieves detailed information about a specific audio entity, including associated files.
- **Path Parameters**:
  - `cat_slug`: Category slug.
  - `author_slug`: Author slug.
  - `entity_slug`: Entity slug.
- **Permissions**: Authenticated users only.
- **Response**:
  - **Success (200)**:
    ```json
    {
      "title": <string>,
      "title_alt": <string>,
      "published_year": <int>,
      "cover": <string>,
      "files": [
        {
          "title": <string>,
          "slug": <string>,
          "url": <string>,
          "size": <int>
        },
        ...
      ]
    }
    ```
  - **Error (404)**: Entity not found.
  - **Error (401)**: Unauthorized if not authenticated.

### 5. Stream Audio File
- **URL**: `/api/stream/<cat_slug>/<author_slug>/<entity_slug>/<file_slug>/`
- **Method**: GET
- **Description**: Streams an MP3 audio file in chunks.
- **Path Parameters**:
  - `cat_slug`: Category slug.
  - `author_slug`: Author slug.
  - `entity_slug`: Entity slug.
  - `file_slug`: File slug.
- **Permissions**: No authentication required.
- **Response**:
  - **Success (200)**:
    - Content-Type: `audio/mpeg`
    - Content-Disposition: `inline; filename="<file_title>.mp3"`
    - Streams the MP3 file in 8KB chunks.
  - **Error (404)**: Audio file not found.

## Models and Serializers
- **EntityIndex**: Represents an audio entity with fields like `title`, `title_alt`, `category_slug`, `author_slug`, `entity_slug`, `content_level`, `is_private`, `total_files`, and `archives_type`.
- **AudioSerializer**: Serializes `EntityIndex` objects for API responses, including relevant fields for list and search results.

## Notes
- The `archives_type` for all endpoints is set to `AUDIOS_ARCHIVES`.
- Private entities (`is_private=True`) are excluded from responses.
- Entities with no files (`total_files=0`) are excluded from list and search results.
- The `KIDS` group restricts content to `TEENAGER` and `CHILDREN` levels.
- Streaming uses chunked transfer encoding for efficient delivery of large MP3 files.
