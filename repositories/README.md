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

## Editing the configuration

Each entry contains a display name, an optional general portal URL, and optional aliases:

```yaml
repositories:
  - repository_name: TCIA
    repository_url: https://nbia.cancerimagingarchive.net/
    aliases:
      - The Cancer Imaging Archive
```

| Property | Meaning |
| --- | --- |
| `repository_name` | Required nonblank display name; prefer the repository acronym. This name also matches source data directly. |
| `repository_url` | Optional absolute HTTP(S) general portal URL. Omit it or use `null` when unavailable. Do not use a collection URL or DOI. |
| `aliases` | Optional list of other source names for the same repository. |

Names and aliases match after trimming surrounding spaces and ignoring case. The
frontend keeps one entry per resolved repository, in first-seen order. For example,
`TCIA`, ` tcia `, and `The Cancer Imaging Archive` all display as one linked `TCIA`
entry when using the configuration above.

To add a repository, add another entry under `repositories`. To change a link or
recognize another source name, update its URL or aliases. No frontend mapping is
hardcoded, and no bundled copy of this YAML needs to be kept in sync.

## Missing data and configuration errors

| Condition | Display behavior |
| --- | --- |
| Configured name with a URL | Displays the configured name as a link to the general portal. |
| Configured name without a URL | Displays the configured name as plain text. |
| Unlisted source name | Displays the trimmed source name as plain text. |
| Missing or blank source name | Omits that entry. |
| YAML is loading, unavailable, or invalid | Displays the source repository names without links; study data remains available. |

Any nonblank repository name still produces an indicator, even when no link is
available. Image Collections counts continue to come from the study API separately.

The frontend rejects invalid YAML, blank configured names or aliases, non-list
aliases, conflicting mappings, and invalid URLs. URLs must use HTTP(S), contain a
host, and have no embedded credentials. It validates the complete configuration
before applying links; a failed validation uses the source-name-only behavior.

## Publishing updates

Follow the [static-content contribution and promotion workflow](../README.md#contribution-workflow).
Each environment reads from its configured static-content base URL. The frontend
fetches this file once per Studies page mount, with a 10-second request timeout.

Publish the YAML before deploying the frontend that loads it. That initial frontend
integration requires an application deployment. Later YAML changes require only
static-content publication/promotion: no frontend or backend rebuild/redeployment,
backend restart, OpenSearch data reload, or reindex.

Already-open pages do not poll for updates. Refresh or revisit Studies to fetch the
configuration again. Browser and hosting/CDN cache lifetimes can delay visibility;
publishing a file does not invalidate already-cached responses automatically.

This file maps existing repository names to display information. It does not add
image collections or associations to a study.

## Implementation

The frontend's `src/utils/repositories.js` loads and validates this file and resolves
names from `image_collection`. `src/pages/studies/studiesController.js` loads the
configuration independently of study data. Its `repositories` row property supplies
the tooltip and its `unique_repository` name array supports the indicator and CSV.
Both are derived frontend values, not new GraphQL fields.