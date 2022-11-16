# netbox-demo-data

This repository serves as "dummy" data that can be used to populate a [NetBox](https://github.com/netbox-community/netbox) instance for the purposes of demonstration and testing. The set of demo data comprises a set of JSON objects which are to be loaded into NetBox's PostgreSQL database using Django's `loaddata` management command (see the instructions below).

Once populated, the demo instance can be accessed using the username `admin` and password `admin`.

Please note that due to very limited testing ability, we are not currently accepting contributions to the demo data set.

## Loading the Database

First, drop and recreate the PostgreSQL database. The example here assumes the database is named `netbox`.

**WARNING:** This will destroy any existing database. Do not perform this operation on a database you care about.

```bash
sudo -u postgres psql -c "DROP database netbox;"
sudo -u postgres psql -c "CREATE database netbox;"
```

Next, apply NetBox's database migrations using the `migrate` management command. (Ensure that the Python virtual environment is active before attempting to invoke the command.)

```bash
source /opt/netbox/venv/bin/activate
./manage.py migrate
```

Finally, load the demo data fixtures from the JSON file.

```bash
./manage.py loaddata -v 3 netbox-demo-$VERSION.json
```

### Docker Commands

```
# Drop & recreate the database
docker-compose exec postgres sh -c 'psql -U $POSTGRES_USER postgres -c "DROP DATABASE netbox;"'
docker-compose exec postgres sh -c 'psql -U $POSTGRES_USER postgres -c "CREATE DATABASE netbox;"'

# Apply migrations
docker-compose exec netbox bash -c "source /opt/netbox/venv/bin/activate && ./manage.py migrate"

# Load the demo data
docker cp netbox-demo-$VERSION.json "$(docker-compose ps -q netbox)":/opt/netbox/netbox/netbox-demo.json
docker-compose exec netbox bash -c "source /opt/netbox/venv/bin/activate && ./manage.py loaddata netbox-demo.json"
```

## Exporting the Database

The following steps are necessary **only** if you intend to save a snapshot of data from a demo instance. This is done using Django's `dumpdata` management command, with a few specific arguments set to ensure a successful export of the database.

```bash
source /opt/netbox/venv/bin/activate
./manage.py dumpdata --natural-foreign --natural-primary -e extras.Script -e extras.Report -e extras.ObjectChange -e extras.CachedValue -e django_rq --indent 2 -o netbox-demo-$VERSION.json
```

### Docker Commands

```
docker-compose exec netbox bash -c "source /opt/netbox/venv/bin/activate && ./manage.py dumpdata --natural-foreign --natural-primary -e extras.Script -e extras.Report -e extras.ObjectChange --indent 2" > netbox-demo-$VERSION.json
```

