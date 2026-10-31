# DocSpring skills for AI agents

[Agent Skills](https://agentskills.io) and an agent plugin for [DocSpring](https://docspring.com), which fills PDF and
Word templates, generates PDFs from HTML templates and collects e-signatures. The skills teach an AI agent how to work
with DocSpring, and the plugin connects the agent to the DocSpring MCP server, which works with your account after you
sign in.

| Skill                          | What it does                                                                                       |
| ------------------------------ | -------------------------------------------------------------------------------------------------- |
| `docspring`                    | Connects to DocSpring and finds your templates, fills them, makes test PDFs and routes to the rest |
| `docspring-pdf-templates`      | Turns a PDF form into a template: AI field detection, checking fields against the pages, versions  |
| `docspring-html-templates`     | Designs and fixes HTML/CSS templates, rendering a test PDF after each change to check the pages    |
| `docspring-word-templates`     | Turns Word documents into templates: tags, loops over table rows, pictures, Word or PDF output     |
| `docspring-page-designer`      | Designs page designs from a JSON layout: sections, text with fields, tables, images, previews      |
| `docspring-api-integration`    | Writes app code with DocSpring's API clients: typed models, generating PDFs, webhooks, errors      |
| `docspring-signature-requests` | Sends templates for signature, makes signing links and embeds signing in your app                  |
| `docspring-embedded-forms`     | Embeds DocSpring forms and the template editor in your web app                                     |

## Install

### Claude Code

```sh
claude plugin marketplace add DocSpring/skills
claude plugin install docspring@docspring
```

### Codex

```sh
codex plugin marketplace add DocSpring/skills
codex plugin add docspring@docspring
```

### GitHub Copilot CLI and VS Code

In a Copilot CLI session or VS Code's chat:

```text
/plugin marketplace add DocSpring/skills
/plugin install docspring@docspring
```

### Gemini CLI

```sh
gemini extensions install https://github.com/DocSpring/skills
```

### Other agents

Install the skills into Cursor, Windsurf, OpenCode and about 80 other agents with the
[skills CLI](https://github.com/vercel-labs/skills):

```sh
npx skills add DocSpring/skills
```

The skills CLI installs only the skills. Add the DocSpring MCP server to your agent as well:
[MCP server setup](https://docspring.com/docs/mcp/).

## The MCP servers

The plugin lists both of DocSpring's MCP servers. Sign in to the one for your account's region and leave the other
disconnected:

- `docspring`: `https://mcp.docspring.com/mcp`, for accounts at app.docspring.com
- `docspring-eu`: `https://mcp-eu.docspring.com/mcp`, for accounts at app-eu.docspring.com

When you sign in, you choose what the agent can do: read only, read and write, or full access. Connecting without the
plugin, and what each access level allows: [MCP server setup](https://docspring.com/docs/mcp/).

## What these files do

The skills are Markdown instructions. There are no scripts, hooks or binaries, and no telemetry. The only network
endpoints are the two MCP servers above, which the agent connects to after you sign in.

## Feedback

This repository is published from DocSpring's main codebase, so pull requests can't be merged here. Open an issue for a
problem or a suggestion, or email support@docspring.com.

## License

[MIT](LICENSE)
