
<!DOCTYPE html>
<html lang="de">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>NantAI – Spracheingabe</title>
  <style>
    body {
      margin: 0;
      padding: 24px;
      background: #111318;
      color: #ffffff;
      font-family: Arial, sans-serif;
      text-align: center;
    }
    main {
      max-width: 500px;
      margin: 50px auto;
    }
    button {
      padding: 16px 22px;
      margin: 8px;
      border: 0;
      border-radius: 12px;
      font-size: 18px;
      cursor: pointer;
    }
    #mikrofon { background: #7c5cff; color: white; }
    #uebernehmen { background: #292d36; color: white; }
    textarea {
      box-sizing: border-box;
      width: 100%;
      min-height: 120px;
      padding: 14px;
      border-radius: 12px;
      font: inherit;
    }
    #status { margin: 20px 0; color: #c5c8d0; }
  </style>
</head>
<body>
  <main>
    <h1>🎙️ NantAI Spracheingabe</h1>
    <p>Sprich deine Nachricht auf Deutsch ein.</p>

    <button id="mikrofon" onclick="starten()">🎙️ Sprechen</button>
    <button onclick="stoppen()">⏹ Stopp</button>

    <p id="status">Bereit</p>

    <textarea id="text" placeholder="Dein erkannter Text erscheint hier"></textarea>
    <br>
    <button id="uebernehmen" onclick="uebernehmen()">Text kopieren</button>
  </main>

  <script>
    var erkennung = null;

    function starten() {
      var API = window.SpeechRecognition ||
                window.webkitSpeechRecognition;

      if (!API) {
        document.getElementById('status').textContent =
          'Dieser Browser unterstützt die Spracherkennung nicht.';
        return;
      }

      erkennung = new API();
      erkennung.lang = 'de-DE';
      erkennung.continuous = true;
      erkennung.interimResults = true;

      erkennung.onstart = function() {
        document.getElementById('status').textContent =
          'Ich höre zu …';
      };

      erkennung.onresult = function(event) {
        var ergebnis = '';

        for (var i = 0; i < event.results.length; i++) {
          ergebnis += event.results[i][0].transcript;
        }

        document.getElementById('text').value = ergebnis;
      };

      erkennung.onerror = function(event) {
        document.getElementById('status').textContent =
          'Sprachfehler: ' + event.error;
      };

      erkennung.onend = function() {
        document.getElementById('status').textContent =
          'Aufnahme beendet';
      };

      try {
        erkennung.start();
      } catch (e) {
        document.getElementById('status').textContent =
          'Bitte warte kurz und versuche es erneut.';
      }
    }

    function stoppen() {
      if (erkennung) erkennung.stop();
    }

    function uebernehmen() {
      var feld = document.getElementById('text');
      feld.select();
      feld.setSelectionRange(0, feld.value.length);

      if (feld.value) {
        document.execCommand('copy');
        document.getElementById('status').textContent =
          'Text kopiert. Du kannst ihn in NantAI einfügen.';
      }
    }
  </script>
</body>
</html>
