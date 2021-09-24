<img src="https://2wz2rk1b7g6s3mm3mk3dj0lh-wpengine.netdna-ssl.com/wp-content/uploads/2018/08/kinetica_logo.svg" alt="Kinetica Logo" width="300"/>

### The Database for Time and Space


# Kinetica KiSQL CLI SQL Utility

-  [Overview](#overview)
-  [Configure](#configure)
-  [Connect](#connect)
-  [Run](#run)
-  [Documentation](#documentation)
-  [About Us](#about-us)
-  [Contact Us](#contact-us)
-  [License](#license)
 

## Overview

KiSQL is a SQL command-line interface for interacting with a Kinetica database.
It can be downloaded to a client and used to run commands on any Kinetica
instance.

KiSQL exposes the complete Kinetica SQL interface, as given in the
[SQL Support](https://docs.kinetica.com/7.1/concepts/sql/) section, including:

- Ingestion of local file data into tables
  ([INSERT INTO...SELECT...FROM FILE](https://docs.kinetica.com/7.1/concepts/sql/#insert-into-select-from-file))
- Uploading of local files into the
  [Kinetica File System (KiFS)](https://docs.kinetica.com/7.1/tools/kifs/) for later ingestion
  ([UPLOAD FILE](https://docs.kinetica.com/7.1/concepts/sql/#sql-kifs-upload-file))
- Downloading of [KiFS](https://docs.kinetica.com/7.1/tools/kifs/) files to local storage
  ([DOWNLOAD FILE](https://docs.kinetica.com/7.1/concepts/sql/#sql-kifs-download-file))

For the full KiSQL documentation, see
[KiSQL](https://docs.kinetica.com/7.1/tools/kisql/).

For command-line help, enter:

    kisql -?


## Configure

Download one of the KiSQL executables, based on the target runtime environment
from which KiSQL will be run.  Note that this environment does not have to
match the Kinetica database environment; e.g., the Windows KiSQL client can
connect to a Kinetica database running on Linux.

| Executable   | Description
| :---         | :---
| `kisql`      | KiSQL for Unix-based systems (Linux, MacOS, etc.) with JRE 1.8+
| `kisql7`     | KiSQL for Unix-based systems (Linux, MacOS, etc.) with JRE 1.7
| `kisql.exe`  | KiSQL for Windows-based systems, with an included JRE 1.8
| `kisql7.exe` | KiSQL for Windows-based systems, with an included JRE 1.8

**Note**:  The KiSQL for Unix-based systems requires a compatible Java Runtime
           Environment installed where the utility will be run.

For the KiSQL full parameter set, see
[KiSQL Parameters](https://docs.kinetica.com/7.1/tools/kisql/#parameters).


### Common Connection Parameters

| Parameter           | Description
| :---                | :---
| `--host <host>`     | Kinetica database host name/IP
| `--port <port>`     | Kinetica database listen port (default is `9191`)
| `--url <url>`       | Kinetica connection URL; used in lieu of the default host/port configuration for SSL or failover connections
| `--user <id>`       | Connection user ID
| `--limit <rows>`    | Number of rows to be requested in a query at a time; default is 10,000 rows
| `--showTime <0\|1>` | Show (`1`) or don't show (`0`) timing-related output; default is to not show
| `--isql`            | Output in isql format


### Mode Parameters

By default, KiSQL will run in interactive mode, sending each semicolon-separated
command entered to the database.  This mode can be changed to a scripted one,
where commands are passed into KiSQL on the command line.

| Parameter          | Description
| :---               | :---
| `--file <path>`    | Run all SQL statements found in the given file and exit; statements are semicolon-separated, unless `--line` is specified
| `--line`           | Run each line of input as a separate SQL statement, even if not terminated with a semicolon
| `--sql <commands>` | Run the specified semicolon-separated commands


### SSL Parameters

| Parameter             | Description
| :---                  | :---
| `--allowHostMismatch` | Allow (if specified) or disallow (if omitted) the Kinetica certificate host name and server host name to be different
| `--trustStore <path>` | Path to client's trust store, used to validate the certificate from the Kinetica server


### Environment Variables

Passwords can be passed into the command via environment variables; otherwise,
KiSQL will prompt for them.

| Environment Variable | Description
| :---                 | :---
| `KI_PWD`             | Password for the connection user specified by `--user`
| `KI_TRUSTSTOREPWD`   | Password to the trust store specified by `--trustStore`


## Connect

KiSQL in interactive mode using host/port:

    kisql --host <ip or hostname> [--port <port>] [--user <userID>] [<parameter1> <value1>...]

KiSQL in interactive mode using URL:

    kisql --url <connection URL> [--user <userID>] [<parameter1> <value1>...]

KiSQL running a SQL script:

    kisql --url <connection URL> [--user <userID>] --file <file> [<parameter1> <value1>...]

KiSQL running in-line SQL commands:

    kisql --url <connection URL> [--user <userID>] --sql "command1[;command2...]" [<parameter1> <value1>...]

KiSQL run using environment variable to not prompt for the user password:

    export KI_PWD=<password>
    kisql --host <ip or hostname> [--port <port>] --user <userID> [<parameter1> <value1>...]


### Examples

To connect, in interactive mode, to a local instance on the default port with
username (prompt for password):

    kisql --host localhost --user auser

To use an environment variable to avoid the password prompt:

    export KI_PWD=apass
    kisql --host localhost --user auser

To connect over SSL, running a given set of commands:

    kisql --url https://12.34.56.78:8082/gpudb-0 --sql "SELECT CURRENT_TIMESTAMP();SELECT SUM(profit) FROM today_sales"

To connect over SSL, running a SQL script:

    kisql --url https://12.34.56.78:8082/gpudb-0 --file daily_sales_checker.sql


## Run

A few interactive-mode commands are listed below; for the full list, see
[KiSQL Interactive Mode](https://docs.kinetica.com/7.1/tools/kisql/#interactive-mode).


| Command | Description
| :---    | :---
| `\d`    | Show all the accessible schemas & tables in the database
| `\h`    | Show a history of SQL commands executed
| `\r`    | Re-run the last SQL command executed
| `\q`    | Quit KiSQL


### Examples

#### List Schemas & Tables

```
Kinetica()=> \d
+---------------+----------------+--------------+
| TABLE_SCHEM   | TABLE_NAME     | TABLE_TYPE   |
+---------------+----------------+--------------+
| SYSTEM        | SYSTEM.ITER    | TABLE        |
| demo          | demo.nyctaxi   | TABLE        |
+---------------+----------------+--------------+
```

#### Run Queries

```
Kinetica()=> SELECT COUNT(*) FROM demo.nyctaxi;
+----------+
|   EXPR_0 |
+----------+
|   500000 |
+----------+
```

#### Use the Query and Output Format Parameters

```
$ kisql --host localhost \
      --isql \
      --showTime 0 \
      --format delim --delim ',' \
      --sql 'SELECT TOP 10 vendor_id, TRIM(fare_amount) AS fare_amount, passenger_count, dropoff_datetime FROM demo.nyctaxi'
```
```
vendor_id,fare_amount,passenger_count,dropoff_datetime
YCAB,20.5,1,2015-04-21 23:40:14
NYC,10.0,1,2015-04-03 01:43:31
YCAB,11.5,1,2015-04-02 22:05:07
NYC,17.5,2,2015-04-21 21:47:20
NYC,8.0,2,2015-04-21 21:32:32
NYC,15.5,1,2015-04-21 21:43:38
YCAB,11.0,1,2015-04-06 13:18:35
YCAB,8.0,1,2015-04-06 13:10:02
NYC,6.5,5,2015-04-14 22:00:52
YCAB,25.5,1,2015-04-29 12:27:31
```

#### Load Files

Create a directory, in which to load your file(s):

```sql
Kinetica()=> CREATE DIRECTORY 'my_directory';
```

Upload your file(s) to KiFS:

```sql
Kinetica()=> UPLOAD FILES 'my_data.csv' INTO 'my_directory';
```

Finally, load your file into a table:

```sql
Kinetica()=> LOAD INTO ki_home.my_data FROM FILE PATHS 'kifs://my_directory/my_data.csv'
```


## Documentation
- [Full Documentation](https://docs.kinetica.com/7.1/)
- [KiSQL Guide](https://docs.kinetica.com/7.1/tools/kisql/)


## About Us
Kinetica is an analytics database for fusing data across streams and data lakes
to unlock value from spatial and temporal data at scale and speed. Learn about
us [here](https://www.kinetica.com/).


## Contact Us
- Follow on Github: <a class="github-button" href="https://github.com/kineticadb" data-size="large" aria-label="Follow @kineticadb on GitHub">Follow @kineticadb</a>
- Email: [support@kinetica.com](mailto:support@kinetica.com)
- Slack: [Slack](https://www.kinetica.com/slack)
- Visit: [https://www.kinetica.com/contact/](https://www.kinetica.com/contact/)


## License

The software is licensed under the MIT license.
