# Triadia · come pubblicarla come app (iOS e Android)

Triadia è una **PWA**: un sito che si installa sul telefono come un'app vera (icona, schermo intero, funziona anche offline). È lo stesso percorso usato per Impostore: **GitHub Pages** per metterla online e **PWABuilder** per l'APK Android. Tutto gratis.

Ci sono 4 passi. Il primo (Firebase) serve solo per il multiplayer online: puoi farlo anche dopo.

---

## 1. Attivare il multiplayer online (Firebase, gratis)

Il multiplayer usa Firebase Realtime Database di Google: fa da "postino" tra i due telefoni. Il piano gratuito (Spark) basta e avanza: fino a 100 giocatori connessi insieme.

1. Vai su **console.firebase.google.com** ed entra con un account Google.
2. **Crea un progetto** → nome `triadia` → puoi disattivare Google Analytics → Crea.
3. Nel menu a sinistra: **Build → Realtime Database → Crea database**.
   - Posizione: **Belgio (europe-west1)**.
   - Modalità: **bloccata** (le regole le mettiamo al punto 4).
4. Nella scheda **Regole** del database cancella tutto, incolla il contenuto del file `database.rules.json` e premi **Pubblica**.
5. Torna alla pagina principale del progetto (icona ingranaggio → **Impostazioni progetto**) → in basso "Le tue app" → icona **`</>`** (Web) → nome `Triadia` → **Registra app** (non serve Firebase Hosting).
6. Firebase ti mostra un blocco `const firebaseConfig = { ... }`. Copia i valori dentro il file **`firebase-config.js`**, così:

```js
window.TRIADIA_FIREBASE = {
  apiKey: "...",
  authDomain: "...",
  databaseURL: "https://triadia-xxxx-default-rtdb.europe-west1.firebasedatabase.app",
  projectId: "...",
  appId: "..."
};
```

Controlla che ci sia **`databaseURL`**: se nel blocco non compare, lo trovi in cima alla pagina del Realtime Database.

> Queste chiavi non sono segrete: è normale che stiano nel sito. A proteggere il database ci pensano le regole del punto 4.

## 2. Metterla online con GitHub Pages

I file sono circa 380 (soprattutto le animazioni dei Triadi). Il caricamento dal sito di GitHub accetta al massimo 100 file per volta, quindi conviene **GitHub Desktop** (gratis, per Windows):

1. Su **github.com** crea un nuovo repository pubblico chiamato `triadia`.
2. Installa **GitHub Desktop** (desktop.github.com), accedi e fai **File → Clone repository** → `triadia`.
3. Copia **tutto il contenuto** di questa cartella (non la cartella stessa) dentro la cartella del repository clonato.
4. In GitHub Desktop scrivi un messaggio tipo "Prima versione" → **Commit to main** → **Push origin**.
5. Su github.com, nel repository: **Settings → Pages** → Source: *Deploy from a branch* → Branch: `main` / `(root)` → **Save**.
6. Dopo un minuto o due il gioco è su **`https://TUONOME.github.io/triadia/`**.

## 3. Android: APK con PWABuilder

1. Vai su **pwabuilder.com**, incolla l'indirizzo del punto 2.6 e premi **Start**.
2. **Package for stores → Android → Generate package**.
3. Nello zip trovi:
   - il file **`.apk`**: da mandare agli amici e installare direttamente (bisogna consentire "installa app sconosciute");
   - il file **`.aab`**: serve solo se un giorno vuoi pubblicarla sul Play Store (25 $ una tantum).
4. Nello stesso zip c'è **`assetlinks.json`**: senza, in cima all'app può comparire la barra dell'indirizzo. Per toglierla va messo in `.well-known/` nella radice del dominio. Con un sito `TUONOME.github.io/triadia` la radice è il repository speciale `TUONOME.github.io`: crea quel repository e mettici `.well-known/assetlinks.json` (più un file vuoto `.nojekyll`).

## 4. iPhone

Senza un Mac non si può pubblicare sull'App Store (serve Xcode e 99 $/anno). Però l'installazione come PWA funziona benissimo:

1. Apri l'indirizzo del punto 2.6 con **Safari**.
2. Tasto **Condividi → Aggiungi alla schermata Home**.

Il gioco si apre a schermo intero con la sua icona, come un'app.

---

## Come funziona il multiplayer

- **Battaglia rapida → Amico online.**
- Uno sceglie **Crea una stanza**: imposta arena e regole e riceve un codice di 5 caratteri (si può condividere con WhatsApp dal pulsante).
- L'altro sceglie **Entra con un codice** e lo inserisce. La partita parte da sola.
- Ognuno gioca con le carte della propria collezione, comprese le versioni cromatiche che usa. Chi vince prende 10 gemme e la vittoria conta per le missioni giornaliere.
- A fine partita c'è **Rivincita**: parte quando l'hanno accettata tutti e due.
- Se uno dei due esce o perde la connessione, l'altro riceve l'avviso. Su iPhone, se si chiude l'app o si blocca lo schermo a lungo durante una partita, la connessione cade e la partita viene interrotta.

## Salvataggio online

I progressi di ogni giocatore vengono copiati anche su Firebase, sotto un codice personale di 10 caratteri (Impostazioni → Salvataggio online). Su un telefono nuovo basta aprire Impostazioni → "Recupera i progressi da un altro telefono" e inserire quel codice. Se cambi le regole del database, ricorda di incollare sempre l'intero contenuto di `database.rules.json` (contiene sia le stanze del multiplayer sia i salvataggi).

## Pubblicare un aggiornamento

Sostituisci i file nel repository con quelli nuovi e fai di nuovo Commit e Push. Chi apre il gioco con internet vede subito la nuova versione (la pagina viene sempre presa prima dalla rete); immagini e suoni si aggiornano in sottofondo. Il numero di versione è in Impostazioni: se un telefono restasse indietro, "Forza l'aggiornamento" scarica di nuovo il gioco senza toccare i progressi. **Non toccare `firebase-config.js`** quando aggiorni, altrimenti perdi la configurazione.

## Modalità test

Nell'app gli strumenti di test (sblocca carte, +500 gemme, ecc.) sono nascosti. Per usarli apri l'indirizzo con **`?dev`** in fondo: `https://TUONOME.github.io/triadia/?dev`. I progressi sono salvati sul singolo telefono o browser.
