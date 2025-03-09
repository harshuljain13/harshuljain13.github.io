## Local setup using Docker (Recommended)

Using Docker to install Jekyll and Ruby dependencies is the easiest way.

```bash
$ bundle install
$ docker compose pull
$ docker compose up
$ bundle exec jekyll serve --trace
```

## Deployment
```bash
$ git push origin main
```