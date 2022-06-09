<p align="center">
  <a href="https://www.stor-systems.com/">
    <img
      alt=""
      src="https://www.stor-systems.com/img/logo2.png"
      height="100"
    />
  <a href="https://sormas.org/">
    <img
      alt="SORMAS - Surveillance, Outbreak Response Management and Analysis System"
      src="https://sormas.org/wp-content/uploads/2020/07/sormas-logo-128x128-1.png"
      height="100"
    />
  </a>
</p>

# Container Postgres

The postgres container is build from image `postgres:10-alpine`.  It uses a prepared `/etc/postgresql/postgresql.conf` file with parameter:

```shell
max_prepared_transactions = 110         # zero disables the feature
```

This is needed to successfully deploy sormas.

During initial setup `/docker-entrypoint-initdb.d/setup_sormas.sh`  is executed. Here the sormas user and databases will get created and configured.

```sql
CREATE USER ${SORMAS_POSTGRES_USER} WITH PASSWORD '${SORMAS_POSTGRES_PASSWORD}' CREATEDB;
CREATE DATABASE ${DB_NAME} WITH OWNER = '${SORMAS_POSTGRES_USER}' ENCODING = 'UTF8';
CREATE DATABASE ${DB_NAME_AUDIT} WITH OWNER = '${SORMAS_POSTGRES_USER}' ENCODING = 'UTF8';
\c ${DB_NAME}
CREATE OR REPLACE PROCEDURAL LANGUAGE plpgsql;
ALTER PROCEDURAL LANGUAGE plpgsql OWNER TO ${SORMAS_POSTGRES_USER};
CREATE EXTENSION temporal_tables;
CREATE EXTENSION pg_trgm;
CREATE EXTENSION pgcrypto;
GRANT EXECUTE ON ALL FUNCTIONS IN SCHEMA public TO ${SORMAS_POSTGRES_USER};
\c ${DB_NAME_AUDIT}
CREATE EXTENSION IF NOT EXISTS plpgsql WITH SCHEMA pg_catalog;
COMMENT ON EXTENSION plpgsql IS 'PL/pgSQL procedural language';
GRANT EXECUTE ON ALL FUNCTIONS IN SCHEMA public TO ${SORMAS_POSTGRES_USER};
ALTER TABLE IF EXISTS schema_version OWNER TO ${SORMAS_POSTGRES_USER};
```

# Environment variables

These configurations of postgres can be passed to container via environment variables. For more information about them, please refer to postgres documentation.
* SUPERUSER_RESERVED_CONNECTIONS
* EFFECTIVE_IO_CONCURRENCY
* RANDOM_PAGE_COST
* BGWRITER_DELAY
* BGWRITER_LRU_MAXPAGES
* BGWRITER_LRU_MULTIPLIER
* BGWRITER_FLUSH_AFTER
* IDLE_IN_TRANSACTION_SESSION_TIMEOUT
