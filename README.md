# Audio Learning Platform - Standalone Installation

Diese Version ist eine eigenstaendig installierbare statische Webanwendung. Sie hat keine Abhaengigkeit von Static.app. Das Frontend spricht direkt mit deinem Supabase-Projekt; Backend, Datenbank, Auth, Storage und Edge Functions bleiben in Supabase.

## Dateien fuer das Webroot

Lade den kompletten Inhalt dieses Ordners in das Zielverzeichnis deines Webservers hoch:

- index.html
- 200.html
- config.js
- config.example.js
- assets/app.js
- assets/app.css
- weitere Dateien aus assets/, falls vorhanden

Wenn du die App unter der Hauptdomain betreibst, liegen diese Dateien direkt im Webroot, z. B. public_html/.

## Supabase konfigurieren

Oeffne config.js und trage deine Werte ein:

```js
window.__AUDIO_LEARNING_CONFIG__ = {
  SUPABASE_URL: "https://dein-projekt.supabase.co",
  SUPABASE_ANON_KEY: "dein-supabase-anon-key",
  APP_VERSION: "v1.0.0",
  APP_BASE_PATH: "/",
  ROUTER_MODE: "browser"
};
```

Der Anon Key ist der oeffentliche Supabase-Schluessel. Service Role Key, Static.app API Key und andere Secrets gehoeren niemals in diese Datei.

## Installation im Webroot

Beispiel:

```txt
public_html/
  index.html
  200.html
  config.js
  assets/
    app.js
    app.css
```

Setze in config.js:

```js
APP_BASE_PATH: "/"
```

## Installation unter /audio/

Beispiel:

```txt
public_html/audio/
  index.html
  200.html
  config.js
  assets/
    app.js
    app.css
```

Setze in config.js:

```js
APP_BASE_PATH: "/audio"
```

Die mitgelieferte index.html erkennt den Ordner meistens automatisch. Den Wert trotzdem explizit zu setzen ist fuer produktive Installationen sauberer.

## Server-Fallback fuer React Router

Die App verwendet im Standardmodus Browser-Routing. Der Webserver muss unbekannte App-Routen auf index.html oder 200.html ausliefern.

Apache .htaccess-Beispiel im App-Ordner:

```apache
RewriteEngine On
RewriteBase /audio/
RewriteRule ^index\.html$ - [L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . /audio/index.html [L]
```

Nginx-Beispiel:

```nginx
location /audio/ {
  try_files $uri $uri/ /audio/index.html;
}
```

Wenn dein Server keine Rewrite-Regeln erlaubt, setze in config.js:

```js
ROUTER_MODE: "hash"
```

Dann nutzt die App URLs wie /audio/#/admin und benoetigt keinen Fallback fuer tiefe Routen.

## Serveranforderungen

- Statischer Webserver fuer HTML, JS, CSS und Assets
- HTTPS empfohlen und fuer moderne Browserfunktionen wie crypto.randomUUID wichtig
- Kein PHP, Node.js oder Datenbankserver auf dem Webserver erforderlich
- Supabase-Projekt mit ausgefuehrter Migration
- Deployte Supabase Edge Functions
- Private Supabase Storage Buckets gemaess Projekt-README

## Funktionalitaet

Nach korrekter Supabase-Konfiguration funktionieren:

- Schuelerbereich ohne Login
- Level LE / M / R aus Supabase
- Units und Audios aus Supabase
- Audio-Wiedergabe ueber Signed URLs
- maximal 2 Wiedergaben pro Audio und anonymem Browser-/Geraete-Identifier
- Admin-Login ueber Supabase Auth
- Level-, Unit- und Audio-Verwaltung
- Playcount-Speicherung und Reset

Ohne echte Supabase-Werte zeigt die App einen Konfigurationshinweis und kann keine Daten laden.
