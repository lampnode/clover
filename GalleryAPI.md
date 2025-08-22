# Gallery API Documentation

This document outlines the API endpoints for the Gallery application, built with Django REST Framework. The API provides access to artworks, media, categories, and related functionalities. Some endpoints require authentication, and certain endpoints filter content based on user group membership (e.g., `KIDS` group).

## Base URL
`/api/`

## Authentication
- **Required**: Some endpoints use `IsAuthenticated` permission, requiring a valid user session.
- **User Groups**: The `KIDS` group restricts access to non-private content only.
- **Public Endpoints**: Endpoints with no permissions are accessible without authentication.

## Endpoints

### 1. Artwork Categories
- **URL**: `/artwork/categories/`
- **Method**: `GET`
- **Permissions**: None (Public)
- **Description**: Lists categories for artworks (type: `DEFAULT`), ordered by descending ID.
- **Query Parameters**: None
- **Response**:
  ```json
  [
    {
      "id": <integer>,
      "name": "<string>",
      "type": "DEFAULT",
      ...
    },
    ...
  ]
  ```
- **Serializer**: `CategorySerializer`

### 2. Media Artwork Categories
- **URL**: `/media/categories/artwork/`
- **Method**: `GET`
- **Permissions**: None (Public)
- **Description**: Lists categories for media artworks (type: `MEDIA_ARTWORK`), ordered by descending ID.
- **Query Parameters**: None
- **Response**:
  ```json
  [
    {
      "id": <integer>,
      "name": "<string>",
      "type": "MEDIA_ARTWORK",
      ...
    },
    ...
  ]
  ```
- **Serializer**: `CategorySerializer`

### 3. Media Photo Categories
- **URL**: `/media/categories/photo/`
- **Method**: `GET`
- **Permissions**: None (Public)
- **Description**: Lists categories for media photos (type: `MEDIA_PHOTO`), ordered by descending ID.
- **Query Parameters**: None
- **Response**:
  ```json
  [
    {
      "id": <integer>,
      "name": "<string>",
      "type": "MEDIA_PHOTO",
      ...
    },
    ...
  ]
  ```
- **Serializer**: `CategorySerializer`

### 4. Media Images
- **URL**: `/media/images/`
- **Method**: `GET`
- **Permissions**: `IsAuthenticated`
- **Description**: Retrieves a paginated list of media artworks, optionally filtered by category. For `KIDS` group users, only non-private media is returned.
- **Query Parameters**:
  - `cid` (optional): `<integer>` Category ID to filter media.
  - `page` (optional): `<integer>` Page number.
  - `page_size` (optional): `<integer>` Items per page (default: 30, max: 100).
- **Response**:
  ```json
  {
    "count": <integer>,
    "next": "<string|null>",
    "previous": "<string|null>",
    "results": [
      {
        "id": <integer>,
        "url": "<string>",
        "media_type": "ARTWORK",
        ...
      },
      ...
    ]
  }
  ```
- **Serializer**: `MediaSerializer`
- **Notes**: Ordered by descending ID.

### 5. Media Photos
- **URL**: `/media/photos/`
- **Method**: `GET`
- **Permissions**: `IsAuthenticated`
- **Description**: Retrieves a paginated list of media photos, optionally filtered by category. For `KIDS` group users, only non-private photos are returned.
- **Query Parameters**:
  - `cid` (optional): `<integer>` Category ID to filter photos.
  - `page` (optional): `<integer>` Page number.
  - `page_size` (optional): `<integer>` Items per page (default: 30, max: 100).
- **Response**:
  ```json
  {
    "count": <integer>,
    "next": "<string|null>",
    "previous": "<string|null>",
    "results": [
      {
        "id": <integer>,
        "url": "<string>",
        "media_type": "PHOTO",
        ...
      },
      ...
    ]
  }
  ```
- **Serializer**: `MediaSerializer`
- **Notes**: Ordered by descending ID.

### 6. Media Detail
- **URL**: `/media/detail/<slug:slug>/`
- **Method**: `GET`
- **Permissions**: `IsAuthenticated`
- **Description**: Retrieves details of a specific media item and related media based on shared tags. For `KIDS` group users, only non-private related media is returned.
- **Path Parameters**:
  - `slug`: `<string>` Media slug.
- **Query Parameters**:
  - `page` (optional): `<integer>` Page number for related media.
  - `page_size` (optional): `<integer>` Related media items per page (default: 10, max: 100).
- **Response**:
  ```json
  {
    "count": <integer>,
    "next": "<string|null>",
    "previous": "<string|null>",
    "results": {
      "media_detail": {
        "id": <integer>,
        "url": "<string>",
        "media_type": "<string>",
        ...
      },
      "related_media": [
        {
          "id": <integer>,
          "url": "<string>",
          "media_type": "<string>",
          ...
        },
        ...
      ]
    }
  }
  ```
- **Serializer**: `MediaSerializer`
- **Notes**: Related media excludes the current item and is ordered by descending ID.

### 7. Media Show
- **URL**: `/media/show/<int:media_type>/`
- **Method**: `GET`
- **Permissions**: `IsAuthenticated`
- **Description**: Returns up to 30 random non-private media items (wallpapers) of the specified type.
- **Path Parameters**:
  - `media_type`: `<integer>` Media type (`1` for `ARTWORK`, `2` for `PHOTO`). Defaults to `PHOTO` if invalid.
- **Response**:
  - **Success** (200 OK):
    ```json
    {
      "title": "wallpapers",
      "pictures": [
        {
          "id": <integer>,
          "url": "<string>",
          "width": <integer>,
          "height": <integer>,
          "type": "<string>"
        },
        ...
      ]
    }
    ```
  - **Error** (404 Not Found):
    ```json
    {
      "error": "<string>"
    }
    ```
- **Notes**: Uses caching (24 hours) for media IDs to optimize performance.

### 8. Artwork List
- **URL**: `/artwork/list/`
- **Method**: `GET`
- **Permissions**: None (Public)
- **Description**: Retrieves a paginated list of non-private artworks, optionally filtered by category.
- **Query Parameters**:
  - `cat` (optional): `<integer>` Category ID to filter artworks.
  - `page` (optional): `<integer>` Page number.
  - `page_size` (optional): `<integer>` Items per page (default: 30, max: 100).
- **Response**:
  ```json
  {
    "count": <integer>,
    "next": "<string|null>",
    "previous": "<string|null>",
    "results": [
      {
        "id": <integer>,
        "title": "<string>",
        "slug": "<string>",
        ...
      },
      ...
    ]
  }
  ```
- **Serializer**: `ArtworkSerializer`
- **Notes**: Ordered by descending ID. Invalid `cat` values are ignored.

### 9. Artwork Detail
- **URL**: `/artwork/detail/<slug:slug>/`
- **Method**: `GET`
- **Permissions**: None (Public)
- **Description**: Retrieves details of an artwork and its associated media.
- **Path Parameters**:
  - `slug`: `<string>` Artwork slug.
- **Response**:
  - **Success** (200 OK):
    ```json
    {
      "title": "<string>",
      "pictures": [
        {
          "id": <integer>,
          "url": "<string>",
          "width": <integer>,
          "height": <integer>,
          "type": "<string>"
        },
        ...
      ]
    }
    ```
  - **Error** (404 Not Found):
    ```json
    {
      "error": "Artwork not found: <string>"
    }
    ```
- **Notes**: Returns all media linked to the artwork.

## Pagination
- **Default Page Sizes**:
  - `MediaImagesView`, `MediaPhotosView`, `ArtworkListView`: 30 items.
  - `MediaDetailView` (related media): 10 items.
- **Query Parameter**: `page_size` (max: 100).
- **Response Format**:
  ```json
  {
    "count": <integer>,
    "next": "<string|null>",
    "previous": "<string|null>",
    "results": [...]
  }
  ```

## Models and Serializers
### Models
- **Artwork**: Fields include `title`, `slug`, `is_private`, `category`.
- **Media**: Fields include `id`, `picture`, `img_width`, `img_height`, `img_type`, `media_type`, `is_private`, `category`.
- **Category**: Fields include `id`, `name`, `type` (`DEFAULT`, `MEDIA_ARTWORK`, `MEDIA_PHOTO`).
- **Tag**: Tags associated with media via `MediaTagRelation`.
- **MediaType**: Enum (`ARTWORK`, `PHOTO`).

### Serializers
- **ArtworkSerializer**: Serializes `Artwork` data.
- **MediaSerializer**: Serializes `Media` data.
- **CategorySerializer**: Serializes `Category` data.

## Error Handling
- **404 Not Found**: Returned for missing resources or errors (e.g., invalid slug).
- **Invalid Parameters**: Handled gracefully (e.g., non-integer `cat` in `ArtworkListView`).

## Caching
- **MediaShowView**: Caches media IDs for 24 hours to improve performance.
