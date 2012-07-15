# Heroku Buildpack: Monit

Run [Monit](http://mmonit.com/monit/) on Heroku.

Based on the [null-buildpack][null-buildpack] by [Ryan Smith][ryandotsmith].

[null-buildpack]: https://github.com/ryandotsmith/null-buildpack
[ryandotsmith]: https://github.com/ryandotsmith

## Usage:

Create a new directory and git repository:

    mkdir heroku-monit
    cd heroku-monit
    git init .

Initialise the Heroku app:

    heroku create --buildpack=https://github.com/k33l0r/monit-buildpack.git

Then create a `monitrc` file (see below) and push to Heroku.

Your repository structure should look like this (the `conf.d` directory is optional):

    .
    ├── conf.d
    │   └── example
    └── monitrc

`monitrc` **must** exist and at the very minimum it should have this content:

    ## Start Monit in the background (run as a daemon):
    set daemon  60              # check services at 1-minute intervals

    ## Set the location of the Monit id file which stores the unique id for the
    ## Monit instance. The id is generated and stored on first Monit start. By 
    ## default the file is placed in $HOME/.monit.id.
    set idfile /app/tmp/.monit.id

    ## Monit has an embedded web server which can be used to view status of 
    ## services monitored and manage services from a web interface.
    set httpd port ${PORT}
        allow ${HTTP_USER}:${HTTP_PASSWD}

    check system ${SYSTEM_NAME}

    ## It is possible to include additional configuration parts from other files or
    ## directories.
    #
    # include /app/conf.d/*
    #

You should also set the `HTTP_USER` and `HTTP_PASSWD` environment variables:

    heroku config:add HTTP_USER=grimlock HTTP_PASSWD=supersecret

Environment variables in the `monitrc` file and in files in the `conf.d` directory will be substituted for the actual values as long as you use the correct format (i.e. `${MY_VARIABLE}`).

The `SYSTEM_NAME` variable defaults to "Heroku", but you can override it if you wish:

    heroku config:add SYSTEM_NAME=Grimlock

## Important: Keep Monit Alive

You need to have a uptime monitor (such as [Pingdom][pingdom] or [Uptime Robot][robot]) monitoring your monit instance to make sure that it stays alive.

[pingdom]: http://www.pingdom.com/
[robot]: http://www.uptimerobot.com/

For this use I suggest configuring a read-only user, e.g.

    set httpd port ${PORT}
        allow ${HTTP_USER}:${HTTP_PASSWD}
        allow ${READ_USER}:${READ_PASSWD} read-only

And of course set the `READ_USER` and `READ_PASSWD` variables…

## Working Example

See the `heroku-monit` repository for a working example: <https://github.com/k33l0r/heroku-monit>

## License

MIT License. Copyright 2012 Matias Korhonen.

See the LICENSE file for more details.

### Dependencies

[Monit][monit] is downloaded as a binary and is licensed independently under the [GNU Affero General Public License][affero].

[GNU gettext][gettext] is downloaded as a binary and is licensed independently under the [GNU General Public License][gpl]

[monit]: http://mmonit.com/monit/
[gettext]: http://www.gnu.org/software/gettext/
[gpl]: http://www.gnu.org/licenses/gpl.html
[affero]: http://www.gnu.org/licenses/agpl.html
