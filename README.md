# Remo

YouTube playlists → Youtarr → local MP3s → Navidrome → Amperfy over WireGuard. Uses Docker Compose and MySQL.

1. If `.env` is missing, copy `.env.example` to `.env`. Set `DATABASE_PASSWORD` and `YOUTARR_ADMIN_PASSWORD` to separate passwords (generate each with `openssl rand -hex 24`). Set `PUID`/`PGID` to your `id -u` / `id -g`; this user must own `data/` and `media/`.
2. Start:

    ```bash
    chmod 600 .env
    docker compose up -d --wait --wait-timeout 180
    ```

3. Open **Navidrome** at `http://<VM-IP>/navidrome/` and create an admin account before downloading music.
4. Open **Youtarr** at `http://<VM-IP>/` using the admin credentials from `.env`:
    - Set Default Subfolder to `music`.
    - In Settings, set custom yt-dlp arguments to `--embed-thumbnail --parse-metadata "%(title)s:%(meta_album)s"` so each new MP3 has its own cover art and album in Navidrome.
    - Add a YouTube playlist, set its **Download Type** to **MP3 Only**, and select existing videos to download.
    - Enable automatic downloads, playlist following, and a schedule for new tracks. Leave automatic removal disabled.
5. In **Amperfy**, add a **Subsonic** server at `http://<VM-IP>/navidrome` with a Navidrome account. Connect WireGuard when away; its routes must reach the VM.

Downloads live in `media/youtube/`, app state in `data/`, and MySQL data in a Docker volume. Empty directories are included via `.gitkeep`; runtime data is ignored by Git. Caddy serves both apps on HTTP port 80; port 80 must be free on the host. Image versions are pinned in `docker-compose.yaml`.

Stop with `docker compose down`. Avoid `--volumes` unless you intend to delete the database.
