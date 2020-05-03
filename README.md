# Docker Image for [webtrees](https://webtrees.github.io/)

[![](https://img.shields.io/docker/cloud/build/flowolf/webtrees.svg?style=popout)](https://hub.docker.com/r/flowolf/webtrees)
[![](https://images.microbadger.com/badges/image/flowolf/webtrees.svg)](https://microbadger.com/images/flowolf/webtrees)
[![](https://images.microbadger.com/badges/version/flowolf/webtrees.svg)](https://microbadger.com/images/flowolf/webtrees)
[![](https://images.microbadger.com/badges/license/flowolf/webtrees.svg)](https://microbadger.com/images/flowolf/webtrees)

This is an up-to-date Docker image for
[webtrees](https://github.com/fisharebest/webtrees) served over HTTP,
designed to be put behind a reverse proxy such as Cloudflare.

## Fork for Raspberry Pi

this repo was forked from [nathanvaughn](https://github.com/NathanVaughn/webtrees-docker), to make it run on raspberry pi.

The repo has not really changed much, just the build process is different ([build process](https://www.docker.com/blog/getting-started-with-docker-for-arm-on-linux/))


## Usage

### Quickstart

An example `docker-compose.yml` file is provided for reference.

You can launch the example with `docker-compose up -d`.

Once you start the container, you can visit
[http://localhost:1000/setup.php](http://localhost:1000/setup.php) and begin the
[setup wizard](https://wiki.webtrees.net/en/Installation#Server_configuration_check).

### Network

The image exposes port 80.

Example `docker-compose`:

```yml
  ports:
    - 80:80
```

### Volumes

The image mounts:
  - `/var/www/webtrees/data/`
  - `/var/www/webtrees/media/`

If you want to add custom themes, you can also mount the
`/var/www/webtrees/themes/` directory.

Example `docker-compose`:

```yml
  volumes:
    - app_data:/var/www/webtrees/data/
    - app_media:/var/www/webtrees/media/
    - app_themes:/var/www/webtrees/themes/

...

volumes:
  app_data:
    driver: local
  app_media:
    driver: local
  app_themes:
    driver: local
```

### MySQL

webtrees requires a MySQL (or compatible equivalent) database.
You will need a separate container for this.

- [MariaDB](https://hub.docker.com/_/mariadb)
- [MySQL](https://hub.docker.com/_/mysql)

## Tags

> **🚨⚠ WARNING ⚠🚨**
If you use the **beta** version of webtrees, you will ***NOT*** be able to use the
1.X.X stable version again. The database schema between the stable and beta versions are
very different, and this is a one-way operation. Make a full backup before choosing
to try the beta version of webtrees so that you can roll back if needed.

### Latest
Currently, the tag `latest` is available for the latest
stable version of webtrees.

Example:

```yml
image: flowolf/webtrees:latest
```

## Issues

New releases of the Dockerfile are automatically generated from upstream
webtrees versions. This means a human does not vette every release. While
I try to stay on top of things, sometimes breaking issues do occur. If you
have any, please feel free to fill out an
[issue](https://github.com/NathanVaughn/webtrees-docker/issues).

## Reverse Proxy Issues

webtrees does not like running behind a reverse proxy, and depending on your setup,
you may need to adjust some database values manually.

For example, if you are accessing webtrees via a reverse proxy serving content
over HTTPS, but using this HTTP container, you would need to make the following
changes in your database:

```sql
mysql -u webtrees -p

use webtrees;
update wt_site_setting set setting_value='https://example.com/login.php' where setting_name='LOGIN_URL';
update wt_site_setting set setting_value='http://example.com/' where setting_name='SERVER_URL';
quit;
```

## Inspiration
The Dockerfile is heavily based off
[solidnerd/docker-bookstack](https://github.com/solidnerd/docker-bookstack).
