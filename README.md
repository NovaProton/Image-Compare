# Image Compare

Check whether two images really are the same, both **byte-for-byte** and **pixel-by-pixel**.

It's handy for answering "is this render or export actually different, or did it just get a new file name?"

The whole tool is a single `index.html` with no dependencies, no build step and no server code. Everything runs in your browser, so **nothing is uploaded**.

## Using it

1. Open the page, either on GitHub Pages or by opening `index.html` straight from your computer.
2. Drop an image on **Image A** and another on **Image B**, or click a box to choose a file.
3. The result appears straight away.

### What the result means

| Verdict | Meaning |
|---|---|
| **Identical** | The files are byte-for-byte the same, so every pixel matches too. |
| **Identical pixels** | Every pixel matches but the file bytes differ, usually because of metadata or compression. |
| **Different** | The dimensions don't match, or at least one pixel differs. |

### What it reports

- **SHA-256 A / B:** a fingerprint of each file. If they match, the files are identical.
- **Files byte-identical:** Yes or No.
- **Dimensions:** the width × height of each image.
- **Pixels that differ:** the count and the percentage of the image.
- **…by more than 10 / 30:** how many of those differences are big enough to see, as opposed to tiny compression noise. Values run from 0 to 255.
- **Largest channel difference:** the biggest change found in any single red, green, blue or alpha value.
- **Changed region:** the box `(x1, y1, x2, y2)` that contains every changed pixel.
- **Difference map:** shown only when something has changed. Black means a match and brighter means a bigger difference. The **Amplify** slider makes faint differences easier to see.

## How it works

1. **Byte check:** each file is hashed with SHA-256 using the browser's built-in Web Crypto API.
2. **Pixel check:** both images are drawn to a canvas, then every pixel is compared across R, G, B and A, and the largest of the four differences is recorded.

Matching hashes mean identical images. Different hashes **don't** always mean different pictures, which is why the pixel check is there as well.

### Limitations

- Images with different dimensions are reported as different, without a pixel comparison.
- The browser decodes images into sRGB, so for colour-managed files (such as those with embedded ICC profiles) pixel values can be very slightly off. The SHA-256 check is always exact.
- The image formats you can use are whatever your browser supports (PNG, JPEG, WebP, GIF, AVIF, etc.).

## Hosting on GitHub Pages

1. Add `index.html` (and this README) to a repository.
2. Go to **Settings → Pages → Build and deployment**, choose **Deploy from a branch**, and select `main` / `(root)`.
3. After a minute or so it will be live at `https://<username>.github.io/<repo>/`.

## Quick check without the page

If you only need to know whether two files are identical:

```bash
sha256sum a.png b.png                 # Linux
shasum -a 256 a.png b.png             # macOS
Get-FileHash a.png, b.png             # Windows PowerShell
```