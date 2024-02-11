<h3 align="center" style="margin:0px">
	<img width="200" src="https://www.kinetica.com/wp-content/uploads/2018/08/kinetica_logo.svg" alt="Kinetica Logo"/>
</h3>
<h5 align="center" style="margin:0px">
	<a href="https://www.kinetica.com/">Website</a>
	|
	<a href="https://docs.kinetica.com/7.2/">Docs</a>
	|
	<a href="https://docs.kinetica.com/7.2/sql/">SQL Docs</a>
	|
	<a href="https://join.slack.com/t/kinetica-community/shared_invite/zt-1bt9x3mvr-uMKrXlSDXfy3oU~sKi84qg">Community Slack</a>   
</h5>


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
[SQL](https://docs.kinetica.com/7.2/sql/) section, including:

- Uploading of local files into the
  [Kinetica File System (KiFS)](https://docs.kinetica.com/7.2/tools/kifs/) for later ingestion
  ([UPLOAD FILE](https://docs.kinetica.com/7.2/sql/kifs/#sql-kifs-upload-file))
- Ingestion of uploaded file data into tables
  ([LOAD INTO](https://docs.kinetica.com/7.2/sql/load/#load-into))
- Downloading of [KiFS](https://docs.kinetica.com/7.2/tools/kifs/) files to local storage
  ([DOWNLOAD FILE](https://docs.kinetica.com/7.2/sql/kifs/#sql-kifs-download-file))

For the full KiSQL documentation, see
[KiSQL](https://docs.kinetica.com/7.2/tools/kisql/).

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
| `kisql.exe`  | KiSQL for Windows-based systems, with an included JRE 1.8

**Note**:  The KiSQL for Unix-based systems requires a compatible Java Runtime
           Environment installed where the utility will be run.

For the KiSQL full parameter set, see
[KiSQL Parameters](https://docs.kinetica.com/7.2/tools/kisql/#parameters).


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
[KiSQL Interactive Mode](https://docs.kinetica.com/7.2/tools/kisql/#interactive-mode).


| Command | Description
| :---    | :---
| `\d`    | Show all the accessible schemas & tables in the database
| `\h`    | Show a history of SQL commands executed
| `\r`    | Re-run the last SQL command executed
| `\q`    | Quit KiSQL


### Examples

List schemas & tables:

```
Kinetica()=> \d
+-------------+---------------+----------------+--------------+
| TABLE_CAT   | TABLE_SCHEM   | TABLE_NAME     | TABLE_TYPE   |
+-------------+---------------+----------------+--------------+
| Kinetica    | SYSTEM        | ITER           | TABLE        |
| Kinetica    | demo          | nyctaxi        | TABLE        |
+-------------+---------------+----------------+--------------+
```

Run queries:

```
Kinetica()=> SELECT COUNT(*) FROM demo.nyctaxi;
+----------+
|   EXPR_0 |
+----------+
|   500000 |
+----------+
```

Pass a query to KiSQL and output the result, separating field values with
commas:

```
kisql --host localhost \
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

### Loading Files via KiFS

Create a directory, in which to upload your file(s):

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
- [Full Documentation](https://docs.kinetica.com/7.2/)
- [KiSQL Guide](https://docs.kinetica.com/7.2/tools/kisql/)


## Support

For bugs, please submit an
[issue on Github](https://github.com/kineticadb/kisql/issues).

For support, you can post on
[stackoverflow](https://stackoverflow.com/questions/tagged/kinetica) under the
``kinetica`` tag or
[Slack](https://join.slack.com/t/kinetica-community/shared_invite/zt-1bt9x3mvr-uMKrXlSDXfy3oU~sKi84qg).


## Contact Us

* Ask a question on Slack:
  [Slack](https://join.slack.com/t/kinetica-community/shared_invite/zt-1bt9x3mvr-uMKrXlSDXfy3oU~sKi84qg)
* Follow on GitHub:
  [Follow @kineticadb](https://github.com/kineticadb) 
* Email us:  <support@kinetica.com>
* Visit:  <https://www.kinetica.com/contact/>
