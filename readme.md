# Angriffsvektoren im Frontend

- https://github.com/RicoKomenda/frontenddays

## Cross-Site-Scripting (XSS)

- Keine Filterung von Eingaben
- Parameter an das Backendsystem weitergeben
- Bei JS-Injection keine Filterung der Ausgabe

### Reflektiert (nicht persistent)

- Benutzereingabe wird direkt von Webanwendung zum Benutzer zurückgesendet
- Kein Persistieren der Webanwendung
- Fehlermeldung, Suchergebnisse etc.

### Beständig

- Benutzereingabe wird zunächst in Webanwendung gespeichert
- Später bei Anfragen aller Benutzer ausgeliefert
- Posts in Benutzerforen, Gästebuch

### DOM-basiert

- Schadcode wird direkt an ein client-seitiges Script übergeben
- Ohne an Server geschickt zu werden

### XSS Beispiele

```
<script>alert('xss');</script>
```

### Verteidigen gegen XSS

- Sanitizing (Benutzereingaben werden von schadhaftem Code bereinigt)
    - Sanitizer Lib schreiben auf Basis des DOM Parsers
        - Script Elemente entfernen
        - schadhafte Attribute entfernen (bspw. src enthält "javascript")
        - Libs:
            - DOMpurify (nutzt DOMParser, AllowList und konfigurierbar)

- Escaping (benutzereingaben werden konvertiert)
    - Escaping von &, <, >, " and '
    - Nutzt PHPs htmlspecialchars funktion
    - &, <, >, ", ', , !, @, $, %, (, ), =, +, {, }, [ und ]
    - Context is key
    - DEFINIERT ein Charset

- Validating
    - Bibliotheken (Yup, Joi, Superstruct)

### Content Security Policy

- Sollte als Antwort vom Webserver kommen
- Erzwingt Trennung des JS Codes vom Inhalt
- Ermöglicht strikte Kontrolle der Herkunft des Codes

### REDOS

- Regular expression denial of service
- Was beinhaltet ein Evil Regex? 
    - Gruppierung mit Wiederholung
    - Innerhalb der Gruppierung:
        - Wiederholung
        - Alternativen mit Überlappung

## Clickjacking

- Den Nutzer glauben lassen, dass er auf etwas klickt, dabei ist es was anderes
- Oft auch auf Phishing Seiten implementiert
- Durch iFrames mittlerweile viel gefährlicher geworden

#### Was dagegen tun?

- X-Frame-Options (DENY, SAMEORIGIN, ALLOW-FROM)
- Besser: CSP (frame-ancestors 'none', 'self', example.com)

## Lokale Datenspeicherung

- Keine sensiblen Daten im Browser speichern
- können über Session Hijacking geklaut werden

## Public Facing Endpoints

- öffentlich leicht erreichbare Endpunkte mit einem Sicherheitsmechanismus einschränken
    - Captcha
    - Honeypots
    - Time-based

## Referrer-Policy

- Benötigt, wenn auf andere Webseite verlinkt
    - Potenziell könnte anhand der URL sensible Daten übergeben werden
    - Session Token können ausgelesen werden

## Autofill & Password Manager

- Es gibt Angreifer-Bibliotheken, die den Autofill des Browsers auslesen
- Webtracking

## Entwicklungsumgebung

- Kostenpflichtige Vendors, die verschiedene CLI/IDE Plugins haben
    - Snyk
    - Checkmarx
    - können oft auch innerhalb der CI-Pipeline verwendet werden (Jenkins, TeamCity, ...)
- Lohnt sich oft nur, wenn man es im größeren Umfang ausrollt und etabliert

## Git-Hooks

- eigene Pre-Commits bauen
- githook-library (github.com/hmrc/security-git-hooks)
- Kostenpflichtige Monitroing & Scan Tools nutzen
    - GitGuardian
    - truffleHog
- OpenSource
    - gitleaks
- Pre-Push Hook für Seurity Maßnahmen

## Supply Chain

### Package Manager Audits

- Eure Package Manager haben eingebaute Audit-Funktionen
- NPM hat sogar fix Tag
    - lieber nicht nutzen, da häufig Fehler auftreten

### SBOM

- Software Bills of Material

### Private Registry

- Packages nicht öffentlich zugänglich
- Caching
- Scoped
- Restricted access
- Eigene Kontrolle
- Empfehlung: Verdaccio (Tool)