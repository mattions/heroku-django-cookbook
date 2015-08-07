Django and Heroku Cookbook
==========================

Collection of snippets and scripts that solve certain problems when deploying
Django apps to Heroku.

Scripts in the `bin` directory are post-compile hooks that are invoked by
the `heroku-buildpack-python`'s
[compile](https://github.com/heroku/heroku-buildpack-python/blob/master/bin/compile)
step. You can install them by copying the `bin` directory into the root
directory of your Heroku application repository.

Installing NodeJS and Less compiler for static assets compilation in your Django app
------------------------------------------------------------------------------------

Heroku provides a bunch of different
[buildpacks](https://devcenter.heroku.com/articles/buildpacks) that target many
popular platforms like Python, Ruby, NodeJS and Java web apps and backends.
While this is great and allows you to deploy virtually anything with a simple
git command, the out-of-the-box solutions offer a limited set of utilities
that are available during the
[Slug compilation](https://devcenter.heroku.com/articles/slug-compiler) phase.
In particular no NodeJS, NPM or [LESS Compiler](http://lesscss.org/)
is available in the `heroku-buildpack-python`. This means that there
is no straightforward way of compiling `.less` stylesheets during
the app deployment.

Fortunately, the Python buildpack provides hooks for running pre-compile
and post-compile scripts. This can be used for customizing the compilation
step and running additional commands without the necessity of maintaining
a separate fork of Heroku's buildpack.
The only thing you need to do is to create a proper `bin/post_compile` bash
script in the root directory of your application.

This is a FORK of https://github.com/nigma/heroku-django-cookbook to support 
django-pipeline instead of django-compressor.

It updates npm, install uglifyjs and cssmin

- `bin/post_compile` : Main script that gets called by the buildpack
- `bin/install_nodejs` : install nodejs (needed to manage bower and friends)
- `bin/install_less` : Install lss via node
- `bin/run_collectstatic`: Runs collect static. Compression happens here with django-pipeline
- `bin/install_bower` : Installs bower to manage all the dependencies
- `bin/install_cssmin` : CSS Minifier of choice
- `bin/install_uglifyjs` : JS Minifier of choice
- `.heroku/collectstatic_disabled` : Needed to avoid heroku re-running the collect static

Note: the empty ``/.heroku/collectstatic_disabled`` file deactivates the default collectstatic
build step that is part of the Heroku's buildpack. This will prevent the build script from doing
unnecessary work that is already handled by the above scripts.

