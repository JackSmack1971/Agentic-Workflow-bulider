# AGENTS.md - Gradio WorkflowBuilder Component

> This file provides essential context for AI coding agents working on the Gradio WorkflowBuilder custom component.

## Project Overview

**Gradio WorkflowBuilder** is a custom Gradio component that provides a visual workflow builder interface powered by SvelteFlow. It enables users to create, edit, and export AI workflow diagrams with drag-and-drop simplicity.

**Architecture**: Dual-stack component with Python backend (Gradio integration) and Svelte/JavaScript frontend (UI layer).

**Key Technologies**:
- Backend: Python 3.10+, Gradio 4.x, Hatchling build system
- Frontend: Svelte 4.x, @xyflow/svelte for node-based UI, TypeScript
- Package management: pip (Python), npm (frontend)

**Project Structure**:
```
/
├── backend/gradio_workflowbuilder/  # Python component logic
│   ├── workflowbuilder.py          # Main component class
│   └── templates/                  # Built frontend assets
├── frontend/                       # Svelte frontend source
│   ├── Index.svelte               # Main component
│   ├── Example.svelte             # Example usage
│   └── package.json               # Frontend dependencies
├── pyproject.toml                 # Python package config
├── space.py                       # Demo Gradio app
└── README.md                      # User documentation
```

## Build and Test Commands

### Python Environment Setup
```bash
# Install in development mode
pip install -e .

# Install with dev dependencies
pip install -e ".[dev]"
```

### Frontend Development
```bash
cd frontend

# Install dependencies
npm install

# Build frontend (outputs to backend/templates/)
# Note: Gradio handles building via its CLI
```

### Building the Component
```bash
# Build Python package (from project root)
python -m build

# Or using hatch
hatch build
```

### Running the Demo
```bash
# Launch demo app
python space.py

# Demo will be available at http://localhost:7860
```

### Testing
**Note**: No test suite currently exists. When adding tests:
```bash
# Python tests (once pytest is added)pytest backend/

# Frontend tests (once added)
cd frontend && npm test
```

## Code Style and Conventions

### Python Style
- **Python version**: Requires >=3.10
- **Type hints**: Use type annotations for function signatures
- **Docstrings**: Use triple-quoted strings for classes and public methods
- **Formatting**: Follow PEP 8 conventions

### Frontend Style
- **Framework**: Svelte 4.x with TypeScript for type safety
- **Component structure**: Single-file components (.svelte)
- **Naming**: camelCase for variables/functions, PascalCase for components
- **State management**: Use Svelte's reactive declarations ($: syntax)

### Do's and Don'ts

**Do**:
- Use Gradio's component patterns (see `workflowbuilder.py` for examples)
- Keep frontend state synchronized with Python backend via `value` prop
- Use @xyflow/svelte for all node-based UI interactions
- Follow the existing node type structure in `Index.svelte`
- Export workflow data as JSON for interoperability

**Don't**:
- Add heavy dependencies without discussion (keep bundle size small)
- Modify `backend/templates/` directly (it's auto-generated from `frontend/`)
- Break the Gradio component contract (preprocess, postprocess methods)
- Hardcode API keys or credentials (use environment variables)
- Change the component's export format without version migration

### Key Files to Reference
- **Good patterns**: `backend/gradio_workflowbuilder/workflowbuilder.py` (component implementation)
- **UI patterns**: `frontend/Index.svelte` (main workflow builder UI)
- **Demo usage**: `space.py` (shows how to use the component)

## Development Workflow

### Making Changes to Frontend
1. Edit files in `frontend/`
2. Gradio's build system will automatically rebuild to `backend/templates/`
3. Test changes by running `python space.py`
4. Component hot-reloads during development

### Making Changes to Backend
1. Edit `backend/gradio_workflowbuilder/workflowbuilder.py`
2. Changes take effect immediately in development mode
3. Test with `python space.py`

### Component Architecture Notes
- **Data flow**: Frontend → Python (via `change` event) → Backend processing
- **Value format**: JSON dict with `{nodes: [...], edges: [...]}`
- **Node validation**: Handled in `_validate_workflow` method
- **Workflow analysis**: Use `WorkflowAnalyzer.analyze_workflow()` for insights

## Git Workflow

### Branch Naming
```
feature/<description>    # New features
fix/<issue>             # Bug fixes
docs/<topic>            # Documentation updates
refactor/<component>    # Code refactoring
```

### Commit Messages
Follow conventional commits:```
feat(scope): Add new feature
fix(scope): Fix bug in component
docs: Update documentation
refactor: Restructure code
style: Format code
test: Add tests
```

### Pre-commit Checklist
Before committing:
- [ ] Code follows style guidelines
- [ ] No console.log or debug statements in production code
- [ ] Component works in demo (`python space.py`)
- [ ] Package builds successfully (`python -m build`)
- [ ] README updated if public API changed

### Pull Request Guidelines
A ready PR should include:
- Clear description of changes and motivation
- Screenshots/video for UI changes
- Test evidence (manual testing notes if no automated tests)
- No breaking changes to component API (or clearly documented migration)
- Updated version in `pyproject.toml` following semver

## Environment Variables and Configuration

### Required for Development
- None - component runs standalone

### Optional for Enhanced Features
- `OPENAI_API_KEY` - For AI model nodes in workflows
- `HF_TOKEN` - For HuggingFace model nodes
- `NEBIUS_API_KEY` - For Nebius image generation

### Configuration Files
- `pyproject.toml` - Python package metadata and dependencies
- `frontend/package.json` - Frontend dependencies
- `frontend/gradio.config.js` - Gradio build configuration

## Common Patterns and Gotchas

### Node Type System
- Each node type must have a corresponding entry in `nodeSchemas` (Index.svelte)
- Node data uses a template structure: `{display_name, template: {field: {type, value}}}`
- Handle types: `object` (input), `string/list/file` (output)

### Workflow Validation
- Use `WorkflowAnalyzer` for workflow structure validation
- Check for disconnected nodes, cycles, and missing required fields
- Validation results include complexity metrics and issue descriptions

### Template Building
- Frontend templates compile to `backend/templates/component/`
- Don't manually edit compiled files - they'll be overwritten
- Gradio's build system handles the compilation automatically

### Known Issues
- Component is in Alpha (v0.0.1) - expect API changes
- No automated test coverage yet
- Author info in `pyproject.toml` needs updating for forks

## When Stuck

If you encounter ambiguity or need clarification:
1. **Check existing code**: Look at `workflowbuilder.py` and `Index.svelte` for patterns
2. **Review Gradio docs**: https://www.gradio.app/guides/custom-components
3. **Inspect the demo**: Run `python space.py` to see component in action
4. **Ask specific questions**: Provide context about what you're trying to achieve

**Don't guess**: If behavior is unclear, propose a plan and ask for validation before implementing.

## Additional Resources

- [Gradio Custom Components Guide](https://www.gradio.app/guides/custom-components)
- [Svelte Documentation](https://svelte.dev/docs)
- [@xyflow/svelte Documentation](https://svelteflow.dev/)
- [Python Packaging](https://packaging.python.org/)

---

**Version**: 0.0.1 (Alpha)  
**Last Updated**: 2025-09-29  
**Maintainers**: See `pyproject.toml` for author information