# tmux - Terminal Multiplexer

## Was ist tmux?

tmux ermoeglicht es, mehrere Terminals in einer einzigen Sitzung zu verwalten. Sessions laufen im Hintergrund weiter, selbst wenn das Terminal geschlossen wird - ideal fuer Server-Arbeit und lange Prozesse.

---

## Installation unter Linux Mint

```bash
# Paketlisten aktualisieren
sudo apt update

# tmux installieren
sudo apt install -y tmux

# Installation pruefen
tmux -V
```

---

## Grundkonzepte

- **Session** - Eine tmux-Sitzung (kann mehrere Fenster enthalten)
- **Window** - Ein Fenster innerhalb einer Session (wie Tabs)
- **Pane** - Ein geteilter Bereich innerhalb eines Fensters
- **Prefix-Taste** - Standardmaessig `Strg+B`, wird vor jedem tmux-Befehl gedrueckt

---

## Sessions verwalten

### Von der Kommandozeile

```bash
# Neue Session starten
tmux

# Neue Session mit Name starten
tmux new -s meine-session

# Alle Sessions auflisten
tmux ls

# An eine bestehende Session anhaengen
tmux attach -t meine-session
tmux a -t meine-session

# An die letzte Session anhaengen
tmux a

# Session beenden
tmux kill-session -t meine-session

# Alle Sessions beenden
tmux kill-server
```

### Innerhalb von tmux (mit Prefix Strg+B)

| Tastenkuerzel | Beschreibung |
|---------------|-------------|
| `Strg+B` dann `d` | Session trennen (detach) - laeuft im Hintergrund weiter |
| `Strg+B` dann `$` | Session umbenennen |
| `Strg+B` dann `s` | Zwischen Sessions wechseln |

---

## Fenster (Windows)

| Tastenkuerzel | Beschreibung |
|---------------|-------------|
| `Strg+B` dann `c` | Neues Fenster erstellen |
| `Strg+B` dann `n` | Naechstes Fenster |
| `Strg+B` dann `p` | Vorheriges Fenster |
| `Strg+B` dann `0-9` | Zu Fenster mit Nummer wechseln |
| `Strg+B` dann `,` | Fenster umbenennen |
| `Strg+B` dann `w` | Fensterliste anzeigen |
| `Strg+B` dann `&` | Fenster schliessen (mit Bestaetigung) |

---

## Panes (geteilte Bereiche)

| Tastenkuerzel | Beschreibung |
|---------------|-------------|
| `Strg+B` dann `%` | Pane vertikal teilen (links/rechts) |
| `Strg+B` dann `"` | Pane horizontal teilen (oben/unten) |
| `Strg+B` dann `Pfeiltaste` | Zwischen Panes wechseln |
| `Strg+B` dann `x` | Aktuelles Pane schliessen |
| `Strg+B` dann `z` | Pane maximieren/wiederherstellen (Zoom) |
| `Strg+B` dann `{` | Pane nach links verschieben |
| `Strg+B` dann `}` | Pane nach rechts verschieben |
| `Strg+B` dann `Leertaste` | Pane-Layout wechseln |
| `Strg+B` dann `o` | Zum naechsten Pane wechseln |
| `Strg+B` dann `q` | Pane-Nummern anzeigen |

### Pane-Groesse aendern

```
Strg+B dann Strg+Pfeiltaste    Groesse in kleinen Schritten aendern
Strg+B dann Alt+Pfeiltaste     Groesse in grossen Schritten aendern
```

---

## Kopier-Modus (Scroll & Copy)

| Tastenkuerzel | Beschreibung |
|---------------|-------------|
| `Strg+B` dann `[` | Kopier-Modus starten (scrollen mit Pfeiltasten/PgUp/PgDn) |
| `q` | Kopier-Modus verlassen |
| `Leertaste` | Markierung starten (im Kopier-Modus) |
| `Enter` | Markierten Text kopieren |
| `Strg+B` dann `]` | Kopierten Text einfuegen |

---

## Nuetzliche Befehle innerhalb von tmux

| Tastenkuerzel | Beschreibung |
|---------------|-------------|
| `Strg+B` dann `:` | Befehlszeile oeffnen |
| `Strg+B` dann `t` | Uhrzeit anzeigen |
| `Strg+B` dann `?` | Alle Tastenkuerzel anzeigen |

---

## Konfiguration (~/.tmux.conf)

Eine Basiskonfiguration fuer angenehmeres Arbeiten:

```bash
# Datei erstellen/bearbeiten
nano ~/.tmux.conf
```

Beispielkonfiguration:

```conf
# Prefix auf Strg+A aendern (bequemer)
unbind C-b
set -g prefix C-a
bind C-a send-prefix

# Maus-Unterstuetzung aktivieren
set -g mouse on

# Panes mit intuitiveren Tasten teilen
bind | split-window -h
bind - split-window -v

# Fenster-Nummerierung bei 1 starten
set -g base-index 1
setw -g pane-base-index 1

# Schnelleres Reagieren auf Escape
set -sg escape-time 0

# Mehr Scrollback-Zeilen
set -g history-limit 10000

# Statusleiste anpassen
set -g status-style 'bg=colour235 fg=colour136'
```

Nach Aenderungen die Konfiguration neu laden:

```bash
# Innerhalb von tmux
Strg+B dann : und eingeben: source-file ~/.tmux.conf

# Oder von der Kommandozeile
tmux source-file ~/.tmux.conf
```

---

## Typischer Workflow

```bash
# 1. Neue Session fuer ein Projekt starten
tmux new -s projekt

# 2. Fenster aufteilen fuer Editor und Terminal
Strg+B dann %

# 3. Bei Bedarf weitere Fenster erstellen
Strg+B dann c

# 4. Session trennen wenn fertig (laeuft weiter)
Strg+B dann d

# 5. Spaeter wieder verbinden
tmux a -t projekt
```

---

## tmux mit Claude Code

tmux eignet sich hervorragend fuer die Arbeit mit Claude Code:

```bash
# Session starten
tmux new -s claude

# Pane teilen: links Claude Code, rechts normales Terminal
Strg+B dann %

# Im linken Pane Claude Code starten
claude

# Im rechten Pane (Strg+B dann Pfeiltaste) Tests oder Befehle ausfuehren
```
