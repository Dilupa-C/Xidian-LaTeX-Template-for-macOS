<div align="center">

# Xidian LaTeX Template Guide for macOS

[![LaTeX](https://img.shields.io/badge/LaTeX-TeX%20Live%202025-008080.svg)](https://www.tug.org/texlive/) [![License](https://img.shields.io/badge/License-MIT-blue.svg)](./LICENSE) [![macOS](https://img.shields.io/badge/macOS-Compatible-success.svg)](https://www.apple.com/macos/)

[🇨🇳 中文](./README.md) | [🇺🇸 English](./README_EN.md)

</div>


## 🙏 Acknowledgements

This project is based on **[XDUTS](https://github.com/note286/xduts)** (Xidian University TeX Suite). Special thanks to **[wjm-jimmy](https://github.com/wjm-jimmy)** for contributing the template.

---

## 📖 Overview

The minimal verified workflow for this repository: **Install fonts → Ensure MacTeX 2025 → Run `sudo tlmgr update --self --all` → Compile following the commands below**. Skipping any step may cause common issues like misplaced images, PDF box errors, or missing packages.


## 1. Environment Requirements (MacTeX 2025)
1. **Fonts**: Install Windows-style Song/Hei/Kai fonts following [Font/Readme.md](./Font/Readme.md).
2. **Check Version**:
   ```bash
   /Library/TeX/texbin/xelatex --version | head -1
   ```
   - If output contains `TeX Live 2025`: Environment is already pointing to 2025.
   - If not 2025: Continue to step 3.
3. **Installation/Switching Guide**: See [MacTex_Installation_Settings/Readme.md](./MacTex_Installation_Settings/Readme.md). If `/usr/local/texlive/2025` doesn't exist, download from `https://mirror.tuna.tsinghua.edu.cn/ctan/systems/mac/mactex/MacTeX.pkg` and install. If multiple versions are installed, navigate to `MacTex_Installation_Settings/` and run `./switch-texlive.sh 2025` to switch symbolic links.
4. **Update tlmgr (Required)**:
   ```bash
   sudo tlmgr update --self --all
   ```
   This must be executed after any installation or switching to get the latest `xdvipdfmx` and packages.

---

## 2. Command-Line Compilation
In the project root directory:
```bash
# Compile main.tex (XeLaTeX + BibTeX)
latexmk -xelatex -bibtex -synctex=1 -interaction=nonstopmode main.tex

# Clean temporary files (keep .bbl)
latexmk -c

# Full clean (remove .bbl too)
latexmk -C
```

> To keep the PDF in the root directory, create a symbolic link after `latexmk` completes: `ln -sf build/main.pdf ./main.pdf` (works with advanced setup).

---

## 3. TeXstudio Quick Configuration
Menu: `Options → Configure TeXstudio → Build`
- `Default Compiler`: `XeLaTeX`
- `Default Bibliography Tool`: `BibTeX`
- `PDF Viewer`: Internal PDF Viewer (Embedded)
- `Quick Build`: Change to `txs:///latexmk`
- `Commands → Latexmk`:
  ```
  latexmk -xelatex -bibtex -synctex=1 -interaction=nonstopmode %.tex
  ```

You can add in **Tools → User Commands**:
```text
latexmk -c %.tex        # Clean
latexmk -C %.tex        # Clean Full
```
Ensure the Root document in TeXstudio is locked to `main.tex`.

---

## 4. Advanced: Centralize Build Output to build/
For a cleaner repository, place output and auxiliary files in `build/` and `build/aux/`:

```bash
latexmk -xelatex -bibtex -synctex=1 \
  -outdir=build \
  -auxdir=build/aux -emulate-aux-dir \
  -interaction=nonstopmode main.tex

latexmk -c -outdir=build -auxdir=build/aux -emulate-aux-dir
latexmk -C -outdir=build -auxdir=build/aux -emulate-aux-dir
```

> `-emulate-aux-dir` requires TeX Live 2025, newer versions of latexmk have it built-in.

### Optional latexmkrc
Create `latexmkrc` in the project root:
```perl
$pdf_mode = 1;
$pdflatex = 'xelatex -interaction=nonstopmode -file-line-error -synctex=1 %O %S';
$bibtex   = 'bibtex %O %S';
$out_dir         = 'build';
$aux_dir         = 'build/aux';
$emulate_aux_dir = 1;
$clean_ext       = 'synctex.gz xdv run.xml';
$clean_full_ext  = 'bbl';
```
After this, simply run `latexmk` / `latexmk -C`.

---

## 5. Directory and .gitignore Suggestions
```
build/
build/aux/
figures/
```
- Recommended additions to `.gitignore`:
  ```
  build/
  *.synctex.gz
  *.xdv
  ```
- To frequently view PDFs, use `ln -sf build/main.pdf ./main.pdf`.
- Place all images in `figures/` (each chapter has its own `chX/xdulogo.pdf` placeholder). Add/replace materials by chapter and reference them in text using `\includegraphics{figures/chX/<filename>}`.

---

## 6. Common Issues
- `PDF file is tagged...` / `Object @page.n already defined.`: These are metadata from old PDF images. Keep TeX Live updated; for individual images, add `pagebox=cropbox` or `trim=..` in `\includegraphics`.
- `Missing character: There is no ， in font cmr12!`: Chinese punctuation should not be in math mode. Use `\text{，}` instead.

---

## 7. About XDUTS
XDUTS (Xidian University TeX Suite) is a LaTeX template for undergraduate and graduate theses at Xidian University, supporting XeLaTeX on Windows/macOS/Linux/Overleaf. The repository includes:
- `xdufont.sty` - Font configuration package
- `xdupgthesis.cls` / `xduugthesis.cls` / `xduugtp.cls` - Document class files

For more details, template parameters, and complete documentation, please read `xduts.pdf`. Also refer to the upstream project: [XDUTS on GitHub](https://github.com/note286/xduts) / [CTAN](https://www.ctan.org/pkg/xduts).

---

## ⚡ Quick Workflow
1. `sudo tlmgr update --self --all`
2. `latexmk -xelatex -bibtex -synctex=1 main.tex`
3. `latexmk -c` or `latexmk -C`

---

## 📄 License

See [LICENSE](./LICENSE) file for details.
