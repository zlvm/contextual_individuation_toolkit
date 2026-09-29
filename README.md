# Toolkit for Measuring Contextual Individuation in Transformer Language Models

Does a transformer language model **individuate** a word's use by context,
even when the word itself is held fixed?

The full design rationale for every choice this toolkit makes is documented
in its companion manuscript, *Technical Manual for a Toolkit for Measuring
Contextual Individuation in Transformer Language Models.

 [![arXiv](https://img.shields.io/badge/arXiv-2609.05333-b31b1b?logo=arxiv&logoColor=white)](https://arxiv.org/abs/2609.05333)

A **bridge form** is a single written word (grapheme) that occurs, unchanged,
across two or more subject areas with a different sense each time - e.g.
`current` as "electric current" (physics), "current account" (finance) and
"ocean current" (physical geography). Because every occurrence of a bridge
form shares the same entry in the model's vocabulary, the model's *static*
embedding layer cannot tell the occurrences apart: it assigns them, up to
positional noise, the same vector. Any separation that appears in later
layers can only come from **context**, not from the word type itself - that
is the structural guarantee the whole design rests on.

All of the analysis lives in a single notebook,
[`contextual_individuation.ipynb`](contextual_individuation.ipynb).

## Method, in brief

1. Declare, per bridge form, which subject areas it should bridge and which
   Wikipedia category supplies each area's text.
2. Fetch a corpus of article introductions from those categories.
3. Locate every occurrence of each bridge form in that corpus.
4. Run a transformer model and keep the hidden state of the bridge form's own
   token, at every layer, for every occurrence.
5. Measure separation between subject areas with the silhouette coefficient
   (cosine distance), layer by layer, one subject-area pair at a time.
6. Visualise the same occurrences with a shared 2D PCA projection, at the
   first and last layer, alongside the silhouette curves.
7. Collect, per bridge form and subject-area pair, the peak pairwise
   silhouette (and a permutation-based null at that layer) into a results
   table, exported as `results_table.csv` and `results_table.tex`.

The notebook itself documents every step and every design decision in
markdown cells immediately above the code that implements it - read it top
to bottom for the full account.

## Setup

```bash
git clone <this repository>
cd contextual-individuation
python -m venv .venv
source .venv/bin/activate   # or .venv\Scripts\activate on Windows
pip install -r requirements.txt
```

Copy `.env.example` to `.env` and fill in a real contact address:

```bash
cp .env.example .env
```

The Wikipedia API asks, as etiquette, for a descriptive `User-Agent` with a
contact address on it - `EMAIL` is used only for that header, sent only to
`en.wikipedia.org`, and is never written into the notebook itself.

## Running

Open `contextual_individuation.ipynb` and run all cells top to bottom. The
notebook processes **one model at a time**: change the `MODEL_INDEX`
constant in the model-selection cell to compare a different architecture
(BERT, RoBERTa, DistilBERT, DeBERTa, GPT-2, or Pythia-410m), then re-run.
Running every candidate model in a single pass is deliberately not
automated, given the computational cost of loading and running several
transformer models in sequence.

To run on Google Colab, uncomment the `!pip install` line in the first code
cell instead of using `requirements.txt` locally.

## Bridge forms

Only `current` is active by default. Nineteen further candidates are
declared, commented out, in the notebook's bridge-forms cell, each with a
proposed Wikipedia category per subject area - these are informed proposals,
not yet volume-checked, and are meant to be activated one at a time following
the same discipline `current` went through (fetch, inspect the article
count, confirm the category is not near-empty, only then trust the panel).

## License

Licensed under [CC BY 4.0](LICENSE) - free to use, share, and adapt,
including for auditing the tool against the paper it accompanies, with
attribution.

## DOI

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.22213867.svg)](https://doi.org/10.5281/zenodo.22213867)
