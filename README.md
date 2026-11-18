# Finnish collocations · Suomen kielen kollokaatiot

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.24348963.svg)](https://doi.org/10.5281/zenodo.24348963)
[![License: CC BY 4.0](https://img.shields.io/badge/license-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)
[![Version](https://img.shields.io/badge/version-1.0-blue.svg)](CHANGELOG.md)

## Summary

A lemma-level dataset of Finnish collocation candidates extracted from a 109-million-token
corpus of public-domain literature and Wikipedia. It contains 1,542,482 lemma pairs ranked by
logDice association, with co-occurrence and dispersion counts, the most frequent surface
realization of the strongest pairs (*juoda kahvia*, *villi kortti*, *meren rannalla*) and 241,216
attested example sentences with provenance. The dataset was extracted by
[Asiasanasto.fi](https://asiasanasto.fi), a Finnish dictionary and thesaurus that presents the
same data on its word pages, e.g. [kortti](https://asiasanasto.fi/sana/kortti).

[Suomeksi alempana](#suomeksi).

## Contents

| File | Records | License |
|------|---------|---------|
| `data/kollokaatiot.tsv.gz` | 1,542,482 lemma pairs, 215,068 with a surface phrase | CC BY 4.0 |
| `data/lemmat.tsv.gz` | 46,564 lemmas with POS, frequency and dictionary URL | CC BY 4.0 |
| `data/esimerkit.jsonl.gz` | 241,216 example sentences with character offsets and provenance | CC BY-SA 4.0 |
| `datapackage.json` | Machine-readable schema ([Frictionless Data Package](https://datapackage.org/)) | |

UTF-8, gzip-compressed; lemmas are lowercase citation forms.

### Corpus

| Source | Documents | Tokens |
|--------|----------:|-------:|
| [Projekti Lönnrot](http://www.lonnrot.net/), public-domain books | 3,172 books | 77.5 M |
| Finnish Wikipedia, filtered sentences | 125,811 articles | 31.6 M |
| **Total** | | **109.1 M** |

Token counts are Voikko-analysed word tokens after deduplication.

### Pair statistics

| POS combination | Pairs |
|-----------------|------:|
| noun + noun | 556,341 |
| noun + verb | 527,236 |
| adjective + noun | 426,404 |
| verb + adverb | 32,501 |

51.9 M windowed co-occurrences over 14.1 M distinct pairs were counted; 1.54 M pass the frequency
and dispersion thresholds. The logDice distribution has a long tail of weak associations
(median 3.25, 90th percentile 5.64); 51,410 pairs score 7 or higher. Filtering by `logdice`
and `docs` yields cleaner lists.

## Data fields

### kollokaatiot.tsv.gz

```
lemma1    pos1       lemma2   pos2       pair_freq  docs  logdice  lemma1_first  phrase
kortti    noun       pelata   verb       429        315   8.77     0.17          pelata korttia
kortti    noun       villi    adjective  106        97    7.93     0.00          villi kortti
```

| Column | Description |
|--------|-------------|
| `lemma1`, `lemma2` | The pair as lemmas. Unordered, so each pair appears once with the alphabetically first lemma as `lemma1`. Sorted by `lemma1`, then `logdice` descending. |
| `pos1`, `pos2` | Part of speech: `noun`, `verb`, `adjective`, `adverb` |
| `pair_freq` | Co-occurrence frequency f(xy) within a ±3-token window inside one sentence |
| `docs` | Dispersion: number of distinct books/articles the pair occurs in |
| `logdice` | `14 + log2(2·f(xy) / (f(x) + f(y)))` ([Rychlý 2008](https://www.sketchengine.eu/wp-content/uploads/2015/03/Lexicographer-Friendly_2008.pdf)). Upper bound 14, roughly independent of corpus size; higher means stronger association. |
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
134,231 sentences come from Projekti Lönnrot and 106,985 from Wikipedia.

## Collection and preprocessing

- **Sources:** Projekti Lönnrot e-books, and a cleaned Finnish Wikipedia snapshot filtered at
  article level (toxicity, language-model perplexity) and at sentence level (length, character set).
- **Lemmatization:** [Voikko](https://voikko.puimula.org/) morphological analysis. Word forms
  with more than one possible lemma (*tuli* → *tulla* / *tuli*) are discarded rather than guessed.
- **Candidate pairs:** ADJ+NOUN, VERB+NOUN, NOUN+NOUN and VERB+ADV (manner adverbs only) within
  a ±3-token window, not crossing sentence boundaries. Pronouns and function words are stoplisted.
- **Thresholds:** f(xy) ≥ 5 with a dispersion of ≥ 3 documents; f(x) ≥ 5. Duplicate paragraphs
  are removed and Wikipedia boilerplate (references, "see also") is dropped.
- **Surface phrases:** for verb pairs, the infinitive plus the collocate's most frequent inflected
  form in the pair; otherwise the most frequent contiguous realization with at most one intervening token.
- **Example sentences:** one per pair for the strongest collocates, preferring short complete
  sentences with the two words at most one token apart; Lönnrot sentences in pre-reform spelling are excluded.

## Intended uses

Lexicography and dictionary-making, language teaching and learner materials, corpus-linguistic
research, NLP features and evaluation (e.g. lexical association, phrase suggestion), and word games.

## Limitations

- The corpus is weighted towards older public-domain literature and encyclopedic
  register; contemporary and colloquial usage is underrepresented.
- Pairs are statistical co-occurrences, not manually verified collocations. Precision has not
  been evaluated; low-scoring pairs are often free combinations.
- High-frequency lemmas attract some generic collocates (*tehdä* → *vuosi*).
- Named entities occasionally leak in (*sota* → *tähti*, from *Tähtien sota*).
- Discarding ambiguous word forms lowers recall for lemmas whose forms are often homographs.

## Maintenance

Maintained by Asiasanasto.fi. New versions are released on GitHub and archived on Zenodo; see
[CHANGELOG.md](CHANGELOG.md). Corrections and suggestions are welcome as GitHub issues.

## License and attribution

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

## Citation

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

Lemmatasoinen aineisto suomen kielen kollokaatioehdokkaista. Aineistossa on 1,5 miljoonaa
sanaparia logDice-assosiaatioarvoineen sekä yhteisesiintymä- ja dispersiolukuineen. Vahvimmista
pareista on mukana myös yleisin pintamuoto (*juoda kahvia*, *villi kortti*) ja esimerkkilause
lähdetietoineen. Aineisto on poimittu 109 miljoonan sanen korpuksesta, joka koostuu Projekti
Lönnrotin tekijänoikeusvapaasta kirjallisuudesta (3 172 kirjaa) ja suomenkielisestä Wikipediasta
(125 811 artikkelia) ja on lemmatisoitu Voikolla. Samat tiedot näkyvät
[Asiasanasto.fi](https://asiasanasto.fi)-sanakirjan sana-artikkeleissa.

Tiedostot, sarakkeet ja menetelmä on kuvattu yllä englanniksi.

**Käyttö:** Aineistoa saa käyttää vapaasti mihin tahansa, myös kaupallisesti. Tilastot,
ilmaukset ja lemmalista ovat [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/deed.fi)
-lisenssin alaisia, ja lisenssi edellyttää vain lähteen mainitsemista. Helpoimmin se käy
lyhyellä maininnalla, jossa on linkki [Asiasanasto.fi](https://asiasanasto.fi)-sivustolle:

> Kollokaatioaineisto: [Asiasanasto.fi](https://asiasanasto.fi), CC BY 4.0

Esimerkkilauseissa on Wikipedian tekstiä, joten ne ovat
[CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/deed.fi) -lisenssin alaisia.
