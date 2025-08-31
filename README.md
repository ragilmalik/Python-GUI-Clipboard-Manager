# Python-GUI-Clipboard-Manager


# Ragilmalik Clipboard Manager (Windows)

A fast, lightweight, **privacy-friendly** clipboard history manager for Windows with a modern UI, **drag-to-reorder**, **Quick Paste Palette**, snipping, exports, and optional OCR. Built with **PySide6 + SQLite** and packaged with **PyInstaller**.

> **Why this exists**
> Because hitting `Ctrl+C` twice and losing the first copy is pain. This app remembers **everything** (text, URLs, images), makes it **searchable**, lets you **pin your top 10**, and gives you a **one-tap paste palette** right where you work.

---

## ✨ Highlights

* **Unlimited history** (text, URLs, images).
* **Auto-capture** from the system clipboard — nothing manual to do.
* **De-dupe with SHA-256**: duplicates aren’t re-added; the original **bumps to the top** (below pins).
* **Pin up to 10** favorites to the top.
* **Quick Paste Palette**: a tiny, draggable popup to search/choose and copy instantly.

  * **Keep on top** toggle (remembered).
  * **Remembers last position** (with off-screen safety).
  * Opens centered; never steals focus from your active app.
* **Views**: Active / Archived / Deleted (Trash).

  * Deleted stays forever until you permanently delete.
* **Table UI** with 2 columns (**Item** + **Time**), checkboxes per row, thin 1px grid for readability.
* **Sorting**: Newest, Oldest, **Custom (drag order)** — drag items and the order persists via DB.
* **Details**: Double-click an item for a **bigger view** (full text or image preview + Copy).
* **Export**: CSV / TXT / XLSX, plus **JPG** for images; if exporting multiple files, creates a folder
  `Clipboard Export Items - {timestamp}` and puts **everything** in there.
* **Backup & Restore**: one-click backup of DB + images + settings; restore anytime.
* **Themes**: **Dark (default)** pure black with cyan outlines, or **Light** pure white with blue outlines.
  Theme applies **app-wide** (including the Quick Palette).
* **Tray icon** + **autostart** (minimize to tray on boot) toggle.
* **Snipping Tool**: multi-monitor aware, capture any region into history.
* **OCR (optional)** for images/snips via Tesseract; OCR text is **searchable**.
* **Scalable design**: SQLite model, clean repo layer, and settings JSON. Easy to extend.

> **Tested on Windows 10/11.** Most features are Windows-first (tray, autostart). The core UI is Qt and portable.

---

## 📦 Download

* **Windows EXE (one-file)** → see **Releases** on your GitHub repo once you publish it.
  *Suggested tag*: `v1.0.0`
* **Source code** → this repository.

> **Tip for maintainers:** Add CI to build the EXE via PyInstaller and upload to Releases automatically.

---

## 🛠️ Install (Developers)

### 1) Requirements

* **Python 3.9+** (Windows 10/11)
* **Pip** up-to-date

### 2) Install dependencies

```bash
python --version
pip install --upgrade pip
pip install PySide6 openpyxl pillow pytesseract
```

> `pillow` + `pytesseract` are only required if you want **OCR**. The app runs fine without them.

### 3) Run

```bash
python clipboard_manager.py
```

### 4) Build a one-file EXE

```bash
pip install pyinstaller
pyinstaller --noconsole --onefile --name "RagilmalikClipboardManager" clipboard_manager.py
```

The EXE will be in `dist/`.

---

## 🔍 Optional: Enable OCR for images/snips

1. **Install the Tesseract OCR engine for Windows** (the actual executable, not just the Python package).

   * Common install paths auto-detected:

     * `C:\Program Files\Tesseract-OCR\tesseract.exe`
     * `C:\Program Files (x86)\Tesseract-OCR\tesseract.exe`
2. In the app: **Tools → Settings… → Auto OCR images** (or run **Tools → OCR Selected Images** on demand).
3. Anything OCR’d becomes searchable immediately.

> If Tesseract is installed in a non-standard path, open an issue — a path picker in Settings is on the roadmap.

---

## 🚀 Features (Full)

* **Capture everything**: text, URLs, images (PNG).
* **No duplicates**: SHA-256 is used to detect duplicates; existing item is bumped up.
* **Pin (up to 10)**: keep your most used clippings on top.
* **Auto sorting**: Newest / Oldest; **Custom** uses DB `order_index` stored on every drag.
* **Robust reordering**: dragging to very top/bottom works; no blank rows, no disappearing items.
* **Views**:

  * **Active** (working set), **Archived** (keep but hide), **Deleted** (Trash, permanent delete available).
* **Find fast**: live search filters results (includes OCR text for image items).
* **Quick Paste Palette**:

  * Centers on current screen, draggable, optional always-on-top, remembers last position.
  * Independent of the main window; doesn’t cause the main window to appear.
  * Type to filter, Enter/double-click to copy.
* **Detail viewer**: double-click to open a big, readable view with Copy.
* **Export**: CSV, TXT, XLSX; images to JPG (92% quality).

  * Exporting **multiple items** creates the folder `Clipboard Export Items - {timestamp}` with all files inside.
* **Backup/Restore**: portable backups of DB + images + settings.
* **Themes**: pure black/white with cyan/blue outlines; applies app-wide.
* **Tray**: show/hide, quick actions (palette, snip, toggle theme), **autostart** toggle.
* **Snipping**: multi-monitor, translucent overlay, keyboard-free capture.

---

## 🧱 Architecture at a Glance

* **Persistence**: `SQLite` database at `%APPDATA%\RagilmalikClipboardManager\clipboard.db`

  * Table: `items(id, type, text, image_path, mime, sha256, pinned, status, created_at, updated_at, order_index, ocr_text)`
  * **`order_index`** drives **Custom (drag order)**; higher values float to the top of a group.
  * **Pinned** vs **Unpinned** are ordered independently in the Active view.
* **Images**: stored in `%APPDATA%\RagilmalikClipboardManager\clip_images\` as PNG.
* **Settings**: JSON at `%APPDATA%\RagilmalikClipboardManager\settings.json`

  * Includes `auto_ocr_images`, `autostart_on_login`, `quick_palette_always_on_top`, `quick_palette_geom`, and more.
* **UI**: PySide6 widgets, `QTableWidget` for a simple, performant table with checkboxes + row selection.
* **Clipboard watcher**: debounced listener; safely suppressed when we programmatically set clipboard content to avoid feedback loops.
* **System tray**: quick actions + notifications. **Autostart** uses HKCU `Run` registry on Windows.

---

## 🔐 Privacy & Safety

* Data is **local-only**: all clips, images, and settings live on your machine.
* You can **archive**, **delete**, or **permanently delete** anything.
* **Tip**: consider adding your password manager domain/app to an **ignore list** (feature on the roadmap).

---

## 🧩 Roadmap

* Global hotkeys (e.g., **Ctrl+Shift+V** to open Quick Paste Palette at caret).
* Per-app/regex **ignore list** (don’t capture secrets).
* Tagging + smart filters.
* ZIP backups with integrity manifests.
* OCR language packs and per-item OCR on demand.

> Have a feature idea? Open an issue or PR — the codebase is designed to be extended.

---

## 🐞 Troubleshooting

* **OCR prompt even after `pip install`**: you need the **Tesseract engine** (the `.exe`). Install it and re-try.
* **Palette won’t stay on top**: check **Keep on top** in the palette; it persists.
* **App starts visible**: enable **Start with Windows (minimize to tray)** in Settings.
* **Build fails for XLSX**: install `openpyxl`:

  ```bash
  pip install openpyxl
  ```

---

## 🧪 For Contributors

* Keep the **repo layer** (`SqliteRepo`) decoupled: add columns via migrations in `ensure_schema()` and expand the dataclass.
* Avoid mutating the table UI directly on reorder — **persist first**, then refresh.
* Theme changes should be **app-wide** (`QApplication.setStyleSheet`).
* Prefer **pure functions** and **single-responsibility** methods to keep the code testable.

---

## 📜 License

MIT — do whatever you want; a credit is appreciated.

---

## ❤️ Credits

* Built with **PySide6**, **SQLite**, and a lot of ☕.
* OCR powered by **Tesseract** (optional).

---

### Quick Start (Windows one-liners)

```bash
python --version
pip install --upgrade pip
pip install PySide6 openpyxl pillow pytesseract
python clipboard_manager.py
```

Build EXE:

```bash
pip install pyinstaller
pyinstaller --noconsole --onefile --name "RagilmalikClipboardManager" clipboard_manager.py
```
