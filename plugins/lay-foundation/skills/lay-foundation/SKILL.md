---
name: lay-foundation
description: Use whenever a Claude user is being onboarded for the first time, or whenever someone says "Foundation legen", "Schicht 1 aufsetzen", "Claude für mich einrichten", "neues CLAUDE.md anlegen", "User-Profil setzen", "/foundation", "ich bin neu hier, wie fange ich an", or asks how to give Claude their personal style/preferences across Cowork and Code. Runs a focused interview, writes `~/.claude/CLAUDE.md` to disk (Schicht 1 für Claude Code), and outputs two ready-to-paste blocks for Claude Desktop UI (Allgemein + Cowork). Must run in Claude Code — the disk write requires it.
---

# Foundation legen — Schicht 1 für beide Claude-Welten

Dieser Skill richtet einen neuen Claude-User auf seine eigene Identität, Sprache und seinen Stil ein. Schicht 1 = die Profil-Schicht, die in **jedem** Chat und **jedem** Code-Projekt geladen wird.

## Was am Ende rauskommt

1. **`~/.claude/CLAUDE.md`** — ausführliches Disk-File. Wird von Claude Code automatisch in jeder Session gelesen.
2. **UI-Block „Allgemein"** — kondensierter Textblock. Der User kopiert ihn in *Claude Desktop → Einstellungen → Allgemein → Anweisungen für Claude*.
3. **UI-Block „Cowork"** — etwas ausführlicherer Textblock mit Identität & Domain. Der User kopiert ihn in *Claude Desktop → Einstellungen → Cowork → Anweisungen*.

Die Blöcke werden **kondensiert** erzeugt, nicht eins-zu-eins mit dem Disk-File — UI-Felder sind für kürzere Texte gedacht.

## Vorbedingung — nur in Claude Code

Der Skill schreibt nach `~/.claude/CLAUDE.md`. Das geht nur in der Code-Welt (Claude Code CLI, VS Code Extension, oder Code-Tab in Claude Desktop). In reinen Cowork-Chats hat Claude keinen direkten Schreibzugriff auf das Home-Verzeichnis.

Wenn der Write fehlschlägt: dem User in einem Satz erklären, dass er den Skill in Claude Code starten muss (Code-Tab in Desktop reicht), und beenden — keine UI-Blöcke ausgeben, weil ohne Disk-File die Hälfte des Setups fehlt.

## Ablauf

Eröffne mit *einem* Satz:

> „Ich richte Schicht 1 für dich ein — dein persönliches Profil, das Claude in jedem Chat und jedem Code-Projekt sieht. Ich stelle dir gleich ein paar Fragen, dann schreibe ich dein Profil und gebe dir zwei Textblöcke zum Kopieren."

Dann: Interview → offene Schlussfrage → Disk-File → UI-Blöcke → Abschluss.

## Interview

Stelle die strukturierten Fragen in **zwei** `AskUserQuestion`-Aufrufen, damit der User sie als zwei zusammenhängende Sets beantworten kann.

### Aufruf 1 — Wer und wie (4 Fragen)

```
1. header: "Rolle"
   question: "Wie würdest du deine berufliche Rolle beschreiben?"
   options:
     - "Selbstständig / Unternehmer"
     - "Angestellt — Management oder Specialist"
     - "Wissensarbeit (Beratung, Recht, Finanzen, Medizin)"
     - "Kreativ / Content (Marketing, Design, Schreiben)"

2. header: "Sprache"
   question: "Welche Sprache und Variante nutzt du primär mit Claude?"
   options:
     - "Deutsch (Schweiz) — kein ß, 'ss' statt 'ß'"
     - "Deutsch (Deutschland / Österreich)"
     - "English"
     - "Mehrsprachig — wechselt je nach Kontext"

3. header: "Ton"
   question: "Wie soll Claude mit dir reden?"
   options:
     - "Direkt, knapp, kein Smalltalk"
     - "Höflich-präzise, etwas Kontext erlaubt"
     - "Locker und ausführlich, gerne mit Erklärungen"
     - "Sparring-Partner — widersprich mir aktiv, wenn ich Quatsch erzähle"

4. header: "Unsicherheit"
   question: "Was tun, wenn deine Anfrage mehrdeutig oder unklar ist?"
   options:
     - "Rückfrage stellen, bevor du rätst"
     - "Plausibelste Annahme treffen und sichtbar machen"
     - "Beide Interpretationen kurz anbieten, ich entscheide"
```

### Aufruf 2 — Vermeiden und Themen (2 Fragen)

```
5. header: "Vermeiden"
   multiSelect: true
   question: "Was nervt dich an typischen KI-Outputs? (mehrere möglich)"
   options:
     - "Buzzwords (innovativ, massgeschneidert, wegweisend, Synergien)"
     - "Übertriebene Höflichkeit (Tolle Frage!, Gerne helfe ich!)"
     - "Wiederholung meiner Frage vor der Antwort"
     - "Zusammenfassung am Ende, wenn nicht angefordert"

6. header: "Themen"
   question: "Worüber redest du am häufigsten mit Claude?"
   options:
     - "Kunden, Vertrieb, Kommunikation"
     - "Recht, Verträge, Compliance"
     - "Schreiben, Content, Marketing"
     - "Strategie, Entscheidungen, Analyse"
```

`Other` ist bei `AskUserQuestion` immer verfügbar — für User, deren Beruf/Sprache/Themen aus dem Rahmen fallen, ist das der Hauptweg. Lies die Other-Antworten ernst und übernimm sie wörtlich.

### Offene Schlussfrage — als normaler Dialog, kein AskUserQuestion

Frag im Klartext:

> „Letzte Frage: Gibt es etwas Spezifisches, das Claude über dich oder deine Arbeit wissen sollte? Zum Beispiel eine Branche, ein wichtiges Projekt, eine konkrete Vorliebe oder ein klares Don't. Wenn nichts mehr fehlt, schreib einfach ‚fertig'."

Was hier rauskommt, übernimmst du wörtlich in den Disk-File-Abschnitt „Spezifisches" und (kondensiert) in den Cowork-UI-Block.

## Output 1 — `~/.claude/CLAUDE.md`

### Backup-Logik (vor dem Schreiben)

```bash
# Pseudo-Ablauf:
if [ -f ~/.claude/CLAUDE.md ]; then
  cp ~/.claude/CLAUDE.md ~/.claude/CLAUDE.md.bak.$(date +%Y-%m-%d-%H%M)
fi
```

Backup-Pfad merken — wird am Ende dem User mitgeteilt.

### Struktur des Disk-Files

```markdown
# [Vorname] — Globale Präferenzen

Diese Datei lädt in jeder Claude-Code-Session, projektunabhängig.

## Identität

[2–4 Zeilen aus Frage 1 + 6 + Schlussfrage. Wer bin ich beruflich, was ist meine Domain, ggf. Firma/Position. Knapp.]

## Sprache

[Aus Frage 2. Bei DE-CH explizit: „kein ß, immer ss". Bei EN: „Always respond in English, do not translate technical terms." Bei mehrsprachig: kurz die Regel.]

- Datumsformat: [Default: 4. Februar 2026 oder 4.2.2026 — anpassen wenn Sprache != DE]
- Währung: [Default: CHF bei DE-CH, EUR bei DE-DE/AT, USD bei EN]

## Ton

[Aus Frage 3 + 4, je 2–3 Zeilen. Konkrete Sätze, nicht abstrakt.]

## Vermeiden im Output

[Aus Frage 5. Bullet-Liste der gewählten Items. Pro Item ein kurzes Negativbeispiel, damit klar ist, was gemeint ist.]

## Domain & Kontext

[Aus Frage 6 + Schlussfrage. Welche Themen, welche Branche, welche typischen Aufgaben.]

## Spezifisches

[Nur wenn Schlussfrage Inhalt brachte. Wörtlich übernehmen.]
```

**Stil-Regeln für das Disk-File selbst:**
- 2. Person Singular (nicht „der User", nicht „man")
- Konkret statt abstrakt — „kein ß, immer ss" statt „Schweizer Rechtschreibung"
- Keine Buzzwords im File — der File ist Vorbild für späteres Verhalten
- Maximal 40–60 Zeilen total. Wer mehr will, kann später selber erweitern.

## Output 2 — Desktop UI-Block „Allgemein"

**Was reingehört:** nur Stil-Regeln und Anti-Patterns. Sprache, Ton, Vermeiden.
**Was NICHT reingehört:** Identität, Domain, Projekt-Kontext. Das gehört in den Cowork-Block.

**Format:** 4–7 Zeilen. Telegrammstil. Gib es als kopierbaren Markdown-Code-Block aus mit explizitem Hinweis, *wo* der User es einfügen soll.

Beispiel-Struktur (anpassen pro User):

````
**UI-Block 1 — Allgemein**

Kopier diesen Block in *Claude Desktop → Einstellungen → Allgemein → Anweisungen für Claude*:

```
Sprache: [eine Zeile aus Frage 2]
Ton: [eine Zeile aus Frage 3]
Bei Unsicherheit: [eine Zeile aus Frage 4]
Vermeiden: [aus Frage 5, kommasepariert]
[Optional: ein einzeiliger Zusatz aus der Schlussfrage, falls stilrelevant]
```
````

## Output 3 — Desktop UI-Block „Cowork"

**Was reingehört:** Identität, Domain, spezifische Vorlieben. Also der Kontext, der für jede Cowork-Session helfen würde.
**Format:** 8–15 Zeilen, mit Mini-Überschriften. Auch hier als kopierbarer Block.

Beispiel-Struktur (anpassen pro User):

````
**UI-Block 2 — Cowork**

Kopier diesen Block in *Claude Desktop → Einstellungen → Cowork → Anweisungen*:

```
## WER ICH BIN
[2–3 Zeilen: Name, Rolle, Domain aus Frage 1 + 6]

## WIE ICH ARBEITE
[2–3 Zeilen: Ton + Unsicherheit aus Frage 3 + 4, knapper als im Disk-File]

## SPEZIFISCHES
[Aus Schlussfrage, falls vorhanden — sonst diesen Abschnitt weglassen]
```
````

## Abschluss

Zum Schluss in höchstens 6 Zeilen:

1. „Disk-File geschrieben nach `~/.claude/CLAUDE.md`."
2. Falls Backup gemacht: „Dein altes File liegt jetzt unter `~/.claude/CLAUDE.md.bak.<timestamp>`."
3. „Die zwei UI-Blöcke oben kopierst du selbst in Claude Desktop — Claude Code sieht die nicht automatisch."
4. „Verifikations-Tipp: Starte einen neuen Cowork-Chat und einen neuen Code-Tab und frag jeweils ‚Was weisst du über mich?'. Wenn beide deine Identität nennen, sitzt Schicht 1 in beiden Welten."

Keine Buzzword-Floskel am Ende („Viel Spass!" o.ä.). Knapp halten.

## Wenn etwas schiefgeht

- **Write-Fehler auf `~/.claude/CLAUDE.md`:** wahrscheinlich nicht in Claude Code. Erklären, abbrechen.
- **Existierendes File ist riesig oder enthält offensichtlich kuratiertes Material:** vor dem Überschreiben dem User zeigen, was drinsteht, und explizit fragen, ob er wirklich neu starten will. Backup wird trotzdem gemacht.
- **User bricht mitten im Interview ab:** keine Files schreiben. Sag nur „Ok, beim nächsten Mal nimmst du da auf, wo du aufgehört hast."
