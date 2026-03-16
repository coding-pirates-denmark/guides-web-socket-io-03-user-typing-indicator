# guides-web-socket-io-03-user-typing-indicator

**Vis når en bruger er ved at skrive (“typing indicator”)**

I denne guide bygger vi en lille realtids‑funktion oven på en almindelig chat‑app:

👉 Vi får chatten til at vise:

**“Anna er ved at skrive…”**

eller

**“Jonas skriver…”**

Det gør chatten meget mere levende – og det lærer dig, hvordan man sender endnu en type Socket.IO‑events.

Selv hvis du ikke har lavet de tidligere guides, kan du sagtens være med.

Vi bygger alt op fra bunden.

## ✅ Hvad du skal bruge

Før du starter, skal du have:

- Node.js installeret
- En kodeeditor (Visual Studio Code anbefales)
- En browser

Hvis du er i tvivl om Node.js:
→ Se guiden [00-getting-started](https://github.com/coding-pirates-denmark/guides-web-socket-io-00-getting-started)

## 📁 1. Opret et nyt projekt

Åbn en terminal og skriv:

```bash
mkdir bruger-skriver
cd bruger-skriver
npm init -y
```

## 📦 2. Installer Express og Socket.IO

```bash
npm install express socket.io
```

- **Express** er vores webserver
- **Socket.IO** giver os live‑kommunikation

## 📂 3. Lav filstrukturen

Din mappe skal se sådan ud:

```
bruger-skriver/
│
├── server.js
└── public/
    └── index.html
```

Opret mappen `public` og filerne `server.js` og `index.html`.

## 🖥️ 4. Skriv serverkoden

Åbn server.js og indsæt:

```javascript
const express = require("express");
const app = express();
const http = require("http").createServer(app);
const io = require("socket.io")(http);

app.use(express.static("public"));

io.on("connection", (socket) => {
  console.log("En bruger forbundet");

  // Modtag besked fra en bruger
  socket.on("chatMessage", (data) => {
    io.emit("chatMessage", data);
  });

  // Modtag "bruger skriver"
  socket.on("typing", (username) => {
    socket.broadcast.emit("typing", username);
  });

  // Modtag "bruger stoppet med at skrive"
  socket.on("stopTyping", () => {
    socket.broadcast.emit("stopTyping");
  });
});

http.listen(3000, () => {
  console.log("Server kører på http://localhost:3000");
});
```

Her sker der tre ting:

1. Når nogen sender en chat‑besked → sendes den til alle
2. Når nogen skriver → sendes “typing” til alle andre
3. Når nogen stopper → sendes “stopTyping” til alle andre

## 🌐 5. Lav chatten i browseren (index.html)

Åbn `public/index.html` og indsæt:

```html
<!DOCTYPE html>
<html lang="da">
<head>
  <meta charset="UTF-8" />
  <title>Chat – Bruger skriver…</title>
  <style>
    body { font-family: sans-serif; }
    #messages { list-style: none; padding: 0; }
    #messages li { margin-bottom: 5px; }
    #typing { color: gray; font-style: italic; }
  </style>
</head>
<body>
  <h1>💬 Chat – Vis “Bruger skriver…”</h1>

  <input id="username" placeholder="Skriv dit brugernavn..." />
  <button id="saveUsername">Gem brugernavn</button>

  <hr />

  <ul id="messages"></ul>

  <p id="typing"></p>

  <form id="form">
    <input id="input" autocomplete="off" placeholder="Skriv en besked..." />
    <button>Send</button>
  </form>

  /socket.io/socket.io.js</script>
  <script>
    const socket = io();

    let username = "";
    const typingText = document.getElementById("typing");
    let typingTimeout;

    // 1. Gem brugernavn
    document.getElementById("saveUsername").onclick = () => {
      const nameInput = document.getElementById("username");
      if (nameInput.value.trim() !== "") {
        username = nameInput.value.trim();
        alert("Brugernavn gemt!");
      }
    };

    // 2. Send beskeder
    form.addEventListener("submit", (e) => {
      e.preventDefault();
      if (!username) {
        alert("Du skal vælge et brugernavn først!");
        return;
      }
      if (input.value) {
        socket.emit("chatMessage", { user: username, text: input.value });
        socket.emit("stopTyping"); // ved send stopper man med at skrive
        input.value = "";
      }
    });

    // 3. Send “bruger skriver…” til serveren når man taster
    input.addEventListener("input", () => {
      socket.emit("typing", username);

      clearTimeout(typingTimeout);
      typingTimeout = setTimeout(() => {
        socket.emit("stopTyping");
      }, 800);
    });

    // 4. Modtag almindelige beskeder
    socket.on("chatMessage", (msg) => {
      const item = document.createElement("li");
      item.textContent = `${msg.user}: ${msg.text}`;
      messages.appendChild(item);
    });

    // 5. Modtag “bruger skriver…”
    socket.on("typing", (user) => {
      typingText.textContent = `${user} skriver...`;
    });

    // 6. Modtag “stop med at skrive”
    socket.on("stopTyping", () => {
      typingText.textContent = "";
    });
  </script>
</body>
</html>
```

## 🚀 6. Start serveren

I terminalen:

```bash
node server.js
```

Du skal se:

```text
Server kører på http://localhost:3000
```

Åbn siden i browseren:

👉 http://localhost:3000

## 🧪 7. Test “Bruger skriver…”

1. Åbn to browser-vinduer
2. Indtast forskellige brugernavne
3. Begynd at skrive i det ene vindue
4. Det andet vindue viser nu:

```text
Anna skriver...
```

Når du stopper med at skrive, forsvinder teksten automatisk.

---

## 🎉 8. Færdig!

Du har nu:

- bygget en chat‑app fra bunden
- tilføjet usernames
- tilføjet “bruger skriver…” indikator
- lært at sende 2 helt nye Socket.IO‑events

Det her er en af de mest klassiske funktioner i alle chat‑apps — og du har lavet den selv!

## ❓ Har du problemer?

Hvis noget driller, så spørg en frivillig, eller opret et issue i dette repo.

God kodning! ⚡
— Coding Pirates Denmark
