# Janis Dashboard

## Projektueberblick

Janis Dashboard ist eine statische Website zur Visualisierung persoenlicher Trail-Running-, Recovery- und Trainingsplandaten. Die Website wird automatisch aus einem privaten Daten-Repository erzeugt und in einem separaten oeffentlichen Repository bereitgestellt. Ziel ist ein modernes, wartungsarmes Dashboard, das Trainingshistorie, Regeneration, Saisonkontext und aktuelle Trainingsplanung in einer klaren Oberflaeche zusammenfuehrt.

Die Website ist:
- statisch deploybar
- ohne Backend und ohne Login
- datengetrieben aus abgeleiteten JSON-Dateien
- fuer Janis als primaeren Nutzer optimiert
- fuer externe Entwickler und KI-Assistenten klar dokumentiert

Die Website ist nicht:
- ein allgemeines Gesundheitsportal
- ein Social-Produkt
- ein System zur Dateneingabe
- ein medizinisches Diagnosetool
- eine 1:1-Spiegelung des privaten Daten-Repositories

## Zielbild

Die Website soll die folgenden Fragen schnell beantworten:

### Overview
- Wie ist mein aktueller Trainingsstatus?
- Wie ist mein aktueller Recovery-Status?
- Was ist diese Woche passiert?
- Was ist heute oder als Naechstes geplant?

### Training
- Entwickelt sich mein Trainingsumfang sinnvoll?
- Wie verteilen sich Intensitaet, Hoehenmeter und Long Runs?
- Wie gut setze ich den Plan um?

### Recovery
- Wie reagieren Schlaf, HRV, Ruhepuls und Readiness auf die Trainingslast?
- Gibt es Hinweise auf Ermuedung, Ueberlastung oder gute Anpassung?

### Performance
- Verbessern sich Effizienz, Pace, Belastbarkeit und Long-Run-Resilienz?
- Welche Trends zeigen die letzten Wochen und Monate?

### Season / Plan
- In welcher Phase der Saison befinde ich mich?
- Welche Rennen und Trainingsbloecke stehen an?
- Wie passt mein Ist-Zustand zum Plan?

## Repository-Architektur

### Privates Quell-Repository
Quell-Repository: `DevJanis/Strava-Data`

Dort liegen die privaten Roh- und Analysequellen, insbesondere:
- `data/activities_enriched.csv`
- `data/health_daily.csv`
- `data/race_calendar.json`
- `data/season_progress.json`
- `data/latest_analysis_briefing.md`
- `health/hr_zones.json`
- `training_sessions/*.json`

Nicht verwendet werden fuer das Dashboard:
- `data/activities.csv`
- `data/activity_feedback.csv`

### Oeffentliches Dashboard-Repository
Ziel-Repository: `DevJanis/dashboard`

Dieses Repository enthaelt:
- Frontend-Code der Website
- abgeleitete, public-safe JSON-Dateien unter `data/derived/`
- GitHub-Workflow fuer Build und Deployment

Wichtig:
Das Dashboard-Repository enthaelt keine Rohdaten und keine synchronisierten Quelldateien aus dem privaten Repository. Es werden ausschliesslich veroeffentlichungssichere Ableitungen gespeichert.

## Datenfluss

```text
Privates Repo: DevJanis/Strava-Data
    -> liest private Quelldaten
    -> validiert und aggregiert Daten
    -> erzeugt public-safe JSON-Ableitungen
    -> schreibt diese nach DevJanis/dashboard:data/derived/

Oeffentliches Repo: DevJanis/dashboard
    -> erkennt Aenderungen an data/derived/
    -> fuehrt Astro Build aus
    -> deployt die statische Website auf GitHub Pages
```

## Zielseiten der Website

Die Website soll mindestens aus folgenden Seiten bestehen:

### 1. Overview
Zeigt den kompakten Gesamtstatus:
- aktuelle Wochen-KPIs
- Recovery-Snapshot
- naechstes Rennen
- heutige oder naechste geplante Einheit
- wichtigste Insights

### 2. Training
Zeigt Trainingshistorie und Belastung:
- Wochenvolumen
- Hoehenmeter
- Sessionanzahl
- Long-Run-Verlauf
- Intensitaetsverteilung
- Soll-Ist-Vergleich zum Plan

### 3. Recovery
Zeigt Regenerationsdaten:
- Schlaftrend
- HRV-Trend
- Ruhepuls-Trend
- Body Battery / Readiness
- Load-vs-Recovery-Darstellung

### 4. Performance
Zeigt Leistungsentwicklung:
- Pace-/HF-Effizienz
- Long-Run-Entwicklung
- Split- oder Belastungstrends
- langfristige Performance-Marker

### 5. Plan
Zeigt den aktuellen Trainingsplan:
- aktueller 14-Tage-Plan
- Schluesseleinheiten
- Fokus des aktuellen Blocks
- Tagesansicht geplanter Sessions

### 6. Season
Zeigt Saison- und Rennkontext:
- aktuelle Phase
- Zielrennen
- Trainingsbloecke
- Timeline / Saisonverlauf

## Produktprinzipien

Die Website soll:
- statisch deploybar sein
- automatisch aus Repo-Daten aktualisiert werden
- ohne manuelle Nachpflege funktionieren
- Insights statt Datenueberladung liefern
- Trainingsrealitaet und Planungslogik verbinden
- privacy-aware aufgebaut sein
- fuer Entwickler und KI leicht verstaendlich bleiben

Die Website soll nicht:
- Rohdaten im Frontend verarbeiten
- ein Backend oder Login benoetigen
- Datenbearbeitung im UI anbieten
- interne Notizen oder sensible Inhalte ungefiltert veroeffentlichen

## Datenmodell im Dashboard-Repo

Das Frontend konsumiert ausschliesslich Dateien aus:

```text
data/derived/
```

Geplante Dateien:

```text
data/derived/
- metadata.json
- overview.json
- weekly_training.json
- recovery_summary.json
- performance_trends.json
- season_timeline.json
- training_plans.json
- training_plan_current.json
- recent_workouts.json
- load_recovery.json
- correlations.json
- insights.json
```

### Grundsatz
- keine CSV-Dateien im Frontend
- keine Rohdaten im oeffentlichen Repo
- keine Business-Logik im Client
- nur public-safe, abgeleitete Daten

## Beschreibung der wichtigsten Derived Files

### `metadata.json`
Globale Metadaten zum Datenstand:
- Generierungszeitpunkt
- Quelle
- Zeitraum der Daten
- verwendete Quelldateien
- aktuell verwendeter Trainingsplan

### `overview.json`
Kompakte Daten fuer die Startseite:
- aktuelle Woche
- letzte 7 Tage Recovery
- aktueller Saisonblock
- naechstes Rennen
- heutige / naechste geplante Einheit

### `weekly_training.json`
Wochenaggregationen fuer Trainingscharts:
- Distanz
- Hoehenmeter
- Trainingszeit
- Sessionanzahl
- Long Run
- Intensitaetsverteilung
- ggf. TRIMP / Load

### `recovery_summary.json`
Taegliche Recovery-Zeitreihe und Baselines:
- Schlaf
- HRV
- Ruhepuls
- Body Battery
- Readiness
- Stress
- ggf. VO2max

### `performance_trends.json`
Langfristige Leistungsentwicklung:
- Pace-/HF-Effizienz
- Long-Run-Resilienz
- Hoehenmeter-Toleranz
- Trendindikatoren ueber mehrere Wochen

### `season_timeline.json`
Saisonkontext:
- Phasen
- Trainingsbloecke
- Zielrennen
- aktuelle Phase
- aktueller Wochenfokus

### `training_plans.json`
Archivierte Uebersicht der synchronisierten Trainingsplaene:
- Plan-Datum
- Gueltigkeitsbereich
- Phase
- Fokus

### `training_plan_current.json`
Veroeffentlichungssichere Darstellung des aktuellen Trainingsplans:
- aktueller Block
- Fokus
- Tagesstruktur
- Session-Typ
- geplante Dauer
- geplante Hoehenmeter
- Intensitaetslabel
- Kennzeichnung von Schluesseleinheiten

### `recent_workouts.json`
Kompakte Liste der letzten absolvierten Einheiten:
- Datum
- Titel
- Typ
- Distanz
- Dauer
- Hoehenmeter
- Herzfrequenz / Load

### `load_recovery.json`
Kombinierte Tagesdaten fuer Belastung und Erholung:
- Trainingslast
- Sessionanzahl
- Schlaf
- HRV
- Ruhepuls
- Body Battery
- Readiness

### `correlations.json`
Berechnete Zusammenhaenge:
- Schlaf vs Readiness
- Last vs HRV
- Last vs Ruhepuls
- weitere statistische Muster

### `insights.json`
Priorisierte, menschenlesbare Kernaussagen fuer die Website:
- wichtigste Trainingsinsights
- wichtigste Recovery-Insights
- auffaellige Trends
- kurze, klare Erklaerungen

## Public-Safe-Regeln

Alle Daten in `data/derived/` muessen veroeffentlichungssicher sein.

Erlaubt sind insbesondere:
- aggregierte KPIs
- Wochenwerte
- Trenddaten
- reduzierte Workout-Zusammenfassungen
- Saison- und Rennkontext
- gefilterte Trainingsplan-Informationen
- statistische Zusammenhaenge
- automatisch generierte Insight-Texte

Nicht ungefiltert veroeffentlicht werden sollen:
- Roh-CSV-Dateien
- vollstaendige private Quelldateien
- interne Freitextnotizen
- sensible Gesundheitsdetails ohne Visualisierungsnutzen
- interne Coaching-Kommentare
- vollstaendige Trainingsplan-Rohstruktur, sofern sie mehr enthaelt als fuer die Website noetig

## Workflow-Architektur

### Workflow 1 - privat: Derive and Publish
Ort: `DevJanis/Strava-Data`

Aufgabe:
- relevante private Quelldaten lesen
- Daten validieren
- public-safe JSON-Ableitungen erzeugen
- diese Dateien nach `DevJanis/dashboard` committen oder pushen

Trigger bei Aenderungen an:
- `data/activities_enriched.csv`
- `data/health_daily.csv`
- `data/race_calendar.json`
- `data/season_progress.json`
- `data/latest_analysis_briefing.md`
- `health/hr_zones.json`
- `training_sessions/**/*.json`

### Workflow 2 - oeffentlich: Build and Deploy
Ort: `DevJanis/dashboard`

Aufgabe:
- bei Aenderungen an `data/derived/**` den Astro-Build ausfuehren
- die Website auf GitHub Pages deployen

Trigger:
- Push auf `main` mit Aenderungen in `data/derived/**`
- optional `workflow_dispatch`

## Token- und Rechte-Modell

### Benoetigtes Secret im privaten Repo
`DASHBOARD_REPO_TOKEN`

Rechte:
- Schreibrechte auf `DevJanis/dashboard`
- optional zusaetzliche Rechte zum Triggern eines Workflows, falls notwendig

### Nicht benoetigt
Ein `SOURCE_REPO_TOKEN` im Dashboard-Repo ist in der aktuellen Architektur nicht erforderlich.

Begruendung:
Das Dashboard-Repo liest keine privaten Quelldaten direkt. Es erhaelt ausschliesslich die bereits erzeugten, public-safe Ableitungen aus dem privaten Repo.

## Technische Empfehlung fuer die Datenpipeline

Die Datenaufbereitung soll im privaten Repo erfolgen. Empfohlen ist eine klare Trennung in:

```text
scripts/dashboard/
- build-dashboard-data.ts oder .py
- publish-dashboard-data.ts oder .py
- optionale Hilfsbibliotheken
```

Verantwortlichkeiten:
- Validierung der Quelldaten
- Auswahl des aktuellen Trainingsplans
- Aggregation von Wochen- und Tageswerten
- Erzeugung der JSON-Dateien in stabiler Struktur
- Push der Ableitungen ins Dashboard-Repo

## Akzeptanzkriterien

### Datenarchitektur
- Das oeffentliche Repo enthaelt keine Rohdaten aus dem privaten Repo.
- Das oeffentliche Repo enthaelt ausschliesslich public-safe JSON-Ableitungen.
- Das Frontend konsumiert ausschliesslich `data/derived/*.json`.
- `activities.csv` und `activity_feedback.csv` werden fuer das Dashboard nicht verwendet.

### Workflow
- Ein Workflow im privaten Repo erzeugt die JSON-Ableitungen.
- Ein separater Workflow im Dashboard-Repo baut und deployt die Website.
- Das Dashboard-Repo benoetigt keinen Zugriff auf das private Repo.
- Aenderungen an relevanten Quelldaten koennen automatisiert eine Aktualisierung des Dashboards ausloesen.

### Frontend
- Die Website besitzt mindestens die Seiten Overview, Training, Recovery, Plan und Season.
- Die Startseite zeigt aktuelle Wochen-KPIs, Recovery-Snapshot, naechstes Rennen, naechste Einheit und Insights.
- Die Training-Seite zeigt Wochenvolumen, Hoehenmeter, Long Runs und Intensitaetsverteilung.
- Die Recovery-Seite zeigt Schlaf, HRV, Ruhepuls und Load-vs-Recovery.
- Die Plan-Seite zeigt den aktuellen Trainingsplan in reduzierter, oeffentlicher Form.
- Die Season-Seite zeigt Phasen, Zielrennen und Trainingsbloecke.

### UX und Qualitaet
- Die Website ist mobil und auf Desktop gut nutzbar.
- Die Darstellung ist auf Lesbarkeit und Insights optimiert.
- Die Website bleibt statisch und schnell.
- Charts und KPI-Karten sind konsistent gestaltet.

### Privacy
- Es werden keine unnoetigen sensiblen Inhalte veroeffentlicht.
- Trainingsplaene und Briefings werden nur in geeigneter, gefilterter Form dargestellt.
- Die Daten in `data/derived/` sind explizit fuer die oeffentliche Visualisierung freigegeben.

## Empfohlene naechste Schritte

1. Public Data Contract fuer alle Dateien in `data/derived/` definieren.
2. Workflow 1 im privaten Repo spezifizieren und implementieren.
3. Workflow 2 im Dashboard-Repo fuer Build und Deploy anlegen.
4. MVP-Frontend in Astro mit den Seiten Overview, Training, Recovery, Plan und Season umsetzen.
5. Zunaechst die Kern-Dateien bereitstellen:
   - `metadata.json`
   - `overview.json`
   - `weekly_training.json`
   - `recovery_summary.json`
   - `training_plan_current.json`
   - `season_timeline.json`
   - `insights.json`

## Kurzfassung fuer externe Entwickler

Janis Dashboard ist eine statische Astro-Website, die persoenliche Trail-Running- und Recovery-Daten visualisiert. Alle Rohdaten verbleiben im privaten Repository `DevJanis/Strava-Data`. Dort werden public-safe JSON-Ableitungen erzeugt und in das oeffentliche Repository `DevJanis/dashboard` geschrieben. Das Frontend konsumiert ausschliesslich diese Derived Files und wird anschliessend ueber GitHub Pages gebaut und deployed.
