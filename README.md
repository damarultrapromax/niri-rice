# Niri Anime Glassmorphism Rice

Rice desktop Wayland berbasis **niri** dengan shell **Noctalia** bergaya *anime glassmorphism*: panel kaca transparan + blur, bar kapsul di atas, dock mengambang dengan magnifikasi ala macOS, dan skema warna yang otomatis mengikuti wallpaper.

> **Catatan:** repo ini berisi file konfigurasi saja, bukan installer otomatis. Sesuaikan path, font, dan aplikasi dengan sistem kamu.

## Stack

| Komponen | Versi saat ini | Fungsi |
|---|---|---|
| [niri](https://github.com/YaLTeR/niri) | 26.04 | Kompositor Wayland tiling scrollable |
| [Noctalia](https://noctalia.dev) | 5.4.1 | Shell: bar, dock, launcher, control center, wallpaper, tema |
| [kitty](https://sw.kovidgoyal.net/kitty/) | 0.49.2 | Terminal utama (tema warna di-generate Noctalia) |
| foot | - | Terminal alternatif (fallback) |
| [fuzzel](https://codeberg.org/dnkl/fuzzel) | 1.15.0 | Application launcher |
| fish + starship | 4.9.3 / 1.27.0 | Shell + prompt |
| JetBrainsMono Nerd Font | 3.5.1 | Font terminal |
| Google Sans Flex | - | Font fuzzel (dari paket font illogical-impulse) |

## Struktur repo

```
.
├── niri/config.kdl                 # konfigurasi niri (keybind, layout, autostart)
├── noctalia/99-anime-glass.toml    # profil tema Anime Glassmorphism
├── kitty/                          # kitty.conf + script search/scroll
├── fuzzel/                         # fuzzel.ini + tema warna
├── foot/foot.ini                   # konfigurasi terminal foot
└── fish/                           # config.fish + generated key bindings
```

## Tampilan

- **Bar atas** — gaya kapsul, tinggi 40 px, opacity 62%, blur kompositor, border tipis `outline_variant`, radius 20, dengan margin samping 180 px.
- **Dock bawah** — ikon 56 px, magnifikasi 1.8x saat hover, smart auto-hide, indikator aplikasi berjalan, pin: kitty, Chrome, Dolphin, Spotify.
- **Launcher & control center** — mengambang di tengah, control center selebar 1200 px dengan label shortcut.
- **Wallpaper** — ambil warna dari wallpaper (`source = "wallpaper"`, scheme `soft`), transisi acak: fade, wipe, disc, stripes, zoom, honeycomb (1800 ms).
- **Sudut & shadow** — corner radius scale 1.25, shadow mengarah ke bawah dengan alpha 0.58.
- **niri** — gap antar window 16 px, focus ring 4 px berwarna `#7fc8ff`, border window dimatikan, screenshot tersimpan di `~/Pictures/Screenshots`.

## Keybind penting

`Mod` = **Super** (saat niri berjalan di TTY).

| Keybind | Aksi |
|---|---|
| `Mod + T` | Buka kitty |
| `Mod + D` | Buka fuzzel |
| `Mod + W` | Buka Google Chrome |
| `Mod + E` | Buka Dolphin |
| `Mod + Space` | Toggle launcher Noctalia |
| `Mod + S` | Toggle control center Noctalia |
| `Mod + Shift + ,` | Buka settings Noctalia |
| `Mod + O` | Toggle overview |
| `Mod + Q` | Tutup window |
| `Mod + H/J/K/L` atau panah | Fokus window |
| `Mod + Ctrl + H/J/K/L` | Pindah window |
| `Mod + U/I` | Workspace atas/bawah |
| `Mod + R` | Siklus lebar kolom (1/3, 1/2, 2/3) |
| `Mod + Shift + E` | Keluar dari niri (dengan konfirmasi) |
| `Super + Alt + L` | Lock screen (swaylock) |
| `Print` | Screenshot |

## Instalasi

### 1. Dependensi (Arch Linux)

```bash
sudo pacman -S niri noctalia kitty foot fuzzel fish starship \
  ttf-jetbrains-mono-nerd eza swaylock brightnessctl playerctl
```

Opsional (dipakai di dock/keybind): `google-chrome`, `dolphin`, `spotify`.
Font **Google Sans Flex** untuk fuzzel perlu dipasang manual (misalnya dari paket font *illogical-impulse*).

### 2. Backup konfigurasi lama

```bash
for d in niri noctalia kitty fuzzel foot fish; do
  [ -d "$HOME/.config/$d" ] && cp -r "$HOME/.config/$d" "$HOME/.config/$d.bak"
done
```

### 3. Salin konfigurasi

```bash
git clone https://github.com/damarultrapromax/niri-rice.git
cd niri-rice
mkdir -p ~/.config/{niri,noctalia,kitty,fuzzel,foot,fish/conf.d}
cp niri/config.kdl ~/.config/niri/
cp noctalia/99-anime-glass.toml ~/.config/noctalia/
cp kitty/* ~/.config/kitty/
cp fuzzel/* ~/.config/fuzzel/
cp foot/foot.ini ~/.config/foot/
cp fish/config.fish ~/.config/fish/
cp fish/conf.d/* ~/.config/fish/conf.d/
```

### 4. Sesuaikan

- Ganti `directory` wallpaper di `~/.config/noctalia/99-anime-glass.toml`:
  ```toml
  [wallpaper]
  directory = "/home/<user>/Pictures/Wallpapers/Anime"
  ```
- Ganti aplikasi `pinned` di bagian `[dock]` kalau tidak semua terpasang.
- Pastikan baris autostart ada di `~/.config/niri/config.kdl`:
  ```kdl
  spawn-at-startup "noctalia"
  ```
- Validasi konfigurasi Noctalia:
  ```bash
  noctalia config validate
  ```

### 5. Login ke sesi niri

Logout, lalu pilih sesi **niri** di display manager. Noctalia akan otomatis dijalankan oleh niri.

## Catatan

- Noctalia memuat **semua file `*.toml`** di `~/.config/noctalia/` secara alfabetis, lalu menimpa dengan `settings.toml` dari state dir. Prefix `99-` dipakai supaya profil ini dimuat paling akhir.
- `kitty/kitty.conf` meng-include tema hasil generate Noctalia:
  `~/.local/state/quickshell/user/generated/terminal/kitty-theme.conf`.
  Kalau tidak memakai Noctalia, comment baris `include` tersebut.
- `fish/config.fish` juga membaca warna terminal hasil generate Noctalia dari
  `~/.local/state/quickshell/user/generated/terminal/sequences.txt` (opsional).
- Konfigurasi output monitor di `config.kdl` sengaja dikomentari (`/-output "eDP-1"`), jadi resolusi/scale mengikuti pengaturan otomatis niri. Uncomment dan sesuaikan kalau perlu.
- Path di config memakai `/home/<user>`; ganti dengan username kamu.

## Kustomisasi cepat

| Ingin mengubah | File | Bagian |
|---|---|---|
| Warna & wallpaper | `noctalia/99-anime-glass.toml` | `[theme]`, `[wallpaper]` |
| Bar & dock | `noctalia/99-anime-glass.toml` | `[bar.default]`, `[dock]` |
| Gap & focus ring | `niri/config.kdl` | `layout { ... }` |
| Keybind | `niri/config.kdl` | `binds { ... }` |
| Prompt & alias | `fish/config.fish` | - |

## Credits

- [niri](https://github.com/YaLTeR/niri) — scrollable-tiling Wayland compositor
- [Noctalia](https://noctalia.dev) — desktop shell & theme engine
- [kitty](https://sw.kovidgoyal.net/kitty/), [fuzzel](https://codeberg.org/dnkl/fuzzel), [foot](https://codeberg.org/dnkl/foot)
- JetBrains Mono Nerd Font, Google Sans Flex

---

### About (English)

My personal **niri** rice with a **Noctalia** shell in an anime glassmorphism style: translucent blurred panels, a capsule top bar, a floating macOS-style magnifying dock, and wallpaper-driven colors. Config files only — adjust paths, fonts, and pinned apps before use. Tested on Arch Linux with niri 26.04 and Noctalia 5.4.1.
