# jacrquarto

A Quarto extension that formats manuscripts for the **Journal of the
Association for Consumer Research (JACR)**, published by the University of
Chicago Press. It produces submission-ready PDF and Word (DOCX) output from a
single `.qmd` source, and is a companion to
[jcrquarto](https://github.com/mmourali/jcrquarto).

The formatting encoded here is drawn from the JACR
[Guidelines for Authors](https://www.journals.uchicago.edu/journals/jacr/instruct).

## Two rendering modes

Set `jacr-mode` in the YAML front matter (or override on the command line):

- `manuscript` (default): submission layout with a title/abstract page,
  numbered pages, and references on a new page.
- `article`: single-column publication/title-page layout (11pt), with the
  author note rendered as a first-page footnote and optional issue/volume
  information at the top.

```bash
quarto render template.qmd                       # manuscript mode (default)
quarto render template.qmd -M jacr-mode:article  # article mode
```

## Installation

Copy the `_extensions/jacrquarto` folder into your project, or install from
GitHub once published:

```bash
quarto use template mmourali/jacrquarto
```

Then reference the format in your document:

```yaml
format:
  jacrquarto-pdf: default
  jacrquarto-docx: default
```

## Citations and references

JACR uses the Association for Consumer Research / JCR house reference format,
**not** standard Chicago author-date. The bundled CSL
(`journal-of-the-association-for-consumer-research.csl`) reproduces it:

- In-text citations are author last name and year with no punctuation between
  them: `(Kinsey 1960)`; page numbers go inside the parentheses with no "p."
  (`(Andreasen 1984, 785)`).
- Multiple citations are listed **chronologically**, separated by semicolons.
- Three authors are cited in full on first mention, then as "et al."; four or
  more authors always use "et al."
- Reference-list entries carry the year in parentheses after the authors, with
  the periodical name unabbreviated and the issue given by month or season, for
  example: Belk, Russell W. (2013), "Extended Self in a Digital World,"
  *Journal of Consumer Research,* 40 (October), 477-500.

Supply references via a `.bib` file and the `bibliography` key.

### Testing reference formats

`reference-test.qmd` renders every tricky reference type so the output can be
compared to the guideline examples; `bibliography.bib` contains matching test
fixtures.

```bash
quarto render reference-test.qmd --to jacrquarto-pdf
```

Verified to match the JACR examples: journal articles (issue by month), books,
book chapters (`in Title, Vol. N, ed. Editor, Place: Publisher, pages`), edited
collections, government publications, websites, newspapers, reprints with an
original date (`1899/1979`), and works with four or more authors (in-text "et
al.", up to seven names then "et al." in the list). In-text behavior is also
verified: two authors always both, three authors full on first mention then
"et al.", multiple citations ordered chronologically, page locators without
"p.", and same-author-same-year disambiguation (1988a, 1988b).

Known limitations, where a rendered entry may need manual adjustment: working
papers do not print the report number or series; unpublished dissertations do
not print the "unpublished dissertation" genre label; and presented/conference
papers do not print the presentation venue held in the entry's note. For these
three types, verify the rendered entry against the guidelines and edit the
`.bib` note fields as needed.

## Headings

The extension applies JACR's heading scheme in manuscript mode:

- Level 1 (`#`): flush left, capitalized, bold.
- Level 2 (`##`): mixed case, flush left, italic.
- Level 3 (`###`): mixed case, flush left, bold, run in with a period.

## Tables, figures, and notes

Captions are set above and labeled `TABLE N` / `FIGURE N`. Tables use
`booktabs` rules with no shading (JACR asks authors to avoid shading, which is
incompatible with its editing software). Color figures are encouraged but must
retain contrast in grayscale.

Custom classes:

- Table/figure notes:

  ```markdown
  ::: {.table-notes}
  *NOTE.*---Text of the note.
  :::
  ```

- Figure notes: any paragraph beginning with `NOTE.` is styled automatically.
- Hypotheses: wrap in `::: {.hypothesis} ... :::` for an indented bold block.

## Front matter and blinding

JACR review is double blind. In manuscript mode the document begins with the
title and abstract only, with no author information, which suits the blinded
review copy. In article mode the title block adds authors and the author note
(affiliations and acknowledgments), matching the "essential title page
information" required for accepted manuscripts: issue volume and number, title,
author names, abstract, affiliations, and acknowledgments.

Set the author note and optional issue information with:

```yaml
jacr-author-note: |
  ... affiliations, positions, addresses, e-mails, and acknowledgments ...
jacr-publication-info: "Journal of the Association for Consumer Research, Vol. 11, No. 3"
```

Keywords are optional; JACR guidelines do not specify a keyword count. They are
rendered only when supplied.

## Online appendix

JACR does **not** print appendices. Put appendix material in a separate online
appendix file (`web-appendix.qmd` is a starter) constrained to 10 MB.
Information essential to the paper should appear alongside the main text as a
table instead. The `.appendix` class (page break before an H1) is available for
use inside the appendix document.

## JACR requirements reflected here

- Total length up to **8,000 words including references** (excluding figure
  captions, tables, and online appendices). The filter does not count words.
- **Abstract fewer than 150 words**; the filter warns at render time if
  exceeded. The abstract need not be in the third person.
- **Numbered pages** and clearly marked sections. The accepted-manuscript file
  is double-spaced (the default here); for the review submission JACR
  encourages 1.5 spacing and figures embedded near the related text. To switch,
  set `linestretch: 1.5`.
- **Footnotes discouraged**, maximum of four; the filter warns past four.
- References single-spaced with a hanging indent, beginning on a new page.

## Style points the extension cannot enforce

JACR specifies prose conventions you must apply yourself, including: no zero
before the decimal in probabilities (`p < .05`); italicize `p, F, M, df, r, t`
but not Greek letters or `SD`/`NS`; use `x` (not `*`) for interactions; report
`F(1, 139) = 34.65, p < .05`; spell out numbers under 10 except with units or
percentages; `%` after numerals; one space after sentences and colons; a serial
comma; no underlining; and no ampersands. See the Guidelines for Authors for
the full list.

## Files

```
_extensions/jacrquarto/
  _extension.yml                                        format definitions (pdf, docx)
  jacrquarto.lua                                        front matter, notes, appendix, validation
  jacr-header.tex                                       LaTeX preamble and mode setup
  journal-of-the-association-for-consumer-research.csl  citation style (ACR/JCR house format)
  jacr-reference.docx                                   DOCX reference document
  partials/                                             LaTeX template partials
template.qmd                                            worked example (main manuscript)
web-appendix.qmd                                        online appendix template
reference-test.qmd                                      reference-format test harness
bibliography.bib                                        example references and test fixtures
```

## Notes and caveats

- The DOCX reference document (`jacr-reference.docx`) is carried over from
  jcrquarto as a starting point. Open it in Word to fine-tune DOCX styles.
- The CSL is the JCR style with its title metadata relabeled for JACR; because
  JACR follows the same ACR house format, the reference and citation output
  matches the JACR examples. Verify a rendered reference list against the
  current guidelines before final submission.

## License

MIT. See `LICENSE`. The CSL is derived from the Citation Style Language project
and is licensed CC BY-SA 3.0.
