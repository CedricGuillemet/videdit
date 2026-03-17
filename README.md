# Video Trimmer

A browser-based video trimming and cropping tool. Upload a video, select a time range, adjust the crop area, and export as MP4 or GIF — all client-side with no server required.

## Features

- **Drag & drop** or click-to-browse file upload (`.mp4` / `.mov`)
- **Thumbnail timeline** generated from the video for visual navigation
- **Draggable start/end handles** to precisely select the trim range
- **Play Selection** button to preview the trimmed segment
- **Crop editor** with 8 draggable anchor handles (corners + edges) overlaid on the video
- **Keep aspect ratio** checkbox — when enabled, the crop area maintains the video's native aspect ratio
- **Save as MP4** — trims (and crops if needed) using stream copy or re-encoding
- **Save as GIF** — generates an optimized GIF with palette generation
- **Client-side processing** powered by [FFmpeg.wasm](https://github.com/ffmpegwasm/ffmpeg.wasm) — nothing is uploaded to a server
- **GitHub Pages deployment** via included GitHub Actions workflow

## Usage

1. Open `index.html` in a modern browser (Chrome, Edge, or Firefox).
   - Serve locally with any static server, e.g.:
     ```bash
     npx serve .
     ```
   - Or simply double-click the file (some browsers may restrict WASM loading from `file://`; use a local server if that happens).
2. Drop a `.mp4` or `.mov` file onto the upload zone (or click to browse).
3. Drag the **purple handles** on the thumbnail timeline to set start/end times.
4. Drag the **green crop box** or its handles to adjust the crop area. Toggle **Keep aspect ratio** as needed.
5. Click **Play Selection** to preview, or **Save MP4** / **Save GIF** to export.
6. Wait for FFmpeg to load (first use only) and process the video. The file downloads automatically.

## Requirements

- A modern browser with **SharedArrayBuffer** support (Chrome 92+, Edge 92+, Firefox 79+).
- If serving locally, the page needs the `Cross-Origin-Opener-Policy` and `Cross-Origin-Embedder-Policy` headers for SharedArrayBuffer. Most simple dev servers handle this. If you encounter issues, use:
  ```bash
  npx serve --cors .
  ```

## How It Works

| Step | Detail |
|------|--------|
| Upload | The video is read entirely in the browser via `URL.createObjectURL`. |
| Thumbnails | A hidden `<video>` element seeks through the file and draws frames to `<canvas>` elements. |
| Cropping | A draggable overlay with 8 anchor points defines the crop region in normalized coordinates. |
| Trimming (MP4) | FFmpeg.wasm uses `-c copy` when no crop is applied, or re-encodes with `-vf crop=...` when cropping. |
| GIF Export | A two-pass pipeline: first generates an optimal palette, then encodes the GIF using `paletteuse`. |
| Download | The output bytes are wrapped in a `Blob` and triggered as a browser download. |

## Deployment

A GitHub Actions workflow is included at `.github/workflows/deploy.yml`. It automatically deploys the site to GitHub Pages when you push to the `main` branch.

To enable it:
1. Go to your repo **Settings → Pages**.
2. Set **Source** to **GitHub Actions**.
3. Push to `main` — the site will be live at `https://<username>.github.io/<repo>/`.

## Tech Stack

- Vanilla HTML / CSS / JavaScript (ES modules)
- [FFmpeg.wasm 0.12](https://github.com/ffmpegwasm/ffmpeg.wasm) loaded from CDN

## License

MIT
