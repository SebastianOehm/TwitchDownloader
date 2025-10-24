> [!WARNING]
> Die deutsche README von TwitchDownloaderWPF k�nnte veraltet sein.
> F�r die aktuellste Version schaue bitte in die [englische ](README.md) README.

# TwitchDownloaderWPF

Eine Windows-WPF-Desktopanwendung, die die Kernfunktionen in verschiedene Komfortfunktionen einbettet.

## Inhaltsverzeichnis

- [TwitchDownloaderWPF](#twitchdownloaderwpf)
  - [Inhaltsverzeichnis](#inhaltsverzeichnis)
  - [Verwendung](#verwendung)
    - [VOD-Download](#vod-download)
    - [Clip-Download](#clip-download)
    - [Chat-Download](#chat-download)
    - [Chat-Aktualisierung](#chat-aktualisierung)
    - [Chat-Rendering](#chat-rendering)
      - [Allgemeines](#allgemeines)
      - [Rendering](#rendering)
      - [Skalierung](#skalierung)
      - [Kodierung](#kodierung)
      - [FFmpeg](#ffmpeg)
      - [Rendergeschwindigkeit optimieren](#rendergeschwindigkeit-optimieren)
    - [Aufgabenwarteschlange](#aufgabenwarteschlange)
    - [Einstellungen](#einstellungen)
  - [Fehlerbehebung](#fehlerbehebung)
    - [Probleme ohne Fehlermeldung](#probleme-ohne-fehlermeldung)
    - [H�ufige Fehler](#h�ufige-fehler)
    - [Seltene Fehler](#seltene-fehler)
  - [Lokalisierung](#lokalisierung)
  - [Designs](#designs)

---

## Verwendung

Die meisten Seiten enthalten auf der linken Seite des Fensters einen Informationsbereich, der das Vorschaubild (falls vorhanden), den Streamer, das Erstellungsdatum und den Titel des VOD/Clips anzeigt. In der Mitte des Fensters befinden sich die Einstellungen f�r den Auftrag, auf der rechten Seite befindet sich ein Bereich zur Protokollierung von Ereignissen.

### VOD-Download

L�dt ein VOD oder Highlight von Twitch herunter.

![Abbildung 1.1](Images/vodExample.png)
<br><sup>*Abbildung 1.1*</sup>

Gib eine g�ltige URL oder ID zu einem VOD oder Highlight ein, um zu beginnen. Falls das VOD oder Highlight privat oder nur f�r Abonnenten sichtbar ist, wird ein OAuth-Token eines Kontos ben�tigt, das Zugriff auf das Video hat. Danach werden die Downloadoptionen freigeschaltet und du kannst den Auftrag anpassen.

**Qualit�t**: W�hlt die Downloadqualit�t und zeigt eine gesch�tzte Dateigr��e an. Gelegentlich bezeichnet Twitch die h�chste Qualit�t als �Source� statt der �blichen Aufl�sungsschreibweise (z. B. 1080p60 in Abbildung 1.1).

**Trim-Modus**: Legt fest, wie der Videoschnitt gehandhabt wird. Videos, die mit dem exakten Schnitt zugeschnitten werden, k�nnen selten innerhalb der ersten/letzten Sekunden Bild- oder Tonstottern aufweisen. Der sichere Schnitt garantiert ruckelfreie Wiedergabe, kann aber ein etwas l�ngeres Video ergeben.

**Trimmen**: Setzt Start- und Endzeit f�r den Videoschnitt im Format \[Stunden\] \[Minuten\] \[Sekunden\]. Durch das Trimmen verringert sich die Gesamtgr��e des Downloads.

**Download-Threads**: Anzahl der parallelen Download-Threads, die gestartet werden.

**OAuth**: Das Autorisierungstoken, mit dem sub-only und private VODs oder Highlights heruntergeladen werden k�nnen. Es wird von uns und Twitch ben�tigt, um unbefugte Downloads kostenpflichtiger oder privater Inhalte zu verhindern. Eine Anleitung zum Abrufen deines OAuth-Tokens findest du im folgenden Video: [https://youtu.be/1MBsUoFGuls](https://www.youtube.com/watch?v=1MBsUoFGuls). <ins>**TEILE DEIN OAUTH-TOKEN MIT NIEMANDEM!**</ins>

**Download**: Startet den Downloadauftrag. �ffnest du stattdessen das Dropdown, kannst du ihn mit der Option *Zur Warteschlange hinzuf�gen* an die [Aufgabenwarteschlange](#aufgabenwarteschlange) senden. Die aktuellen Download-Einstellungen werden in beiden F�llen verwendet.

### Clip-Download

L�dt einen Clip von Twitch herunter.

![Abbildung 2.1](Images/clipExample.png)
<br><sup>*Abbildung 2.1*</sup>

Gib eine g�ltige URL oder ID zu einem Clip ein, um zu beginnen. Danach werden die Downloadoptionen freigeschaltet und du kannst den Auftrag anpassen.

**Qualit�t**: W�hlt die Clipqualit�t vor dem Download.

**Metadaten kodieren**: Kodiert Clip-Metadaten wie Ausstrahlungsdatum und Clip-ID mithilfe von FFmpeg in die endg�ltige MP4-Datei.

**Download**: Startet den Downloadauftrag. �ffnest du stattdessen das Dropdown, kannst du ihn mit der Option *Zur Warteschlange hinzuf�gen* an die [Aufgabenwarteschlange](#aufgabenwarteschlange) senden. Die aktuellen Download-Einstellungen werden in beiden F�llen verwendet.

### Chat-Download

L�dt den Chat eines VOD, Highlights oder Clips herunter.

![Abbildung 3.1](Images/chatdownload1Example.png)
<br><sup>*Abbildung 3.1*</sup>

![Abbildung 3.2](Images/chatdownload2Example.png)
<br><sup>*Abbildung 3.2*</sup>

Gib eine g�ltige URL oder ID zu einem VOD, Highlight oder Clip ein, um zu beginnen. Danach werden die Downloadoptionen freigeschaltet und du kannst den Auftrag anpassen. Wenn das VOD oder Highlight nur f�r Abonnenten sichtbar oder privat ist, kann der Chat nicht heruntergeladen werden. Das ist eine Einschr�nkung der Twitch-API, nicht von TwitchDownloader.

**Downloadformat**: Dateiformat, in dem der heruntergeladene Chat gespeichert wird.

- `JSON` gibt eine umfangreiche Version des Chats aus, die zum Aktualisieren und Rendern verwendet werden kann.
- `Text` gibt eine reine Textversion des Chats aus, ideal zum Mitlesen beim Anschauen eines VODs.
- `HTML` erzeugt eine lokale Webseite, die das Erscheinungsbild der Twitch-Webseite emuliert.

**Komprimierung** (nur JSON): Komprimiert die ausgegebene JSON-Datei mithilfe des GZip-Standards und reduziert so die Dateigr��e um 40�90 %. Nicht empfohlen, wenn du Chat-Dateien manuell bearbeiten m�chtest, ohne den [Chat-Updater](#chat-aktualisierung) zu verwenden.

**Zeitstempelformat** (nur Text): �ndert das Zeitstempelformat im Text-Download. Zur Auswahl stehen `UTC`, relativ zum Beginn des Videos (`Relative`) und `None`.

**Trimmen**: Setzt Start- und Endzeit f�r den Chat-Download im Format \[Stunden\] \[Minuten\] \[Sekunden\]. Durch das Trimmen verringert sich die Gesamtgr��e des Downloads.

**Bilder einbetten** (nur JSON & HTML): L�dt die Emotes und Abzeichen des Streamers herunter und speichert sie im Chat. Die Dateigr��e steigt dadurch stark an.

**Emotes von Drittanbietern** (nur JSON & HTML): L�dt zus�tzlich Emotes der angegebenen Drittanbieter herunter und speichert sie im Chat. Wenn der Streamer bei einem Anbieter nicht registriert ist, wird dieser �bersprungen.

**Download-Threads**: Anzahl der parallelen Download-Threads. Bei manchen Internetverbindungen erlauben die Twitch-Server nicht mehr als einen Chat-Download-Thread.

**Download**: Startet den Downloadauftrag. �ffnest du stattdessen das Dropdown, kannst du ihn mit der Option *Zur Warteschlange hinzuf�gen* an die [Aufgabenwarteschlange](#aufgabenwarteschlange) senden. Die aktuellen Download-Einstellungen werden in beiden F�llen verwendet.

### Chat-Aktualisierung

Aktualisiert eingebettete Emotes, Abzeichen, Bits und Zuschnitte eines JSON-Chatdownloads und/oder konvertiert einen JSON-Chat in ein anderes Format.

![Abbildung 4.1](Images/chatupdateExample.png)
<br><sup>*Abbildung 4.1*</sup>

Klicke auf **Durchsuchen** und navigiere zu einem zuvor heruntergeladenen JSON-Chat, um zu beginnen. Danach werden die Aktualisierungsoptionen freigeschaltet und du kannst den Auftrag anpassen. Wenn das Quellvideo des Chats noch existiert, werden seine Informationen im Informationsbereich geladen.

**Downloadformat**: Dateiformat, in dem der aktualisierte Chat gespeichert wird.

- `JSON` gibt eine umfangreiche Version des Chats aus, die zum Aktualisieren und Rendern verwendet werden kann.
- `Text` gibt eine reine Textversion des Chats aus, ideal zum Mitlesen beim Anschauen eines VODs.
- `HTML` erzeugt eine lokale Webseite, die das Erscheinungsbild der Twitch-Webseite emuliert.

**Komprimierung** (nur JSON): Komprimiert die ausgegebene JSON-Datei mithilfe des GZip-Standards und reduziert so die Dateigr��e um 40�90 %. Nicht empfohlen, wenn du Chat-Dateien manuell bearbeiten m�chtest, ohne den [Chat-Updater](#chat-aktualisierung) zu verwenden.

**Zeitstempelformat** (nur Text): �ndert das Zeitstempelformat im Text-Chat. Zur Auswahl stehen `UTC`, relativ zum Beginn des Videos (`Relative`) und `None`.

**Trimmen**: Setzt Start- und Endzeit f�r den aktualisierten Chat im Format \[Stunden\] \[Minuten\] \[Sekunden\]. Wird der Zeitraum erweitert, versucht der Updater Kommentare abzurufen, die im urspr�nglichen Download nicht enthalten waren. Wird der Zeitraum verk�rzt, werden keine Kommentare entfernt.

**Fehlendes einbetten** (nur JSON & HTML): L�dt alle Emotes oder Abzeichen nach, die im urspr�nglichen JSON-Chat nicht enthalten waren. Bereits vorhandene Emotes oder Abzeichen werden **nicht** �berschrieben.

**Einbettungen ersetzen** (nur JSON & HTML): Entfernt alle vorhandenen Emotes und Abzeichen aus dem JSON-Chat und l�dt die aktuellen Emotes und Abzeichen herunter.

**Emotes von Drittanbietern** (nur JSON & HTML): L�dt zus�tzlich Emotes der angegebenen Drittanbieter herunter und speichert sie im Chat. Wenn der Streamer bei einem Anbieter nicht registriert ist, wird dieser �bersprungen.

**Aktualisieren**: Startet den Aktualisierungsauftrag. �ffnest du stattdessen das Dropdown, kannst du ihn mit der Option *Zur Warteschlange hinzuf�gen* an die [Aufgabenwarteschlange](#aufgabenwarteschlange) senden. Die aktuellen Aktualisierungseinstellungen werden in beiden F�llen verwendet.

### Chat-Rendering

Rendert einen Chat im JSON-Format als Video.

![Abbildung 5.1](Images/chatrender1Example.png)
<br><sup>*Abbildung 5.1*</sup>

![Abbildung 5.2](Images/chatrender2Example.png)
<br><sup>*Abbildung 5.2*</sup>

![Abbildung 5.3](Images/chatrender3Example.png)
<br><sup>*Abbildung 5.3*</sup>

![Abbildung 5.4](Images/chatrender4Example.png)
<br><sup>*Abbildung 5.4*</sup>

![Abbildung 5.5](Images/chatrender5Example.png)
<br><sup>*Abbildung 5.5*</sup>

![Abbildung 5.6](Images/rangeExample.png)
<br><sup>*Abbildung 5.6*</sup>

Klicke auf **Durchsuchen** und navigiere zu einem zuvor heruntergeladenen JSON-Chat, um zu beginnen. Anschlie�end kannst du die Renderoptionen anpassen.

**Rendern**: Startet den Renderauftrag. �ffnest du stattdessen das Dropdown, kannst du ihn mit der Option *Zur Warteschlange hinzuf�gen* an die [Aufgabenwarteschlange](#aufgabenwarteschlange) senden. Alternativ kannst du mit der Option *Teilrendering* einen kleineren Abschnitt des Chats rendern, siehe Abbildung 5.6. Die aktuellen Render-Einstellungen werden in allen F�llen verwendet.

#### <ins>Allgemeines</ins>

**Breite**: Breite der gerenderten Chat-Ausgabe. Muss eine gerade Zahl sein.

**H�he**: H�he der gerenderten Chat-Ausgabe. Muss eine gerade Zahl sein.

**Schriftart**: Schriftart, die im gerenderten Chat verwendet wird. Die Twitch-Webseite nutzt *Inter*, das als *Inter Embedded* mit TwitchDownloader ausgeliefert wird.

**Schriftgr��e**: Gr��e der Schrift.

**Schriftfarbe**: Farbe der Nachrichten.

**Hintergrundfarbe**: Hintergrundfarbe der Chat-Ausgabe.

**Alternative Hintergrundfarbe**: Alternative Hintergrundfarbe f�r Nachrichten. Erfordert aktivierte *Alternative Hintergr�nde*.

#### <ins>Rendering</ins>

**Konturen**: F�gt Benutzernamen und Nachrichten einen d�nnen schwarzen Umriss hinzu.

**Zeitstempel**: Zeichnet neben Chat-Nachrichten einen Zeitstempel relativ zum Start des Videos ein.

**Abonnenten-Nachrichten**: Zeigt Abonnements, Re-Subs und Geschenkabos an. Wenn deaktiviert, werden sie aus dem Render entfernt.

**Chat-Abzeichen**: Zeigt Chat-Abzeichen neben den Benutzernamen an.

**Aktualisierungsrate**: Zeit in Sekunden zwischen den n�chsten Kommentarbl�cken. Niedrigere Werte machen den Chat leichter lesbar, erh�hen aber die Renderzeit minimal.

**Dispersion**: Im November 2022 f�hrte eine �nderung der Twitch-API dazu, dass Chatnachrichten nur noch sekundengenau heruntergeladen werden. Diese Option nutzt zus�tzliche Metadaten, um zu versuchen, Nachrichten zu ihrem tats�chlichen Sendezeitpunkt wiederherzustellen. Dies kann eine andere Kommentarreihenfolge zur Folge haben. Erfordert eine Aktualisierungsrate unter 1,0 f�r effektive Ergebnisse.

**Alternative Hintergr�nde**: Wechselt die Hintergrundfarbe jeder zweiten Chat-Nachricht, um sie besser zu unterscheiden.

**Lesbarkeit der Benutzernamen erh�hen**: Erh�ht den Kontrast zwischen Benutzernamen und Hintergrund �hnlich der Option _Readable Colors_ im Twitch-Chat. Wenn Render-Konturen aktiviert sind, erh�ht diese Option den Kontrast der Benutzernamen gegen�ber dem Umriss statt gegen�ber dem Hintergrund.

**BTTV-Emotes**: Aktiviert BTTV-Emotes im Render.

**FFZ-Emotes**: Aktiviert FFZ-Emotes im Render.

**7TV-Emotes**: Aktiviert 7TV-Emotes im Render.

**Offline**: Rendert, ohne Netzwerkanfragen zu stellen; verwendet nur Informationen und Bilder, die im Chat-JSON eingebettet sind.

**Benutzer-Avatare**: Aktiviert Benutzer-Avatare im Render.

**Chat-Abzeichen-Filter**: Rendert die angegebenen Abzeichen nicht. In Abbildung 5.2 werden z. B. die Abzeichen *Kein Audio* und *Kein Video* nie angezeigt.

**Ignorierte Nutzer**: Kommagetrennte, nicht zwischen Gro�- und Kleinschreibung unterscheidende Liste von Nutzern, die aus dem Render entfernt werden. In Abbildung 5.2 werden etwa Streamlabs, StreamElements und Nightbot entfernt.

**Liste verbotener W�rter**: Kommagetrennte, nicht zwischen Gro�- und Kleinschreibung unterscheidende Liste von W�rtern, deren Auftreten Nachrichten aus dem Render entfernt. In Abbildung 5.2 wird jede Nachricht entfernt, die `" pog "`, `"[pOg+"` oder `"/POg9"` enth�lt. Eine Nachricht, die `" poggers "` enth�lt, wird nicht entfernt.

**Emoji-Anbieter**: Stil der im Render verwendeten Emojis. Derzeit werden _Twemoji_ von Twitter, _Noto Color_ von Google und die Emojis deines Systems (_None_) unterst�tzt.

#### <ins>Skalierung</ins>

**Emote-Skalierung**: Skalierung der Emotes.

**Abzeichen-Skalierung**: Skalierung der Abzeichen.

**Emoji-Skalierung**: Skalierung der Emojis.

**Avatar-Skalierung**: Skalierung der Avatare.

**Kontur-Skalierung**: Dicke der Render-Konturen.

**Vertikale Abstands-Skalierung**: Skalierung des Abstands zwischen Nachrichten.

**Seitliche Abstands-Skalierung**: Skalierung der horizontalen Abst�nde.

**Abschnittsh�hen-Skalierung**: Skalierung der H�he einer Textzeile.

**Wortabstands-Skalierung**: Skalierung des Abstandes zwischen W�rtern.

**Emote-Abstands-Skalierung**: Skalierung des Abstandes zwischen Emotes und anderen Emotes oder W�rtern.

**Markierungsbalken-Skalierung**: Skalierung der Breite der Seitenleiste f�r hervorgehobene/Abonnenten-Nachrichten.

**Markierungseinzug-Skalierung**: Skalierung des Einzugs von hervorgehobenen/Abonnenten-Nachrichten.

#### <ins>Kodierung</ins>

**Dateiformat**: Dateiformat der gerenderten Ausgabe.

**Codec**: Codec der gerenderten Ausgabe.

**Bildrate**: Bildrate der gerenderten Ausgabe.

**Maske erzeugen**: Erstellt eine zweite Ausgabedatei, die eine Schwarz-Wei�-Maske des Textes und der Bilder enth�lt. Der Alpha-Kanal der Hintergrundfarbe MUSS kleiner als 255 sein.

**Sch�rfung**: Wendet einen Sch�rfefilter auf das gerenderte Video an. Erh�ht Renderzeit und Dateigr��e leicht. Funktioniert am besten mit einer _Schriftgr��e_ von 24 oder gr��er.

#### <ins>FFmpeg</ins>

**Warnung: Das �ndern der FFmpeg-Argumente kann Pipe-Fehler verursachen!**

**Eingabeargumente**: Argumente, mit denen die gerenderte Eingabe an FFmpeg �bergeben wird.

**Ausgabeargumente**: Argumente, mit denen die codierte Ausgabe von FFmpeg gesteuert wird.

**Auf Standardeinstellungen zur�cksetzen**: Setzt beide FFmpeg-Argumentlisten auf ihre Standardwerte zur�ck.

#### <ins>Rendergeschwindigkeit optimieren</ins>

Wenn sich Rendergeschwindigkeiten zu langsam anf�hlen, versuche Folgendes:

| Gro�er Einfluss            | Mittlerer Einfluss              | Kleiner Einfluss                              |
|----------------------------|---------------------------------|------------------------------------------------|
| Renderbreite reduzieren    | BTTV-, FFZ-, 7TV-Emotes deaktivieren | Dispersion deaktivieren, falls Aktualisierungsrate < 1,0 |
| Renderh�he reduzieren      | Aktualisierungsrate erh�hen     | Abonnenten-Nachrichten deaktivieren           |
| Bildrate reduzieren        | Auf System-Emojis wechseln      | Konturen deaktivieren                         |
| Maske erzeugen deaktivieren|                                 | Alternative Hintergr�nde deaktivieren         |
| Bildsch�rfung deaktivieren |                                 | Benutzer-Avatare deaktivieren                 |
| Codec auf H.264 umstellen  |                                 |                                                |

### Aufgabenwarteschlange

Erstelle und verwalte mehrere Auftr�ge.

![Abbildung 6.1](Images/taskqueueExample.png)
<br><sup>*Abbildung 6.1*</sup>

![Abbildung 6.2](Images/massurlExample.png)
<br><sup>*Abbildung 6.2*</sup>

![Abbildung 6.3](Images/massvodExample.png)
<br><sup>*Abbildung 6.3*</sup>

![Abbildung 6.4](Images/massclipExample.png)
<br><sup>*Abbildung 6.4*</sup>

![Abbildung 6.5](Images/enqueueExample.png)
<br><sup>*Abbildung 6.5*</sup>

Die Aufgabenwarteschlange erm�glicht es, viele Auftr�ge nacheinander oder parallel auszuf�hren. Jeder Auftrag der anderen f�nf Seiten kann �ber die jeweiligen *Zur Warteschlange hinzuf�gen*-Buttons an die Aufgabenwarteschlange gesendet werden, siehe Abbildung 6.5.

Auf der Seite der Aufgabenwarteschlange gibt es vier Begrenzungen:

**VOD-Downloads**: Anzahl der VOD-/Highlight-Downloads, die gleichzeitig stattfinden d�rfen.

**Clip-Downloads**: Anzahl der Clip-Downloads, die gleichzeitig stattfinden d�rfen.

**Chat-Downloads**: Anzahl der Chat-Downloads/-Aktualisierungen, die gleichzeitig stattfinden d�rfen.

**Chat-Renderings**: Anzahl der Chat-Renderings, die gleichzeitig stattfinden d�rfen.

Die Aufgabenwarteschlange bietet au�erdem drei Arten von Massendownloads:

**URL-Liste**: Eine Liste von VOD-, Highlight- oder Clip-URLs, die alle mit denselben Warteschlangeneinstellungen verarbeitet werden. Siehe Abbildung 6.2 und Abbildung 6.5.

**VOD-Suche**: Ein Fenster, das alle VODs eines Streamers durchsucht und mit denselben Warteschlangeneinstellungen verarbeitet. Siehe Abbildung 6.3 und Abbildung 6.5.

**Clip-Suche**: Ein Fenster, das alle Clips eines Streamers durchsucht und mit denselben Warteschlangeneinstellungen verarbeitet. Siehe Abbildung 6.3 und Abbildung 6.5.

### Einstellungen

Steuere das Verhalten der Anwendung.

![Abbildung 7.1](Images/settingsExample.png)
<br><sup>*Abbildung 7.1*</sup>

**Cache-Ordner**: Verzeichnis, in dem tempor�re Arbeitsdateien gespeichert werden. Dazu geh�ren VOD-Downloads, Emotes, Abzeichen und Cheermotes.

- **Leeren**: L�scht alle Cache-Dateien von TwitchDownloader. Nicht empfohlen, es sei denn, die Anwendung verh�lt sich nicht korrekt.
- **Durchsuchen**: W�hle einen neuen Ordner f�r den tempor�ren Cache. Vorhandene Cache-Dateien werden nicht �bertragen.

**Spenden-Schaltfl�che ausblenden**: Blendet die Spenden-Schaltfl�che aus.

**Zeitformat**: Legt fest, wie Zeit in der Benutzeroberfl�che und in Dateinamen-Vorlagen verarbeitet wird.

**Ausf�hrliche Fehlermeldungen**: Aktiviert beschreibende Popups, wenn ein Fehler auftritt.

**Design**: Anwendungsdesign. Siehe [Designs](#designs) f�r weitere Details.

**Sprache**: Anwendungssprache. Siehe [Lokalisierung](#lokalisierung) f�r weitere Details.

**Maximale Thread-Bandbreite**: Maximale Bandbreite, die neue Download-Threads verwenden d�rfen, in Kibibyte pro Sekunde (KiB/s).

**Protokollierungsstufen**: Aktiviert verschiedene Protokollierungsstufen, um das Debuggen zu erleichtern.

**Dateinamen-Vorlagen f�r Downloads**: Vorlagen zur Erstellung der Standarddateinamen beim Download.

**Standardeinstellungen wiederherstellen**: Stellt alle Standardeinstellungen wieder her, einschlie�lich der gemerkten Einstellungen der einzelnen Seiten. Ein Neustart der Anwendung ist erforderlich, um die �nderungen anzuwenden.

**Speichern**: Speichert die aktuellen �nderungen im Einstellungsfenster und schlie�t das Fenster.

**Abbrechen**: Verwirft die aktuellen �nderungen im Einstellungsfenster und schlie�t das Fenster.

## Fehlerbehebung

### Probleme ohne Fehlermeldung

Einige Probleme f�hren nicht zu einer Fehlermeldung. Diese sollten als [Issue auf GitHub](https://github.com/lay295/TwitchDownloader/issues) gemeldet werden, inklusive der verwendeten Eingaben. Beispiele f�r solche Probleme:

- Videodownloads bleiben l�nger als 5 Minuten bei `Downloading: 99%` h�ngen
- Chat-Renderings aktualisieren ihren Status l�nger als 10 Sekunden nicht
- Chat-Renderings lassen Kommentare aus
- Ein UI-Element reagiert nicht auf die �nderung des Anwendungsdesigns
- Das �ndern einer Option � etwa Emotes einbetten � hat keinen Effekt

### H�ufige Fehler

Ein Fehler gilt als �h�ufig�, wenn er vor oder unmittelbar nach dem Start eines Auftrags auftritt. H�ufige Fehler verf�gen �ber eine verst�ndliche Fehlermeldung, manchmal begleitet von einem Popup, das erkl�rt, was schiefgelaufen ist. Beispiele f�r h�ufige Fehler:

- Thumbnail konnte nicht gefunden werden
  - Das VOD ist entweder abgelaufen oder l�uft aktuell
- Video-/Clip-Informationen konnten nicht abgerufen werden
  - Das verlinkte VOD/der Clip ist ung�ltig, wurde entfernt oder ist privat/nur f�r Abonnenten und es wurde kein g�ltiges OAuth bereitgestellt
- Eingaben k�nnen nicht analysiert werden
  - Eine oder mehrere Render-Eingaben sind ung�ltig, siehe Protokoll

### Seltene Fehler

Ein seltener Fehler �u�ert sich durch ein Popup mit dem Titel �Fatal Error� oder eine schwer verst�ndliche Fehlermeldung. Diese sollten als [Issue auf GitHub](https://github.com/lay295/TwitchDownloader/issues) gemeldet werden, inklusive der Eingaben, die zum Fehler gef�hrt haben. Beispiele f�r seltene Fehler:

- Fehler beim Konvertieren des Wertes �XXX� in den Typ �XXX�. Pfad �XXX�, Zeile #, Position #.
- Kann auf den untergeordneten Wert von Newtonsoft.Json.Linq.JValue nicht zugreifen.
- Antwortstatuscode meldet keinen Erfolg: 404 (Not Found).
- Die Pipe wurde beendet.
  - Es gab ein Problem mit FFmpeg. Setze die Argumente auf Standard zur�ck und versuche es erneut. Falls das nicht hilft, erstelle ein neues Issue auf GitHub.

Um uns bei der Fehleranalyse zu helfen, aktiviere bitte `Ausf�hrliche Fehlermeldungen` in den [Einstellungen](#einstellungen) und mache einen Screenshot des resultierenden Popups mit dem Titel �Verbose Error Output�.

## Lokalisierung

Die WPF-Anwendung ist dank gro�z�giger Mitglieder der Community in mehreren Sprachen verf�gbar.

Wenn du dir eine �bersetzung zutraust und TwitchDownloaderWPF in deiner Sprache nicht verf�gbar oder unvollst�ndig ist, freuen wir uns �ber deine Hilfe!

Wenn du Unterst�tzung beim Einstieg ben�tigst, wirf einen Blick auf den Commit [53245be1fe55768d525905e81cc2cd0c12469b03](https://github.com/lay295/TwitchDownloader/commit/53245be1fe55768d525905e81cc2cd0c12469b03), schau dir [AvailableCultures.cs](Services/AvailableCultures.cs) an, lies den urspr�nglichen [Lokalisierungs-Thread](https://github.com/lay295/TwitchDownloader/issues/445) oder erstelle ein [neues Issue](https://github.com/lay295/TwitchDownloader/issues/new/choose), um nach Hilfe zu fragen.

Wenn du unsicher bist, wie du eine Zeichenfolge oder einen Teil davon �bersetzen sollst, kann sie auf Englisch bleiben.

## Designs

Die Anwendung enth�lt ein helles und ein dunkles Design sowie eine Option, automatisch zwischen beiden zu wechseln � basierend auf dem aktuellen Windows-App-Design. Derzeit werden das helle und das dunkle Design bei jedem Start �berschrieben, um sicherzustellen, dass sie immer aktuell sind.

Um dein eigenes Design zu erstellen, dupliziere eines der enthaltenen Designs und �ffne es mit deinem bevorzugten Texteditor.

Die WPF-Anwendung nutzt einige Elemente des Pakets _HandyControl_, das nur eingeschr�nkte Design-Anpassung bietet. Die meisten HandyControl-Elemente unterst�tzen lediglich Text- und Rahmenfarben, weshalb die Hintergr�nde entweder hell oder dunkel sein m�ssen. Steuere dies mit dem booleschen Schl�ssel `DarkHandyControl`.

Um dunkle Titelleisten f�r dein Design zu aktivieren, setze den booleschen Schl�ssel `DarkTitleBar` auf true.

Die �brigen `SolidColorBrush`-Schl�ssel steuern die Farben der Anwendung und ihrer Elemente, z. B. Hintergrund, Text- und Rahmenfarben. Die �Inner�-Schl�ssel dienen dazu, verschachtelten Elementen mehr Tiefe zu verleihen, wie im folgenden Diagramm gezeigt:

```
/---------------------------[-][#][x]
|           AppBackground           |
| /-------------------------------\ |
| |     AppElementBackground      | |
| | /---------------------------\ | |
| | | AppInnerElementBackground | | |
| | |                           | | |
| | \---------------------------/ | |
| |                               | |
| \-------------------------------/ |
|                                   |
\-----------------------------------/
```

In diesem Fall wird `AppElementBackground` von einem Frame verwendet, w�hrend `AppInnerElementBackground` f�r einen beschrifteten Rahmen, einen leeren Bildhintergrund oder �hnliches genutzt wird.

**Wichtige Hinweise**

- `Dark.xaml` und `Light.xaml` werden beim Start der Anwendung immer �berschrieben.
- Dateinamen werden ohne Ber�cksichtigung der Gro�-/Kleinschreibung gelesen; `Dark.xaml` und `dark.xaml` k�nnen daher nicht unterschieden werden.
- Vergiss nicht, den Autorenkommentar am Anfang der Design-Datei anzupassen!
- Dein Design kann in das Programm aufgenommen werden! Lade es in deinen eigenen Fork von TwitchDownloader hoch und [erstelle einen Pull Request](https://github.com/lay295/TwitchDownloader/pulls).

Offline-Anweisungen zum Erstellen eigener Designs findest du in [`Themes/README.txt`](Themes/README.txt); diese Datei wird bei jedem Start neu erstellt.