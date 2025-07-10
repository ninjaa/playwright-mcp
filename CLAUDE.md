# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a fork of Microsoft's `playwright-mcp` repository, which provides a Model Context Protocol (MCP) server that enables browser automation using Playwright. The fork includes additional JavaScript execution tools and is maintained with a specific branching strategy.

**Upstream**: https://github.com/microsoft/playwright-mcp  
**Fork**: https://github.com/ninjaa/playwright-mcp

## Branch Strategy

- **`main`**: Tracks upstream Microsoft repository for pulling upstream changes
- **`snapse-main`**: Primary development branch with our enhancements and customizations
- **`js-tools`**: Feature branch for JavaScript tool additions (merged into snapse-main)

When pulling upstream changes: merge `main` ← `upstream/main`, then rebase `snapse-main` onto `main`.

## Development Commands

### Build & Development
```bash
npm run build              # Compile TypeScript
npm run watch              # Watch mode for development
npm run clean              # Clean build artifacts
```

### Testing  
```bash
npm test                   # Run all tests
npm run ctest              # Chrome tests only
npm run ftest              # Firefox tests only
npm run wtest              # WebKit tests only
npm run etest              # Extension tests only
```

### Code Quality
```bash
npm run lint               # Lint code and update README
npm run update-readme      # Update tool documentation in README
```

## Architecture

The codebase follows a modular tool-based architecture:

### Core Components
- **`src/server.ts`**: Main MCP server implementation
- **`src/connection.ts`**: Handles MCP protocol connections
- **`src/browserContextFactory.ts`**: Manages browser context creation and lifecycle
- **`src/context.ts`**: Browser automation context management
- **`src/tab.ts`**: Individual browser tab handling

### Tool System
Tools are organized in `src/tools/` with each tool defining:
- Schema validation using Zod
- Capability classification (core, tabs, pdf, etc.)
- Action handlers for browser automation

**Key Tools**:
- **Navigation**: `navigate.ts` - URL navigation and history
- **Interactions**: `common.ts` - clicks, typing, element selection
- **JavaScript**: `javascript.ts` - Custom JS execution tools (fork addition)
- **Resources**: `screenshot.ts`, `pdf.ts`, `network.ts`
- **Vision Mode**: `vision.ts` - Screenshot-based interactions

### Configuration
- **`src/config.ts`**: Configuration schema and validation
- **CLI**: `cli.js` - Command-line interface with extensive options
- **Multiple Modes**: Supports both accessibility snapshots (default) and vision mode

## Fork-Specific Enhancements

### JavaScript Tools (Added)
Three new tools in `src/tools/javascript.ts`:

1. **`browser_evaluate`**: Execute JavaScript in page context
2. **`browser_get_html`**: Get page or element HTML content  
3. **`browser_get_attribute`**: Get element attributes

These tools are automatically registered in both snapshot and vision tool arrays in `src/tools.ts`.

## Test Architecture

Tests use Playwright Test framework with custom fixtures:
- **`tests/fixtures.ts`**: Custom test fixtures for MCP client simulation
- **Test Projects**: Chrome, Firefox, WebKit, Extension mode
- **Test Categories**: Core interactions, navigation, resources, utilities

Example test structure:
```typescript
test('tool_name', async ({ client, server }) => {
  // Setup test server content
  server.setContent('/', '<html>content</html>', 'text/html');
  
  // Call MCP tool
  const result = await client.callTool({
    name: 'browser_navigate',
    arguments: { url: server.PREFIX }
  });
  
  // Assert expected output format
  expect(result).toHaveTextContent('expected output');
});
```

## Integration Notes

- **Extension Support**: Can load Chrome extensions via CLI flags
- **Headless/Headed**: Configurable browser modes
- **Network Control**: Request blocking/allowing capabilities  
- **Storage State**: Persistent or isolated session management
- **Docker Support**: Available for headless automation

## Key Files for Tool Development

When adding new tools:
1. Create tool module in `src/tools/`
2. Import and register in `src/tools.ts`
3. Add tests in `tests/` following existing patterns
4. Run `npm run update-readme` to update documentation

The tool system uses a consistent pattern with `defineTool()` helper and MCP protocol compliance.