## Local setup using Docker (Recommended)

Using Docker to install Jekyll and Ruby dependencies is the easiest way.

```bash
$ docker compose pull
$ docker compose up
```

Note that when you run it for the first time, it will download a docker image of size 400MB or so. To see the template running, open your browser and go to `http://localhost:8080`. You should see a copy of the theme's demo website.

### Deploy on [Netlify](https://www.netlify.com/)

1. [Use this template -> Create a new repository](https://github.com/new?template_name=al-folio&template_owner=alshedivat).
2. Netlify: **Add new site** -> **Import an existing project** -> **GitHub** and give Netlify access to the repository you just created.
3. Netlify: In the deploy settings

   - Set **Branch to deploy** to `main`
   - **Base directory** is empty
   - Set **Build command** to `sed -i "s/^\(baseurl: \).*$/baseurl:/" _config.yml && bundle exec jekyll build`
   - Set **Publish directory** to `_site`

4. Netlify: Add the following two **environment variables**

   - | Key            | Value                                                                                  |
     | -------------- | -------------------------------------------------------------------------------------- |
     | `JEKYLL_ENV`   | `production`                                                                           |
     | `RUBY_VERSION` | set to the Ruby version found in `.github/workflows/deploy.yml` (for example, `3.3.5`) |

5. Netlify: Click **Deploy** and wait for the site to be published. If you want to use your own domain name, follow the steps in [this documentation](https://docs.netlify.com/domains-https/custom-domains/).

### Deployment to another hosting server (non GitHub Pages)

If you decide to not use GitHub Pages and host your page elsewhere, simply run:

```bash
$ bundle exec jekyll build
```

which will (re-)generate the static webpage in the `_site/` folder.
Then simply copy the contents of the `_site/` directory to your hosting server.

If you also want to remove unused css classes from your file, run:

```bash
$ purgecss -c purgecss.config.js
```

which will replace the css files in the `_site/assets/css/` folder with the purged css files.

**Note:** Make sure to correctly set the `url` and `baseurl` fields in `_config.yml` before building the webpage. If you are deploying your webpage to `your-domain.com/your-project/`, you must set `url: your-domain.com` and `baseurl: /your-project/`. If you are deploying directly to `your-domain.com`, leave `baseurl` blank, **do not delete it**.

### Deployment to a separate repository (advanced users only)

**Note:** Do not try using this method unless you know what you are doing (make sure you are familiar with [publishing sources](https://help.github.com/en/github/working-with-github-pages/about-github-pages#publishing-sources-for-github-pages-sites)). This approach allows to have the website's source code in one repository and the deployment version in a different repository.

Let's assume that your website's publishing source is a `publishing-source` subdirectory of a git-versioned repository cloned under `$HOME/repo/`.
For a user site this could well be something like `$HOME/<user>.github.io`.

Firstly, from the deployment repo dir, checkout the git branch hosting your publishing source.

Then from the website sources dir (commonly your al-folio fork's clone):

```bash
$ bundle exec jekyll build --destination $HOME/repo/publishing-source
```

This will instruct jekyll to deploy the website under `$HOME/repo/publishing-source`.

**Note:** Jekyll will clean `$HOME/repo/publishing-source` before building!

The quote below is taken directly from the [jekyll configuration docs](https://jekyllrb.com/docs/configuration/options/):

> Destination folders are cleaned on site builds
>
> The contents of `<destination>` are automatically cleaned, by default, when the site is built. Files or folders that are not created by your site will be removed. Some files could be retained by specifying them within the `<keep_files>` configuration directive.
>
> Do not use an important location for `<destination>`; instead, use it as a staging area and copy files from there to your web server.

If `$HOME/repo/publishing-source` contains files that you want jekyll to leave untouched, specify them under `keep_files` in `_config.yml`.
In its default configuration, al-folio will copy the top-level `README.md` to the publishing source. If you want to change this behavior, add `README.md` under `exclude` in `_config.yml`.

**Note:** Do _not_ run `jekyll clean` on your publishing source repo as this will result in the entire directory getting deleted, irrespective of the content of `keep_files` in `_config.yml`.

## Upgrading from a previous version

If you installed **al-folio** as described above, you can manually update your code by following the steps below:

```bash
# Assuming the current directory is <your-repo-name>
$ git remote add upstream https://github.com/alshedivat/al-folio.git
$ git fetch upstream
$ git rebase v0.13.4
```

If you have extensively customized a previous version, it might be trickier to upgrade.
You can still follow the steps above, but `git rebase` may result in merge conflicts that must be resolved.
See [git rebase manual](https://help.github.com/en/github/using-git/about-git-rebase) and how to [resolve conflicts](https://help.github.com/en/github/using-git/resolving-merge-conflicts-after-a-git-rebase) for more information.
If rebasing is too complicated, we recommend re-installing the new version of the theme from scratch and port over your content and changes from the previous version manually. You can use tools like [meld](https://meldmerge.org/)
or [winmerge](https://winmerge.org/) to help in this process.
