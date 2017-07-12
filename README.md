#> Odoo Magento Connector v10

## Pre-requisite

1. to install docker, refers to [this
   documentation](https://docs.docker.com/engine/installation/linux/ubuntulinux/),

2. to install docker-compose, run

        sudo pip install docker-compose

## Installation

```
$ git submodule init
$ git submodule update

$ cp branches.template.yaml branches.yaml
$ # edit branches.yaml, replace <user> by your github user

$ # gitaggregate is installed with pip (https://pypi.python.org/pypi/git-aggregator)
$ gitaggregate -c branches.yaml

$ docker-compose build --pull
```

Image docs: https://github.com/camptocamp/docker-odoo-project

## Magento

You must add this line in `/etc/hosts/`:

```
127.0.0.1 magento
```

So magento is reachable from both Odoo and your computer on `http://magento`.

## Run 

Start everything:

```
docker-compose up -d
```

Usually, when developing on Odoo, you will prefer to start Magento in background
and start Odoo in foreground with an interactive tty. So instead of the command, above, run:

Start Magento in background:

```
docker-compose up -d magento
```

Start Odoo in foreground:

```
docker-compose run --rm -p 8069:8069 odoo odoo --workers=0

```

## Build Docs

Run:

```
mkdir -p conn-docs/connector conn-docs/connector_magento
```

Add a Docker host volume:

      - "./conn-docs:/docs"

Create a test database:

```
docker-compose run --rm -e DB_NAME=testdb odoo testdb-gen -i connector_magento
```

Build connector docs:

```
docker-compose run --rm -e DB_NAME=testdb -e LOCAL_USER_ID=$(id -u) odoo gosu odoo sphinx-build -b html odoo/external-src/connector/connector/doc /docs/connector -aE
```

Build magento connector docs:

```
docker-compose run --rm -e DB_NAME=testdb -e LOCAL_USER_ID=$(id -u) odoo gosu odoo sphinx-build -b html odoo/external-src/connector-magento/connector_magento/doc /docs/connector_magento -aE
```

## Run Tests

Create a test database:

```
docker-compose run --rm -e DB_NAME=testdb odoo testdb-gen -i connector_magento
```

Run tests

```
docker-compose run --rm -e DB_NAME=testdb odoo pytest -s odoo/external-src/connector/connector --cov=odoo/external-src/connector/connector
docker-compose run --rm -e DB_NAME=testdb odoo pytest -s odoo/external-src/connector-magento/connector_magento --cov=odoo/external-src/connector-magento/connector_magento
```

With coverage HTML report
```
docker-compose run --rm -e DB_NAME=testdb odoo pytest -s odoo/external-src/connector/connector --cov=odoo/external-src/connector/connector --cov-report=html:/coverage
docker-compose run --rm -e DB_NAME=testdb odoo pytest -s odoo/external-src/connector-magento/connector_magento --cov=odoo/external-src/connector-magento/connector_magento --cov-report=html:/coverage
```

The coverage report is in the `coverage` folder

More docs on testing with the image: https://github.com/camptocamp/docker-odoo-project#running-tests
