# Study repository links

[repositories.yml](repositories.yml) controls the parent repository names and general
portal links in the Studies page's Additional CRDC Nodes tooltip (CTDC-2304).
The CTDC frontend loads it from:

```text
REACT_APP_STATIC_CONTENT_URL + /repositories/repositories.yml
```

Study associations come from the existing GraphQL query:

```graphql
getAllStudies {
  image_collection {
    repository_name
  }
}
```

The frontend matches these source names against this YAML. Collection names and
`image_collection_url` values are not used to identify parent repositories or create
their general links. This feature does not use interop data or require a new backend field.

## Configuration

```yaml
repositories:
  - repository_name: TCIA
    repository_url: https://nbia.cancerimagingarchive.net/
    aliases:
      - The Cancer Imaging Archive
```

| Field | Meaning |
| --- | --- |
| `repository_name` | Required nonblank display name, preferably the acronym. Also matches source names directly. |
| `repository_url` | Optional absolute HTTP(S) portal URL without credentials. Use the general repository link, not a collection URL or DOI. |
| `aliases` | Optional list of other nonblank source names for the same repository. |

Add an entry for a new repository, or update its URL and aliases. Matching ignores
case and surrounding spaces; duplicate repositories appear once. Names and aliases
must not conflict with another entry.

## Display behavior

- Configured repositories display their names with links when URLs are provided.
- Unknown repositories or entries without URLs display names as plain text.
- Blank source names are omitted.
- While YAML is loading, unavailable, or invalid, source names remain visible
  without links and study data remains available.
