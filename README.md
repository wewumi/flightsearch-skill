# Claude-Skills von Marc

## ✈️ Flugsuche

Findet die günstigsten bzw. besten Flüge über Google Flights, Momondo, Kiwi und Trip.com – ab allen sinnvollen Flughäfen im Umkreis (Standard: Region Stuttgart) – und maximiert die Zeit vor Ort. Der Skill recherchiert nur, er bucht nie.

### Installation (Claude Code)

Im Terminal:

```bash
claude plugin marketplace add wewumi/flightsearch-skill && claude plugin install flugsuche@marc-skills
```

Oder direkt in einer Claude-Code-Sitzung:

```
/plugin marketplace add wewumi/flightsearch-skill
/plugin install flugsuche@marc-skills
```

### Immer die neueste Version bekommen

Der Skill wird laufend weiterentwickelt. Damit du Updates automatisch erhältst:

1. In Claude Code `/plugin` öffnen
2. **Marketplaces** → **marc-skills** auswählen
3. **Enable auto-update** wählen

Alternativ manuell aktualisieren:

```bash
claude plugin update flugsuche@marc-skills
```

### Nutzung

Einfach fragen, z. B.: *„Finde mir einen günstigen Flug nach Lissabon im März, ein verlängertes Wochenende.“*

### Hinweis zur Claude-App (claude.ai)

Wer Claude nicht über Claude Code nutzt, kann die Datei [`plugins/flugsuche/skills/flugsuche/SKILL.md`](plugins/flugsuche/skills/flugsuche/SKILL.md) herunterladen, in einen Ordner `flugsuche` legen, als ZIP packen und unter **Einstellungen → Fähigkeiten → Skills** hochladen. Updates kommen dort nicht automatisch – bei neuen Versionen erneut hochladen.
