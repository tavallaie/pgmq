# Contributing to Postgres Message Queue (PGMQ)

## Installation

The fastest way to get started is by running the docker image, where PGMQ comes pre-installed.

```bash
docker run -d --name postgres -e POSTGRES_PASSWORD=postgres -p 5432:5432 ghcr.io/pgmq/pgmq-pg:latest
```

## PGXN Installation

PGXN is a distributed network of extensions that make it easier to install and manage PostgreSQL extensions.

Install PGXN Client:

You can install the PGXN client by following the instructions provided in the [PGXN Client Installation Guide](https://pgxn.github.io/pgxnclient/install.html)

Install PGMQ using PGXN

```bash
pgxn install pgmq
```

Alternatively, you can manually install PGMQ by downloading and building from the source using the following commands:
> **Note:** Run these commands as the user who owns the PostgreSQL installation (often `root` on Unix-based systems) or use `sudo` if necessary.

```
curl -LO https://api.pgxn.org/dist/pgmq/1.4.2/pgmq-1.4.2.zip
unzip pgmq-1.4.2.zip
cd pgmq-1.4.2
make
make install
```

## Building from source

PGMQ requires the `postgres-server-dev` package to build. For example, to install
version 14 on ubuntu:

```bash
sudo apt-get install postgres-server-dev-14
```

## Platform-Specific Installation Instructions

### Windows Installation

If you're working on native Windows, follow these steps to install and build pgmq:

1. Install PostgreSQL for Windows

- Download the official PostgreSQL installer from [PostgreSQL Windows Downloads](https://www.postgresql.org/download/windows/).

- Follow the installation process, ensuring you include pgAdmin and Command Line Tools

- After installation, add the PostgreSQL binary directory (typically `C:\Program Files\PostgreSQL\<version>\bin`) to your system's `PATH` environment variable. This will allow access to PostgreSQL tools like `pg_config`.

2. Install Build Tools (MinGW)

- Download and install [MinGW](https://sourceforge.net/projects/mingw/) from MinGW. Select the GCC compiler for C/C++
- During installation, ensure the binaries are added to the `PATH`. MinGW is essential for compiling the `pgmq` extension on Windows.

3.  Install `pgmq` from Source

- Download the `pgmq` source:

```powershell
Invoke-WebRequest -Uri https://api.pgxn.org/dist/pgmq/1.4.2/pgmq-1.4.2.zip -OutFile pgmq-1.4.2.zip
Expand-Archive -Path pgmq-1.4.2.zip -DestinationPath .\pgmq-1.4.2
cd .\pgmq-1.4.2

- Build and install `pgmq`: Use the following commands to compile and install pgmq using MinGW:

```bash
make PG_CONFIG="C:/Program Files/PostgreSQL/<version>/bin/pg_config"
make install
```

4. Create the pgmq Extension

- After installation, connect to PostgreSQL and create the pgmq extension:

```sql
CREATE EXTENSION pgmq cascade;
```

### Mac Installation

If you are using macOS, you can install PostgreSQL and build PGMQ using Homebrew.

1. Install PostgreSQL using Homebrew:

```bash
brew install postgresql
```

2. Clone the PGMQ repository and build it:

```bash
git clone https://github.com/pgmq/pgmq.git
cd pgmq/pgmq-extension
make
sudo make install
```

3. Create the extension in PostgreSQL:

```sql
CREATE EXTENSION pgmq cascade;
```

## Installing Postgres

If you already have Postgres installed locally, you can skip to [Install PGMQ to Postgres](#install-pgmq-to-postgres).

If you need to install Postgres or want to set up a new environment for PGMQ development, [pgenv](https://github.com/theory/pgenv/) is a command line utility that makes it very easy to install and manage multiple versions of Postgres.
Follow the [installation instructions](https://github.com/theory/pgenv/?tab=readme-ov-file#installation) to install it.
If you are on MacOS, you may need link `brew link icu4c --force` in order to successfully build Postgres.

Install Postgres 16.3

```bash
pgenv build 16.3
pgenv use 16.3
```

Connect to Postgres:

```bash
psql -U postgres
```

A fresh install will have not have PGMQ installed.

```psql
postgres=# \dx
                 List of installed extensions
  Name   | Version |   Schema   |         Description
---------+---------+------------+------------------------------
 plpgsql | 1.0     | pg_catalog | PL/pgSQL procedural language
(1 row)
```

## Installing PGMQ

Clone the repo and change into the directory.

```bash
git clone https://github.com/pgmq/pgmq.git
cd pgmq/pgmq-extension
```

### Install PGMQ to Postgres

This will install the extension to the Postgres using the `pg_config` that is currently on your `PATH`. If you have multiple versions of Postgres installed, make sure you are using the correct installation by running `make install PG_CONFIG=/path/to/pg_config`.

```bash
make
make install
```

Finally, you can create the extension and get started with the example in the [README.md](README.md#sql-examples).

```psql
CREATE EXTENSION pgmq cascade;
```

### Installing pg_partman (optional)

If you are working with partitioned queues, you will need to install `pg_partman` version <= 4.7.0

```bash
make install-pg-partman
```

Then,

```sql
CREATE EXTENSION pg_partman;
```

## Running tests

Tests are written for [pg_regress](https://www.postgresql.org/docs/current/regress-run.html) and [pg_isolation_regress](https://github.com/postgres/postgres/blob/master/src/test/isolation/README). The latter is available on Postgres 14 and higher, so the `Makefile` skips them for earlier versions.

Once you have a postgres instance with the extension installed, run:

```bash
make installcheck
```

## Releases

The PGMQ Postgres Extension is released as a bundle with Postgres (in a container) and as PGXN distribution. Both of these flows are managed in a [Github workflow](https://github.com/pgmq/pgmq/blob/main/.github/workflows/release.yml). To create a release,

1. Update and commit the new valid [semver](https://semver.org/) version in [pgmq.control](https://github.com/pgmq/pgmq/blob/main/pgmq-extension/pgmq.control).
2. Create a [Github release](https://github.com/pgmq/pgmq/releases) using the extension's version for the `tag` and `title`. Auto-generate the release notes and/or add more relevant details as needed.

### Container Images

Postgres images with PGMQ and all required dependencies are built and published to `ghcr.io/pgmq/pg{PG_VERSION}-pgmq:{TAG}` for all supported Postgres versions and PGMQ releases.

### Extension Packages

The required extension files are publish to and hosted at [PGXN](https://pgxn.org/dist/pgmq/).

### Client SDKs

See subdirectories for the [Rust](https://github.com/pgmq/pgmq/tree/main/core) and [Python](https://github.com/pgmq/pgmq/tree/main/tembo-pgmq-python) SDK release processes.
