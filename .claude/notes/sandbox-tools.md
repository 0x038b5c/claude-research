# Installed Tools & Packages

## System Tools (non-standard, notable)
| Tool | Version/Notes |
|------|--------------|
| `gh` | GitHub CLI — authenticated via GH_TOKEN |
| `age` | Modern encryption tool — used for secrets |
| `git` | With SSH signing configured |
| `ffmpeg` | 6.1.1 — full codec support (x264, x265, av1, opus, etc.) |
| `imagemagick` | 6.9.12 (legacy Q16) |
| `graphviz` | dot/neato etc. |
| `pandoc` | 3.1.3 (+lua, -server) |
| `pdftk-java` | PDF toolkit |
| `libreoffice` | 24.2.7 — full suite: Writer, Calc, Impress, Draw, Base |
| `tesseract` | 5.3.4 OCR |
| `texlive` | base + latex-extra + xetex + science + pictures + lang-greek |
| `unoserver` | LibreOffice UNO server for document conversion |
| `gcc/g++` | 13.x |
| `java` | default-jre-headless |
| `node` | present (/usr/bin/node) |
| `python3` | 3.12 |
| `strace/gdb` | NOT present |
| `nmap/tcpdump` | NOT present |
| `curl/wget/nc` | present |

## Python Packages (pip, key ones)
### Document / File Processing
- `python-docx`, `python-pptx`, `openpyxl`, `xlsxwriter`
- `pypdf`, `pypdfium2`, `pdfminer.six`, `pdfplumber`, `pikepdf`, `pdf2image`, `img2pdf`, `pdfkit`
- `camelot-py`, `tabula-py` (PDF table extraction)
- `odfpy`, `markitdown`, `pandoc`
- `reportlab`

### Vision / Image / Media
- `Pillow` (12.x), `Wand` (ImageMagick binding)
- `opencv-python`, `opencv-contrib-python`, `opencv-python-headless`
- `scikit-image`
- `imageio`, `imageio-ffmpeg`
- `mediapipe` (0.10.33 — Google MediaPipe for pose/face/hands)
- `pytesseract` (Tesseract OCR binding)
- `magika` (Google file type detection ML)

### ML / Data Science
- `numpy`, `scipy`, `pandas` (3.x!), `scikit-learn`
- `matplotlib`, `seaborn`
- `sympy`, `mpmath`
- `onnxruntime` (1.24.4 — run ONNX models)
- `tensorflow`? — `absl-py`, `flatbuffers`, `protobuf` present (TF deps)
- `networkx` (graph algorithms)

### Web / Automation
- `playwright` (1.56.0) — headless browser automation
- `beautifulsoup4`, `lxml`, `requests`
- `Flask`, `Werkzeug`
- `grip` (GitHub-flavored markdown preview server)
- `mkdocs`, `mkdocs-material`

### Audio
- `sounddevice` (audio I/O — likely no audio hardware but library present)

### Utilities
- `click`, `python-frontmatter`, `python-dotenv`
- `graphviz` (Python binding), `matplotlib`
- `psutil`, `cryptography`
- `pyoo` (LibreOffice Python UNO bridge)
- `tabulate`, `backrefs`

## npm Global Packages
| Package | Version |
|---------|---------|
| `@mermaid-js/mermaid-cli` | 11.12.0 — CLI mermaid renderer |
| `docx` | 9.6.1 |
| `playwright` | 1.56.0 |
| `pptxgenjs` | 4.0.1 |
| `pdf-lib` | 1.17.1 |
| `pdfjs-dist` | 5.6.205 |
| `sharp` | 0.34.5 — image processing |
| `typescript` | 6.0.3 |
| `tsx` | 4.21.0 |
| `ts-node` | 10.9.2 |
| `react` + `react-dom` | 19.2.5 |
| `react-icons` | 5.6.0 |
| `marked` | 18.0.2 |
| `markdown-pdf` | 11.0.0 |
| `markdownlint-cli` + `cli2` | latest |
| `remark-cli` | 12.0.1 |
| `graphviz` | 0.0.9 |
| `markdown-toc` | 1.2.0 |

## Playwright Browsers
Installed at `/home/claude/.cache/` and `/usr/local/lib/python3.12/dist-packages/playwright`
Both Python and Node playwright present — full headless browser capability.

## What's Notably ABSENT
- No `nmap`, `tcpdump`, `wireshark` — no network scanning
- No `strace`, `gdb`, `ltrace` — no debugging tools
- No GPU/CUDA (single vCPU, no `/dev/nvidia*`)
- No audio hardware (sounddevice installed but no hw)
- No `docker` / container tooling inside the VM
