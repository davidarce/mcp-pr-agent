# MCP PR Agent

A comprehensive Model Context Protocol (MCP) server that helps developers create better pull requests by analyzing git repository changes, managing PR templates, and providing intelligent template suggestions.

## Features

### 🔍 Git Analysis
- **Change Detection**: Automatically detects modified, added, and deleted files
- **Diff Generation**: Provides complete git diffs with intelligent truncation
- **Commit History**: Shows relevant commit messages and statistics
- **Branch Comparison**: Compares changes against any base branch (default: main)

### 📝 Template Management
- **Multiple Templates**: Supports 7 different PR template types
- **Template Categories**: Bug fixes, features, documentation, refactoring, testing, performance, and security
- **Content Management**: Stores and retrieves complete template content
- **Customizable**: Easy to add or modify templates

### 🤖 Intelligent Suggestions
- **Smart Mapping**: Automatically suggests appropriate templates based on change analysis
- **Context Awareness**: Considers file types, change patterns, and keywords
- **Flexible Matching**: Supports multiple aliases for change types

## Installation

### Prerequisites
- Python 3.13+
- [uv](https://docs.astral.sh/uv/) (Python package manager)
- Git repository
- MCP-compatible client (like Claude Code)

### Setup
1. Clone the repository:
   ```bash
   git clone <repository-url>
   cd mcp-pr-agent
   ```

2. Install dependencies using uv:
   ```bash
   uv sync
   ```

3. Run the server:
   ```bash
   uv run server.py
   ```

## MCP Tools

### 1. `analyze_file_changes`
Analyzes git repository changes and provides comprehensive diff information.

**Parameters:**
- `base_branch` (str, optional): Base branch to compare against (default: "main")
- `include_diff` (bool, optional): Include full diff content (default: true)
- `max_diff_lines` (int, optional): Maximum diff lines to include (default: 500)
- `working_directory` (str, optional): Directory to run git commands in (default: auto-detected)

**Returns:** JSON object containing:
- `files_changed`: List of modified files with status
- `statistics`: Diff statistics summary
- `commits`: Recent commit messages
- `diff`: Complete diff content (if requested)
- `truncated`: Whether diff was truncated
- `total_diff_lines`: Total number of diff lines

**Example Usage:**
```python
# Basic usage
result = await analyze_file_changes()

# Compare against develop branch
result = await analyze_file_changes(base_branch="develop")

# Get summary without full diff
result = await analyze_file_changes(include_diff=False)

# Limit diff size
result = await analyze_file_changes(max_diff_lines=100)
```

### 2. `get_pr_templates`
Retrieves all available PR templates with their content.

**Parameters:** None

**Returns:** JSON array of template objects containing:
- `filename`: Template file name
- `type`: Template category/type
- `content`: Complete template content

**Available Templates:**
- **bug.md**: Bug Fix template
- **feature.md**: New Feature template
- **docs.md**: Documentation Update template
- **refactor.md**: Code Refactoring template
- **test.md**: Test Update template
- **performance.md**: Performance Improvement template
- **security.md**: Security Update template

### 3. `suggest_template`
Analyzes change descriptions and suggests the most appropriate PR template.

**Parameters:**
- `changes_summary` (str): Description of what the changes do
- `change_type` (str): Type of change (bug, feature, docs, refactor, test, etc.)

**Returns:** JSON object containing:
- `recommended_template`: Complete template object
- `reasoning`: Explanation for the suggestion
- `template_content`: Ready-to-use template content
- `usage_hint`: Instructions for using the template

**Supported Change Types:**
- **Bug/Fix**: Maps to bug.md template
- **Feature/Enhancement**: Maps to feature.md template
- **Docs/Documentation**: Maps to docs.md template
- **Refactor/Cleanup**: Maps to refactor.md template
- **Test/Testing**: Maps to test.md template
- **Performance/Optimization**: Maps to performance.md template
- **Security**: Maps to security.md template

## Template Structure

Each template follows a consistent structure:

```markdown
## [Template Type]

### Description
<!-- Brief description of the change -->

### [Template-specific sections]
<!-- Varies by template type -->

### Testing
- [ ] Relevant tests added/updated
- [ ] Manual testing completed

### [Additional sections as needed]
```

### Customizing Templates

Templates are stored in the `templates/` directory. To add or modify templates:

1. Create/edit `.md` files in the `templates/` directory
2. Update `DEFAULT_TEMPLATES` in `server.py` if adding new templates
3. Update `TYPE_MAPPING` for new change type aliases

## Integration Examples

### With Claude Code
```python
# Analyze current repository changes
result = mcp.call_tool("analyze_file_changes", {
    "base_branch": "main",
    "include_diff": True
})

# Get all available templates
templates = mcp.call_tool("get_pr_templates", {})

# Get template suggestion
suggestion = mcp.call_tool("suggest_template", {
    "changes_summary": "Added user authentication system",
    "change_type": "feature"
})
```

### Workflow Example
1. **Analyze Changes**: Use `analyze_file_changes` to understand what was modified
2. **Get Suggestions**: Use `suggest_template` to get appropriate template recommendations
3. **Create PR**: Use the suggested template content to create a comprehensive PR description

## Configuration

### Environment Variables
- `MCP_PR_AGENT_TEMPLATES_DIR`: Custom templates directory path
- `MCP_PR_AGENT_DEFAULT_BRANCH`: Default base branch (default: "main")

### Claude Code Integration
Add this MCP server to Claude Code using the following command:

```bash
claude mcp add pr-agent -- uv --directory <mcp-pr-agent-directory> run server.py
```

Replace `<mcp-pr-agent-directory>` with the full path to your mcp-pr-agent directory.

**Example:**
```bash
claude mcp add pr-agent -- uv --directory /Users/username/projects/mcp-pr-agent run server.py
```

### Manual MCP Client Configuration
For other MCP clients, add to your configuration:

```json
{
  "mcpServers": {
    "pr-agent": {
      "command": "uv",
      "args": ["--directory", "/path/to/mcp-pr-agent", "run", "server.py"]
    }
  }
}
```

## Development

### Running Tests
```bash
uv run pytest test_server.py -v
```

### Adding New Templates
1. Create template file in `templates/` directory
2. Add to `DEFAULT_TEMPLATES` dictionary
3. Add type mappings to `TYPE_MAPPING`
4. Update documentation

### Error Handling
The server includes comprehensive error handling:
- Git command failures
- Missing repositories
- Template file errors
- Invalid parameters

## Troubleshooting

### Common Issues

**"Git error" responses:**
- Ensure you're in a git repository
- Check that the base branch exists
- Verify git is accessible in PATH

**Empty results:**
- Check if you're on the correct branch
- Ensure there are changes to analyze
- Verify the base branch comparison

**Template not found:**
- Ensure templates directory exists
- Check template file permissions
- Verify template files are properly formatted

### Debug Information
The `analyze_file_changes` tool includes debug information in the `_debug` field:
- Working directory paths
- MCP roots detection
- Server process information

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests for new functionality
5. Submit a pull request using the appropriate template!

## License

[Add your license information here]

## Support

[Add support contact information here]