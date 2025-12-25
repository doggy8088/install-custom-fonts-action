# install-custom-fonts-action

Install custom font files (OTF/TTF/WOFF) on GitHub Actions Linux runners to prevent missing fonts or inconsistent typography in tests, reports, or screenshots.

## Requirements

- Linux runners only (e.g., Ubuntu / Debian-based)
- GitHub-hosted Ubuntu runners:
  - `ubuntu-20.04`
  - `ubuntu-22.04`
  - `ubuntu-24.04`
  - `ubuntu-latest`

## Features

- ✅ Batch install fonts from a directory
- ✅ Install specific font files
- ✅ Support for OTF and TTF formats (primary)
- ✅ Support for WOFF format (fallback)
- ✅ Automatic font cache update
- ✅ Installation verification output

## Quick Start

### Install fonts from a directory

```yaml
steps:
  - uses: actions/checkout@v4

  - name: Install custom fonts
    uses: doggy8088/install-custom-fonts-action@v1
    with:
      fonts-dir: 'fonts'
```

### Install specific font files

```yaml
steps:
  - uses: actions/checkout@v4

  - name: Install custom fonts
    uses: doggy8088/install-custom-fonts-action@v1
    with:
      font-files: |
        fonts/MyFont-Regular.ttf
        fonts/MyFont-Bold.ttf
        assets/SpecialFont.otf
```

### Use both methods together

```yaml
steps:
  - uses: actions/checkout@v4

  - name: Install custom fonts
    uses: doggy8088/install-custom-fonts-action@v1
    with:
      fonts-dir: 'fonts'
      font-files: 'extra/SpecialFont.otf'
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `fonts-dir` | No | `''` | Relative path to fonts directory (from workspace root). Installs all OTF/TTF/WOFF files in the directory |
| `font-files` | No | `''` | List of font file paths (space or newline separated). Supports OTF/TTF/WOFF formats |

**Notes:**

- At least one of `fonts-dir` or `font-files` must be specified
- All paths are relative to workspace root (`${{ github.workspace }}`)
- Remember to run `actions/checkout` before using this action if fonts are in your repository

## Usage Examples

### Example 1: Install fonts from project directory

Assuming your project structure:

```
your-repo/
├── fonts/
│   ├── NotoSansTC-Regular.otf
│   ├── NotoSansTC-Bold.otf
│   └── custom/
│       └── SpecialFont.ttf
└── .github/
    └── workflows/
        └── test.yml
```

Workflow configuration:

```yaml
name: Test
on: [push]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Install fonts
        uses: doggy8088/install-custom-fonts-action@v1
        with:
          fonts-dir: 'fonts'

      - name: Run tests that require fonts
        run: npm test
```

### Example 2: Install only specific font files

```yaml
steps:
  - uses: actions/checkout@v4

  - name: Install fonts
    uses: doggy8088/install-custom-fonts-action@v1
    with:
      font-files: |
        fonts/NotoSansTC-Regular.otf
        fonts/NotoSansTC-Bold.otf
```

### Example 3: Download fonts from external source

```yaml
steps:
  - name: Download fonts
    run: |
      mkdir -p custom-fonts
      curl -L https://example.com/MyFont.ttf -o custom-fonts/MyFont.ttf

  - name: Install fonts
    uses: doggy8088/install-custom-fonts-action@v1
    with:
      fonts-dir: 'custom-fonts'
```

### Example 4: Install fonts from multiple sources

```yaml
steps:
  - uses: actions/checkout@v4

  - name: Download additional fonts
    run: |
      mkdir -p downloaded-fonts
      curl -L https://example.com/ExtraFont.ttf -o downloaded-fonts/ExtraFont.ttf

  - name: Install all fonts
    uses: doggy8088/install-custom-fonts-action@v1
    with:
      fonts-dir: 'fonts'
      font-files: 'downloaded-fonts/ExtraFont.ttf'
```

## Supported Font Formats

| Format | Extensions | Support Status | Notes |
|--------|-----------|----------------|-------|
| OpenType | `.otf`, `.OTF` | ✅ Full support | Recommended, excellent cross-platform compatibility |
| TrueType | `.ttf`, `.TTF` | ✅ Full support | Recommended, most common format |
| Web Open Font | `.woff`, `.WOFF` | ⚠️ Fallback support | Some applications may not support this format |

## Verify Font Installation

After installation, this action automatically displays the list of installed fonts. You can also manually verify in subsequent steps:

```yaml
steps:
  - name: Install fonts
    uses: doggy8088/install-custom-fonts-action@v1
    with:
      fonts-dir: 'fonts'

  - name: Verify fonts
    run: |
      fc-list | grep "Font Name"
      # Or list all installed fonts
      fc-list
```

## Recommended Settings

It's recommended to add a timeout to prevent excessively long font installation times:

```yaml
steps:
  - name: Install fonts
    uses: doggy8088/install-custom-fonts-action@v1
    timeout-minutes: 5
    with:
      fonts-dir: 'fonts'
```

## FAQ

### Q: Application can't find fonts after installation?

**A:** Check the following:

1. Font file format is correct (OTF/TTF/WOFF)
2. Application has reloaded the font cache
3. Using the correct font name (check with `fc-list`)

### Q: Is WOFF2 format supported?

**A:** Currently only WOFF is supported. WOFF2 is primarily for web use and most system applications don't support it. We recommend using OTF or TTF formats.

### Q: Can I download fonts directly from the internet?

**A:** This action doesn't provide download functionality, but you can download font files in a previous step and then use this action to install them. See "Example 3".

### Q: Where are fonts installed?

**A:** Fonts are installed to `$HOME/.local/share/fonts`, which is the standard user fonts directory and doesn't require sudo permissions.

### Q: Do I need to run checkout first?

**A:** If your font files are in the repository, you must run `actions/checkout@v4` first. If fonts are downloaded from external sources, it's not required.

## Versioning

Using Semantic Versioning (SemVer):

- Major version branch: `v1`
- Specific version tags: `v1.0.0`, `v1.1.0`, etc.

## Security Recommendations

To avoid the risk of tag movement, you can pin to a specific commit SHA:

```yaml
steps:
  - uses: doggy8088/install-custom-fonts-action@<commit-sha>
    with:
      fonts-dir: 'fonts'
```

Or use the major version branch (more convenient, automatically receives backward-compatible updates):

```yaml
steps:
  - uses: doggy8088/install-custom-fonts-action@v1
    with:
      fonts-dir: 'fonts'
```

## License

This project is licensed under the [MIT License](LICENSE).

**Note:** When using this action to install fonts, ensure you have the legal rights to use those fonts.
