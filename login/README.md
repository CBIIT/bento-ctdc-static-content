# CTDC Login Page Content

This guide explains how to edit the CTDC `/user/login` page from this static-content repository.

## Files

- `login/loginView.yaml`: Main content file for the login page.
- `login/assets/`: Images and icons referenced by `login/loginView.yaml`.

The frontend loads this file from the static-content base URL configured by `REACT_APP_STATIC_CONTENT_URL`, using the relative path `/login/loginView.yaml`.

Relative asset paths are resolved from `login/loginView.yaml`. For example:

```yaml
src: assets/help-icon.svg
```

points to:

```text
login/assets/help-icon.svg
```

## Required Metadata

Keep these top-level fields in `login/loginView.yaml`:

```yaml
page: /user/login
title: Login
```

These fields help downstream indexing, including dataloader/OpenSearch indexing for Global Search.

## Top-Level Schema

`login/loginView.yaml` uses fixed top-level areas plus a repeatable `sections` array:

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

Top-level fields:

| Field | Use |
| --- | --- |
| `page` | Frontend route for this content. Keep as `/user/login`. |
| `title` | Page title for indexing. Keep as `Login`. |
| `assets` | Image and icon paths used by the login page. |
| `hero` | Top hero content. |
| `sections` | Repeatable left-column content boxes. |
| `warning` | Warning notice below the left-column sections. |
| `help` | Right-side Help panel. |

## Assets

Each asset supports:

```yaml
src: assets/file-name.svg
alt: Accessible image description
```

Supported asset keys:

| Asset key | Use |
| --- | --- |
| `lockBorder` | Hero lock border image. |
| `lockIcon` | Hero lock image. |
| `helpIcon` | Help panel header icon. |
| `videoThumbnail` | Tutorial video thumbnail image. |
| `playIcon` | Tutorial video play button image. |
| `arrowOpen` | Accordion collapse icon. |
| `arrowClosed` | Accordion expand icon. |
| `externalLinkIcon` | Icon shown beside outbound links. |

To update an image:

1. Add or replace the image file in `login/assets/`.
2. Update the matching `assets.<assetName>.src` value in `login/loginView.yaml`.
3. Update `alt` text if the image meaning changes.

## Sections

The `sections` array controls the main left-column boxes. Add, remove, or reorder entries in this array to change the left-column page content.

Supported section types:

| Type | Use |
| --- | --- |
| `rasLogin` | RAS login box. It uses the same layout as `contentBox`, plus a block-level `rasButtonText` value for the RAS login button label. |
| `contentBox` | General editable content box. Use this for Request Access and any new similar boxes. |

Each section supports:

| Field | Required | Use |
| --- | --- | --- |
| `id` | Yes | Stable unique ID for the section. |
| `type` | Yes | `rasLogin` or `contentBox`. Other types are ignored by the frontend. |
| `title` | Yes | Section heading. |
| `blocks` | Yes | Ordered list of renderable blocks, block groups, and accordion groups. |

Use only one `blocks` key per section. If a section needs multiple text areas, accordions, or documentation blocks, add multiple sibling entries under that one `blocks` list.

Valid sibling block entries:

```yaml
sections:
  - id: example-section
    type: contentBox
    title: Example Section
    blocks:
      - paragraph: "First sibling block."

      - blocks:
          - paragraph: "Second sibling block group."
          - paragraph: "Another paragraph in the same group."

      - accordions:
          - title: Example accordion
            blocks:
              - paragraph: "Accordion content."
```

Invalid duplicate sibling keys:

```yaml
sections:
  - id: example-section
    type: contentBox
    title: Example Section
    blocks:
      - paragraph: "First group."
    blocks:
      - paragraph: "Second group."
```

Do not repeat `blocks:` at the same indentation level. YAML parsers can reject duplicate keys or silently drop one of them. The login frontend uses `js-yaml`, which rejects duplicate keys and shows a "Login page content is not valid YAML" error.

## Section Block Order

The frontend follows the order of the YAML:

- `sections` order controls the left-column box order.
- A section's `blocks` list controls the order of text groups and accordion groups.
- An `accordions` list controls accordion row order.
- `warning` always renders after the left-column `sections`.
- In `help`, `blocks`, `tutorial`, and `contact` render in the YAML key order after the fixed Help header.

This means blocks can appear before accordions, after accordions, or between multiple accordion groups.

## Section Block Entry Types

A section's `blocks` list can contain three kinds of entries.

Plain content blocks:

```yaml
blocks:
  - paragraph: "Intro paragraph."
  - listWithDots:
      - "First bullet"
      - "Second bullet"
```

For a `rasLogin` section, add `rasButtonText` inside the block group where the RAS button should display:

```yaml
sections:
  - id: ras-login
    type: rasLogin
    title: Log in with NIH Researcher Auth Service (RAS)
    blocks:
      - blocks:
          - paragraph: "Before accessing CTDC data, you are required to verify your identity."
          - rasButtonText: Login with RAS
          - paragraph: "Complete identity verification before accessing controlled-access data."
```

Block groups:

```yaml
blocks:
  - blocks:
      - paragraph: "First group paragraph."
      - paragraph: "Second paragraph in the same group."

  - title: Documentation
    blocks:
      - listWithDots:
          - "$$[eRA Commons Account Creation](https://www.era.nih.gov/register-accounts/create-and-edit-an-account.htm)$$"
```

Use a block group when several blocks should render together as one section item. The group `title` is optional.

Do not repeat the section-level `blocks:` key to add another group. Keep one section-level `blocks:` list, then add each group as a `- blocks:` item inside that list.

Accordion groups:

```yaml
blocks:
  - accordions:
      - title: How to sign in
        blocks:
          - listWithNumbers:
              - "Begin from the CTDC login page."
```

Adjacent plain blocks render together as one text group. Block groups render as separate text sections. Accordion groups render as accordion rows with the shared accordion styling.

## RAS Login Section

Use `type: rasLogin` for the RAS login card.

```yaml
sections:
  - id: ras-login
    type: rasLogin
    title: Log in with NIH Researcher Auth Service (RAS)
    blocks:
      - blocks:
          - paragraph: "Before accessing CTDC data, you are required to verify your identity."
          - rasButtonText: Login with RAS
          - paragraph: "Complete identity verification before accessing controlled-access data."
      - accordions:
          - title: How to sign in
            blocks:
              - listWithNumbers:
                  - "Begin from the CTDC login page and select the RAS sign-in option."
                  - "Complete the required identity proofing steps."
```

Only `rasLogin` sections show the RAS login button. Put `rasButtonText` inside the RAS text block group. On wider screens, the button displays to the right of that group; on smaller screens, it stacks below it.

The RAS login button destination is not configured in static content. It is controlled by the frontend environment variable `REACT_APP_RAS_AUTHORIZE_URL`. The frontend assumes this URL is configured.

## Content Box Sections

Use `type: contentBox` for editable boxes similar to Request Access.

```yaml
sections:
  - id: request-access
    type: contentBox
    title: Request Access
    blocks:
      - paragraph: "Access to controlled access data in the CTDC is governed by dbGaP. To learn more about requesting access to controlled access data please follow this link: $$[CTDC #/request-access page](url:/#/request-access target:_self)$$."

      - blocks:
          - paragraph: "Optional grouped paragraph one."
          - paragraph: "Optional grouped paragraph two."

      - accordions:
          - title: Optional accordion
            blocks:
              - paragraph: "Optional accordion copy."
```

To add a new box, add another `type: contentBox` entry under `sections`:

```yaml
sections:
  - id: another-section
    type: contentBox
    title: Another Editable Box
    blocks:
      - paragraph: "This can be added without frontend code changes."
```

## Accordions

Accordion rows are controlled by an `accordions` entry inside a section's `blocks` list.

```yaml
blocks:
  - accordions:
      - title: Existing accordion
        blocks:
          - paragraph: "Existing accordion text."

      - title: New accordion
        blocks:
          - paragraph: "Add the new accordion text here."
          - listWithDots:
              - "First bullet"
              - "Second bullet"
```

Each accordion entry should have:

- `title`
- `blocks`

Accordion options:

| Field | Use |
| --- | --- |
| `collapsible` | Defaults to `true`. Set `false` to show the content without a toggle. |
| `defaultOpen` | Defaults to `false`. Set `true` to start a collapsible accordion open. Users can still close it. |

Accordion open-state options:

- Omit both fields for a normal accordion that starts closed.
- Use `defaultOpen: true` for an accordion that starts open and can still be closed.
- Use `collapsible: false` for content that is always visible and cannot be closed.
- Do not combine `collapsible: false` with `defaultOpen`; `defaultOpen` is ignored when content is not collapsible.

To remove an accordion row, delete the full entry. To reorder rows, move the full entry up or down in the `accordions` list. To move the whole accordion group before or after text blocks, move the full `- accordions:` entry within the section's `blocks` list.

## Blocks

Editable copy lives in `blocks` arrays. The same block format is supported in:

- `rasLogin` section blocks
- `contentBox` section blocks
- accordion blocks
- `warning.blocks`
- `help.blocks`
- `help.tutorial.blocks`
- `help.contact.blocks`

Supported block types:

| Block | Use |
| --- | --- |
| `paragraph` | A paragraph or inline formatted text. |
| `listWithDots` | Bulleted list. |
| `listWithNumbers` | Numbered list. |
| `listWithAlphabets` | Lower-alpha lettered list. This matches the existing CTDC About page format. |
| `listWithLetters` | Alias for `listWithAlphabets`. |
| `table` | About-format table support. Use sparingly on the login page. |

Example:

```yaml
blocks:
  - paragraph: "A paragraph of text."
  - listWithDots:
      - "Bulleted item one"
      - "Bulleted item two"
  - listWithNumbers:
      - "Numbered item one"
      - "Numbered item two"
  - listWithAlphabets:
      - "Lettered item one"
      - "Lettered item two"
```

## Nested Lists

Use object list items when a list item needs a nested list.

```yaml
blocks:
  - paragraph: "The verification process typically takes up to 30 minutes and requires:"
  - listWithAlphabets:
      - "A mobile phone with a working camera"
      - "Your Social Security number"
      - text: "One of the following valid government-issued IDs:"
        listWithDots:
          - "U.S. driver's license"
          - "State-issued ID"
          - "U.S. passport"
```

Use `text` for the parent list item text. Then add one nested list key such as `listWithDots`, `listWithNumbers`, or `listWithAlphabets`.

## Inline Formatting

Login content supports the Bento-style `$$...$$` tokens used by CTDC About page content.

| Token | Use |
| --- | --- |
| `$$*text*$$` | Bold text. |
| `$$#text#$$` | Section-style heading text. |
| `$$~text~$$` | First-title style heading text. |
| `$$!text!$$` | Italic text. |
| `$$@text@$$` | Highlighted email/text. |
| `$$>text>$$` | Indented text. |
| `$$%space%$$` | Small vertical space. |
| `$$[label](https://example.org)$$` | Link. |
| `$$[label](target:_self url:https://example.org)$$` | Same-tab link with no outbound icon. |
| `$${link:https://example.org/file.pdf,title:Download file}$$` | Download link. |

Examples:

```yaml
blocks:
  - paragraph: "Use $$*bold text*$$ for emphasis."
  - paragraph: "$$#Before You Begin#$$"
  - paragraph: "Contact $$[NCICRDC@mail.nih.gov](NCICRDC@mail.nih.gov)$$ for help."
  - paragraph: "Open $$[GraphQL](url:/#/graphql target:_self)$$ in the same app."
  - paragraph: "$$%space%$$"
```

Do not use raw HTML inside block fields.

## Links And Outbound Icons

Use Bento-style links inside `paragraph` text or list item text.

External link:

```yaml
- paragraph: "$$[eRA Commons Account Creation](https://www.era.nih.gov/register-accounts/create-and-edit-an-account.htm)$$"
```

Internal CTDC app link:

```yaml
- paragraph: "$$[Request Access](url:/#/request-access target:_self)$$"
```

Email link:

```yaml
- paragraph: "$$[NCICRDC@mail.nih.gov](NCICRDC@mail.nih.gov)$$"
```

Download link:

```yaml
- paragraph: "$${link:https://example.org/file.pdf,title:Download Guide}$$"
```

Outbound icon behavior is based on the URL and target:

- External HTTP/HTTPS links open in a new tab and show the outbound icon by default.
- App-relative URLs such as `/#/graphql` or `/documentation` do not show the outbound icon.
- Email links such as `mailto:NCICRDC@mail.nih.gov` do not show the outbound icon.
- Links with `target:_self` do not show the outbound icon.

Standard inline links like `[Label](https://example.org)` are supported for simple text, but Bento-style links are preferred because they support `target:_self`, download links, and the correct outbound icon rules.

Login link styling is automatic for links in `blocks`. No extra YAML switch is needed.

## Spacing

Use this token for intentional vertical spacing:

```yaml
- paragraph: "$$%space%$$"
```

Do not use quoted blank spaces such as:

```yaml
- paragraph: "      "
```

Whitespace-only paragraphs are valid YAML, but they are not reliable for visual spacing in the frontend.

## Warning Notice

The `warning` area renders below the main left-column sections.

```yaml
warning:
  title: Warning Notice
  defaultOpen: true
  blocks:
    - paragraph: "Warning notice text."
```

Supported fields:

| Field | Use |
| --- | --- |
| `title` | Warning notice heading. |
| `blocks` | Warning notice body blocks. |
| `defaultOpen` | Defaults to `false`. Set `true` to start the warning open. Users can still close it. |
| `collapsible` | Defaults to `true`. Set `false` to show the warning content without a toggle. |

Warning open-state options:

- Omit both fields for a warning that starts closed and can be opened.
- Use `defaultOpen: true` for a warning that starts open and can still be closed.
- Use `collapsible: false` for warning content that is always visible and cannot be closed.
- Do not combine `collapsible: false` with `defaultOpen`; `defaultOpen` is ignored when the warning is not collapsible.

## Help Panel

The `help` area renders in the right-side Help panel.

```yaml
help:
  ariaLabel: Help and Support
  headerText: NEED HELP?
  blocks:
    - paragraph: "For help signing in or accessing your account, review the tutorial below or contact CTDC support."
  tutorial:
    title: Creating Accounts to Access CTDC data
    blocks:
      - paragraph: "This tutorial explains the steps involved in creating a Login.gov account."
    videoUrl: https://nccrdataplatform.ccdi.cancer.gov/video/NCCRCreatingAccountsTutorial.mp4
    playButtonAriaLabel: Play tutorial video
  contact:
    title: Let us assist you with your login or access issues
    blocks:
      - paragraph: "If you experience any difficulties, please reach out to our support team."
    buttonText: Contact Us
    href: mailto:NCICRDC@mail.nih.gov
    target: _self
```

Supported fields:

| Field | Use |
| --- | --- |
| `ariaLabel` | Accessible label for the Help panel landmark. |
| `headerText` | Help panel header text. |
| `blocks` | Generic Help panel blocks. |
| `tutorial.title` | Tutorial heading. |
| `tutorial.blocks` | Tutorial body blocks. |
| `tutorial.videoUrl` | Hosted tutorial video URL. |
| `tutorial.playButtonAriaLabel` | Accessible label for the play button. |
| `contact.title` | Contact section heading. |
| `contact.blocks` | Contact section body blocks. |
| `contact.buttonText` | Contact button label. |
| `contact.href` | Contact button URL, such as `mailto:NCICRDC@mail.nih.gov`. |
| `contact.target` | Optional link target, such as `_self` or `_blank`. |
| `contact.rel` | Optional relationship value. New-tab contact links default to `noopener noreferrer`. |

Within `help`, `blocks`, `tutorial`, and `contact` render in the YAML key order after the fixed Help header. Put `blocks` before `tutorial` for intro copy, or after `contact` for closing copy.

## Tutorial Video

The tutorial video URL is configured here:

```yaml
help:
  tutorial:
    videoUrl: https://nccrdataplatform.ccdi.cancer.gov/video/NCCRCreatingAccountsTutorial.mp4
```

Replace `videoUrl` with the new hosted video URL.

## Buttons

RAS login button:

```yaml
sections:
  - id: ras-login
    type: rasLogin
    blocks:
      - blocks:
          - paragraph: "Before accessing CTDC data, you are required to verify your identity."
          - rasButtonText: Login with RAS
```

The RAS button URL comes from the frontend environment variable `REACT_APP_RAS_AUTHORIZE_URL`, not from `login/loginView.yaml`.

Contact button:

```yaml
help:
  contact:
    buttonText: Contact Us
    href: mailto:NCICRDC@mail.nih.gov
    target: _self
```

Use `mailto:` for email buttons. If `target: _blank` is used, the frontend applies `rel: noopener noreferrer` by default unless `rel` is provided in YAML.

## Tables

The frontend supports the CTDC About-style `table` block.

```yaml
blocks:
  - table:
      - head:
          - "Resource"
          - "Link"
      - body:
          - row:
              - "RAS Help"
              - "$$[RAS Help](https://example.org/ras-help)$$"
```

Use tables sparingly on the login page because accordions and sidebars have limited width.

## Editing Checklist

Before opening a pull request:

- Confirm `login/loginView.yaml` is valid YAML.
- Confirm `page: /user/login` and `title: Login` remain at the top level.
- Confirm image paths point to files under `login/assets/`.
- Confirm main left-column boxes live under `sections`.
- Confirm each section has an `id`, `type`, `title`, and one `blocks` key.
- Use `type: rasLogin` only for the RAS login card.
- Use `type: contentBox` for Request Access and any new similar boxes.
- Confirm the RAS login button label is set with `rasButtonText` inside the `rasLogin` block group.
- Confirm section `blocks` lists use supported blocks, block groups, or `accordions` groups.
- Confirm each accordion entry has a `title` and `blocks`.
- Confirm nested lists use `text` plus a nested list key.
- Confirm intentional spacing uses `$$%space%$$`.
- Confirm links use full URLs, `mailto:`, or CTDC same-app paths such as `/#/graphql`.
- Confirm the contact button uses `href: mailto:NCICRDC@mail.nih.gov` when it should open an email client.
- Load `/user/login` and verify text, links, images, accordions, video playback, and buttons.
