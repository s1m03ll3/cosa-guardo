# CosaGuardo – Documento di progetto

_Ultimo aggiornamento: 9 novembre 2026 · Fase 1 (prototipo)_

## 1. Visione
App che risolve l'indecisione serale: **non scegli tu, sceglie lei**. Premi "🎲 Scegli per me" e l'app ti propone un film disponibile sulle tue piattaforme o nelle tue liste; se vuoi, prima dici che voglia hai o descrivi cosa ti va. L'elenco completo resta disponibile, ma è secondario. In più: carte da scorrere per insegnarle i tuoi gusti e un cine-quiz quotidiano con classifica settimanale, che dà un motivo per aprire l'app ogni giorno.

## 2. Decisioni prese
| Tema | Decisione |
|---|---|
| Contenuti | Solo film. Serie TV in una fase successiva |
| Storico suggerimenti | Non previsto |
| Principio guida | **L'utente non sceglie: sceglie l'app.** Ogni schermata spinge verso un solo film proposto ("🎲 Scegli per me"); liste ed elenco sono strumenti secondari, non il centro dell'app |
| Navigazione | 5 schede: **Scegli · Ce l'ho/Manca · Game · Liste · Profilo** (icone disegnate; per Ce l'ho/Manca una margherita senza un petalo) |
| Pagina Scegli | In alto: **Che voglia hai?** (tutti i mood visibili, a griglia), **Pesca da** (piattaforme, La mia lista, Rewatch, liste a tema e collegate) e il bottone **🎲 Scegli per me**. Il resto (descrizione libera, genere, durata/epoca/voto, opzioni) sta in **"Personalizza la scelta"**, chiuso, con il numero di filtri attivi. Sotto: **📋 Preferisco vedere l'elenco (N)**. Il film proposto dice in una riga **perché** è stato scelto |
| Film già visti | **Non vengono mai proposti**, tranne quando in "Pesca da" è selezionata la lista **Rewatch** (e solo da lì). Tolta l'opzione "escludi film già visti" |
| Scheda film | Lo guardo · Trailer (**Premium**) · Salva · Già visto · **Non mi interessa**. Nessun link a siti esterni |
| Ce l'ho / Manca | (ex Scopri) Si apre direttamente sulle carte, "come con le figurine". ← **manca** = non visto · → **ce l'ho** = visto (va in Rewatch) · ↓ o ✕ **escludi** · **doppio tap ❤️** = nella mia lista. Mentre trascini compare l'etichetta dal lato giusto. Ogni film compare una volta sola. Tutorial al primo accesso |
| Liste | Sempre presenti: **La mia lista** e **Rewatch** (i film già visti, utilizzabile in Scegli). Altre liste **a tema** create dall'utente (es. "Film romantici"). Rinomina con ✏️. Niente più liste coppia/amici |
| Collegamento liste | Dentro una lista: "Collega a una persona" → si cerca il **@nomeutente** → invito da accettare. Chi accetta vede la lista tra le sue come **"Lista @nome"** (sola lettura, sempre aggiornata) e può usarla in Scegli. Chi non è registrato riceve un **link di iscrizione**. Il collegamento si può impostare anche mentre si crea la lista |
| Game | **Cine-quiz del giorno**: da 1 a 5 domande al giorno uguali per tutti, 15 secondi (10 per vero/falso). Si vede solo il risultato finale. Punti = 100 + 10 per ogni secondo avanzato. **Punteggio cumulativo lun–dom**, classifica settimanale e podio della settimana precedente. **Domenica: domanda bonus che vale doppio** |
| Sfide tra amici (Game) | Nella pagina Game, accanto al quiz, la scheda **⚔️ Sfide**. Si sfida un **contatto** (aggiunto per @nomeutente con richiesta da accettare, oppure chi condivide con te una lista collegata) o direttamente **@nome**; chi non è iscritto riceve un invito. **Ogni giorno un tipo diverso**, a rotazione: ⚡ domanda secca (vince chi risponde giusto più in fretta) · 🧠 raffica di 5 domande · 🎞️ indovina il film dal fotogramma · 🎵 indovina il film dalla colonna sonora. Si gioca a turni con le stesse domande; vince chi fa più punti, a parità il più veloce. **Chi vince sceglie il film dalla sua lista** (o pesca a caso); chi perde, all'apertura dell'app, vede **"Hai perso! Stasera si guarda…"** con la locandina. Pareggio: film estratto dalle liste di entrambi. Rivincita il giorno dopo |
| Account | Apple, Google, email + ospite. **@nomeutente** unico. Avatar: **personaggi famosi ritratti nel ruolo** (fotogramma o locandina del film con ritaglio sul volto, es. Joaquin Phoenix nei panni di Joker) e personaggi d'animazione (vedi §5, diritti d'immagine) |
| Registrazione | Benvenuto → account → personaggio → piattaforme (con nota: "se non trovi il tuo abbonamento, aggiungilo dal Profilo") → **Porta i tuoi dati** (importazione, facoltativa) → **carte "Ce l'ho o manca?"** direttamente nella registrazione, con la nota che si può continuare nell'app. Niente scelta dei generi (resta nel Profilo) |
| Importazione dati | Da **Letterboxd** (visti, voti, watchlist, liste), **IMDb** (voti, watchlist) e **cronologia Netflix** (solo film), caricando i file CSV che queste app fanno esportare. Ogni film viene riconosciuto su TMDB (codice IMDb, oppure titolo + anno, anche in inglese). Visti → Rewatch, watchlist → La mia lista, voti → gusti, liste Letterboxd → liste a tema. Disponibile anche dal Profilo. TV Time, Trakt e altre: in arrivo (Trakt con collegamento diretto all'account) |
| Guadagni | Pubblicità + Premium (niente pubblicità, trailer, liste illimitate) |
| Tecnologia (proposta) | App: React Native + Expo · Backend: Supabase (Postgres + Auth) · Dati film: TMDB |

## 3. Funzioni del prototipo (`index.html`)
- Tutte le decisioni sopra sono già provabili nel prototipo.
- Ricerca descrittiva con **concetti** colloquiali ("che cazzo sto guardando", "per piangere", "finale a sorpresa"…) + generi, temi, durata, periodo, attori/registi.
- Follow-up "com'è andata?" sull'ultimo film scelto, alla riapertura dell'app.
- Pubblicità simulata (interstitial ogni 3 ricerche/scelte, banner in Liste) e Premium simulato.
- Modalità demo (42 film con locandine vere) se non c'è una chiave TMDB; strumento "multi-utente" per provare collegamenti e classifica; giocatori simulati in classifica.

## 4. Schema del database (bozza per Supabase/Postgres)
Rispecchia il "finto database" del prototipo (sezione 3 del codice).

```sql
create table profiles (
  id            uuid primary key references auth.users on delete cascade,
  display_name  text not null,
  username      text unique check (username ~ '^[a-z0-9._]{3,20}$'),  -- @nomeutente (null per gli ospiti)
  avatar        text,                    -- id del personaggio scelto
  is_guest      boolean default false,
  provider_ids  int[]  default '{}',     -- piattaforme TMDB (8=Netflix, 337=Disney+ ...)
  fav_genres    int[]  default '{}',
  filters       jsonb  default '{}',     -- ultimi filtri usati
  premium_until timestamptz,             -- null = piano gratuito
  onboarded     boolean default false,
  created_at    timestamptz default now()
);

create table lists (
  id          uuid primary key default gen_random_uuid(),
  name        text not null,
  type        text not null check (type in ('personal','rewatch','custom')),
  owner_id    uuid references profiles on delete cascade,
  invite_code text unique,               -- token del link di iscrizione (creato quando serve)
  created_at  timestamptz default now()
);
-- ogni utente ha una sola 'personal' e una sola 'rewatch'
create unique index one_personal on lists(owner_id) where type='personal';
create unique index one_rewatch  on lists(owner_id) where type='rewatch';

-- proprietario + persone a cui la lista è collegata (sola lettura)
create table list_members (
  list_id   uuid references lists on delete cascade,
  user_id   uuid references profiles on delete cascade,
  role      text not null check (role in ('owner','viewer')),
  joined_at timestamptz default now(),
  primary key (list_id, user_id)
);

create table list_items (
  id           uuid primary key default gen_random_uuid(),
  list_id      uuid references lists on delete cascade,
  tmdb_id      int,                      -- null se aggiunto a mano
  custom_title text,
  snapshot     jsonb,                    -- titolo, anno, poster, generi, durata
  added_by     uuid references profiles on delete cascade,
  added_at     timestamptz default now(),
  unique (list_id, tmdb_id)
);

-- inviti di collegamento (da accettare)
create table list_invites (
  id         uuid primary key default gen_random_uuid(),
  list_id    uuid references lists on delete cascade,
  from_user  uuid references profiles on delete cascade,
  to_user    uuid references profiles on delete cascade,
  status     text not null default 'pending' check (status in ('pending','accepted','declined')),
  created_at timestamptz default now()
);

-- film visti: alimentano anche la lista Rewatch (che quindi non ha righe in list_items)
create table seen (
  user_id     uuid references profiles on delete cascade,
  tmdb_id     int,
  snapshot    jsonb,
  in_rewatch  boolean default true,      -- false se tolto da Rewatch
  seen_at     timestamptz default now(),
  primary key (user_id, tmdb_id)
);

create table dismissed (                 -- "non mi interessa" / "escludi"
  user_id uuid references profiles on delete cascade,
  tmdb_id int,
  created_at timestamptz default now(),
  primary key (user_id, tmdb_id)
);

-- importazioni da altre app (per mostrare "✓ N film importati" e non ripetere)
create table imports (
  id uuid primary key default gen_random_uuid(),
  user_id uuid references profiles on delete cascade,
  source text check (source in ('letterboxd','imdb','netflix','trakt','tvtime')),
  found int, missing int,
  created_at timestamptz default now()
);

-- contatti (richiesta da accettare) e sfide tra amici
create table contacts (
  id uuid primary key default gen_random_uuid(),
  from_user uuid references profiles on delete cascade,
  to_user   uuid references profiles on delete cascade,
  status    text not null default 'pending' check (status in ('pending','accepted','declined')),
  created_at timestamptz default now()
);
create table duels (
  id uuid primary key default gen_random_uuid(),
  from_user uuid references profiles on delete cascade,     -- chi lancia la sfida (gioca per primo)
  to_user   uuid references profiles on delete cascade,
  type      text check (type in ('secca','set','frame','sound')),
  day       date, seed text,                               -- stesse domande per entrambi
  status    text check (status in ('playing','waiting','choosing','done','declined')),
  results   jsonb,                                         -- {utente: {punti, tempo, risposte}} – calcolati dal server
  winner    uuid,                                          -- null = pareggio
  film      jsonb,                                         -- il film scelto dal vincitore
  seen_by   uuid[] default '{}',                           -- chi ha già visto il risultato
  created_at timestamptz default now()
);

create table swipes (                    -- carte di Ce l'ho/Manca: ogni film una volta sola
  user_id  uuid references profiles on delete cascade,
  tmdb_id  int,
  verdict  text check (verdict in ('seen','not_seen','dismiss','want')),
  snapshot jsonb,
  created_at timestamptz default now(),
  primary key (user_id, tmdb_id)
);

create table ratings (
  user_id   uuid references profiles on delete cascade,
  tmdb_id   int,
  value     smallint check (value in (-1, 1)),
  genre_ids int[],
  rated_at  timestamptz default now(),
  primary key (user_id, tmdb_id)
);

-- cine-quiz
create table quiz_questions (
  id        serial primary key,
  category  text, question text, answers text[],   -- answers[1] = giusta
  media     jsonb,                                  -- emoji, locandina…
  seconds   smallint default 15,
  is_bonus  boolean default false,
  approved  boolean default false                   -- revisione umana prima di pubblicare
);
create table quiz_days (                 -- domande del giorno, uguali per tutti
  day date primary key, question_ids int[]
);
create table quiz_plays (
  user_id  uuid references profiles on delete cascade,
  day      date,
  week     date,                         -- lunedì della settimana
  answers  jsonb,                        -- [{question_id, ok, points}]
  points   int default 0,
  primary key (user_id, day)             -- una sola partita al giorno
);
create view quiz_weekly as
  select week, user_id, sum(points) as points from quiz_plays group by week, user_id;
```
**Sicurezza (Row Level Security):** ognuno legge/scrive solo i propri dati; vede una lista solo se è in `list_members`; i `viewer` non possono modificarla. Accettare un invito o un link passa da funzioni server. **Ricerca utenti:** solo per @nomeutente. **Quiz:** la risposta giusta non arriva mai al telefono prima di rispondere; il punteggio lo calcola il server (tempo misurato lato server), così la classifica non si può truccare.

**Chiave TMDB:** mai nell'app. Le chiamate passano da una funzione server che aggiunge la chiave e mette in cache le risposte.

## 5. Piano economico
1. **Pubblicità (utenti gratuiti)** – AdMob: interstitial ogni 3 azioni, ma **mai alla prima del giorno e mai tra "Scegli per me" e il film proposto**: compare su "Scegline un altro" o sull'elenco. Banner nelle schermate secondarie, eventuale *rewarded* ("guarda un video per un aiuto nel quiz").
2. **Premium** (es. 2,49 €/mese o 19,99 €/anno): niente pubblicità, **trailer**, liste illimitate; in futuro filtri avanzati, serie TV.
3. **Il quiz come motore di ritorno quotidiano**: più aperture = più pubblicità viste. Possibili premi settimanali sponsorizzati (es. un mese di Premium al primo classificato).
4. **Affiliazioni** (da verificare): prove gratuite delle piattaforme.
5. **Costi:** Apple 99 $/anno, Google 25 $ una tantum, Supabase ~25 $/mese dopo la fase gratuita, dominio ~20 €/anno, **licenza commerciale TMDB (da richiedere)**, eventuale AI per descrizioni e domande del quiz.

⚠️ **Rischi da chiarire prima della Fase 3**
- **TMDB:** l'uso gratuito è solo non commerciale; con pubblicità o abbonamenti serve un accordo (anche per i dati JustWatch). Alternative: Watchmode, Streaming Availability.
- **Colonne sonore nelle sfide:** nel prototipo le melodie sono ricostruite nota per nota dal telefono; anche le melodie sono opere protette, quindi per lo store servono licenze (es. anteprime ufficiali di 30 secondi da un servizio musicale) o brani di pubblico dominio.
- **Avatar con attori reali:** foto di attori e immagini di personaggi (De Niro, Disney/Pixar…) sono protette da diritti d'immagine e copyright. Nel prototipo vanno bene; per lo store servono licenze oppure **illustrazioni originali "ispirate a"** commissionate per l'app (consigliato).
- **Locandine nel quiz:** stesso discorso; da coprire con la licenza TMDB o con domande senza immagini.

## 6. Roadmap
| Fase | Contenuto | Stato |
|---|---|---|
| 1 | Prototipo completo + schema DB | ✅ in revisione |
| 2 | Licenza TMDB · decisione su avatar · Supabase: tabelle, regole di sicurezza, login · proxy API · banca domande del quiz | da fare |
| 3 | App Expo: porting schermate, deep link per i collegamenti (cosaguardo.app/invito/…), notifiche ("@simona ti ha collegato una lista", "Nuove domande nel quiz!", "Classifica finale della settimana", "Com'era il film di ieri?") · ricerca descrittiva con AI lato server | da fare |
| 4 | AdMob, acquisti in-app (RevenueCat), privacy policy GDPR, consenso pubblicità (UMP) | da fare |
| 5 | Test con amici (TestFlight / Play test interno) → pubblicazione | da fare |
| 6 | Serie TV, filtri avanzati, statistiche, sfide tra amici nel quiz | futuro |

## 7. Ricerca descrittiva e quiz: dal prototipo alla versione reale
- **Descrivi – prototipo:** regole + dizionario italiano + 10 "concetti" con parole chiave TMDB verificate e film di riferimento. Non capisce trame libere.
- **Descrivi – produzione:** un modello AI lato server trasforma la frase in filtri (stesso formato) e per le trame libere propone titoli che poi verifichiamo su TMDB.
- **Quiz – prototipo:** 89 domande scritte a mano in 12 categorie (citazioni, emoji, locandina misteriosa che si mette a fuoco col tempo, attori, registi, intruso, Oscar, colonne sonore, date, vero/falso, numeri, cinema italiano) + 7 domande bonus.
- **Quiz – produzione:** banca domande sul server, nuove domande generate in parte automaticamente (dati TMDB + AI) ma **sempre approvate da una persona** prima di andare online.

## 8. Domande aperte
- Nome definitivo dell'app
- Avatar: licenze o illustrazioni originali?
- Premi per la classifica settimanale del quiz?
- Frequenza pubblicità: ogni 3 ricerche è un compromesso da testare con utenti reali
