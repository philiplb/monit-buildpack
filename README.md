# Heroku Buildpack: Monit

Run Monit on Heroku. WIP and still undocumented.

Based on the [null-buildpack][null-buildpack] by [Ryan Smith][ryandotsmith].

[null-buildpack]: https://github.com/ryandotsmith/null-buildpack
[ryandotsmith]: https://github.com/ryandotsmith

## Usage:

Your repository structure should look like this:

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
