# CosaGuardo - Appunti sull'app

## 1. Obiettivo della sessione

- Costruire "CosaGuardo": app che dice che film guardare stasera in base a filtri/umore, alle piattaforme streaming dell'utente (es. Netflix + Disney, regione IT) e alle sue liste.
- Obiettivo finale: pubblicazione sugli store (iOS/Android) con guadagni da pubblicità (AdMob) + Premium. Per ora prototipo in un unico file HTML; la versione vera sarà Expo (React Native) + Supabase (Postgres).

## 2. Passi principali completati

Infrastruttura
- Repo pubblico: github.com/s1m03ll3/cosa-guardo, branch `main`. Sito di prova: https://s1m03ll3.github.io/cosa-guardo/ (Pages attivato a mano da Simona; si prova da telefono).
- App: `index.html` unico (HTML+CSS+JS vanilla). "Finto database" in localStorage (chiave `cosaguardo_db_v3`) con tabelle che rispecchiano lo schema Supabase futuro: users, profiles, lists, list_members, list_items, list_invites, seen, ratings, dismissed, swipes, quiz, contacts, duels, session.
- Dati da TMDB API v3 (discover con `with_watch_providers` e `watch_region=IT`, search movie/person/keyword, dettagli con watch/providers e video, `/find` per id IMDb); immagini da image.tmdb.org. Senza chiave parte in modalità demo (dati finti). La chiave TMDB si inserisce da Profilo → 🛠 Sviluppatore.
- Ambiente cloud "cosa-guardo" con accesso a api.themoviedb.org e image.tmdb.org (necessario per i test con dati reali). Test con Playwright (Chromium headless) in modalità demo e reale.
- Subagent "punti-principali" in `.claude/agents/punti-principali.md` (commit 4993184).

Cronologia
1. Prototipo iniziale: filtri, piattaforme, scelta casuale; collegamento a TMDB; modalità demo e messaggi di diagnosi quando TMDB non risponde.
2. Struttura "da store": finto DB, account, onboarding, `PROGETTO.md` (visione, decisioni, schema SQL, piano economico, rischi, roadmap).
3. Liste: "La mia lista", "Rewatch" (derivata dai visti), liste personalizzate; condivisione per invito a @nome da accettare (appare come "Lista @nome", sola lettura); copia/collegamento liste come funzione Premium. Tolte le modalità coppia/amici.
4. Ricerca descrittiva per concetti (dizionario `CONCEPTS` con film-seme verificati su TMDB); ricerca per titolo parziale, attore o descrizione per aggiungere film alle liste.
5. Pagina Scegli: mood, "Pesca da" (piattaforme/liste), "🎲 Scegli per me" con animazione e pubblicità simulata, filtri avanzati (genere, durata, epoca, voto, solo disponibili, popolari) in "Personalizza la scelta", opzione elenco. I film già visti non vengono mai proposti, tranne pescando dalla lista Rewatch. Conferma/voto sull'ultimo film suggerito.
6. "Ce l'ho / Manca" (ex Scopri, icona margherita senza un petalo): carte swipe, doppio tap = ❤️ in La mia lista, ✕ Escludi, tutorial; non ripropone film già valutati. Direzione dello swipe invertita due volte su richiesta di Simona.
7. Registrazione: avatar = personaggio di film (36 personaggi), piattaforme, importazione dati, carte Ce l'ho/Manca. Tolta la scelta dei generi preferiti.
8. Importazione da export CSV di Letterboxd, IMDb, Netflix con abbinamento ai film TMDB (id IMDb, titolo esatto it/en ± anno, fallback prudente) e barra di avanzamento; visti → Rewatch, watchlist → La mia lista, liste → liste personalizzate, voti convertiti. TV Time, Trakt ecc. segnate "in arrivo".
9. Pagina Game: quiz del giorno (1-5 domande, 10-15 s, classifica settimanale, bonus domenicale a punteggio doppio) e "Sfide" tra amici (domanda secca, set di domande, indovina il frame, indovina la colonna sonora con melodie sintetizzate in WebAudio). Il vincitore sceglie il film dalla propria lista; lo sconfitto vede "Hai perso! Stasera si guarda:"; pareggio = film a caso dalle liste di entrambi. Commit 7505f17.
10. Un'altra sessione Claude (session_01FNbEyh3K6a25sYMmkCnVfj) lavora in parallelo sullo stesso repo. Ultimo suo commit: 6e7fbc7 "Revisione UX: tre schermate e subito il primo film" (vedi sezione 4).

Problemi risolti
- Blocco TMDB nell'anteprima (modalità demo), compatibilità iPhone vecchi (`structuredClone`), form di registrazione che si cancellava, griglia che tornava in alto, ricerca persone troppo larga, 404 su GitHub Pages, conflitto di push con l'altra sessione (lavoro alternativo salvato nel branch locale `v6-mia-versione`), ritagli avatar sbagliati, abbinamenti import errati (es. Spirited Away), errore dopo logout nel conteggio (guard `me()`), barre dell'equalizzatore.

## 3. Decisioni architetturali e vincoli

- Prototipo single-file: iterare in fretta e provare subito sul telefono via GitHub Pages.
- localStorage come DB finto con schema identico al futuro Supabase, per migrare senza ripensare i dati.
- Solo film (niente serie) per ora; niente storico; regione IT; tutto in italiano.
- Monetizzazione: pubblicità (AdMob) + Premium.
- Commit firmati come s1m03ll3 <s1m03ll3@users.noreply.github.com> (mai email personale). Il repo è pubblico: niente email né chiavi API nei file.
- Revisione UX (6e7fbc7) ha cambiato alcune scelte precedenti. Verificato su `PROGETTO.md`:
  - il trailer è GRATUITO per tutti (non più Premium, contrariamente al punto 5);
  - Premium = niente pubblicità + liste illimitate (gratis "1 lista su 3");
  - la scheda film ha "Lo guardo" / "Un altro", poi Salva, Già visto, Non mi interessa, Trailer; si chiude con ✕ o trascinando;
  - registrazione ridotta (benvenuto, piattaforme, griglia "Quali hai già visto?", poi subito il primo film); si parte da ospite; account solo quando serve, con test di 3 domande "Che personaggio sei?" che propone 4 avatar;
  - pubblicità mai dopo "Non mi interessa"/"Già visto"; interstitial ogni 3 ricerche/scelte, banner in Liste;
  - carte con etichette "Visto / Non visto"; quiz con esito dopo ogni risposta.
  - Verificato in `index.html`: Scegli ha la frase a effetto, il bottone con il numero di candidati, "Che film ti senti?", "Scegli tra" dentro Personalizza e l'avviso con rimedio quando i filtri danno zero film.

## 4. Stato attuale e problemi aperti

Funziona (prototipo)
- Scegli, Ce l'ho/Manca, Liste (con condivisione simulata), ricerca per concetti, importazione CSV, Game (quiz + sfide), onboarding ridotto, modalità demo e reale con TMDB.

Da fare / aperti
- Passaggio a Expo + Supabase: login vero, contatti/sfide/inviti reali tra dispositivi, AdMob e acquisti in-app veri.
- Licenza commerciale TMDB da verificare.
- Diritti delle foto degli avatar (valutare illustrazioni originali).
- Licenze delle colonne sonore (ora melodie sintetizzate, da verificare).
- Nome definitivo dell'app; premi della classifica quiz; frequenza delle pubblicità.
- Due sessioni Claude in parallelo sullo stesso repo: meglio una alla volta, sempre `git pull` prima di modificare. Il branch locale `v6-mia-versione` contiene il lavoro alternativo.

## 5. Prompt di partenza per la prossima chat

```
Ciao, parliamo in italiano. Stiamo lavorando a "CosaGuardo", app che consiglia che film guardare stasera (filtri/umore, piattaforme dell'utente, liste). Apri la sessione con repository s1m03ll3/cosa-guardo e ambiente cloud "cosa-guardo".

Prima di tutto:
1. Leggi HANDOFF.md, PROGETTO.md e README.md.
2. Fai git pull su main prima di modificare qualsiasi cosa (un'altra sessione Claude può aver pubblicato modifiche).
3. Verifica in index.html lo stato attuale (la "Revisione UX" 6e7fbc7 ha cambiato alcune scelte: trailer gratuito, registrazione in tre schermate, ospite).

Regole di lavoro:
- Testa con Playwright (Chromium headless), in modalità demo e con dati reali TMDB (la chiave si inserisce da Profilo → Sviluppatore; non scriverla mai nei file, il repo è pubblico).
- Firma i commit come s1m03ll3 <s1m03ll3@users.noreply.github.com> (mai l'email personale).
- Pubblica su main: il sito si aggiorna su https://s1m03ll3.github.io/cosa-guardo/ e lo provo dal telefono.
- Rispondi in italiano, in modo semplice e concreto.

Cosa voglio fare ora: [scrivi qui il prossimo obiettivo, ad es. nuove rifiniture UX oppure inizio della migrazione a Expo + Supabase].
```
