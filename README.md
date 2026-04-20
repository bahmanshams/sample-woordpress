# WordPress / WooCommerce Docker stack

Stack: `nginx` + `wordpress:php8.2-fpm` + `mariadb`

## Configuration

Copy `.env.example` to `.env` and replace every password and WordPress salt before starting the stack:

```bash
cp .env.example .env
```

For production behind Nginx Proxy Manager, set `APP_HOST` to the public hostname and `APP_SCHEME=https`. TLS terminates in Nginx Proxy Manager; this stack serves plain HTTP internally.

## Start locally

```bash
docker compose up -d
```

WordPress will be available at `http://localhost:8088` by default. Set `NGINX_PORT` in `.env` to the host port that Nginx Proxy Manager should forward to.

## Nginx Proxy Manager SSL

Create a Proxy Host in Nginx Proxy Manager with:

- Domain Names: your public WordPress hostname.
- Scheme: `http`.
- Forward Hostname / IP: the Docker host IP, such as `127.0.0.1` when Nginx Proxy Manager runs on the same host and can reach host-published ports.
- Forward Port: the value of `NGINX_PORT` from `.env`; default is `8088`.
- SSL: request or select the certificate in Nginx Proxy Manager, then enable Force SSL.

Nginx Proxy Manager sends `X-Forwarded-Proto: https`; the bundled Nginx config passes that through to PHP so WordPress treats the request as HTTPS.

## Stop

```bash
docker compose down
```

## Reset all local WordPress data

```bash
docker compose down
rm -rf db_data wordpress_data
mkdir -p db_data wordpress_data
```

## Notes

- Database credentials and WordPress salts are required through `.env`; do not commit real values.
- Runtime database files and generated WordPress files are intentionally ignored by git.
- Back up `db_data/` and `wordpress_data/` before production updates.
- Keep WordPress core, themes, and plugins patched before exposing the service publicly.
- Install WooCommerce from the WordPress admin UI after first boot, or manage plugins through a deployment process.
- To embed your local configurator, create a WordPress page and add a Custom HTML block with:

```html
<iframe
  src="http://127.0.0.1:5500/index.html"
  width="100%"
  height="1400"
  style="border:0;"
  loading="lazy"
></iframe>
```

If the iframe does not render, check whether the local static server on `127.0.0.1:5500` is sending `X-Frame-Options` or CSP headers.
