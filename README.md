# Finnish collocations · Suomen kielen kollokaatiot

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.24348963.svg)](https://doi.org/10.5281/zenodo.24348963)

A lemma-level collocation dataset for Finnish: **1.5 million word pairs** scored by logDice
association, with co-occurrence and dispersion counts, the most frequent surface realization of
each pair (*juoda kahvia*, *villi kortti*, *meren rannalla*) and 241,216 attested example
sentences. Extracted by [Asiasanasto.fi](https://asiasanasto.fi), a Finnish dictionary and thesaurus,
where the same data appears on every word page, e.g. [kortti](https://asiasanasto.fi/sana/kortti).

[Suomeksi alempana](#suomeksi).

## Files

| File | Contents | License |
|------|----------|---------|
| `data/kollokaatiot.tsv.gz` | 1,542,482 lemma pairs with association statistics, 215,068 with a surface phrase | CC BY 4.0 |
| `data/lemmat.tsv.gz` | 46,564 lemmas: POS, corpus frequency, dictionary URL | CC BY 4.0 |
| `data/esimerkit.jsonl.gz` | 241,216 attested example sentences with character offsets and provenance | CC BY-SA 4.0 |

UTF-8, gzip-compressed; lemmas are lowercase citation forms.

### kollokaatiot.tsv.gz

```
lemma1    pos1       lemma2   pos2       pair_freq  docs  logdice  lemma1_first  phrase
kortti    noun       pelata   verb       429        315   8.77     0.17          pelata korttia
kortti    noun       villi    adjective  106        97    7.93     0.00          villi kortti
```

| Column | Meaning |
|--------|---------|
| `lemma1`, `lemma2` | The pair as lemmas. Unordered, so each pair appears once with the alphabetically first lemma as `lemma1`. Sorted by `lemma1`, then `logdice` descending. |
| `pos1`, `pos2` | Part of speech: `noun`, `verb`, `adjective`, `adverb` |
| `pair_freq` | Co-occurrence frequency f(xy) within a ±3-token window inside one sentence |
| `docs` | Dispersion: number of distinct books/articles the pair occurs in |
| `logdice` | `14 + log2(2·f(xy) / (f(x) + f(y)))` ([Rychlý 2008](https://www.sketchengine.eu/wp-content/uploads/2015/03/Lexicographer-Friendly_2008.pdf)). Upper bound 14, roughly corpus-size independent; ≥ 7 is typically a clear collocation. |
| `lemma1_first` | Linear-order preference: share of co-occurrences where `lemma1` precedes `lemma2` (0–1) |
| `phrase` | Most frequent surface realization, e.g. *juoda kahvia*. Only for each lemma's top-ranked collocates, otherwise empty. |

### lemmat.tsv.gz

`lemma`, `pos`, `freq` (lemma frequency in the corpus) and `url`, the lemma's Asiasanasto.fi entry
with definitions, inflection tables, synonyms and etymology.

### esimerkit.jsonl.gz

One JSON object per line:

```json
{"lemma":"kortti","partner":"villi","phrase":"villi kortti",
 "sentence":"Voittomäärä oikeutti sen kuitenkin niin sanotun villin kortin pudotuspelipaikkaan.",
 "lemma_span":[55,61],"partner_span":[48,54],"corpus":"wikipedia","title":"Miami Marlins","author":null,
 "source_url":"https://fi.wikipedia.org/wiki/Miami%20Marlins"}
```

`lemma_span` and `partner_span` are half-open `[start, end)` character offsets into `sentence`.

## Method

- **Corpora:** about 3,700 public-domain books from [Projekti Lönnrot](http://www.lonnrot.net/)
  and the Finnish Wikipedia, lemmatized with the [Voikko](https://voikko.puimula.org/)
  morphological analyzer.
- **Candidate pairs:** ADJ+NOUN, VERB+NOUN, NOUN+NOUN and VERB+ADV (manner adverbs only) within
  a ±3-token window, not crossing sentence boundaries. Pronouns and function words are stoplisted.
- **Lemma ambiguity:** word forms with more than one possible lemma (*tuli* → *tulla* / *tuli*)
  are discarded rather than guessed.
- **Thresholds:** f(xy) ≥ 5 with a dispersion of ≥ 3 documents; f(x) ≥ 5. Duplicate paragraphs
  are deduplicated and Wikipedia boilerplate (references, "see also") is dropped.
- **Surface phrases:** for verb pairs, the infinitive plus the collocate's most frequent inflected
  form in the pair; otherwise the most frequent contiguous realization with at most one intervening token.

### Known limitations

- The corpora skew towards older literature and encyclopedic register, so contemporary and
  colloquial vocabulary is underrepresented.
- High-frequency lemmas attract some generic collocates (*tehdä* → *vuosi*).
- Named entities occasionally leak in (*sota* → *tähti*, from *Tähtien sota*).
- Lönnrot texts contain pre-reform orthography; example sentences with old spelling were filtered out.

## Using the data

The pair statistics, phrases and lemma list (`kollokaatiot.tsv.gz`, `lemmat.tsv.gz`) are licensed
under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) (see `LICENSE`). You may use them
for any purpose, including commercial use, as long as you give credit.

The easiest way is a short line with a link to [Asiasanasto.fi](https://asiasanasto.fi),
wherever you'd normally mention your sources:

> Collocation data: [Asiasanasto.fi](https://asiasanasto.fi), CC BY 4.0

> Kollokaatioaineisto: [Asiasanasto.fi](https://asiasanasto.fi), CC BY 4.0

Where a link isn't possible, writing out `asiasanasto.fi` is fine.

The example sentences (`esimerkit.jsonl.gz`) include text from Wikipedia, so that file is under
[CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/) (see `LICENSE-EXAMPLES`): if you
share a modified version of it, keep the same license. The Lönnrot sentences are in the public domain.

For academic work, you can cite it like this:

```bibtex
@misc{asiasanasto_collocations,
  title  = {Finnish collocations (Suomen kielen kollokaatiot)},
  author = {{Asiasanasto.fi}},
  year   = {2026},
  doi    = {10.5281/zenodo.24348963},
  url    = {https://asiasanasto.fi},
  note   = {Dataset, https://github.com/apinanaivot/finnish-collocations}
}
```

---

## Suomeksi

Lemmatasoinen kollokaatioaineisto: 1,5 miljoonaa suomen kielen sanaparia logDice-assosiaatioarvoineen,
yhteisesiintymä- ja dispersiolukuineen. Vahvimmista pareista on mukana myös yleisin pintamuoto
(*juoda kahvia*, *villi kortti*) ja esimerkkilause lähdetietoineen. Aineisto on poimittu
Voikolla lemmatisoidusta korpuksesta, joka koostuu Projekti Lönnrotin tekijänoikeusvapaasta
kirjallisuudesta ja suomenkielisestä Wikipediasta. Samat tiedot näkyvät
[Asiasanasto.fi](https://asiasanasto.fi)-sanakirjan sana-artikkeleissa.

Tiedostot, sarakkeet ja menetelmä on kuvattu yllä englanniksi.

**Käyttö:** Aineistoa saa käyttää vapaasti mihin tahansa, myös kaupallisesti. Tilastot,
ilmaukset ja lemmalista ovat [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/deed.fi)
-lisenssin alaisia, ja lisenssi edellyttää vain lähteen mainitsemista. Helpoimmin se käy
lyhyellä maininnalla, jossa on linkki [Asiasanasto.fi](https://asiasanasto.fi)-sivustolle:

> Kollokaatioaineisto: [Asiasanasto.fi](https://asiasanasto.fi), CC BY 4.0

Esimerkkilauseissa
on Wikipedian tekstiä, joten ne ovat [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/deed.fi)
-lisenssin alaisia.
