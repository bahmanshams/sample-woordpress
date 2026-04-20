# WordPress / WooCommerce Docker stack

Stack: `nginx` + `wordpress:php8.2-fpm` + `mariadb`

## Configuration

Copy `.env.example` to `.env` and replace every password and WordPress salt before starting the stack:

```bash
cp .env.example .env
```

For production, set `APP_HOST` to the public hostname, set `APP_SCHEME=https`, and terminate TLS in front of this stack or expose it through a secured reverse proxy.

## Start locally

```bash
docker compose up -d
```

WordPress will be available at `http://localhost` by default. For local development on a different port, set `NGINX_PORT=8088` in `.env`.
The internal Docker hostname for `cp-api` is `http://woo-local.local/`.

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
