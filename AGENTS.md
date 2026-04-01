# ckeditor5-react

Official React integration for CKEditor 5. Provides `<CKEditor>` and `<CKEditorContext>` class-based React components that manage editor lifecycle, watchdog error recovery, and React prop-to-editor-API synchronization. Published as `@ckeditor/ckeditor5-react`.

## Setup & Commands

```bash
# Install dependencies
npm install
# or: yarn

# Build production bundle (UMD output to dist/)
npm run build

# Build development bundle with file watcher
npm run develop

# Run tests (requires Chrome; runs in Karma)
npm test
# or: npm t

# Run tests with coverage report
npm run coverage

# Run tests with options
npm test -- --coverage --watch --browsers Firefox

# Lint all JS/JSX files
npm run lint

# Generate changelog
npm run changelog

# Bump package version
npm run release:bump-version

# Publish to npm (publishes dist/ only)
npm run release:publish
```

## Code Conventions

- **File extension**: use `.jsx` for React components; `.js` for non-JSX modules
- **License header**: every `.js` and `.jsx` file must start with the CKEditor license header — the `ckeditor5-rules/license-header` ESLint rule enforces this
- **Indentation**: tabs (enforced by `eslint-config-ckeditor5`)
- **Class-based components**: both `CKEditor` and `CKEditorContext` are React class components — do not convert to functional/hooks
- **PropTypes**: define `propTypes` and `defaultProps` on each component class
- **Global declarations**: declare browser globals at the top with `/* globals window, HTMLDivElement */`

## Testing

- **Framework**: Mocha + Chai + Sinon, run in Karma (browser-based — requires Chrome)
- **Test files**: mirror `src/` in `tests/` using `.jsx` extension
- **Test utilities**: shared mocks live in `tests/_utils/`; tests for those utilities live in `tests/_utils-tests/`
- **Integration tests**: `tests/integrations/` for cross-component scenarios
- **Regression tests**: `tests/issues/` for issue-specific regression cases
- **Coverage**: `babel-plugin-istanbul` instruments `src/` and `tests/_utils-tests/`; generate with `npm run coverage`
- **Enzyme**: use `enzyme` + `enzyme-adapter-react-16`; call `configure({ adapter: new Adapter() })` at the top of each test file

## Do

- Use `EditorWatchdog` (or `EditorWatchdogAdapter`) to manage editor lifecycle — never call `Editor.create()` directly in component code
- Override `shouldComponentUpdate` to return `false` by default; let CKEditor manage its own DOM
- Use the named lock ID `REACT_INTEGRATION_READ_ONLY_LOCK_ID` when calling `enableReadOnlyMode` / `disableReadOnlyMode`
- Call `this.props.onError` (never `throw`) for editor initialization and runtime errors, passing `{ phase, willEditorRestart }`
- Use `uid()` from `@ckeditor/ckeditor5-utils` when a unique ID is needed
- Store the `EditorWatchdog` instance in `this.watchdog`; access the editor via `this.watchdog.editor`
- Add new test cases to `tests/issues/` for bug fixes to prevent regressions

## Don't

- Don't convert class components to functional components or React hooks — the Enzyme test suite relies on class component APIs
- Don't call `Editor.create()` directly — always go through the watchdog
- Don't call `editor.isReadOnly = true` — use the lock mechanism (`enableReadOnlyMode` / `disableReadOnlyMode`) required since CKEditor 5 v34
- Don't pass data via both `data` prop and `config.initialData` — `config.initialData` takes precedence and mixing them logs a warning
- Don't import from `@ckeditor/ckeditor5-*` in `src/` beyond watchdog and utils — the consuming application provides the editor build
- Don't modify `dist/` — it is a build artifact and is gitignored
- Don't commit files with missing or incorrect license headers (pre-commit hook will block it)

## Architecture

See `ARCHITECTURE.md` for component design, data flow, and key design decisions.

## Security

See `SECURITY.md` for mandatory security requirements and prohibited patterns.

## Safety & Permissions

Allowed without approval:
- Read/list files
- Run lint on specific files
- Run single-file tests

Ask before:
- Installing or removing packages
- Deleting files or directories
- Running the full test suite (requires a browser)
- Modifying CI/CD configuration or webpack config
- Pushing to remote branches or publishing

## PR & Commit Guidelines

- Commit message format follows [CKEditor 5 convention](https://github.com/ckeditor/ckeditor5-design/wiki/Git-commit-message-convention): `Type: Short message. Closes #NNN.`
- Common types: `Feature`, `Fix`, `Other`, `Internal`, `Release`, `Travis`
- PR template (`.github/PULL_REQUEST_TEMPLATE.md`) provides the required format — fill in the type, message, and issue reference

## References

- Architecture: `ARCHITECTURE.md`
- Security: `SECURITY.md`
- Contributing: `CONTRIBUTING.md`
- CKEditor 5 documentation: https://ckeditor.com/docs/ckeditor5/latest/
- React integration guide: https://ckeditor.com/docs/ckeditor5/latest/builds/guides/integration/frameworks/react.html
