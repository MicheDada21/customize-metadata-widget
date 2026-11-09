---
name: widget-modifica-metadati
description: "Apre un widget con le anteprime di Google, browser, WhatsApp e iMessage per scegliere titolo, descrizione e immagine di una pagina, poi passa la bozza a metadata-general-audit. Usa quando l'utente chiede di cambiare o scegliere i metadati, il titolo SEO o l'anteprima di condivisione di una pagina."
disable-model-invocation: true
---

# Modifica i metadati di una pagina con anteprima

## Ambito

La skill lavora su una pagina alla volta e finisce quando l'utente invia la bozza dal widget. Applicazione e controllo del sito spettano alla skill `metadata-general-audit`. Fino all'invio la chat contiene solo il widget e una riga che invita a compilarlo e inviarlo. Ciò che noti e che il widget non copre, per esempio problemi del sito, dei redirect o del contenuto, entra nella lista delle osservazioni della bozza. Se la richiesta indica già i valori esatti, apri direttamente `metadata-general-audit` con quei valori.

## Raccogli i valori attuali

1. Prendi la pagina di cui parla la conversazione, anche quando la skill è stata incollata senza nominarla. Se le pagine citate sono più d'una o nessuna, chiedi quale e aspetta la risposta.
2. Trova il file o la funzione che produce i metadati, compresi i valori ereditati dai layout, il modello del titolo (per esempio `%s | Brand`) e i file `opengraph-image` o `twitter-image` nel segmento della route e in quelli superiori.
3. Leggi titolo, descrizione, immagine con testo alternativo, ogni campo di «Decidi i valori tecnici», favicon e dati strutturati del sito. La base è la pagina pubblicata, con il sorgente come fallback quando la pagina non è raggiungibile; scrivi nel widget quale hai usato e quando.
4. Leggi il contenuto della pagina per le proposte di testo.

Il passo è finito quando ogni campo ha un valore copiato verbatim, un'eredità dichiarata o la dicitura «non disponibile».

## Proponi titoli e descrizioni

Scrivi due o tre titoli e due o tre descrizioni candidati seguendo le indicazioni di Google.

- Il titolo nomina il soggetto e l'argomento di questa pagina. Anche la home dice cosa offre il sito, al posto di «Home».
- La descrizione è una sintesi autonoma dell'intera pagina, in frasi compiute, con fatti presenti nella pagina, perché i motori di risposta come AI Overviews, ChatGPT e Perplexity citano le frasi che stanno in piedi da sole.
- I testi usano la lingua della pagina.
- Il nome del sito compare una volta, all'inizio o alla fine, con un separatore come `|` o `-`. Se il modello del titolo lo aggiunge già, il candidato contiene solo la parte della pagina.

Il passo è finito quando ogni candidato rispetta queste regole e la soglia del suo contatore.

## Prepara le immagini

Il widget propone tre immagini 1200 x 630, ciascuna etichettata con la sua fonte. Riempi i posti a cascata, passando alla fonte successiva solo quando la precedente è esaurita o manca, e scrivilo nel widget.

1. L'immagine attuale. Se contiene testo, proponi la sua versione generata.
2. Le immagini di contenuto della pagina, dalla più grande e più vicina all'inizio, ritagliate con il soggetto al centro. Icone, loghi e decorazioni restano fuori.
3. Uno screenshot del primo viewport significativo, inquadrato sul solo contenuto, con ogni elemento intero e il banner dei cookie chiuso.
4. Un'anteprima generata a tinte piatte, sullo sfondo dominante della pagina con un accento del design system, variando la coppia di colori tra un'anteprima e l'altra.

Le immagini generate contengono solo grafica e marchio, in PNG. Foto e screenshot vanno in JPEG, e quelli con testo portano un avviso. Il passo è finito quando i tre posti sono pieni o ogni fonte è esaurita, e ogni file rispetta i requisiti in fondo.

## Decidi i valori tecnici

Per ogni campo applica la regola e proponi il valore migliore, oppure «invariato, va già bene» quando il valore attuale la rispetta già. Ogni proposta porta una motivazione di una riga. URL, dimensioni e formato dell'immagine hanno una proposta per ciascuna delle tre immagini.

| Campo | Regola |
| --- | --- |
| Canonical | URL assoluto in HTTPS della pagina stessa sull'host canonico, con il solo percorso e la barra finale coerente con il resto del sito |
| `og:url` | uguale al canonical |
| URL dell'immagine | assoluto e stabile sull'host canonico o nello spazio immagini del progetto, risponde 200 alla prima richiesta, con il solo percorso più l'eventuale hash di versione di Next.js; nome descrittivo in kebab-case nella lingua della pagina, per esempio `/og/stack-hardware.jpg` |
| `og:image:width`, `og:image:height`, `og:image:type` | dimensioni e formato reali del file |
| `og:type` | `article` per articoli e post datati, `website` per le altre pagine |
| `og:locale` | lingua della pagina nella forma `it_IT`, coerente con `<html lang>` |
| `og:site_name` | nome del sito, identico in tutte le pagine e nei dati strutturati `WebSite` |
| `twitter:card` | `summary_large_image` |
| Robots | `index, follow, max-snippet:-1, max-image-preview:large` per le pagine pubbliche; `noindex` per i duplicati, i risultati di ricerca interni e le pagine dietro login |
| Dati strutturati della pagina | JSON-LD del tipo schema.org che descrive il contenuto visibile, per esempio `Article`, `Product`, `CollectionPage` o `ProfilePage`, con i soli fatti presenti nella pagina e `dateModified` quando la pagina ha una data; `Organization` o `Person` e `WebSite` appartengono al sito e vanno nella lista delle osservazioni |
| Modello del titolo | quello del layout, oppure `title.absolute` quando il titolo contiene già il nome del sito; il modello vale per tutto il sito, quindi lo cambia solo l'utente |

Il passo è finito quando ogni campo ha una proposta con la sua motivazione.

## Costruisci il widget

Usa il renderer di widget del client dopo averne letto per intero le istruzioni correnti. Se manca, prepara una pagina HTML autonoma con un pulsante che copia la bozza, e dillo all'utente in una riga. I testi del widget usano la lingua del progetto.

Il widget ha due sezioni separate da un divisore sottile con un'etichetta discreta, per esempio «Decisioni tecniche». La sezione in alto contiene i campi che scrive l'utente e le anteprime.

- Titolo e descrizione, riusati per browser, ricerca e card social. Sopra ciascuno i candidati compaiono come opzioni cliccabili, che riempiono il campo e lo lasciano modificabile.
- Titolo e descrizione social facoltativi, da compilare quando devono differire.
- Immagine di anteprima, scelta tra miniature cliccabili, con il suo testo alternativo.

Accanto a ogni campo mostra il valore attuale e una frase sul suo uso. Un campo obbligatorio vuoto mostra un messaggio sotto il campo.

Sotto ogni campo di testo un contatore mostra caratteri e soglia, per esempio «48 / ottimale fino a 60 (convenzione)», contando i grafemi con `Intl.Segmenter`. Il contatore del titolo conta il titolo finale, con il modello applicato. Oltre la soglia passa ad avviso con un colore e una parola, il campo resta modificabile e una regione `aria-live` discreta annuncia il cambio di stato.

| Campo | Soglia | Origine |
| --- | --- | --- |
| Titolo | 60 | convenzione |
| Descrizione | 160 | convenzione |
| Titolo social | 60 | convenzione |
| Descrizione social | 80 | WhatsApp |
| Testo alternativo | 125 | convenzione |

Con la descrizione social vuota, il contatore della descrizione segna anche la soglia di 80.

Le anteprime portano l'etichetta «approssimativa» e, come i contatori, si aggiornano a ogni battitura.

- Il risultato di Google, con favicon, nome del sito, URL come breadcrumb (dominio › sezione › pagina), titolo e descrizione. Misura la larghezza resa con `measureText` del canvas e tronca con «...» il titolo oltre circa 600 px in Arial 20 px (stima) e la descrizione dopo circa due righe della stessa colonna in Arial 14 px. Senza favicon o dati `WebSite`, mostra ciò che Google userebbe al loro posto, cioè un'icona generica e il dominio.
- La scheda del browser, con favicon e titolo finale troncato alla larghezza di una scheda.
- La card di un link su WhatsApp e su iMessage, con l'immagine scelta e il troncamento.

La sezione in basso elenca le decisioni tecniche. Ogni riga mostra il campo, il valore attuale, la proposta e la motivazione. Un'icona a matita con etichetta accessibile «Modifica» seguita dal nome del campo apre il valore in un campo di testo; una riga modificata mostra «modificato da te» e un comando per tornare alla proposta. Canonical e modello del titolo alimentano subito le anteprime, e i valori dell'immagine seguono l'immagine scelta.

Campi e anteprime stanno nella stessa vista e si impilano sugli schermi stretti con valori e pulsanti interi. Il widget lavora solo sulla bozza.

## Salva e invia la bozza

Salva dopo ogni modifica con lo storage dell'ambiente e mostra «Salvataggio», «Salvata» o «Riprova». La chiave è la coppia di pagina e base. Alla riapertura ripristina la bozza se la coppia coincide; se la base è cambiata, mostra la differenza e chiedi se tenerla. Con lo storage in errore il widget funziona e l'invio resta disponibile.

L'azione principale, per esempio «Invia alla chat per implementare», manda un unico messaggio con un blocco JSON che contiene questi dati.

- La skill che applica la bozza, `metadata-general-audit`, all'indirizzo `https://www.cryptohelvetia.com/ch-labs/prompt-library/skills/metadata-general-audit/SKILL.md`, così un agente in una nuova chat la scarica e la segue.
- La pagina e il file che produce i metadati.
- La base, con origine e momento della lettura.
- I valori proposti, verbatim, compreso il titolo finale con il modello applicato.
- L'immagine scelta, con fonte, percorso o URL, ritaglio e testo alternativo.
- Le decisioni tecniche, ciascuna con valore attuale, proposta, motivazione e autore, «agente» o «utente».
- La mappatura sui tag, per esempio titolo verso `<title>`, `og:title` e `twitter:title`, canonical verso `<link rel="canonical">` e `og:url`.
- La lista delle osservazioni.

Se l'ambiente manca dell'invio diretto, il pulsante copia il messaggio negli appunti e lo dice. Dopo l'invio la bozza resta salvata.

Il widget è pronto quando, con una bozza di prova inviata, ogni comportamento descritto in questa sezione e in «Costruisci il widget» funziona su telefono e su desktop, provato fin dove l'ambiente lo consente. A quel punto mostralo con la sola riga d'invito.

## Requisiti delle anteprime, verificati il 23 ottobre 2026

| Requisito | Valore | Fonte |
| --- | --- | --- |
| Larghezza dell'immagine | almeno 900 px | Apple |
| Immagine per WhatsApp | sotto 600 KB, almeno 300 px di larghezza, proporzioni entro 4:1 | WhatsApp |
| Formati dell'immagine | JPEG o PNG; WebP manca dagli elenchi | Apple, WhatsApp, Meta |
| Testo nell'immagine | sconsigliato, illeggibile a schermo piccolo | Apple |
| Titolo social | breve e specifico, senza nome del sito; due righe su WhatsApp | Apple, WhatsApp |
| Titolo e descrizione in ricerca | nessun limite fisso, taglio in base alla larghezza resa | Google |
| Nome del sito in ricerca | preso dai dati strutturati `WebSite`, poi da `og:site_name`, titolo e intestazioni | Google |

Fonti: [Apple TN3156](https://developer.apple.com/documentation/technotes/tn3156-create-rich-previews-for-messages), [WhatsApp link previews](https://developers.facebook.com/documentation/business-messaging/whatsapp/link-previews/), [Google, titoli](https://developers.google.com/search/docs/appearance/title-link), [Google, snippet](https://developers.google.com/search/docs/appearance/snippet), [Google, nome del sito](https://developers.google.com/search/docs/appearance/site-names).
