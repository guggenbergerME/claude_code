# Claude Code Agents / Agent SDK

## Ueberblick

Das **Claude Agent SDK** ermoeglicht es, die gleichen Tools und den Agenten-Loop von Claude Code programmatisch zu nutzen. Damit lassen sich eigene KI-Agenten bauen, die autonom Dateien lesen, Code schreiben, Befehle ausfuehren und sogar Unteragenten (Subagents) starten koennen.

**Pakete:**

| Sprache | Paket |
|---------|-------|
| TypeScript/JS | `@anthropic-ai/claude-agent-sdk` |
| Python | `claude-agent-sdk` |

## Installation

```bash
# TypeScript
npm install @anthropic-ai/claude-agent-sdk

# Python
pip install claude-agent-sdk
```

## Grundlagen: Die query()-Funktion

Der zentrale Einstiegspunkt ist `query()`. Sie erstellt einen Agenten-Loop und gibt einen async Iterator zurueck, der Nachrichten streamt waehrend Claude autonom arbeitet.

### TypeScript Beispiel

```typescript
import { query } from "@anthropic-ai/claude-agent-sdk";

for await (const message of query({
  prompt: "Finde und behebe den Bug in auth.py",
  options: {
    allowedTools: ["Read", "Edit", "Bash"],
    permissionMode: "acceptEdits"
  }
})) {
  if (message.type === "assistant" && message.message?.content) {
    for (const block of message.message.content) {
      if ("text" in block) console.log(block.text);
      else if ("name" in block) console.log(`Tool: ${block.name}`);
    }
  } else if (message.type === "result") {
    console.log(`Fertig: ${message.subtype}`);
  }
}
```

### Python Beispiel

```python
import asyncio
from claude_agent_sdk import query, ClaudeAgentOptions, AssistantMessage, ResultMessage

async def main():
    async for message in query(
        prompt="Finde und behebe den Bug in auth.py",
        options=ClaudeAgentOptions(
            allowed_tools=["Read", "Edit", "Bash"],
            permission_mode="acceptEdits",
        ),
    ):
        if isinstance(message, AssistantMessage):
            for block in message.content:
                if hasattr(block, "text"):
                    print(block.text)
        elif isinstance(message, ResultMessage):
            print(f"Fertig: {message.subtype}")

asyncio.run(main())
```

### Unterschied zum Anthropic Client SDK

Mit dem Client SDK muss man den Tool-Loop selbst implementieren. Mit dem Agent SDK arbeitet Claude autonom:

```python
# Client SDK: Man implementiert den Tool-Loop selbst
response = client.messages.create(...)
while response.stop_reason == "tool_use":
    result = your_tool_executor(response.tool_use)
    response = client.messages.create(tool_result=result, **params)

# Agent SDK: Claude handhabt Tools selbststaendig
async for message in query(prompt="Behebe den Bug in auth.py"):
    print(message)
```

## Konfigurationsoptionen

### Wichtige Parameter fuer query()

| Parameter | Typ | Beschreibung |
|-----------|-----|-------------|
| `prompt` | `string` | Eingabe-Prompt (Pflicht) |
| `allowedTools` | `string[]` | Erlaubte Tools einschraenken |
| `disallowedTools` | `string[]` | Bestimmte Tools blockieren |
| `permissionMode` | `PermissionMode` | Berechtigungsmodus |
| `systemPrompt` | `string / object` | System-Prompt Konfiguration |
| `model` | `string` | Claude-Modell |
| `cwd` | `string` | Arbeitsverzeichnis |
| `maxTurns` | `number` | Maximale Konversationsrunden |
| `maxBudgetUsd` | `number` | Maximale Kosten in USD |
| `agents` | `Record<string, AgentDefinition>` | Subagent-Definitionen |
| `mcpServers` | `Record<string, McpServerConfig>` | MCP-Server Konfigurationen |
| `resume` | `string` | Session-ID zum Fortsetzen |
| `settingSources` | `SettingSource[]` | Dateisystem-Einstellungen laden |

### Berechtigungsmodi

| Modus | Verhalten | Einsatz |
|-------|-----------|---------|
| `'default'` | Erfordert `canUseTool`-Callback | Eigene Genehmigungslogik |
| `'acceptEdits'` | Dateibearbeitungen automatisch erlaubt | Vertrauenswuerdige Entwicklung |
| `'bypassPermissions'` | Keine Berechtigungsabfragen | CI/CD, Automatisierung |
| `'plan'` | Nur Planung, keine Ausfuehrung | Vorschau |

### System-Prompt Konfiguration

```typescript
// Eigener System-Prompt
options: { systemPrompt: "Du bist ein Python-Experte." }

// Claude Code System-Prompt verwenden
options: {
  systemPrompt: { type: "preset", preset: "claude_code" },
  settingSources: ["project"]  // laedt CLAUDE.md Dateien
}
```

## Verfuegbare Tools

| Tool | Beschreibung |
|------|-------------|
| `Read` | Dateien lesen |
| `Write` | Neue Dateien erstellen |
| `Edit` | Bestehende Dateien bearbeiten |
| `Bash` | Terminal-Befehle ausfuehren |
| `Glob` | Dateien nach Muster suchen |
| `Grep` | Dateiinhalte mit Regex durchsuchen |
| `WebSearch` | Web-Suche |
| `WebFetch` | Webseiten abrufen und parsen |
| `Task` | Subagenten aufrufen |
| `AskUserQuestion` | Rueckfragen an den Benutzer |
| `NotebookEdit` | Jupyter Notebooks bearbeiten |

## Subagenten (Multi-Agent Orchestrierung)

Subagenten sind separate Agent-Instanzen fuer fokussierte Teilaufgaben mit eigenem Kontext, eigenen Tools und eigenem System-Prompt.

### Subagent-Definition

```typescript
import { query } from "@anthropic-ai/claude-agent-sdk";

for await (const message of query({
  prompt: "Pruefe das Authentifizierungsmodul auf Sicherheitsprobleme",
  options: {
    allowedTools: ["Read", "Grep", "Glob", "Task"],
    agents: {
      "code-reviewer": {
        description: "Code-Review Spezialist. Fuer Qualitaets- und Sicherheitsreviews.",
        prompt: `Du bist ein Code-Review Spezialist mit Expertise in Sicherheit und Best Practices.
Identifiziere Sicherheitsluecken, Performanceprobleme und schlage Verbesserungen vor.`,
        tools: ["Read", "Grep", "Glob"],
        model: "sonnet"
      },
      "test-runner": {
        description: "Fuehrt Tests aus und analysiert Ergebnisse.",
        prompt: "Du bist ein Test-Spezialist...",
        tools: ["Bash", "Read", "Grep"]
      }
    }
  }
})) {
  if ("result" in message) console.log(message.result);
}
```

### AgentDefinition Felder

| Feld | Typ | Pflicht | Beschreibung |
|------|-----|---------|-------------|
| `description` | `string` | Ja | Wann dieser Agent verwendet werden soll |
| `prompt` | `string` | Ja | System-Prompt des Agenten |
| `tools` | `string[]` | Nein | Erlaubte Tools (erbt alle wenn leer) |
| `model` | `string` | Nein | Modell-Override (`sonnet`, `opus`, `haiku`) |

### Typische Tool-Kombinationen fuer Subagenten

| Einsatz | Tools |
|---------|-------|
| Nur-Lese-Analyse | `Read`, `Grep`, `Glob` |
| Test-Ausfuehrung | `Bash`, `Read`, `Grep` |
| Code-Aenderungen | `Read`, `Edit`, `Write`, `Grep`, `Glob` |
| Vollzugriff | Alle Tools (Feld weglassen) |

### Wichtige Regeln

- Subagenten koennen **keine eigenen Subagenten** starten (kein Verschachteln)
- `Task` darf **nicht** in den Tools eines Subagenten enthalten sein
- Claude entscheidet anhand des `description`-Feldes, wann ein Subagent gestartet wird

### Dateisystem-basierte Subagenten

Alternativ koennen Subagenten als Markdown-Dateien in `.claude/agents/` definiert werden:

```
.claude/agents/reviewer.md
```

## Eigene MCP-Tools erstellen

```typescript
import { query, tool, createSdkMcpServer } from "@anthropic-ai/claude-agent-sdk";
import { z } from "zod";

const meinTool = tool(
  "meinTool",
  "Fuehrt eine benutzerdefinierte Aktion aus",
  { input: z.string() },
  async (args) => {
    return { content: [{ type: "text", text: `Verarbeitet: ${args.input}` }] };
  }
);

const server = createSdkMcpServer({
  name: "mein-server",
  tools: [meinTool]
});

for await (const message of query({
  prompt: "Verwende meinTool",
  options: { mcpServers: { "mein-server": server } }
})) { /* ... */ }
```

## Session-Verwaltung (Konversation fortsetzen)

```typescript
let sessionId: string | undefined;

// Erste Anfrage
for await (const message of query({
  prompt: "Lies das Authentifizierungsmodul",
  options: { allowedTools: ["Read", "Glob"] }
})) {
  if (message.type === "system" && message.subtype === "init") {
    sessionId = message.session_id;
  }
}

// Spaeter fortsetzen mit vollem Kontext
for await (const message of query({
  prompt: "Finde jetzt alle Stellen die es aufrufen",
  options: { resume: sessionId }
})) {
  if ("result" in message) console.log(message.result);
}
```

## Hooks (Lifecycle-Events)

Hooks erlauben es, auf bestimmte Ereignisse im Agenten-Loop zu reagieren:

```typescript
import { query, HookCallback } from "@anthropic-ai/claude-agent-sdk";
import { appendFile } from "fs/promises";

const logAenderung: HookCallback = async (input) => {
  const filePath = (input as any).tool_input?.file_path ?? "unbekannt";
  await appendFile("./audit.log", `${new Date().toISOString()}: ${filePath} geaendert\n`);
  return {};
};

for await (const message of query({
  prompt: "Refaktorisiere utils.py",
  options: {
    permissionMode: "acceptEdits",
    hooks: {
      PostToolUse: [{ matcher: "Edit|Write", hooks: [logAenderung] }]
    }
  }
})) {
  if ("result" in message) console.log(message.result);
}
```

### Verfuegbare Hook-Events

`PreToolUse`, `PostToolUse`, `PostToolUseFailure`, `Notification`, `UserPromptSubmit`, `SessionStart`, `SessionEnd`, `Stop`, `SubagentStart`, `SubagentStop`, `PreCompact`, `PermissionRequest`

## Praxisbeispiel: CI/CD Pipeline

```typescript
for await (const message of query({
  prompt: "Fuehre Tests aus, behebe Fehler und stelle sicher dass alle bestehen",
  options: {
    allowedTools: ["Read", "Edit", "Bash", "Glob", "Grep"],
    permissionMode: "bypassPermissions",
    settingSources: ["project"],
    maxBudgetUsd: 5.0
  }
})) {
  if ("result" in message) console.log(message.result);
}
```

## Authentifizierung

```bash
# Direkt ueber Anthropic API
export ANTHROPIC_API_KEY=dein-api-key

# Amazon Bedrock
export CLAUDE_CODE_USE_BEDROCK=1

# Google Vertex AI
export CLAUDE_CODE_USE_VERTEX=1
```

## Sandbox-Konfiguration

```typescript
options: {
  sandbox: {
    enabled: true,
    autoAllowBashIfSandboxed: true,
    network: { allowLocalBinding: true },
    excludedCommands: ["docker"]
  }
}
```

## Weiterführende Ressourcen

- Agent SDK Ueberblick: https://platform.claude.com/docs/en/agent-sdk/overview
- Quickstart: https://platform.claude.com/docs/en/agent-sdk/quickstart
- TypeScript Referenz: https://platform.claude.com/docs/en/agent-sdk/typescript
- Subagents Doku: https://platform.claude.com/docs/en/agent-sdk/subagents
- npm Paket: https://www.npmjs.com/package/@anthropic-ai/claude-agent-sdk
- GitHub (TypeScript): https://github.com/anthropics/claude-agent-sdk-typescript
- GitHub (Python): https://github.com/anthropics/claude-agent-sdk-python
