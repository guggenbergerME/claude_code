# Die wichtigsten Claude Code Befehle

## Claude Code starten

```bash
# Interaktiven Chat starten
claude

# Mit einer direkten Anfrage starten
claude "erklaere mir diesen Code"

# Im Non-Interactive-Modus ausfuehren (Ergebnis direkt ausgeben)
claude -p "was macht diese Funktion?"

# Vorherige Konversation fortsetzen
claude --continue
```

## Slash-Befehle (im Chat)

Diese Befehle werden innerhalb einer laufenden Claude-Code-Sitzung eingegeben:

| Befehl | Beschreibung |
|--------|-------------|
| `/help` | Hilfe anzeigen |
| `/clear` | Konversation zuruecksetzen |
| `/compact` | Kontext komprimieren um Token zu sparen |
| `/cost` | Token-Verbrauch und Kosten anzeigen |
| `/doctor` | Installation und Konfiguration pruefen |
| `/init` | CLAUDE.md Projektdatei erstellen |
| `/review` | Code-Review durchfuehren |
| `/vim` | Vim-Modus aktivieren/deaktivieren |
| `/model` | KI-Modell wechseln |
| `/memory` | Projektgedaechtnis bearbeiten |
| `/fast` | Schnellen Modus umschalten |
| `/terminal-setup` | Terminal fuer Bildanzeige einrichten |

## Kommandozeilen-Optionen

```bash
# Hilfe anzeigen
claude --help

# Version anzeigen
claude --version

# Mit einem bestimmten Modell starten
claude --model claude-sonnet-4-6

# Ausfuehrlichen Output aktivieren
claude --verbose

# Eingabe per Pipe uebergeben
cat datei.py | claude -p "erklaere diesen Code"

# Systemanweisung mitgeben
claude -p --system "Du bist ein Python-Experte" "optimiere diese Funktion"
```

## MCP-Server verwalten

```bash
# MCP-Server hinzufuegen
claude mcp add <name> <befehl> [argumente...]

# Alle MCP-Server auflisten
claude mcp list

# MCP-Server entfernen
claude mcp remove <name>
```

## Konfiguration

```bash
# Konfiguration anzeigen
claude config list

# Einstellung setzen
claude config set <schluessel> <wert>

# API-Key konfigurieren
claude config set apiKey "dein-key"
```

## CLAUDE.md - Projektgedaechtnis

Die Datei `CLAUDE.md` im Projektverzeichnis speichert Anweisungen, die Claude bei jedem Start automatisch liest:

```bash
# CLAUDE.md interaktiv erstellen
claude /init

# Oder manuell erstellen
echo "# Projektanweisungen" > CLAUDE.md
echo "- Verwende TypeScript" >> CLAUDE.md
echo "- Tests mit pytest ausfuehren" >> CLAUDE.md
```

## Tastenkuerzel (waehrend einer Sitzung)

| Taste | Funktion |
|-------|----------|
| `Enter` | Nachricht absenden |
| `Escape` | Aktuelle Aktion abbrechen |
| `Escape` (doppelt) | Chat beenden |
| `Tab` | Autocomplete |
| `Strg+C` | Laufende Aktion abbrechen |
| `Strg+L` | Terminal leeren |

## Nuetzliche Anwendungsbeispiele

```bash
# Code erklaeren lassen
claude "erklaere die Funktion in src/main.py"

# Bug fixen
claude "finde und behebe den Bug in der Login-Funktion"

# Tests schreiben
claude "schreibe Unit-Tests fuer die Datei utils.py"

# Refactoring
claude "refaktorisiere die Klasse UserService"

# Git-Commit erstellen
claude /commit

# Code-Review eines PRs
claude /review
```
