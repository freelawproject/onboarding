# Coding Standards

## Python

### Type Hints

Type hints are required for function signatures:

```python
def get_court_by_id(court_id: str) -> Court | None:
    ...
```

### Pathlib Over os.path

Use `pathlib.Path` instead of `os.path` for file operations:

```python
# Good
from pathlib import Path
config_path = Path("config") / "settings.json"

# Avoid
import os
config_path = os.path.join("config", "settings.json")
```

### Imports

Keep imports at the top of the file. Local imports are only acceptable to resolve circular dependencies.

### Formatting

We use [ruff](https://docs.astral.sh/ruff/) for both linting and formatting. Configuration lives in each repo's `pyproject.toml`. Run it locally via pre-commit or directly:

```bash
ruff check .
ruff format .
```

## JavaScript / TypeScript

Conventions vary by project. Check each repo's linting config (ESLint, Prettier, etc.) and follow what's already there.

## Testing Philosophy

### Test Our Code Only

Trust that libraries and frameworks work. Don't test Django ORM, built-in decorators, or third-party packages. Focus on:

- Custom validation logic
- Business logic and model methods
- View responses and status codes
- Integration points between our systems

### TDD-Style

Tests should break when our code changes. If you rename a button from "Sign in" to "Log in", a test should fail. If no test fails, that's a coverage gap worth flagging.

### Factories, Not Fixtures

Use [factory_boy](https://factoryboy.readthedocs.io/) (or similar) to create test data. Factories are explicit, composable, and don't suffer from the "mystery data" problem that fixture files create.

## Presentation Layer

Templates should be dumb. Logic belongs in **models** or **JavaScript**, not in views or templates.

| Layer          | Responsibility                                              |
| -------------- | ----------------------------------------------------------- |
| **Models**     | Business logic, computed properties, data transformation    |
| **Views**      | Orchestration only — fetch data, call model methods, render |
| **Templates**  | Display only — loops, simple show/hide conditionals         |
| **JavaScript** | Client-side interactivity and state                         |

### Django Templates

- Use named endblocks: `{% endblock content %}` not `{% endblock %}`
- Lowercase form methods: `method="post"` not `method="POST"`

## Never Silence Warnings

Understand and fix the root cause. Don't add `# noqa`, `// eslint-disable`, or `type: ignore` without understanding what the warning means and why it exists.

If a warning is genuinely a false positive, document why in the comment:

```python
# noqa: E501 - URL cannot be shortened
LONG_API_URL = "https://..."
```

Taking the time to understand warnings leads to better, more reliable code.

## Accessibility (A11y)

Accessibility is critical in all code we write. We target **WCAG 2.2 AA** standards.

This means:

- Semantic HTML elements (`<nav>`, `<main>`, `<button>`, not `<div onclick>`)
- Proper ARIA labels where semantic HTML isn't sufficient
- Keyboard navigability for all interactive elements
- Sufficient color contrast ratios
- Alt text for images, labels for form inputs

Building accessibility in from the start is much easier than retrofitting it later, and it makes the product better for everyone.

## Internationalization (i18n)

Check for language translations associated with your changes. If you add or modify user-facing strings, make sure the corresponding translation files are updated. Don't assume English-only — if the project has i18n set up, use it.

## Keep Tests and Docs in Sync

When you change code, check whether tests or documentation need to be updated too. A feature change without a test update is incomplete. A public API change without a docs update leaves users guessing.
