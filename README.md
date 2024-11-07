# netbox-demo-data

This repository provides "dummy" data that can be used to populate a [NetBox](https://github.com/netbox-community/netbox) instance for the purposes of demonstration and testing. A PostgreSQL database dump is provided for each minor version of NetBox, beginning with v3.0. See below for instructions on populating your database from a SQL dump.

Once populated, the demo instance can be accessed using the username `admin` and password `admin`.

Also included in this repository are JSON data dumps that were captured using Django's `dumpdata` management command. While these have been retained for NetBox v3.6 and earlier for historical purposes, we no longer generate demo data in this format due to its limitations dealing with complex data models. (Specifically, it can be very difficult to ensure that model data is exported in a sequence that can be imported successfully without manual intervention.)

## Loading the Data

First, drop and recreate the PostgreSQL database. The example here assumes the database is named `netbox`.

> [!CAUTION]
> This will destroy any existing database. Do not perform this operation on a database you care about.

```bash
sudo -u postgres psql -c "DROP database netbox;"
sudo -u postgres psql -c "CREATE database netbox;"
```

Next, use the PosgtreSQL `psql` client to load the data from the desired file:

```bash
sudo -u postgres psql netbox < netbox-demo-data/sql/netbox-demo-$VERSION.sql
```

### Docker Commands
```
# stop netbox
docker compose stop netbox

# Drop & recreate the database
docker compose exec postgres sh -c 'psql -U $POSTGRES_USER postgres -c "DROP DATABASE netbox;"'
docker compose exec postgres sh -c 'psql -U $POSTGRES_USER postgres -c "CREATE DATABASE netbox;"'

# Load the demo data, change VERSION if need
(VERSION=v4.1; curl https://raw.githubusercontent.com/netbox-community/netbox-demo-data/refs/heads/master/sql/netbox-demo-$VERSION.sql | docker compose exec -T postgres psql -U $POSTGRES_USER netbox)
# run netbox, wait migrations done
docker compose up netbox 
```

## Exporting the Data

The following steps are necessary **only** if you intend to save a snapshot of data from a demo instance. This is done using PostgreSQL's `pg_dump` utility. (Again, the example here assumes the database is named `netbox`.)

```bash
sudo -u postgres pg_dump netbox > snapshot.sql
```

