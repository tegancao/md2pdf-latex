# md2pdf-latex

Turn a Markdown file into a **LaTeX-typeset PDF**, with real TeX math, sensible tables and wrapped code, in one
command. It also writes the generated `.tex` next to the PDF, so the PDF's exact source can be kept and recompiled.

```
python3 md2pdf.py notes.md notes.pdf        # writes notes.tex and notes.pdf
```

See [`example/example.md`](example/example.md) and the result, [`example/example.pdf`](example/example.pdf).

## What it handles

- **Math:** `$inline$` and `$$display$$` math, typeset by TeX.
- **Tables sized from their content:** tables that fit keep their natural width; wide tables give every column at
  least its longest word (so long names never overlap) and share the rest of the line, switching to a smaller font if
  needed. (By default, pandoc sizes columns from the length of the `|---|` separators, which often squeezes or overflows.)
- **Long code lines wrap** instead of running off the page.
- **Colored blocks:** `<div style="color:#RRGGBB">` around Markdown becomes colored text (handy for answers vs. questions).
- **Figures:** a `**Label:** ![](image.png)` line keeps the label and the centered figure together on one page.
- **Title and contents:** the first `# Heading` becomes the document title; documents with several `#` headings also
  get a table of contents with page numbers.
- **Unicode:** arrows, symbols, accents, Japanese and more, through a chain of fallback fonts.

## Requirements

- [pandoc](https://pandoc.org) (`brew install pandoc`), or pass the path to a pandoc binary as the third argument.
- A TeX installation with `lualatex`, e.g. [MacTeX or BasicTeX](https://tug.org/mactex/). BasicTeX lacks three
  packages this needs (`fvextra`, `lualatex-math`, `xurl`); they're included in [`texmf/`](texmf) and found
  automatically, so nothing has to be installed with `tlmgr`.
- Python 3 (standard library only).

Tested on macOS (Apple Silicon) with pandoc 3.12 and BasicTeX 2023. The fallback fonts in
[`header.tex`](header.tex) (STIX Two, Hiragino Sans, Apple Symbols, Menlo, Arial Unicode) ship with macOS; on Linux
or Windows, replace them with fonts you have (e.g. Noto).

## How it works

1. **pandoc** converts the Markdown to a standalone `.tex` file, with a Lua filter ([`filter.lua`](filter.lua)) that
   sizes table columns, wraps code blocks, colors `<div>` blocks and keeps labeled figures together, plus a preamble
   ([`header.tex`](header.tex)) with fonts and packages.
2. **LuaLaTeX** compiles that `.tex` twice (so the table of contents and page references settle).

To recompile a kept `.tex` without pandoc, from the `.tex` file's folder:

```
TEXINPUTS=<this repo>/texmf//: LUAINPUTS=<this repo>/texmf//: lualatex FILE.tex     # run twice
```

## License

MIT (see [LICENSE](LICENSE)) for this tool's own code. The files in [`texmf/`](texmf) are unmodified builds of the
CTAN packages [fvextra](https://ctan.org/pkg/fvextra), [lualatex-math](https://ctan.org/pkg/lualatex-math) and
[xurl](https://ctan.org/pkg/xurl), distributed under the LaTeX Project Public License (LPPL).
