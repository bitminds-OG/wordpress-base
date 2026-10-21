# wordpress-base

Self-built WordPress/PHP base images, so we can ship PHP security patches the day
php.net releases them — without waiting for the official `php` / `wordpress`
Docker Hub images to catch up.

## Images

| Image | Contents |
|---|---|
| `ghcr.io/bitminds-og/php:X.Y.Z-apache-trixie` | PHP apache image, built from source via [docker-library/php](https://github.com/docker-library/php) |
| `ghcr.io/bitminds-og/wordpress-base:W.A.B-phpX.Y.Z` | Official WordPress image recipe ([docker-library/wordpress](https://github.com/docker-library/wordpress)) on top of our php image |

## Usage

```dockerfile
FROM ghcr.io/bitminds-og/wordpress-base:7.1.3-php8.3.36
```

The base provides PHP + Apache + the standard WordPress extensions (mysqli, gd,
intl, zip, imagick, opcache, …) and `/usr/src/wordpress` incl. `wp-config-docker.php`.
It does **not** include `pdo_mysql` — install it in your project Dockerfile if needed.

## Releasing a security patch (e.g. the next PHP CVE)

1. Bump `PHP_VERSION` (+ `PHP_MINOR` if needed) in `.github/workflows/build.yml`
   and set `PHP_DOCKER_LIBRARY_SHA` to the docker-library/php commit containing it.
2. Update the `FROM` tag in `wordpress/Dockerfile`.
3. Actions → `build-base-images` → **Run workflow** (manual only; the PHP source
   compile takes ~30–40 min).
4. Point your projects at the new tag, rebuild, deploy.

`wordpress/Dockerfile`, `wordpress/wp-config-docker.php` and
`wordpress/docker-entrypoint.sh` are vendored from docker-library/wordpress —
re-copy them from upstream when bumping the WordPress version.
