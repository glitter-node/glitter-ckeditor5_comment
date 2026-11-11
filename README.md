# Glitter CKEditor5 Comment

An independent CKEditor 5 comment editor plugin for Gnuboard7, derived from the MIT-licensed `g7-comment-editor` originally authored by William Cho.

Glitter.kr maintains this adaptation, including its security, compatibility, and Content Security Policy (CSP) improvements.

The original author's copyright and MIT license notices are preserved in [LICENSE](LICENSE). Additional attribution and licensing information is available in [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md).

## Project Information

| Item | Description |
|---|---|
| Plugin identifier | `glitter-ckeditor5_comment` |
| Display name | Glitter CKEditor5 Comment |
| Vendor / Maintainer | Glitter.kr |
| Version | **0.1.2** |
| Repository | [glitter-node/glitter-ckeditor5_comment](https://github.com/glitter-node/glitter-ckeditor5_comment) |
| Required plugin | `sirsoft-ckeditor5 >=1.0.3` |
| License | MIT |
| Original project | `g7-comment-editor` by William Cho |
| Database migrations | None |

The plugin requires `sirsoft-ckeditor5` to be installed and active.

Its manifest declares the dependency, and the plugin checks the required version during installation and activation.

### Optional Integration

When `window.GlitterCkeditor5Expansion.rescan` is available, the comment renderer requests a rescan after upgrading rendered comment content.

`glitter-ckeditor5_expansion` is an optional integration and is not a manifest dependency.

## Features

Glitter CKEditor5 Comment extends the existing Gnuboard7 comment system without replacing its underlying comment storage or submission flow.

### CKEditor 5 Integration

- Enhances comment, reply, and edit textareas with CKEditor 5.
- Synchronizes editor content with the original textarea and Gnuboard7 input flow.
- Supports dynamically inserted comment forms.
- Handles editor initialization and teardown.
- Reuses the CKEditor 5 UMD distribution, CSS, and translations provided by `sirsoft-ckeditor5`.
- Uses the Gnuboard7 asset API for editor resource loading.
- Avoids unnecessary duplicate CKEditor initialization.

### HTML Sanitization

The plugin retains its server-side HTML sanitizer and shared allow-list policy.

- Sanitizes submitted comment HTML.
- Sanitizes rendered comment content.
- Preserves supported formatting through explicitly allowed markup and CSS classes.
- Retains empty-content and minimum-length validation.
- Uses existing `sirsoft-board` comment validation and data-filter hooks.
- Does not require additional database tables or migrations.

The sanitizer remains responsible for restricting unsupported or unsafe HTML.

### Supported Content

- Rich-text comments and replies
- Comment editing
- Supported standalone SNS links
- External image URLs
- Allowed text formatting
- Supported alignment and indentation formatting
- Optional integration with Glitter CKEditor5 Expansion

Actual external content rendering depends on browser policies, the hosting site's CSP, and third-party service restrictions.

## Content Security Policy (CSP) Compatibility

### Improvements in v0.1.2

Version 0.1.2 improves compatibility with strict Content Security Policy configurations by reducing reliance on dynamically loaded third-party scripts and inline styling.

The changes focus on the following CSP directives:

- `script-src`
- `style-src`
- `style-src-elem`
- `style-src-attr`

The objective is to support environments that do not permit `'unsafe-inline'` or `'unsafe-eval'`, without weakening the hosting application's security policy.

These changes improve the plugin's own CSP compatibility. They do not establish complete CSP compliance for Gnuboard7 core, CKEditor 5, or other installed extensions.

### External SNS SDK Removal

Previous rendering paths could rely on dynamically loaded external SNS JavaScript SDKs.

Version 0.1.2 removes the relevant dynamic SDK-loading behavior and uses safer link-based fallbacks for supported external content.

This reduces the need to allow additional third-party script origins through `script-src`.

Supported SNS links remain accessible without requiring the hosting site to load the corresponding platform SDK.

Link-based fallbacks are not equivalent to fully interactive third-party embeds. Their appearance and behavior may differ from the previous implementation.

### CSS Class-Based Formatting

Version 0.1.2 improves alignment and indentation handling in sanitized comment HTML.

Formatting that previously depended on inline style attributes is represented using supported CSS classes and corresponding stylesheet rules.

This approach reduces conflicts with strict policies such as:

```http
style-src 'self';
style-src-elem 'self';
style-src-attr 'none';
```

The sanitizer's formatting rules and the frontend stylesheet must remain consistent so that allowed formatting is displayed correctly.

### External Stylesheets

Static presentation rules are maintained in the plugin's stylesheet assets rather than being introduced through inline style attributes or dynamically inserted style elements where the updated implementation provides an external CSS equivalent.

The plugin continues to reuse CKEditor 5 assets supplied by the required `sirsoft-ckeditor5` dependency.

The hosting application's CSP must allow the relevant local JavaScript and CSS resources.

### CSP Scope and Limitations

Version 0.1.2 addresses CSP-related behavior within Glitter CKEditor5 Comment.

It does not modify:

- Gnuboard7 core
- `sirsoft-ckeditor5`
- `sirsoft-board`
- Other installed plugins
- Server-level CSP headers
- Nginx or web server security configuration

Strict CSP compatibility also depends on the behavior of the required editor plugin, other extensions, saved comment HTML, and the browser.

A successful build or plugin activation does not, by itself, prove that every comment editing and rendering scenario works under a strict CSP.

## Architecture and Assets

The plugin uses the existing Gnuboard7 plugin architecture.

### Server Integration

The server-side sanitizer operates through `sirsoft-board` comment validation and data-filter hooks.

It preserves the existing comment data flow without introducing additional tables or migrations.

### Frontend Integration

The frontend provides one global JavaScript entry and a generated IIFE bundle.

CKEditor 5 UMD, CSS, and translation resources are reused from `sirsoft-ckeditor5` through the Gnuboard7 asset API.

The bundled frontend output is:

```text
dist/js/plugin.iife.js
```

The build process writes this generated JavaScript asset within the bundled plugin.

Static styling is maintained through the plugin's existing resource and asset structure.

## Build and Validation

Run the following commands from the Gnuboard7 project root:

```sh
npm --prefix plugins/_bundled/glitter-ckeditor5_comment run build
npm --prefix plugins/_bundled/glitter-ckeditor5_comment run build:check
```

These commands build and check the plugin's frontend assets.

### Recommended Static Checks

Before distribution, verify:

- JavaScript syntax
- PHP syntax
- Plugin manifest validity
- Dependency declarations
- Generated JavaScript assets
- External SNS SDK-loading paths
- Inline style generation patterns
- Sanitizer allow-list behavior
- Alignment and indentation class mappings
- Required stylesheet availability

### Runtime Verification

The following scenarios should be checked in the target Gnuboard7 environment:

- Creating a new comment
- Editing an existing comment
- Replying to a comment
- Dynamically inserted comment forms
- CKEditor initialization and teardown
- Empty-content and minimum-length validation
- Server-side HTML sanitization
- Supported SNS links and external images
- Alignment and indentation formatting
- Coexistence with other CKEditor extensions
- Strict CSP browser console and violation reports

The v0.1.2 CSP improvements were reviewed through the relevant source modifications and build/deployment workflow.

Complete browser-level verification under a strict CSP, including all supported editor and comment-rendering scenarios, remains a separate validation requirement.

## Installation

### Requirements

- A compatible Gnuboard7 installation
- `sirsoft-ckeditor5` version `1.0.3` or later
- The required editor plugin installed and activated
- A PHP runtime compatible with the installed Gnuboard7 version
- Appropriate JavaScript and CSS asset access

The plugin does not independently bundle the CKEditor 5 editor distribution.

### Installation Sequence

Install and activate `sirsoft-ckeditor5` first if it is not already available.

For a bundled-source installation, run the following commands from the Gnuboard7 project root after reviewing the target environment and approving the operation:

```sh
/usr/local/bin/php83 artisan plugin:install glitter-ckeditor5_comment --vendor-mode=bundled
/usr/local/bin/php83 artisan plugin:activate glitter-ckeditor5_comment
```

The PHP executable shown above is an environment-specific example. Use the appropriate CLI PHP executable for your Gnuboard7 installation.

The plugin checks the required `sirsoft-ckeditor5` dependency during installation and activation.

### Updating an Existing Installation

Use the Gnuboard7 plugin management lifecycle supported by the installed core version.

When the relevant command and bundled-source option are supported, an update may be performed with:

```sh
/usr/local/bin/php83 artisan plugin:update glitter-ckeditor5_comment --source=bundled
```

Review the available command options before execution.

Back up the relevant application files and database before updating a production installation.

After updating, confirm the installed version, activation status, and published JavaScript/CSS assets.

Do not manually overwrite the active installed plugin directory as a substitute for the supported plugin update lifecycle.

## Compatibility and Coexistence

The plugin uses its own:

- Plugin identifier
- PHP namespace
- Settings namespace
- JavaScript global
- CSS identifiers and classes
- Translated message namespace

The CKEditor UMD script identifier `ckeditor5_script` intentionally matches the public asset-loading contract used by `sirsoft-ckeditor5`, preventing unnecessary duplicate execution.

When the original `g7-comment-editor` asset is already present, Glitter CKEditor5 Comment defers textarea binding to that active editor while retaining its independent server-side sanitizer.

It also skips textareas whose adjacent DOM already contains a CKEditor instance.

Gnuboard7 core asset priority loads the original plugin before this plugin.

The original plugin does not expose a shared editor-instance reservation API. Runtime verification is therefore necessary when both plugins are active.

### Optional Expansion Integration

Glitter CKEditor5 Comment can coexist with Glitter CKEditor5 Expansion.

When the expansion plugin exposes `window.GlitterCkeditor5Expansion.rescan`, the comment renderer can request a rescan after comment content has been upgraded.

This integration is optional and does not create a mandatory dependency on Glitter CKEditor5 Expansion.

### Functional Limitations

Image upload is not part of the original comment editor integration because it does not integrate with the `sirsoft-board` attachment flow.

The plugin does not introduce an administrator settings screen, additional routes, or database migrations.

Existing comment rows are not modified during installation or uninstallation.

## Version History

### v0.1.2 — CSP Compatibility Improvements

- Removed relevant dynamic external SNS SDK-loading behavior.
- Introduced safer link-based fallbacks for supported SNS content.
- Improved alignment and indentation formatting through CSS classes.
- Reduced reliance on inline style attributes in sanitized comment content.
- Improved compatibility with strict `script-src` and `style-src` policies.
- Preserved the existing comment editor integration and server-side sanitizer.
- Preserved existing database and comment storage behavior.
- Kept Gnuboard7 core and official dependencies unchanged.

These changes reduce CSP incompatibilities within the plugin but do not guarantee complete strict-CSP compatibility across all browser and dependency combinations.

### v0.1.1 — Previous Release

- Maintained the independent comment editor integration.
- Preserved the original comment submission and validation flow.
- Reused CKEditor 5 assets from `sirsoft-ckeditor5`.
- Maintained server-side HTML sanitization.
- Supported dynamically inserted comment forms.
- Preserved coexistence behavior with the original comment editor.

## License and Attribution

Glitter CKEditor5 Comment is derived in part from the MIT-licensed `g7-comment-editor` project originally authored by William Cho.

**Original work**

- Project: `g7-comment-editor`
- Author: William Cho
- Copyright (c) 2026 William Cho
- License: MIT

**Modifications and extensions**

- Project: Glitter CKEditor5 Comment
- Maintainer: Glitter.kr
- Copyright (c) 2026 Glitter.kr
- License: MIT

The original author's applicable copyright and license notices are preserved.

The modifications and independently authored components contributed by Glitter.kr are distributed under the MIT License.

CKEditor 5, Gnuboard7, and other third-party components remain subject to their respective licenses.

For details, see:

- [LICENSE](LICENSE)
- [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md)

## Repository

**Glitter CKEditor5 Comment v0.1.2**

https://github.com/glitter-node/glitter-ckeditor5_comment

Maintained by Glitter.kr.
