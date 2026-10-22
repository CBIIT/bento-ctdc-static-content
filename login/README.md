# CTDC Login Page Content

This guide explains how to edit the CTDC `/user/login` page from
`login/loginView.yaml`. Frontend implementation details live in
`crdc-ctdc-ui/src/pages/login/README.md`:
[crdc-ctdc-ui/src/pages/login/README.md](https://github.com/CBIIT/crdc-ctdc-ui/blob/main/src/pages/login/README.md).

The frontend loads:

```text
REACT_APP_STATIC_CONTENT_URL + /login/loginView.yaml
```

If remote YAML cannot load or parse, the frontend shows a dismissible banner
and renders its bundled fallback copy from
`crdc-ctdc-ui/src/assets/login/loginView.yaml`.

## YAML Structure

Keep this top-level order so the file is easy to scan and supports
dataloader/OpenSearch indexing:

```yaml
page: /user/login
title: Login

hero:
  ...

sections:
  ...

warning:
  ...

help:
  ...

assets:
  ...

styles:
  ...
```

| Area | Use |
| --- | --- |
| `page`, `title` | Required metadata for the login page and search indexing. |
| `hero` | Top page heading. |
| `sections` | Repeatable left-column content boxes. |
| `warning` | Warning notice below the left-column boxes. |
| `help` | Right-side Help panel. |
| `assets` | Image/icon paths under `login/assets/`. |
| `styles` | Optional named React inline style presets. |

## Metadata

Keep these values at the top of the file:

```yaml
page: /user/login
title: Login
```

## Hero

`hero.title` controls the top page heading:

```yaml
hero:
  title: Login to the CTDC
```

## Sections

`sections` renders in YAML order. Supported section types are:

| Type | Use |
| --- | --- |
| `rasLogin` | Same layout as `contentBox`, plus the special RAS login button. |
| `contentBox` | General editable content box. |

Each section should have one `blocks` key. Add sibling content as list items
under that single `blocks` array; do not repeat `blocks:` at the same
indentation level.

```yaml
sections:
  - id: ras-login
    type: rasLogin
    title: Log in with NIH Researcher Auth Service (RAS)
    blocks:
      - blocks:
          - paragraph: "Intro text."
          - rasButton:
              text: Login with RAS
              style: rasButton
      - accordions:
          - title: How to sign in
            defaultOpen: false
            blocks:
              - listWithNumbers:
                  - "Begin from the CTDC login page."

  - id: request-access
    type: contentBox
    title: Request Access
    blocks:
      - paragraph: "Learn more on the $$[request access page](url:/#/request-access target:_self)$$."
```

### Section Blocks

Editable copy lives in `blocks` arrays. The same block format also works in
accordions, `warning.blocks`, `help.blocks`, `help.tutorial.blocks`, and
`help.contact.blocks`.

| Block | Use |
| --- | --- |
| `blocks` | Nested group; wraps several child blocks with one optional `style`. |
| `paragraph` | Paragraph text with inline Bento tokens. |
| `span` | Inline text fragment with inline Bento tokens. |
| `listWithDots` | Bulleted list. |
| `listWithNumbers` | Numbered list. |
| `listWithAlphabets` | Lower-alpha lettered list. |
| `listWithLetters` | Alias for `listWithAlphabets`. |
| `table` | About-format table; use sparingly on the login page. |

Only section-level `- blocks:` groups render an optional group `title`.
Generic nested `blocks` groups are wrappers for child blocks and style only.

Use a nested group when several blocks should share one style. The style names
below are examples; define the names you use under top-level `styles`.

```yaml
blocks:
  - style: exampleGroup
    blocks:
      - paragraph:
          text: "Example heading text."
          style: exampleHeading
      - paragraph:
          text: "Go to $$[example page](url:https://example.org style:exampleLink externalIconStyle:exampleIcon)$$"
          style: exampleParagraph
```

`span` is inline only. It can style a short text fragment, but it cannot contain
lists, accordions, tables, or nested blocks. Add the next block as another list
item after the span.

Duplicate YAML keys can overwrite content or trigger fallback. For multiple
groups, keep one parent `blocks:` key and add more `- blocks:` list items.

### RAS Button

Use `rasButton` only inside a `rasLogin` section text group. It supports
`text` and optional `style`. Do not add `href`, `target`, or `rel`; the URL is
always controlled by the frontend `REACT_APP_RAS_AUTHORIZE_URL`.

### Accordions

Add accordions inside a section's `blocks` list.

| Field | Use |
| --- | --- |
| `title` | Accordion row label. |
| `blocks` | Accordion body content. |
| `collapsible` | Defaults to `true`; set `false` for always-visible content. |
| `defaultOpen` | Defaults to `false`; ignored when `collapsible: false`. |

### Links And Tokens

Login text uses Bento-style `$$...$$` tokens, not full Markdown.

| Token | Use |
| --- | --- |
| `$$*text*$$` | Bold/title style. |
| `$$#text#$$` | Section-style heading. |
| `$$~text~$$` | First-title style heading. |
| `$$!text!$$` | Italic style. |
| `$$@text@$$` | Highlighted email/text. |
| `$$>text>$$` | Indented text. |
| `$$%space%$$` | Small vertical space. |
| `$$[label](https://example.org)$$` | Link. |
| `$${link:https://example.org/file.pdf,title:Download Guide}$$` | Download-style link. |

Plain Markdown-style `[Label](https://example.org)`, `**bold**`, and
`*italic*` text is displayed literally.

External HTTP/HTTPS links open in a new tab and show the outbound icon.
Same-app links, `mailto:`, `tel:`, hash links, and `target:_self` links do not
show the icon. Unsupported schemes such as `javascript:`, `data:`, `file:`,
`blob:`, `ftp:`, and protocol-relative URLs are not rendered as links.

## Warning

`warning` renders below the left-column sections and uses the same block rules
described above:

```yaml
warning:
  title: Warning Notice
  defaultOpen: false
  blocks:
    - paragraph: "Warning notice text."
```

`collapsible` defaults to `true`; `defaultOpen` defaults to `false`.

## Help

`help` renders in the right-side Help panel. Its subareas render in YAML key
order after the Help header.

```yaml
help:
  ariaLabel: Help and Support
  headerText: NEED HELP?
  blocks:
    - paragraph: "Generic help copy."
  contact:
    blocks:
      - paragraph: "Contact support for help."
    button:
      text: Contact Us
      style: contactButton
      href: mailto:NCICRDC@mail.nih.gov
      target: _self
```

| Area | Use |
| --- | --- |
| `blocks` | Generic Help panel content. |
| `tutorial` | Optional tutorial copy, thumbnail/video, and play label. |
| `contact` | Contact copy and configurable contact button. |

`help.contact.button` supports `text`, `style`, `href`, `target`, and `rel`.
Safe `mailto:`, `tel:`, HTTP/HTTPS, hash, root-relative, and relative links are
supported.

## Assets

Each asset uses:

```yaml
assets:
  lockIcon:
    src: assets/lock-icon.svg
    alt: Lock Icon
```

Supported keys are `lockBorder`, `lockIcon`, `helpIcon`, `videoThumbnail`,
`playIcon`, `arrowOpen`, `arrowClosed`, and `externalLinkIcon`.

## Styles

Define reusable presets under top-level `styles`, then reference them with
`style` where needed. This is a generic example; use names that describe the
content you are styling.

```yaml
styles:
  exampleGroup:
    marginTop: 24
  exampleHeading:
    fontWeight: 700
  exampleParagraph:
    textAlign: center
  exampleLink:
    color: "#005ea8"
    textDecoration: underline
  exampleIcon:
    color: "#005ea8"
```

Rules:

- `style` applies where it is declared: section, warning, Help, tutorial, contact, nested group, list, table, button, or link.
- For `paragraph` and `span`, put `style` inside the text object.
- `style` can be one preset name, an array of preset names, or a small inline style object.
- Link tokens can use `style:styleName` and `externalIconStyle:styleName`.
- Style keys must be React camelCase, such as `fontSize`, `marginTop`, or `backgroundColor`.

Advanced styling is allowed, but must be locally tested before merging.
Layout-affecting properties such as `display`, `position`, `width`, `height`,
`overflow`, `transform`, and `zIndex` can hide content or affect mobile layout.

## Preview Checklist

- Keep `page: /user/login` and `title: Login`.
- Keep top-level content in the documented YAML order.
- Keep image paths under `login/assets/`.
- Use one `blocks` key per section.
- Use `rasButton` only in the `rasLogin` section.
- Confirm each accordion has `title` and `blocks`.
- Use supported block, token, link, and style formats.
- Preview `/user/login` on desktop and mobile.
- Confirm the fallback banner is not visible.
