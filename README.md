# Still Life — A Curated Gallery

A small, hand-built gallery viewer for still-life photography and paintings, styled like a quiet museum wall: gilded frames, mat boards, plaque-style captions, and a click-to-zoom lightbox.

🔗 **Live:** [benchvue.github.io/still-life](https://benchvue.github.io/still-life/)

---

## File structure

```
still-life/
│   index.html
│   README.md
│
└───images/
    └───2026_05_13/
            azalea-1.webp
            azalea-2.webp
            forsythia-1.webp
            tulip-1.webp
            tulip-2.webp
```

Each new collection lives in its own dated subfolder under `images/` (format `YYYY_MM_DD`).

---

## Adding a new piece

Open `index.html` and copy any existing `<figure class="piece">` block. Change the four data points:

```html
<figure class="piece"
        data-title="Your Title Here"
        data-date="Plate VI · 20 May 2026"
        data-src="images/2026_05_20/your-image.webp">
  <div class="frame">
    <div class="mat">
      <div class="image-wrap">
        <img src="images/2026_05_20/your-image.webp"
             alt="Description"
             loading="lazy" decoding="async">
      </div>
    </div>
  </div>
  <figcaption class="plaque">
    <div class="title">Your Title Here</div>
    <div class="date">Plate VI · 20 May 2026</div>
    <svg class="ornament"><use href="#orn"/></svg>
  </figcaption>
</figure>
```

That's it — no JavaScript edits needed. The grid auto-flows.

---

## Converting images to WebP (Windows / PowerShell)

PNG and JPEG photos are usually 5–15 MB each, which makes the gallery slow to load. **WebP at quality 82** typically shrinks them to 100–400 KB with no visible loss. This project uses WebP everywhere.

### One-time setup — install ImageMagick

```powershell
winget install ImageMagick.ImageMagick
```

**Important:** after installation, **close and reopen your PowerShell window**. The `magick` command won't be recognized in the session that did the install — PATH only refreshes for new shells.

Verify it works:

```powershell
magick --version
```

You should see version output. If you get *"not recognized"*, refresh PATH in the current session:

```powershell
$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")
```

### Convert a folder of PNG/JPEG to WebP

Navigate into the image folder, then run one command:

```powershell
cd C:\Users\benchvue\Documents\still-life\images\2026_05_13
magick mogrify -format webp -quality 82 -resize "1600x1600>" *.png
```

```
magick mogrify -format webp -quality 82 -resize "1600x1600>" *.JPG
```

What each flag does:

| Flag | Meaning |
|---|---|
| `-format webp` | Output format |
| `-quality 82` | Visually lossless for photos. Drop to `75` for smaller files, raise to `90` for more fidelity. |
| `-resize "1600x1600>"` | Shrink to fit **1600 px on the long edge**, only if larger. The `>` is what makes it conditional. |
| `*.png` | Source files. Change to `*.jpg` for JPEGs. |

The originals are left untouched — WebP copies appear next to them.

### Delete the originals once you've verified the WebP files

```powershell
Remove-Item *.png
```

(Or `*.jpg` if you started from JPEGs.)

### Quick sanity check

```powershell
dir
```

Each WebP file should be **under ~400 KB**. If anything is still multi-MB, lower quality or max dimension:

```powershell
magick mogrify -format webp -quality 75 -resize "1200x1200>" *.png
```

---

## Optional: separate thumbnail and full-size versions

For galleries with many images or very large source files, generate two sizes — a smaller one for the grid, a larger one for the lightbox:

```powershell
# Thumbnails for the grid — 800 px, quality 78
magick mogrify -path . -format webp -quality 78 -resize "800x800>" -filename-format "%t-thumb.webp" *.png

# Full size for the lightbox — 1800 px, quality 85
magick mogrify -path . -format webp -quality 85 -resize "1800x1800>" -filename-format "%t-full.webp" *.png
```

Then in `index.html`, point the grid `<img src>` to `-thumb.webp` and `data-src` (which the lightbox uses) to `-full.webp`.

---

## Publishing changes

```powershell
cd C:\Users\benchvue\Documents\still-life
git add .
git commit -m "Add May 20 collection"
git push
```

GitHub Pages picks up the new files within a minute or two.

---

## Stack

- Pure HTML + CSS + a tiny bit of JS for the lightbox.
- No build step, no framework, no dependencies.
- Fonts: *Italiana*, *Cormorant Garamond*, *Inter* (Google Fonts).
- Hosted on GitHub Pages.

---

*MMXXVI*
