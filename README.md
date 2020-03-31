# ETESync Sever Docker Images

Docker image for [ETESync](https://www.etesync.com/) based on the [server](https://github.com/etesync/server) repository by [Tom Hacohen](https://github.com/tasn).

## Tags

The following tags are built on latest python image and master branch of ETESync Server 

- `latest` [(master:tags/latest/Dockerfile)](https://github.com/victor-rds/docker-etesync-server/blob/master/tags/base/Dockerfile)
- `slim`  [(master:tags/slim/Dockerfile)](https://github.com/victor-rds/docker-etesync-server/blob/master/tags/slim/Dockerfile)
- `alpine` [(master:tags/debian/Dockerfile)](https://github.com/victor-rds/docker-etesync-server/blob/master/tags/alpine/Dockerfile)

Starting on v0.3.0 ther will be builds base stable published version of ETESync

- `0.3.0` [(v0.3.0:tags/latest/Dockerfile)](https://github.com/victor-rds/docker-etesync-server/blob/master/tags/base/Dockerfile)
- `0.3.0-slim`  [(v0.3.0:tags/slim/Dockerfile)](https://github.com/victor-rds/docker-etesync-server/blob/master/tags/slim/Dockerfile)
- `0.3.0-alpine` [(v0.3.0:tags/debian/Dockerfile)](https://github.com/victor-rds/docker-etesync-server/blob/master/tags/alpine/Dockerfile)

## Usage

```docker run  -d -e SUPER_USER=admin -e SUPER_PASS=changeme -p 80:3735 -v /path/on/host:/data victorrds/etesync```

Create a container running ETESync usiong http protocol.

## Volumes

`/data`: database file location

## Ports

This image exposes the **3735** TCP Port

## Environment Variables

- **SERVER**: Defines how the container will serve the application, the options are:
  - `http` Runs using HTTP protocol, this is the default mode.
  - `https` same as above but with TLS/SSL support, see below how to use with your own certificates.
  - `uwsgi` start using uWSGI native protocol, for reverse-proxies/load balances, such as _nginx_, that support this protocol
  - `http-socket` Similar to the first option, but without uWSGI HTTP router/proxy/load-balancer, this recommended for any reverse-proxies/load balances, that support HTTP protocol, like _traefik_
  - `django-server` this mode uses the embedded django http server, `./manage.py runserver :3735`, this is not recommeded but can be useful for debugging
- **SUPER_USER** and **SUPER_PASS**: Username and password of the django superuser (only used if no previous database is found, both must be used together);
- **SUPER_EMAIL**: Email of the django superuser (optional, only used if no database is found);
- **PUID** and **PGID**: set user and group when running using uwsgi, default: `1000`;
- **ETESYNC_DB_PATH**: Location of the ETESync SQLite database. default: `/data` volume;

## Settings and Customization

Custom settings can be added to `/etesync/etesync_site_settings.py`, this file overrides the default `settings.py`, mostly for _Django: The Web framework_ options, this image uses the variables below to set some of these options.

### _Environment Variables on `/etesync/etesync_site_settings.py`_

- **ALLOWED_HOSTS**:  the ALLOWED_HOSTS settings, must be valid domains separated by `,`. default: `*` (not recommended for production);
- **DEBUG**: enables Django Debug mode, not recommended for production defaults to False;
- **LANGUAGE_CODE**: Django language code, default: `en-us`;
- **SECRET_FILE**: Defines file that contains the value for django's `SECRET_KEY` if not found a new one is generated. default: `/etesync/secret.txt`.
- **USE_TZ**: Force Django to use time-zone-aware datetime objects internally, defaults to `false`;
- **TIME_ZONE**: time zone, defaults to `UTC`;

### _Using uWSGI with HTTPS_

If you want to run ETESync Server HTTPS using uWSGI you need to pass certificates or the image will generate a self-sign certificate for `localhost`.

By default ETESync will look for the files `/certs/crt.pem` and `/certs/key.pem`, if for some reason you change this location change the **X509_CRT** and **X509_KEY** environment variables

### _Serving Static Files_

When behind a reverse-proxy/http server compatible `uwsgi` protocol the static files are located at `/var/www/etesync/static`, files will be copied if missing on start.