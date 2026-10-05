# laden-docker

The Laden AS business site (Norwegian and English pages, portfolio, videos) served from an nginx container.

Live Docker copy: [https://laden.no/docker/laden/](https://laden.no/docker/laden/)

Copyright Laden AS (Org.nr. 937 285 833). The code is open source under the MIT License. See [LICENSE](LICENSE). The Laden name, mark and brand are trademarks of Laden AS and are not given away by that licence.

## What is here

- `docker-compose.yml` runs one `web` service (nginx 1.27 alpine), read-only, with `no-new-privileges`.
- `Dockerfile` copies `nginx.conf` and the `site/` folder into the image.
- `nginx.conf` serves only `/docker/laden/`. Every other path returns 404.
- `site/docker/laden/` is the site exactly as the live container serves it.
- `deploy/compose.live.yml` is the Compose file the live stack on the Laden VPS uses (`/opt/laden-docker`). There the HTML sits in the external named volume `laden_site` instead of being baked into the image.

## Run it

```bash
cp .env.example .env   # optional, only to change the port
docker compose up -d --build
```

Open [http://127.0.0.1:18083/docker/laden/](http://127.0.0.1:18083/docker/laden/). /docker/laden/ redirects to /docker/laden/en/.

The port is `18083` on `127.0.0.1` by default. Change `HOST_PORT` (and `BIND_ADDR`) in `.env` to use something else.

Stop it with `docker compose down`.

## Host proxy

The container speaks plain HTTP and only listens on localhost. On the live server the host web server (Apache on the Laden VPS) owns the domain and the HTTPS certificate and proxies `https://laden.no/docker/laden/` to `http://127.0.0.1:18083/docker/laden/`. Put your own Nginx, Apache or Caddy in front in the same way if you run this on a public host. Do not publish the port to `0.0.0.0` without a proxy in front.

## Not in this repo

No secrets. This stack needs none: no `.env` with real values, no keys, no database.
