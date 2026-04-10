# nginx-fancy-index-hook

A drop-in dark-themed directory listing UI for nginx's `autoindex` module. No server-side scripts — pure HTML/CSS/JS injected via `ngx_http_addition_module`.

<img width="3360" height="1892" alt="image" src="https://github.com/user-attachments/assets/438c614f-24b0-459a-abc0-c1824c35764e" />
<img width="3360" height="1892" alt="image" src="https://github.com/user-attachments/assets/70b80635-a5ee-4117-b282-e86f2c6a2798" />

## Features

- Dark theme with animated gradient background
- File type badges (EFI, iPXE, kPXE, LKRN, image, audio, …)
- Audio player with ID3 tag display (title, artist, album, artwork) — reads only the first 256 KB per file
- Video player with autoplay
- Image gallery with prev/next navigation, zoom, auto-gallery mode for image-only folders
- Grid and list view toggle
- Search/filter bar (searches filenames and ID3 metadata)
- Multi-select with ZIP download (client-side, via JSZip)
- Inline file preview panel: Markdown rendering, syntax-highlighted code (highlight.js, 40+ languages), hex viewer for binaries, PE/EFI header parser
- Git repo detection: reads `.git/HEAD`, branch, last commit, remote origin, and last 5 commits from the git log — works in subfolders too
- Keyboard navigation (↑ ↓ Space Esc ← →)
- Breadcrumb path navigation
- Samba-friendly (bind to a specific interface for LAN-only access)

## Requirements

- nginx compiled with `ngx_http_addition_module` (included in most distros)

Check:
```sh
nginx -V 2>&1 | grep addition
```

## Installation

1. Copy `templates/` to your nginx root (e.g. `/var/www/html/templates/`):

```sh
cp -r templates/ /var/www/html/templates/
```

2. Add to your nginx server block (see `nginx-example.conf`):

```nginx
location /files/ {
    alias /var/www/files/;
    autoindex on;
    autoindex_exact_size off;
    autoindex_localtime on;
    add_before_body /templates/nginx-before.txt;
    add_after_body  /templates/nginx-after.txt;
}

# Protect templates — never serve directly
location /templates/ {
    internal;
}
```

3. Reload nginx:

```sh
nginx -t && systemctl reload nginx
```

## Git repo browsing

If a directory contains a `.git` folder, the UI automatically fetches branch, latest commit, remote origin, and the last 5 commits from `.git/logs/`. This works in subdirectories too — it walks up parent directories to find the repo root and shows a back-navigation link.

nginx does not list dotfiles/dotdirectories in `autoindex` output, but the template fetches `.git/HEAD` directly, so no special nginx configuration is needed.

## Dependencies (loaded from CDN)

| Library | Purpose |
|---|---|
| [htmx](https://htmx.org) | Minimal fetch helpers |
| [marked](https://marked.js.org) | Markdown rendering |
| [jsmediatags](https://github.com/nicolo-ribaudo/jsmediatags) | ID3 tag reading |
| [JSZip](https://stuk.github.io/jszip/) | Client-side ZIP generation |
| [highlight.js](https://highlightjs.org) | Syntax highlighting |
| [pako](https://github.com/nodeca/pako) | (optional) zlib for git object reading |

All loaded from cdnjs / unpkg — no build step required.

## License

MIT
