# Fisica capovolta → Markdown/MDX — stato del lavoro

Checkpoint generato durante la migrazione del libro da EPUB a Markdown/MDX
per l'import su GitHub e la pubblicazione come sito dinamico.

## Cosa contiene questo archivio

- `convert.py` — script Python che converte le sezioni XHTML dell'EPUB
  originale (`fisica_capovolta1_v1_1_20170916.epub`) in file `.mdx`.
- `site_content/` — l'output già generato dallo script, pronto da importare
  in un repository Git:
  - `intro/` — Presentazione, Download
  - `moduli/00-premesse-matematiche/`, `01-le-basi-della-fisica/`,
    `02-equilibrio/` — i capitoli del libro, un file `.mdx` per sezione
  - `static/img/` — le 57 immagini del libro (percorsi già riscritti come
    `/img/nome-file.ext`, compatibili con la convenzione di Docusaurus)

## Come funziona la conversione (`convert.py`)

1. Legge ogni sezione XHTML dell'EPUB e appiattisce le tabelle usate solo
   per affiancare immagini (layout, non contenuto tabellare vero).
2. Rimuove il markup "cruft" lasciato dall'editor WYSIWYG originale
   (`<span class="Apple-style-span">`, sottolineature, tag vuoti).
3. Riconosce i box colorati del libro dal colore di sfondo e dal testo
   dell'intestazione, e li converte in componenti MDX:
   - arancione + "PER INIZIARE" → `<PerIniziare>`
   - arancione + "PER RIASSUMERE" → `<PerRiassumere>`
   - grigio ("Esempio") → `<Esempio>`
   - giallo chiaro ("F.A.Q.") → `<FAQ>`
   - azzurro chiaro ("Esercizi") → `<Esercizi>`
   - tabella bordata "IL MODELLO DI RIFERIMENTO" → `<ModelloRiferimento>`
4. Decodifica le formule (immagini WIRIS con MathML nascosto in un
   attributo `data-mathml`) e le converte in LaTeX vero, es. `$a\times {10}^{n}$`,
   invece di lasciarle come immagini.
5. Converte gli `<iframe>` di YouTube in un componente `<Video id="..." title="..." />`.
6. Passa il resto del contenuto attraverso Pandoc (HTML → GFM Markdown) per
   liste, tabelle semplici, grassetto/corsivo, link.
7. Scrive un file `.mdx` per sezione con frontmatter (`title`, `sidebar_position`)
   e copia le immagini effettivamente usate in `static/img/`.

Punti già testati e corretti nel corso del lavoro: box duplicati con lo
stesso colore ma significato diverso (Per iniziare / Per riassumere),
formule/video adiacenti spezzati da Pandoc, percorsi immagine relativi non
validi tra cartelle diverse, tabelle-nella-tabella (layout immagini dentro
un box colorato), attributi di presentazione residui nelle tabelle-dati
complesse (es. "ordini di grandezza") che Pandoc non riesce a esprimere in
Markdown puro e lascia come HTML grezzo (valido in MDX, ma "sporco").

## Prossimi passi

1. **Componenti React/MDX ancora da scrivere**: `PerIniziare`,
   `PerRiassumere`, `ModelloRiferimento`, `Esempio`, `FAQ`, `Esercizi`,
   `Video` esistono per ora solo come tag nei file `.mdx` — vanno creati
   come componenti reali (es. in Docusaurus: `src/components/`) con lo
   stile visivo (colori, bordo, icona) coerente con l'originale.
2. **Revisione umana leggera dei contenuti**: rileggere in particolare i
   file con tabelle-dati complesse rimaste come HTML grezzo
   (`04-lunghezza-e-volume.mdx`, `05-massa-e-densita.mdx`,
   `06-tempo-valore-medio-e-incertezza.mdx`,
   `01-vettori-forze-ed-equilibrio.mdx`,
   `04-pressione-ed-equilibrio-dei-fluidi.mdx`) per verificare che rendano
   bene; occasionali micro-artefatti di formattazione (es. un grassetto che
   nell'originale era in realtà solo enfasi visiva) possono essere sistemati
   a mano più velocemente che via script.
3. **Impostare il progetto sito** (es. Docusaurus): creare il repository,
   importare `site_content/`, configurare la sidebar sull'ordine dei moduli
   (già presente come `sidebar_position` nel frontmatter), scrivere i
   componenti del punto 1.
4. **Prima app interattiva e primo Google Gem**, come discusso in
   precedenza nella conversazione.

## Nota per la ripresa del lavoro

L'EPUB originale è stato estratto in `/home/claude/epub_extract/` (non
incluso in questo archivio per evitare duplicazioni di grandi cartelle di
immagini — è già tutto processato in `site_content/`). Se serve rigenerare
la conversione, basta rilanciare `python3 convert.py` avendo a disposizione
di nuovo `epub_extract/OEBPS/` con lo stesso contenuto dell'EPUB caricato.
