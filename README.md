# Gradio WorkflowBuilder

[![Version](https://img.shields.io/badge/version-0.0.1-orange)](https://pypi.org/project/gradio-workflowbuilder/)
[![License](https://img.shields.io/badge/license-Apache%202.0-green)](LICENSE)
[![Python](https://img.shields.io/badge/python-3.10%2B-blue)](https://python.org)
[![Development Status](https://img.shields.io/badge/status-Alpha-yellow)](https://pypi.org/project/gradio-workflowbuilder/)

> **Visual workflow builder custom component for Gradio, powered by SvelteFlow**

A professional drag-and-drop workflow builder component that integrates seamlessly with Gradio applications, enabling visual creation and editing of AI workflow diagrams with 25+ specialized node types. [[EVID: src/backend/gradio_workflowbuilder/workflowbuilder.py:L9-L11 | Component description from docstring]]

## ✨ Features

- **🎨 Visual Workflow Design** - Drag-and-drop interface with real-time node connections [[EVID: src/frontend/Index.svelte:L1-L15 | Svelte component implements drag-and-drop]]
- **25+ Node Types** - Specialized nodes for inputs, AI models, data processing, tools, and outputs [[EVID: src/frontend/Index.svelte:L65-L90 | Component categories defined]]
- **🔄 Bidirectional Sync** - Seamless state management between Python backend and Svelte frontend [[EVID: src/backend/gradio_workflowbuilder/workflowbuilder.py:L48-L52 | preprocess method handles frontend-to-backend sync]]
- **📤 JSON Export** - Export complete workflow configurations for persistence and reuse [[EVID: src/demo/app.py:L6-L10 | export_workflow function]]
- **🎯 Property Editing** - Edit node properties through collapsible side panels [[EVID: src/frontend/Index.svelte:L35-L38 | Property panel state management]]
- **🔍 Workflow Validation** - Built-in analyzer for detecting disconnected nodes and structural issues [[EVID: src/backend/gradio_workflowbuilder/workflowbuilder.py:L98-L135 | WorkflowAnalyzer class]]

## 🏗️ Architecture

### High-Level System Design

```mermaid
flowchart TB
    subgraph Frontend["Frontend Layer (Svelte)"]
        UI[Index.svelte]
        Canvas[Visual Canvas]
        Sidebar[Component Sidebar]
        Props[Property Panel]
    end
    
    subgraph Backend["Backend Layer (Python)"]
        Component[WorkflowBuilder Component]
        Validator[Workflow Validator]
        Analyzer[WorkflowAnalyzer]
    end
    
    subgraph Data["Data Flow"]
        Nodes[(Nodes)]
        Edges[(Edges)]
        JSON[JSON Export]
    end
    
    UI --> Canvas
    UI --> Sidebar
    UI --> Props
    Canvas --> Nodes
    Canvas --> Edges
    Nodes --> Component
    Edges --> Component
    Component --> Validator
    Validator --> Analyzer
    Component --> JSON
    
    style Frontend fill:#e3f2fd
    style Backend fill:#f3e5f5
    style Data fill:#fff3e0
```

### Component Relationship Diagram

```mermaid
classDiagram
    class WorkflowBuilder {
        +Dict value
        +str label
        +preprocess(payload) Dict
        +postprocess(value) Dict
        +example_payload() Dict
    }
    
    class WorkflowAnalyzer {
        +analyze_workflow(workflow) Dict
        -_count_node_types()
        -_check_disconnected_nodes()
    }
    
    class IndexSvelte {
        +nodes Array
        +edges Array
        +componentCategories Object
        +handleDragStart()
        +connectNodes()
        +updateNodeProperty()
    }
    
    class GradioInterface {
        +change Event
        +input Event
    }
    
    WorkflowBuilder --> WorkflowAnalyzer : validates
    WorkflowBuilder --> GradioInterface : emits
    IndexSvelte --> WorkflowBuilder : syncs state
    
    note for WorkflowBuilder "Python backend component\nHandles validation & events"
    note for IndexSvelte "Svelte frontend\nVisual workflow canvas"
```

### Data Processing Flow

```mermaid
sequenceDiagram
    participant User
    participant Canvas as Canvas UI
    participant Frontend as Svelte Frontend
    participant Backend as Python Backend
    participant Validator
    
    User->>Canvas: Drag node to canvas
    Canvas->>Frontend: Add node to state
    Frontend->>Frontend: Update nodes array
    Frontend->>Backend: Emit change event
    Backend->>Validator: Validate workflow
    Validator->>Validator: Check structure
    Validator-->>Backend: Return validation results
    Backend->>Frontend: Process & sync state
    Frontend->>Canvas: Re-render workflow
    Canvas-->>User: Display updated workflow
    
    User->>Canvas: Connect nodes
    Canvas->>Frontend: Add edge to state
    Frontend->>Backend: Emit change event
    Backend->>Validator: Validate connections
    Validator-->>Backend: Validation results
    Backend->>Frontend: Update state
    
    User->>Canvas: Click "Export"
    Canvas->>Backend: Request JSON export
    Backend->>Backend: Serialize workflow
    Backend-->>User: Return JSON
```

## 📦 Installation

**Quickstart (Tutorial)**

```bash
pip install gradio_workflowbuilder
```

[[EVID: README.md:L17-L19 | Installation command from README]]

**Development Mode (How-to)**

```bash
# Clone repository
git clone <repository-url>
cd gradio_workflowbuilder

# Install in editable mode
pip install -e .

# Install with development dependencies
pip install -e ".[dev]"
```

[[EVID: AGENTS.md:L32-L38 | Development installation commands]]

## 🚀 Quick Start

### Basic Usage (Tutorial)

```python
import gradio as gr
from gradio_workflowbuilder import WorkflowBuilder

# Create a simple workflow builder interface
with gr.Blocks() as demo:
    workflow_builder = WorkflowBuilder(
        label="🎨 Visual Workflow Designer",
        info="Drag components to build your workflow"
    )
    
demo.launch()
```

[[EVID: src/demo/app.py:L1-L3 | Import statements from demo]]

### Complete Example with Export (How-to)

```python
import gradio as gr
from gradio_workflowbuilder import WorkflowBuilder
import json

def export_workflow(workflow_data):
    """Export workflow as formatted JSON"""
    if not workflow_data:
        return "No workflow to export"
    return json.dumps(workflow_data, indent=2)

with gr.Blocks(title="Workflow Builder") as demo:
    workflow_builder = WorkflowBuilder(
        label="🎨 Visual Workflow Designer",
        info="Drag → Connect → Edit → Export"
    )
    
    with gr.Row():
        export_output = gr.Code(
            language="json",
            label="Workflow Configuration"
        )
    
    export_btn = gr.Button("💾 Export JSON")
    
    # Connect export button
    export_btn.click(
        fn=export_workflow,
        inputs=[workflow_builder],
        outputs=[export_output]
    )

demo.launch()
```

[[EVID: src/demo/app.py:L6-L65 | Complete demo implementation]]

## 📖 Component Reference

### WorkflowBuilder Class (Reference)

#### Initialization Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `value` | `Optional[Dict[str, Any]]` | `None` | Default workflow data with nodes and edges [[EVID: src/backend/gradio_workflowbuilder/workflowbuilder.py:L19-L20 | Parameter definition]] |
| `label` | `Optional[str]` | `None` | Component label [[EVID: src/backend/gradio_workflowbuilder/workflowbuilder.py:L21 | Parameter definition]] |
| `info` | `Optional[str]` | `None` | Additional component information |
| `show_label` | `Optional[bool]` | `None` | Whether to show the label |
| `container` | `bool` | `True` | Whether to use container styling |
| `scale` | `Optional[int]` | `None` | Relative width scale |
| `min_width` | `int` | `160` | Minimum width in pixels [[EVID: src/backend/gradio_workflowbuilder/workflowbuilder.py:L26 | Default min_width]] |
| `visible` | `bool` | `True` | Whether component is visible |
| `elem_id` | `Optional[str]` | `None` | HTML element ID |
| `elem_classes` | `Optional[List[str]]` | `None` | CSS classes |
| `render` | `bool` | `True` | Whether to render immediately |

#### Events (Reference)

- **`change`** - Triggered when workflow value changes (user input OR function update) [[EVID: src/backend/gradio_workflowbuilder/workflowbuilder.py:L15 | EVENTS definition]]
- **`input`** - Triggered only on user input changes

### Workflow Data Format (Reference)

```python
{
    "workflow_id": "unique-id",
    "workflow_name": "My Workflow",
    "nodes": [
        {
            "id": "Node-1",
            "type": "Input",
            "position": {"x": 100, "y": 200},
            "data": {
                "display_name": "Input Node",
                "template": {
                    "field_name": {
                        "display_name": "Field Label",
                        "type": "string",
                        "value": "default value",
                        "is_handle": true
                    }
                }
            }
        }
    ],
    "edges": [
        {
            "id": "e1",
            "source": "Node-1",
            "source_handle": "output_field",
            "target": "Node-2",
            "target_handle": "input_field"
        }
    ]
}
```

[[EVID: src/backend/gradio_workflowbuilder/workflowbuilder.py:L62-L94 | example_payload method structure]]

## 🎯 Node Types (Reference)

The component includes 25+ specialized node types organized into categories: [[EVID: src/frontend/Index.svelte:L65-L90 | componentCategories definition]]

### Input/Output Nodes
- **ChatInput** 💬 - User message input [[EVID: src/frontend/Index.svelte:L69-L88 | ChatInput definition]]
- **ChatOutput** 💭 - AI response output
- **Input** 📥 - Generic data source (string, image, video, audio, file)
- **Output** 📤 - Generic data sink

### AI Model Nodes
- **OpenAI** - OpenAI API integration
- **Anthropic Claude** - Claude model integration
- **Ollama** - Local model serving
- **HuggingFace** - HF model inference

### Data Processing Nodes
- **ExecutePython** 🐍 - Execute Python code [[EVID: src/frontend/Index.svelte:L269-L305 | ExecutePython node definition]]
- **ConditionalLogic** 🔀 - Branching logic
- **Wait** ⏳ - Delay execution

### Tools & Utilities
- **GoogleSearch** 🔍 - Web search integration
- **FileReader** 📁 - File I/O operations
- **JSONParser** - JSON manipulation

## 🛠️ Development (How-to)

### Project Structure (Explanation)

```
src/
├── backend/
│   └── gradio_workflowbuilder/
│       ├── workflowbuilder.py      # Main component class
│       └── templates/              # Built frontend (auto-generated)
├── frontend/
│   ├── Index.svelte               # Main workflow canvas UI
│   ├── Example.svelte             # Example component
│   └── package.json               # Frontend dependencies
├── demo/
│   ├── app.py                     # Demo application
│   └── space.py                   # Space launcher
├── pyproject.toml                 # Python package config
└── README.md                      # Documentation
```

[[EVID: AGENTS.md:L20-L30 | Project structure from AGENTS.md]]

### Building the Component (How-to)

**Python Package**

```bash
# Build distribution
python -m build

# Or using hatch
hatch build
```

[[EVID: AGENTS.md:L50-L55 | Build commands]]

**Frontend Assets**

```bash
cd frontend
npm install
# Gradio's build system handles compilation automatically
```

[[EVID: AGENTS.md:L41-L48 | Frontend build process]]

### Running the Demo (Tutorial)

```bash
python space.py
# Server available at http://localhost:7860
```

[[EVID: AGENTS.md:L57-L61 | Demo launch command and default port]]

### Making Changes (How-to)

**Frontend Development**

1. Edit files in `frontend/` directory
2. Gradio automatically rebuilds to `backend/templates/`
3. Test changes by running `python space.py`
4. Component hot-reloads during development

[[EVID: AGENTS.md:L158-L163 | Frontend development workflow]]

**Backend Development**

1. Edit `backend/gradio_workflowbuilder/workflowbuilder.py`
2. Changes take effect immediately in development mode
3. Test with `python space.py`

[[EVID: AGENTS.md:L165-L168 | Backend development workflow]]

## 🔧 Advanced Usage (How-to)

### Workflow Analysis (How-to)

```python
from gradio_workflowbuilder import WorkflowAnalyzer

# Analyze workflow structure
workflow_data = {
    "nodes": [...],
    "edges": [...]
}

analysis = WorkflowAnalyzer.analyze_workflow(workflow_data)

print(f"Complexity: {analysis['complexity']}")
print(f"Node types: {analysis['node_types']}")
print(f"Issues: {analysis['issues']}")
```

[[EVID: src/backend/gradio_workflowbuilder/workflowbuilder.py:L98-L135 | WorkflowAnalyzer implementation]]

### Custom Node Properties (How-to)

Node templates support various field types:

```python
{
    "template": {
        "text_field": {
            "display_name": "Text Input",
            "type": "string",
            "value": "default"
        },
        "number_field": {
            "display_name": "Number Input",
            "type": "number",
            "value": 42,
            "min": 0,
            "max": 100
        },
        "choice_field": {
            "display_name": "Select Option",
            "type": "options",
            "options": ["opt1", "opt2"],
            "value": "opt1"
        },
        "connection_point": {
            "display_name": "Input",
            "type": "object",
            "is_handle": true
        }
    }
}
```

[[EVID: src/frontend/Index.svelte:L109-L132 | Node template structure]]

## 📋 Requirements (Reference)

### Python Dependencies

- **Python** ≥ 3.10 [[EVID: src/pyproject.toml:L15 | requires-python constraint]]
- **Gradio** ≥ 4.0, < 6.0 [[EVID: src/pyproject.toml:L19 | gradio dependency]]

### Frontend Dependencies

- **Svelte** ^4.0.0 [[EVID: src/frontend/package.json:L23-L25 | peerDependencies]]
- **@xyflow/svelte** ^1.0.2 [[EVID: src/frontend/package.json:L13-L17 | core dependencies]]
- **@gradio/atoms** 0.16.1
- **@gradio/utils** 0.10.2

### Build System

- **Hatchling** (build backend) [[EVID: src/pyproject.toml:L2-L6 | build-system]]
- **Node.js** (for frontend development)

## 🧪 Testing

⚠️ **No automated test suite currently exists.** [[EVID: AGENTS.md:L63-L71 | Testing note]]

When adding tests:

```bash
# Python tests (future)
pytest backend/

# Frontend tests (future)
cd frontend && npm test
```

## 📝 Development Status (Explanation)

**Current Version:** 0.0.1 (Alpha) [[EVID: src/pyproject.toml:L11 | version field]]

**Development Stage:** Alpha - expect API changes [[EVID: AGENTS.md:L203 | Known issues]]

**Known Limitations:**
- No automated test coverage [[EVID: AGENTS.md:L204 | Known issues]]
- Author information needs updating for forks [[EVID: AGENTS.md:L205 | Known issues]]
- Component API may change in future releases

## 🤝 Contributing (How-to)

### Code Style

**Python**
- Python ≥ 3.10 required
- Type hints for function signatures
- Docstrings for classes and public methods
- Follow PEP 8 conventions

**Frontend**
- Svelte 4.x with TypeScript
- camelCase for variables/functions
- PascalCase for components
- Reactive declarations with `$:` syntax

[[EVID: AGENTS.md:L74-L90 | Code style guidelines]]

### Git Workflow

**Branch Naming**
```
feature/<description>    # New features
fix/<issue>             # Bug fixes
docs/<topic>            # Documentation
refactor/<component>    # Code refactoring
```

**Commit Messages** (Conventional Commits)
```
feat(scope): Add new feature
fix(scope): Fix bug
docs: Update documentation
refactor: Restructure code
```

[[EVID: AGENTS.md:L172-L195 | Git workflow guidelines]]

## 📄 License

This project is licensed under the **Apache License 2.0** [[EVID: src/pyproject.toml:L14 | license field]]

## 🔗 Resources

- [Gradio Custom Components Guide](https://www.gradio.app/guides/custom-components)
- [Svelte Documentation](https://svelte.dev/docs)
- [@xyflow/svelte Documentation](https://svelteflow.dev/)
- [Python Packaging](https://packaging.python.org/)

[[EVID: AGENTS.md:L220-L226 | Additional resources]]

---

**Built with** 🐍 Python · 🎨 Svelte · ⚡ Gradio

**Powered by** [@xyflow/svelte](https://svelteflow.dev/) for visual workflow editing

---
