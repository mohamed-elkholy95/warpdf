# Wrapdf - Claude Code Instructions

## Project Overview

Wrapdf is a free, open-source, privacy-first PDF toolkit that runs entirely in the browser. All PDF processing happens client-side using WebAssembly (WASM) modules - no files are ever uploaded to a server.

**Tech Stack**: TypeScript, Vite 7.x, WASM (PyMuPDF, Ghostscript), i18next, Service Workers

## Quick Commands

```bash
# Development
npm run dev          # Start dev server with WASM headers
npm run build        # Production build
npm run preview      # Preview production build

# Documentation
npm run docs:dev     # VitePress docs dev server
npm run docs:build   # Build documentation

# Code Quality
npm run lint         # Run ESLint
npm run format       # Format with Prettier
npm run check-i18n   # Validate translation completeness
```

## Project Structure

```
src/
├── js/
│   ├── main.ts              # App entry point, router, UI initialization
│   ├── logic/               # PDF tool implementations
│   │   ├── coherentpdf.ts   # cpdf WASM wrapper
│   │   ├── mupdf.ts         # PyMuPDF WASM wrapper
│   │   └── [tool].ts        # Individual tool logic
│   ├── components/          # UI components (dialogs, previews)
│   ├── utils/               # Helpers (download, i18n, fonts)
│   └── types/               # TypeScript type definitions
├── scss/                    # Styles (modular SCSS)
├── html/                    # 127 tool HTML pages
└── locales/                 # i18n JSON files (en, de, zh, vi, it)
public/
├── sw.js                    # Service worker for offline support
├── wasm/                    # WASM binaries (git-lfs tracked)
└── assets/                  # Static assets
docs/                        # VitePress documentation
```

## Key Architectural Patterns

### WASM Module Loading
- PyMuPDF: `@bentopdf/pymupdf-wasm` - PDF rendering, conversion
- Ghostscript: `@bentopdf/gs-wasm` - PDF/A conversion, compression
- CPDF: Custom build in `/public/wasm/cpdf/`
- Always lazy-load WASM modules only when needed

### i18n System
- Uses i18next with HTML data attributes: `data-i18n="key"`
- Locale files in `src/locales/{lang}.json`
- Run `npm run check-i18n` after adding new strings
- Support: English (en), German (de), Chinese (zh), Vietnamese (vi), Italian (it)

### Tool Implementation Pattern
Each tool follows this pattern:
1. HTML page in `src/html/tools/[tool].html`
2. Logic in `src/js/logic/[tool].ts`
3. Registered in router (`src/js/main.ts`)
4. i18n keys in all locale files

### Storage Keys
- Fonts: IndexedDB database `wrapdf-fonts`
- Shortcuts: localStorage key `wrapdf_shortcuts`
- Service Worker cache: `wrapdf-v7`

## Important Constraints

### DO NOT
- Create e2e or Playwright tests (user instruction)
- Upload files to any server - all processing is client-side
- Modify `@bentopdf/*` npm package references (external dependencies)
- Add synchronous WASM loading - always use async/await

### ALWAYS
- Test with `npm run build && npm run preview` before PRs
- Run `npm run check-i18n` when adding translatable strings
- Use TypeScript strict mode
- Follow existing code patterns in `src/js/logic/`

## Development Notes

### WASM Headers Required
Dev server needs these headers (configured in vite.config.ts):
- `Cross-Origin-Opener-Policy: same-origin`
- `Cross-Origin-Embedder-Policy: require-corp`

### Git LFS
Large WASM files are tracked with Git LFS:
- `*.wasm`, `*.wasm.gz`, `*.data`, `*.data.gz`

### Docker
```bash
docker build -t wrapdf .
docker run -p 8080:80 wrapdf
```

## Licensing

Dual-licensed under AGPL-3.0 (open source) and Commercial License.
- Author: Mohamed Elkholy
- Contact: contact@wrapdf.com
- GitHub: github.com/mohamed-elkholy95/warpdf
