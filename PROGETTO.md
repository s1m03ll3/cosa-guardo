# CosaGuardo – Documento di progetto

_Ultimo aggiornamento: 9 novembre 2026 · Fase 1 (prototipo)_

## 1. Visione
App che risolve l'indecisione serale: scegli genere e mood, l'app propone **un solo film**, preso dalle piattaforme a cui sei abbonato o dalle liste (personali o condivise con partner/amici).

## 2. Decisioni prese
| Tema | Decisione |
|---|---|
| Contenuti | Solo film. Serie TV in una fase successiva |
| Storico suggerimenti | Non previsto |
| Liste | Più liste per utente: Personale, Coppia (max 2), Amici (max 10) |
| Condivisione | Si cerca l'utente registrato **per @nomeutente** e gli arriva un **invito da accettare**; chi non è registrato riceve un **link di iscrizione** e, registrandosi, entra subito nella lista. Niente codici da digitare |
| Liste collegate (Premium) | Una lista condivisa può *collegare* le liste personali dei membri: i film non vengono duplicati e restano aggiornati. In alternativa, **copia** dei film in un'altra lista (Premium) |
| Ricerca film | Un solo campo: titolo (anche parziale), attore/regista o descrizione, risultati raggruppati |
| Selezione | Si sceglie da quali fonti pescare: piattaforme e/o una o più liste |
| Account | Apple, Google, email + uso senza account (ospite). Alla registrazione si sceglie un **@nomeutente** unico (suggerito dal nome, modificabile dal profilo) |
| Guadagni | Pubblicità + abbonamento Premium (vedi §5) |
| Tecnologia (proposta) | App: React Native + Expo · Backend: Supabase (Postgres + Auth) · Dati film: TMDB |

## 3. Funzioni del prototipo (`index.html`)
- Onboarding: benvenuto → account → piattaforme → generi preferiti → film già visti
- Scegli: fonti multiple, generi, mood, durata, epoca, voto, "solo film conosciuti", "escludi visti", "solo film che posso vedere"
- Risultato: piattaforme evidenziate, provenienza ("Noi due · aggiunto da Marco"), Salva in lista, Già visto → voto 👍/👎
- Liste: creazione, inviti a utenti registrati (accetta/rifiuta, badge sulla scheda Liste), link di iscrizione, membri, chi ha aggiunto cosa, chi l'ha già visto
- Liste collegate e copia liste (Premium)
- Suggerimenti personalizzati: generi preferiti e voti pesano sull'estrazione
- Pubblicità simulata: interstitial durante il caricamento (1ª scelta e poi ogni 3) + banner nella pagina Liste
- Premium simulato: niente pubblicità, liste illimitate (gratis: 3)
- Profilo: piattaforme, generi, abbonamento, esci, **elimina account** (obbligatorio per Apple)
- **Follow-up**: pulsante "▶️ Lo guardo!"; alla riapertura l'app chiede com'è andata con l'ultimo film (Visto → voto 👍/👎 · Dopo → in lista · No)
- **Ricerca descrittiva** ("un horror coi fantasmi che non duri più di 2h"): riconosce generi, mood, durata, periodo, qualità, temi (→ parole chiave TMDB) e attori/registi
- **Aggiunta in lista** con un'unica ricerca: titolo (anche parziale), attore/regista o descrizione
- **Appunti sull'app** (strumento di revisione): pulsante 📝 in ogni schermata; la nota resta collegata alla sezione in cui la scrivi (Cerca · Filtri, Liste · nome lista, Scheda film, Onboarding…). Pagina *Profilo → Apri gli appunti* con tutte le note raggruppate per sezione, "Vai alla sezione", segna come fatto, copia tutto. Salvati a parte nel browser: non si cancellano con il reset dei dati
- Modalità demo (42 film) se non c'è una chiave TMDB; strumento "multi-utente" per provare le liste condivise

## 4. Schema del database (bozza per Supabase/Postgres)
Rispecchia il "finto database" del prototipo (sezione 3 del codice).

```sql
-- utenti: gestiti da Supabase Auth (auth.users); qui il profilo pubblico
create table profiles (
  id            uuid primary key references auth.users on delete cascade,
  display_name  text not null,
  username      text unique check (username ~ '^[a-z0-9._]{3,20}$'),  -- @nomeutente (null per gli ospiti)
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
  type        text not null check (type in ('personal','couple','friends')),
  owner_id    uuid references profiles on delete set null,
  invite_code text unique,               -- token del link di iscrizione (null per le liste personali)
  created_at  timestamptz default now()
);

create table list_members (
  list_id   uuid references lists on delete cascade,
  user_id   uuid references profiles on delete cascade,
  role      text not null default 'member' check (role in ('owner','member')),
  joined_at timestamptz default now(),
  primary key (list_id, user_id)
);

-- film: salviamo solo id TMDB + pochi dati per mostrare la lista senza chiamare l'API
create table list_items (
  id         uuid primary key default gen_random_uuid(),
  list_id    uuid references lists on delete cascade,
  tmdb_id    int,                        -- null se aggiunto a mano
  custom_title text,
  snapshot   jsonb,                      -- titolo, anno, poster, generi, durata
  added_by   uuid references profiles on delete cascade,
  added_at   timestamptz default now(),
  unique (list_id, tmdb_id)
);

-- inviti a utenti già registrati (da accettare)
create table list_invites (
  id         uuid primary key default gen_random_uuid(),
  list_id    uuid references lists on delete cascade,
  from_user  uuid references profiles on delete cascade,
  to_user    uuid references profiles on delete cascade,
  status     text not null default 'pending' check (status in ('pending','accepted','declined')),
  created_at timestamptz default now()
);

-- liste collegate (Premium): la lista condivisa "include" i film di una lista personale
create table list_links (
  list_id        uuid references lists on delete cascade,   -- lista condivisa
  linked_list_id uuid references lists on delete cascade,   -- lista personale collegata
  linked_by      uuid references profiles on delete cascade,
  created_at     timestamptz default now(),
  primary key (list_id, linked_list_id)
);

create table seen (
  user_id uuid references profiles on delete cascade,
  tmdb_id int,
  seen_at timestamptz default now(),
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
```
**Sicurezza (Row Level Security):** ogni utente legge/scrive solo il proprio profilo, `seen` e `ratings`; vede liste e film solo se è in `list_members`. Gli ingressi (accetta invito, link di iscrizione) passano da funzioni server che controllano il limite di membri. Chi è membro di una lista condivisa può leggere anche i film delle liste collegate. **Ricerca utenti:** solo per @nomeutente (inizio del nome utente), mai per nome o email, per privacy. Da prevedere: parole riservate (admin, cosaguardo…) e filtro per nomi offensivi.

**Chiave TMDB:** non sarà mai nell'app. Le chiamate passano da una funzione server (Supabase Edge Function) che aggiunge la chiave e mette in cache le risposte.

## 5. Piano economico
Le sole pubblicità difficilmente bastano: è un'app che si apre poche volte a settimana, per pochi minuti. Proposta su più livelli:

1. **Pubblicità (utenti gratuiti)** – AdMob
   - Interstitial durante la "scelta" (già nel prototipo, frequenza regolabile)
   - Banner nelle schermate secondarie
   - Eventuale *rewarded*: "guarda un video per sbloccare un filtro Premium per oggi"
2. **Premium** (es. 2,49 €/mese o 19,99 €/anno): niente pubblicità, liste illimitate, in futuro filtri avanzati, serie TV, statistiche di coppia
   - Le liste condivise aiutano: se una persona della coppia è Premium, si può valutare di estendere i vantaggi alla lista
3. **Affiliazioni** (da verificare): link verso prove gratuite o abbonamenti delle piattaforme e noleggio dei film
4. **Costi da coprire (stima di partenza):** Apple 99 $/anno, Google 25 $ una tantum, Supabase gratis all'inizio poi ~25 $/mese, dominio e sito ~20 €/anno, **licenza commerciale TMDB (da richiedere: costo da definire)**

⚠️ **Rischio principale:** l'uso gratuito di TMDB è solo **non commerciale**. Con pubblicità o abbonamenti serve un accordo commerciale con TMDB, anche per i dati di disponibilità di JustWatch. Va chiarito **prima** di iniziare la Fase 3. In alternativa si può valutare un'API di disponibilità a pagamento (es. Watchmode, Streaming Availability).

## 6. Roadmap
| Fase | Contenuto | Stato |
|---|---|---|
| 1 | Prototipo completo + schema DB | ✅ in revisione |
| 2 | Contatto TMDB per licenza commerciale · Supabase: tabelle, regole di sicurezza, login · proxy API | da fare |
| 3 | App Expo: porting schermate, collegamento al DB, deep link per gli inviti (cosaguardo.app/invito/…), notifiche ("Simona ti ha invitato in Noi due", "Marco ha aggiunto un film a Noi due", "Com'era il film di ieri?") · ricerca descrittiva con AI lato server | da fare |
| 4 | AdMob, acquisti in-app (RevenueCat), privacy policy GDPR, consenso pubblicità (UMP) | da fare |
| 5 | Test con amici (TestFlight / Play test interno) → pubblicazione | da fare |
| 6 | Serie TV, filtri avanzati, statistiche | futuro |

## 7. Ricerca descrittiva: dal prototipo alla versione reale
- **Prototipo:** regole e dizionario italiano. Funziona bene su frasi con generi, temi comuni, durata, periodo e nomi; non capisce trame libere ("quel film dove uno rivive sempre lo stesso giorno").
- **Produzione:** un modello AI lato server riceve la frase e restituisce lo stesso formato di filtri (generi, temi, persone, durata, periodo). Per le trame libere può anche proporre direttamente i titoli, che poi verifichiamo su TMDB. Costo stimato molto basso per ricerca (frazioni di centesimo), da includere nel piano costi; si può limitare il numero di ricerche AI per gli utenti gratuiti (leva per il Premium).

## 8. Domande aperte
- Nome definitivo dell'app e verifica che sia libero (store, dominio, marchio)
- Un utente Premium "copre" anche il/la partner nella lista Coppia?
- Frequenza pubblicità: ogni 3 scelte è un compromesso da testare con utenti reali
