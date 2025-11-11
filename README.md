# MacOS 使用西电 LaTeX 模板说明

此仓库验证通过的最小工作流：**安装字体 → 确保 MacTeX 2025 → 运行 `sudo tlmgr update --self --all` → 依照下述命令编译**。若任何环节跳过，最常见问题是图片位置异常、PDF 盒子错位或宏包缺失。

---

## 1. 环境要求（MacTeX 2025）
1. **字体**：按照 [Font/Readme.md](./Font/Readme.md) 安装 Windows 版宋/黑/楷体。
2. **检查版本**：
   ```bash
   /Library/TeX/texbin/xelatex --version | head -1
   ```
   - 输出含 `TeX Live 2025`：环境已指向 2025。
   - 不是 2025：继续看第 3 步。
3. **安装/切换教程**：见 [MacTex_Installation_Settings/Readme.md](./MacTex_Installation_Settings/Readme.md)。若 `/usr/local/texlive/2025` 不存在，请按文档下载 `https://mirror.tuna.tsinghua.edu.cn/ctan/systems/mac/mactex/MacTeX.pkg` 并安装；若已安装多个版本，进入 `MacTex_Installation_Settings/` 目录执行 `./switch-texlive.sh 2025` 切换软链接。
4. **更新 tlmgr（必做）**：
   ```bash
   sudo tlmgr update --self --all
   ```
   任何安装或切换后都要执行一次，以获得最新 `xdvipdfmx` 与宏包。

---

## 2. 命令行编译流程
在项目根目录：
```bash
# 编译 main.tex（XeLaTeX + BibTeX）
latexmk -xelatex -bibtex -synctex=1 -interaction=nonstopmode main.tex

# 清理临时文件（保留 .bbl）
latexmk -c

# 完全清理（连 .bbl 一并删除）
latexmk -C
```

> 若 PDF 需要留在根目录，可在 `latexmk` 完成后创建软链接 `ln -sf build/main.pdf ./main.pdf`（配合进阶方案）。

---

## 3. TeXstudio 快速配置
菜单：`Options → Configure TeXstudio → Build`
- `Default Compiler`：`XeLaTeX`
- `Default Bibliography Tool`：`BibTeX`
- `PDF Viewer`：Internal PDF Viewer (Embedded)
- `Quick Build`：改为 `txs:///latexmk`
- `Commands → Latexmk`：
  ```
  latexmk -xelatex -bibtex -synctex=1 -interaction=nonstopmode %.tex
  ```

可在 **Tools → User Commands** 中添加：
```text
latexmk -c %.tex        # Clean
latexmk -C %.tex        # Clean Full
```
确保 TeXstudio 顶部 Root 文档已经锁定为 `main.tex`。

---

## 4. 进阶：生成物集中到 build/
想让仓库更整洁，可将输出与辅助文件放在 `build/` 和 `build/aux/`：

```bash
latexmk -xelatex -bibtex -synctex=1 \
  -outdir=build \
  -auxdir=build/aux -emulate-aux-dir \
  -interaction=nonstopmode main.tex

latexmk -c -outdir=build -auxdir=build/aux -emulate-aux-dir
latexmk -C -outdir=build -auxdir=build/aux -emulate-aux-dir
```

> `-emulate-aux-dir` 需要 TeX Live 2025，新版本的 latexmk 已内置。

### 可选 latexmkrc
在项目根新建 `latexmkrc`：
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
之后只需 `latexmk` / `latexmk -C`。

---

## 5. 目录与忽略建议
```
build/
build/aux/
figures/
```
- `.gitignore` 建议添加：
  ```
  build/
  *.synctex.gz
  *.xdv
  ```
- 需要常看 PDF，可 `ln -sf build/main.pdf ./main.pdf`。
- 图片统一放在 `figures/`（每章自带 `chX/xdulogo.pdf` 占位），可按章节新增/替换素材并在正文中使用 `\includegraphics{figures/chX/<文件名>}`。

---

## 6. 常见提示
- `PDF file is tagged...` / `Object @page.n already defined.`：为旧图像 PDF 的 metadata，保持 TeX Live 最新即可，少量图像可在 `\includegraphics` 中补 `pagebox=cropbox` 或 `trim=..`。
- `Missing character: There is no ， in font cmr12!`：中文标点不要置于数学模式，可写 `\text{，}`。

---

## 7. XDUTS 简介
XDUTS（Xidian University TeX Suite）为西电本科/研究生论文模板，支持 XeLaTeX 于 Windows/macOS/Linux/Overleaf。仓库包含：
- `xdufont.sty` 字体配置包
- `xdupgthesis.cls` / `xduugthesis.cls` / `xduugtp.cls` 等类文件

更多细节、模板参数与完整说明请阅读 `xduts.pdf`；同时可参考上游项目：[XDUTS on GitHub](https://github.com/note286/xduts) / [CTAN](https://www.ctan.org/pkg/xduts)。

---

### 一句话工作流
1. `sudo tlmgr update --self --all`
2. `latexmk -xelatex -bibtex -synctex=1 main.tex`
3. `latexmk -c` 或 `latexmk -C`
