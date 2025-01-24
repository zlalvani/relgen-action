# Relgen Action

This action uses [relgen](https://github.com/zlalvani/relgen) to automatically review, describe, and label pull requests using AI.

## Features

- 🤖 AI-powered PR reviews and descriptions
- 🏷️ Intelligent PR labeling
- ⚙️ Customizable templates and prompts
- 🔄 Updates on PR changes

## Usage

```yaml
name: PR Assistant
on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  relgen:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write
      issues: write
    steps:
      - uses: actions/checkout@v4
      - uses: zlalvani/relgen-action@v1
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          llm-key: ${{ secrets.OPENAI_API_KEY }} # or ANTHROPIC_API_KEY or DEEPSEEK_API_KEY
          llm-provider: openai # or anthropic or deepseek
          llm-model: gpt-4o-mini # or claude-3-sonnet-20240229 or deepseek-chat etc
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `github-token` | GitHub token for API access | Yes | N/A |
| `llm-key` | LLM API key (OpenAI or Anthropic) | Yes | N/A |
| `llm-provider` | LLM provider (`openai` or `anthropic`) | Yes | `openai` |
| `llm-model` | Model to use (e.g. `gpt-4o-mini` for OpenAI or `claude-3-sonnet-20240229` for Anthropic) | Yes | N/A |
| `description-template` | Path to description template file | No | Default template |
| `description-prompt` | Path to description prompt file | No | Default prompt |
| `write-mode` | Where to write descriptions (comma-separated list of: `title,comment,description` or `off` to disable) | No | `comment` |
| `label-mode` | How to write labels (`add`, `set`, or `off`) | No | `add` |
| `review-mode` | How to write reviews (`add` or `off`) | No | `add` |
| `footer` | Footer text used to identify relgen comments | No | Default footer with link to action |
| `verbose` | Enable verbose logging | No | `false` |
| `config` | Path to relgen config file | No | N/A |

## Customization

### Templates

Create a markdown template file (e.g. `.github/templates/pr-description.md`):

```markdown
### Changes
A bullet point list of the important changes

### Implementation
Technical details of the implementation

### Other Notes
Additional context or minor changes
```

### Prompts

Create a prompt file (e.g. `.github/prompts/pr-description.txt`):

```
Focus on the technical impact of the changes.
Highlight any breaking changes.
Include performance implications.
```

Then update your workflow:

```yaml
- uses: zlalvani/relgen-action@v1
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
    llm-key: ${{ secrets.OPENAI_API_KEY }} # or ANTHROPIC_API_KEY
    llm-provider: openai # or anthropic
    llm-model: gpt-4o-mini # or claude-3-sonnet-20240229 etc
    write-mode: title,comment,description # Write to PR title, comment and description
    description-template: .github/templates/pr-description.md
    description-prompt: .github/prompts/pr-description.txt
```

## Configuration File

You can use a configuration file to manage your settings and add custom review rules. Create a `relgen.config.json` file:

```json
{
  "llm": {
    "provider": "openai",
    "model": "gpt-4o-mini",
    "apiKey": "your-openai-api-key-here"
  },
  "integrations": {
    "github": {
      "token": "your-github-token-here"
    },
    "linear": {
      "token": "your-linear-token-here"
    }
  },
  "commands": {
    "remote": {
      "pr": {
        "review": {
          "rules": [{ "file": "examples/rules/injectable.md" }]
        }
      }
    }
  }
}
```

Then reference it in your workflow:

```yaml
- uses: zlalvani/relgen-action@v1
  with:
    config: .github/relgen.config.json
```

The config file supports:
- LLM provider settings
- Integration tokens
- Custom review rules
- And more configuration options
