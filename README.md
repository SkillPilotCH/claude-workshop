# SkillPilot Workshop — Claude Code Plugins

Claude-Code-Plugin-Marketplace für SkillPilot-Workshop-Teilnehmer. Enthält wiederverwendbare Skills, die im Workshop benutzt und nach dem Workshop weiter genutzt werden.

## Installation

In Claude Code (CLI, VS Code Extension, oder Code-Tab in Claude Desktop):

```
/plugin marketplace add https://github.com/SkillPilotCH/claude-workshop
/plugin install lay-foundation@skillpilot-workshop
```

Danach steht der Skill via `/lay-foundation` zur Verfügung (genauer Aufruf wird nach der Installation in Claude Code angezeigt).

## Plugins in diesem Marketplace

### `lay-foundation`

Onboarding-Skill für Schicht 1 (User-Identität / globales Profil). Führt ein kurzes Interview, schreibt `~/.claude/CLAUDE.md` auf Disk und gibt zwei kopierbare Textblöcke für die Claude Desktop UI aus:

1. **Allgemein** → *Einstellungen → Allgemein → Anweisungen für Claude*
2. **Cowork** → *Einstellungen → Cowork → Anweisungen*

Damit ist die Foundation in beiden Claude-Welten (Cowork-Chats und Claude Code) konsistent.

Voraussetzung: muss in Claude Code ausgeführt werden (der Disk-Write nach `~/.claude/CLAUDE.md` geht nicht aus reinen Cowork-Chats).

## Konzeptioneller Hintergrund

Das didaktische Modell hinter dem Foundation-Layer (vier Schichten in zwei Werkzeug-Welten) erklärt das SkillPilot-Workshop-Material unter [skillpilot.ch](https://skillpilot.ch). Kurzfassung: Claude lädt Kontext aus mehreren `CLAUDE.md`-Files übereinander; in welcher Welt (Cowork / Claude Code) das passiert, hängt davon ab, womit der User arbeitet. Dieses Plugin legt die unterste Schicht (User-Profil) für beide Welten gleichzeitig.

## Maintainer

**SkillPilot** ([skillpilot.ch](https://skillpilot.ch)) — Claude-Cowork-Enabling-Workshops, Foundation- und Builder-Linie. Betrieben von **CleverAI GmbH** ([cleverai.ch](https://cleverai.ch)), Winkel ZH.

Kontakt: [stephan@cleverai.ch](mailto:stephan@cleverai.ch)

## Lizenz

[MIT](LICENSE) — Forken, anpassen, intern weiterverteilen ist ausdrücklich erwünscht. Wer auf diesem Material aufsetzt, freut sich SkillPilot über einen kurzen Hinweis (kein Muss) an die Adresse oben.
