# CTDC Login Page Content

This guide explains how to edit the CTDC `/user/login` page from the
static-content repository. Frontend implementation details live in
`crdc-ctdc-ui/src/pages/login/README.md`:
[crdc-ctdc-ui/src/pages/login/README.md](https://github.com/CBIIT/crdc-ctdc-ui/blob/main/src/pages/login/README.md).

## Files

| Path | Use |
| --- | --- |
| `login/loginView.yaml` | Main login page content file. |
| `login/assets/` | Images and icons referenced by `login/loginView.yaml`. |

The frontend loads:

```text
REACT_APP_STATIC_CONTENT_URL + /login/loginView.yaml
```

If the remote YAML cannot load, times out, or cannot parse, the frontend shows
a dismissible banner and renders its bundled fallback copy from
`crdc-ctdc-ui/src/assets/login/loginView.yaml`.

## Required Metadata

Keep these fields at the top level:

```yaml
page: /user/login
title: Login
```

They support dataloader/OpenSearch indexing for Global Search.

## Page Schema

`login/loginView.yaml` uses this top-level shape:

```yaml
page: /user/login
title: Login

assets:
  ...

hero:
  title: Login to the CTDC

sections:
  ...

warning:
  ...

help:
  ...
```

| Area | Use |
| --- | --- |
| `assets` | Image/icon paths under `login/assets/`. |
| `hero` | Top page heading and lock imagery. |
| `sections` | Repeatable left-column boxes. |
| `warning` | Warning notice below the left-column boxes. |
| `help` | Right-side Help panel. |

## Assets

Each asset uses:

```yaml
src: assets/file-name.svg
alt: Accessible image description
```

Supported asset keys are `lockBorder`, `lockIcon`, `helpIcon`,
`videoThumbnail`, `playIcon`, `arrowOpen`, `arrowClosed`, and
`externalLinkIcon`.

To update an image, add or replace the file under `login/assets/`, update the
matching `assets.<assetName>.src`, and update `alt` text if the meaning changes.

## Sections

`sections` controls the left-column boxes. Add, remove, or reorder entries to
change the page content.

| Type | Use |
| --- | --- |
| `rasLogin` | RAS login box. Same layout as `contentBox`, plus the RAS button. |
| `contentBox` | General editable content box, such as Request Access. |

Each section should include `id`, `type`, `title`, and one `blocks` key. Do not
repeat `blocks:` at the same indentation level; duplicate YAML keys can fail
parsing and trigger the fallback banner.

Example:

```yaml
sections:
  - id: ras-login
    type: rasLogin
    title: Log in with NIH Researcher Auth Service (RAS)
    blocks:
      - blocks:
          - paragraph: "Before accessing CTDC data, you are required to verify your identity."
          - rasButtonText: Login with RAS
      - accordions:
          - title: How to sign in
            blocks:
              - listWithNumbers:
                  - "Begin from the CTDC login page."

  - id: request-access
    type: contentBox
    title: Request Access
    blocks:
      - paragraph: "Access to controlled access data in the CTDC is governed by dbGaP. Learn more on the $$[request access page](url:/#/request-access target:_self)$$."
```

Section `blocks` are rendered in YAML order:

| Entry | Use |
| --- | --- |
| `- paragraph`, `- listWithDots`, etc. | Plain blocks; adjacent plain blocks render together. |
| `- blocks:` | Separate block group. Optional `title` becomes a subsection title. |
| `- accordions:` | Accordion group. |

## RAS Button

Only `type: rasLogin` renders the RAS login button. Place the label in the text
group where the button should appear:

```yaml
- blocks:
    - paragraph: "Intro text."
    - rasButtonText: Login with RAS
```

The button URL is controlled by the frontend environment variable
`REACT_APP_RAS_AUTHORIZE_URL`, not by YAML. It must be an absolute `http://` or
`https://` URL. Missing, unresolved, relative, or invalid values disable the
button and show the configured unavailable message.

## Accordions

Add accordions inside a section's `blocks` list:

```yaml
- accordions:
    - title: Preparing your identity
      defaultOpen: true
      blocks:
        - paragraph: "The verification process typically takes up to 30 minutes."
```

| Field | Use |
| --- | --- |
| `title` | Accordion row label. |
| `blocks` | Accordion body content. |
| `collapsible` | Defaults to `true`. Set `false` for always-visible content. |
| `defaultOpen` | Defaults to `false`. Set `true` to start open. Ignored when `collapsible: false`. |

To remove or reorder accordions, delete or move the full item under
`accordions`.

## Blocks

Editable copy lives in `blocks` arrays. The same block format works in section
blocks, accordion blocks, `warning.blocks`, `help.blocks`,
`help.tutorial.blocks`, and `help.contact.blocks`.

| Block | Use |
| --- | --- |
| `paragraph` | Paragraph text with inline Bento tokens. |
| `listWithDots` | Bulleted list. |
| `listWithNumbers` | Numbered list. |
| `listWithAlphabets` | Lower-alpha lettered list. |
| `listWithLetters` | Alias for `listWithAlphabets`. |
| `table` | About-format table; use sparingly on the login page. |

Nested list example:

```yaml
- listWithAlphabets:
    - "A mobile phone with a working camera"
    - text: "One valid government-issued ID:"
      listWithDots:
        - "U.S. driver's license"
        - "U.S. passport"
```

Use `$$%space%$$` for intentional vertical spacing. Do not use blank-space-only
paragraphs.

## Inline Tokens And Links

Login content uses Bento-style `$$...$$` tokens:

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

Link behavior is based on URL and target:

- External `http://` and `https://` links open in a new tab and show the outbound icon.
- Same-app links such as `/#/request-access`, `mailto:`, `tel:`, hash links, and `target:_self` links do not show the icon.
- Raw email addresses in Bento links become `mailto:` links.
- Unsupported schemes such as `javascript:`, `data:`, `file:`, `blob:`, `ftp:`, and protocol-relative URLs such as `//example.org` are not rendered as links.

## Warning

`warning` renders below the left-column sections:

```yaml
warning:
  title: Warning Notice
  defaultOpen: true
  blocks:
    - paragraph: "Warning notice text."
```

`collapsible` defaults to `true`; `defaultOpen` defaults to `false`.
Use `collapsible: false` for content that should always be visible.

## Help Panel

The `help` area renders in the right-side Help panel:

```yaml
help:
  ariaLabel: Help and Support
  headerText: NEED HELP?
  blocks:
    - paragraph: "Generic help intro copy."
  tutorial:
    title: Creating Accounts to Access CTDC data
    blocks:
      - paragraph: "Tutorial description."
    videoUrl: https://nccrdataplatform.ccdi.cancer.gov/video/NCCRCreatingAccountsTutorial.mp4
    playButtonAriaLabel: Play tutorial video
  contact:
    title: Let us assist you with your login or access issues
    blocks:
      - paragraph: "Contact support for help."
    buttonText: Contact Us
    href: mailto:NCICRDC@mail.nih.gov
    target: _self
```

`blocks`, `tutorial`, and `contact` render in YAML key order after the Help
header. `ariaLabel` defaults to `Help and Support`; `playButtonAriaLabel`
defaults to `Play tutorial video`; unsafe contact button hrefs are ignored.
`contact.target` and `contact.rel` are optional. New-tab contact buttons default
to `rel: noopener noreferrer` unless `rel` is provided.

## Previewing Changes

Point the frontend to your static-content branch:

```text
REACT_APP_STATIC_CONTENT_URL=https://raw.githubusercontent.com/CBIIT/bento-ctdc-static-content/refs/heads/<branch-name>
```

For local login-button testing, also configure:

```text
REACT_APP_RAS_AUTHORIZE_URL=<RAS authorize URL>
```

If the fallback banner appears, the branch YAML did not load or parse. Confirm
your branch content is visible on `/user/login`, not only that the page renders.

## Editing Checklist

- Keep `page: /user/login` and `title: Login`.
- Keep image paths under `login/assets/`.
- Use one `blocks` key per section.
- Use `rasButtonText` only in the `rasLogin` section.
- Confirm each accordion has `title` and `blocks`.
- Use supported block and link formats.
- Use `$$%space%$$` for intentional spacing.
- Preview `/user/login` and verify text, links, images, accordions, video, buttons, and that the fallback banner is not visible.
