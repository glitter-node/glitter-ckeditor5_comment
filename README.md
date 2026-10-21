# Glitter CKEditor5 Comment

An independent Gnuboard7 plugin derived from the MIT-licensed `g7-comment-editor` by William Cho. Glitter.kr maintains this adaptation; the upstream copyright and MIT notice are preserved in [LICENSE](LICENSE).

- **Identifier:** `glitter-ckeditor5_comment`
- **Display name:** Glitter CKEditor5 Comment
- **Vendor / Author:** Glitter.kr
- **Version:** 0.1.0
- **Repository:** https://github.com/glitter-node/glitter-ckeditor5_comment
- **Required plugin:** `sirsoft-ckeditor5 >=1.0.3`; the manifest declares it, and this plugin checks that it is active and meets the version on install and activation.
- **Optional integration:** if `window.GlitterCkeditor5Expansion.rescan` is available, the comment renderer asks it to rescan after upgrading rendered comment content. Expansion is not a manifest dependency.
- **Database:** no tables or migrations. The server sanitizer runs on sirsoft-board comment validation/data filter hooks.
- **Assets:** one global JS entry and generated IIFE. CKEditor UMD/CSS/translations are reused from sirsoft-ckeditor5 through the G7 asset API.

## Preserved behavior

The plugin enhances comment, reply, and edit textareas with CKEditor 5; synchronizes editor data to the existing textarea and G7 input flow; handles dynamically inserted forms and teardown; sanitizes submitted and rendered HTML using the shared allow-list policy; renders supported standalone SNS links and external image URLs; and retains the server-side sanitizer and empty-content/minimum-length validation.

Image upload is not part of the upstream comment plugin because it does not integrate with sirsoft-board's attachment flow. No admin settings screen, routes, or migration exist in the upstream implementation.

## Build and checks

```sh
npm --prefix plugins/_bundled/glitter-ckeditor5_comment run build
npm --prefix plugins/_bundled/glitter-ckeditor5_comment run build:check
```

The build only writes `dist/js/plugin.iife.js` inside this bundled plugin.

## Install sequence (after explicit production approval)

Install and activate the required editor plugin first if needed, then use:

```sh
php artisan plugin:install glitter-ckeditor5_comment --vendor-mode=bundled
php artisan plugin:activate glitter-ckeditor5_comment
```

The manifest dependency check requires `sirsoft-ckeditor5 >=1.0.3`. The comment plugin itself does not modify existing comment rows on install or uninstall.

## Coexistence notes

The identifier, PHP namespace, settings namespace, JS global, CSS IDs/classes and translated message namespace are independent. The CKEditor UMD script ID `ckeditor5_script` intentionally matches sirsoft-ckeditor5's public asset-loading contract to prevent a second UMD execution. When the original `g7-comment-editor` asset is already present, this plugin defers textarea binding to that active editor and still runs its independent server sanitizer. It also skips any textarea whose adjacent DOM already contains a CKEditor instance. Core asset priority loads the original before this plugin. The original does not expose a shared editor-instance reservation API, so runtime verification remains necessary.

## License and provenance

This derivative preserves William Cho's upstream MIT notice and adds Glitter.kr's modification notice. See [LICENSE](LICENSE).
