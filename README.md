# Remo

YouTube playlists → Youtarr → local MP3s → Navidrome → Amperfy over WireGuard. Uses Docker Compose and MySQL.

1. If `.env` is missing, copy `.env.example` to `.env`. Set `DATABASE_PASSWORD` and `YOUTARR_ADMIN_PASSWORD` to separate passwords (generate each with `openssl rand -hex 24`). Set `PUID`/`PGID` to your `id -u` / `id -g`; this user must own `data/` and `media/`. Optionally set `HTTP_PORT` to change the host port from 80.
2. Start:

    ```bash
    chmod 600 .env
    docker compose up -d
    ```

3. Open **Navidrome** at `http://<VM-IP>/navidrome/` and create an admin account before downloading music.
4. Open **Youtarr** at `http://<VM-IP>/` using the admin credentials from `.env`:
    - For individual song artwork, set custom yt-dlp arguments to `--embed-thumbnail`. Navidrome will show embedded artwork per song.
    - Add a YouTube playlist, set its **Download Type** to **MP3 Only**, select existing videos to download and enable `Auto-download new videos`.
5. In **Amperfy**, add a **Subsonic** server at `http://<VM-IP>/navidrome` with a Navidrome account. Connect WireGuard when away; its routes must reach the VM.

Downloads live in `media/youtube/`, app state in `data/`, and MySQL data in a Docker volume. Empty directories are included via `.gitkeep`; runtime data is ignored by Git. Caddy serves both apps on host HTTP port 80 by default. If you change `HTTP_PORT`, include `:<HTTP_PORT>` after `<VM-IP>` in the Youtarr, Navidrome, and Amperfy URLs. Image versions are pinned in `docker-compose.yaml`.

Stop with `docker compose down`. Avoid `--volumes` unless you intend to delete the database.
