# pterodactyl-theme (snapshot pribadi)

Snapshot beku dari tema panel + patch lokal. **Repo ini tidak di-update dari upstream.**

- Upstream: https://github.com/alfathzero/pterodactyl-theme
- Commit upstream terakhir yang dipakai: `77ca625` (2026-11-14)
- Snapshot dibuat: 2026-11-17

## Patch lokal (beda dari upstream)

### 1. `leaderboard/app/Console/Commands/CollectLeaderboardCommand.php`
Server dengan state Wings `starting` ikut dihitung **online** (bukan cuma
`running`). Alasannya: banyak egg bot tidak pernah mencetak string startup
detection (`config_startup.done`), jadi Wings menahan state `starting` walau
container sudah Up berjam-jam. Efek di server uji: 3 online -> 16 online
(16 = jumlah container yang benar-benar Up).

### 2. `widgets/app/Http/Controllers/WidgetController.php`
Widget dashboard ambil status **live** dari Wings `GET /api/servers`
(1 request per node, bukan 1 per server), di-cache **5 detik**.
- Online = state `running` atau `starting`.
- Kalau Wings tidak bisa dihubungi, otomatis fallback ke `storage/app/leaderboard.json`.
- Data per-user (widget "Server Saya") juga pakai data live, lengkap memory%/disk%.

### 3. `assets/custom-widgets.js` + `templates/base-core.blade.php`
- Polling widget naik dari 60 detik -> **10 detik** (`POLL_MS = 10000`).
- Versi asset dinaikkan ke `custom-widgets.js?v=6` supaya browser tidak pakai
  cache lama.

## Catatan pasang

```bash
PANEL_DIR=/var/www/pterodactyl ./install.sh        # tema + widget + leaderboard + musik
PANEL_DIR=/var/www/pterodactyl ./chat-install.sh   # chat room (3 tabel DB + cron prune)
```

Setelah **update panel** Pterodactyl, jalankan ulang `install.sh` (dan
`chat-install.sh` kalau pakai chat) karena blade + `routes/base.php` ke-overwrite.

## Kredensial
Jangan simpan token GitHub di repo ini.
