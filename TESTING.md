# Export and install testing (2026-10-13)

Results of testing the lab's "Export for BigCommerce" output in a sandbox.

| Test | Method | Result |
|---|---|---|
| Setting keys | Every key in the config.json export checked against Cornerstone's `config.json` | 37 keys, all valid, all 7 themes |
| CSS compiles | Compiled with Stencil CLI 10.0.0's own compiler (node-sass and dart-sass), pasted at the end of `theme.scss` and as an imported partial | 28 of 28 builds pass |
| Theme bundles | `stencil bundle` on Cornerstone with the config merged into a variation and the CSS partial imported | Passes |
| Works on the live theme | CSS applied to saved copies of adrenalinerush.shop's home, category, product and cart pages | No leftover old colors, no text below WCAG AA contrast, 6 themes x 4 pages |
| Instructions | Each step checked against BigCommerce's help center ("Stencil Themes", "Using Script Manager", "Page Builder", "API Accounts") and Stencil CLI 10's `--help` and source | Corrected, see below |

## What the testing changed

- Adding a separate `custom.scss` does nothing: the theme only compiles files that `theme.scss` imports. The steps now paste into `theme.scss`.
- The store already has a custom CSS layer of `body ... !important` rules (black cards, white product text, purple description tab). The export now includes a patch that outranks each one.
- Script Manager only accepts JavaScript and applies to the live store at once, so it is no longer suggested.
- Theme Styles font menus only list the theme's own fonts, and the browser editor cannot edit `config.json`. Fonts now load from the CSS; the config export is for Stencil CLI only.
- On an inactive theme, Page Builder's Publish button makes it live. The steps say to use Save while previewing.
- Four setting keys that do not exist in Stencil themes were removed.
- `stencil push` bundles by itself and asks whether to apply (default No). Stencil CLI 10 needs Node.js 24+.
- Contrast fixes: Afterglow buttons use near-black text on pink, Grape Soda's header purple is slightly deeper so white text passes.

## Not tested

Nothing was uploaded to the real store (that needs your login). Two things can only be confirmed in your control panel:
whether `theme.scss` is editable in your theme's file editor, and whether homepage Page Builder content appears on a copied theme. The install steps tell you what to do in either case.

## Real-theme test (2026-10-13, later the same day)

Your own theme (Roots 5.2.2, downloaded with "Download current theme") was built locally with Stencil CLI 10.0.0
following Part B, with the Afterglow CSS added as `_lab-theme.scss` and imported at the end of `theme.scss`.

- `stencil start` previewed it against the live catalog at localhost:3000.
- Home, category, product and cart: no leftover old colors, no text below WCAG AA contrast, fonts loaded. Checked at desktop and phone width.
- Found and fixed in the steps: `stencil init` fails on downloaded themes ("No package manager auto-detected") because they have no lockfile. Part B now uses `--skipInstall` and a separate `npm install`, which was tested.

Still untested: the control-panel route (Part A) and uploading with `stencil push`. Nothing was uploaded.

## Scrolling promo bar (added 2026-10-13)

Tested in the same local build of the store's Roots 5.2.2 theme:

- Markup pasted above `{{> components/common/header }}` in `templates/layout/base.html`; CSS is Part 3 of the custom.css export.
- Roots pins the header to the top on phones and desktop and sets the page offset with JavaScript. The bar is pinned above it and the header and page move down by the bar's height. Checked on desktop and phone, after the header shrinks on scroll, and with the phone menu open.
- Pause button stops and restarts the scroll and updates its screen-reader label. Scrolling also pauses on hover and keyboard focus, and is off when the device asks for reduced motion (CSS rule present; not emulated in the test browser).
- Afterglow and Drop Culture scroll; the other themes get a still, centered bar. Both checked in the real theme.
- 14 of 14 compiles pass (7 themes, node-sass and dart-sass).
