# Claude Code unter Linux Mint installieren

## Voraussetzungen

- Linux Mint (20.x, 21.x oder 22.x)
- Terminal-Zugang
- Internetverbindung
- Ein Anthropic-Account mit API-Zugang oder ein Max-Abo

## Schritt 1: Node.js installieren

Claude Code benoetigt Node.js Version 18 oder hoeher.

```bash
# Node.js Repository hinzufuegen (NodeSource)
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -

# Node.js installieren
sudo apt-get install -y nodejs

# Installation pruefen
node --version
npm --version
```

**Alternative mit nvm (Node Version Manager):**

```bash
# nvm installieren
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash

# Terminal neu starten oder source ausfuehren
source ~/.bashrc

# Node.js installieren
nvm install 22
nvm use 22
```

## Schritt 2: Claude Code installieren

```bash
# Claude Code global installieren
npm install -g @anthropic-ai/claude-code
```

## Schritt 3: Claude Code starten

```bash
# In ein Projektverzeichnis wechseln
cd /pfad/zu/deinem/projekt

# Claude Code starten
claude
```

Beim ersten Start wirst du aufgefordert, dich mit deinem Anthropic-Account anzumelden.

## Schritt 4: Authentifizierung

Es gibt mehrere Moeglichkeiten sich zu authentifizieren:

1. **Anthropic Console (API-Key):** Waehle diese Option und folge den Anweisungen im Browser
2. **Claude Max/Pro Abo:** Waehle die Option fuer das Max-Abonnement
3. **API-Key direkt setzen:**

```bash
export ANTHROPIC_API_KEY="dein-api-key-hier"
claude
```

## Updates

```bash
# Claude Code auf die neueste Version aktualisieren
npm update -g @anthropic-ai/claude-code
```

## Deinstallation

```bash
npm uninstall -g @anthropic-ai/claude-code
```

## Fehlerbehebung

### Berechtigungsfehler bei npm install -g

```bash
# npm-Verzeichnis anpassen
mkdir -p ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc

# Dann erneut installieren
npm install -g @anthropic-ai/claude-code
```

### Node.js-Version zu alt

```bash
# Aktuelle Version pruefen
node --version

# Falls aelter als v18, Node.js aktualisieren (siehe Schritt 1)
```
