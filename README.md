# bds-headless-client-example
Sample Brightspace Data Sets headless client using OAuth 2.0 refresh tokens

## Prerequisites

* [Brightspace Data
  Sets](https://community.brightspace.com/s/question/0D56100000xrq5eCAA/)
  * This example was tested against version 1.0 (determined by the value of
    config variable `d2l.Tools.BrightspaceDataSets.Version`)
* [Registered OAuth 2.0
  application](http://docs.valence.desire2learn.com/basic/oauth2.html) and
  corresponding [refresh
  token](https://community.brightspace.com/s/article/ka1610000000pYqAAI/How-to-obtain-an-OAuth-2-0-Refresh-Token)
  with scope `core:*:*`
* [Python 3](https://www.python.org/)
  * This example was tested using Python 3.6
* [PostgreSQL](https://www.postgresql.org/) server and database
  * This example was tested using PostgreSQL 9.6.2

## Setup

* Dependent libraries installed by running `python -m pip install -r
  requirements.txt`
* A file named `config.json` based on the [sample file](config-sample.json)
  * Note: this file contains sensitive information, and its file permissions
    should be set so that it is only readable by the user running this script
    (e.g. `chmod 600 config.json`)
* Create the required tables by running the SQL scripts in
  [schema/tables](./schema/tables) on the database being used either manually or
  using [create_schema.py](./create_schema.py)

### Configs

| key           | Value                                       |
| ------------- | ------------------------------------------- |
| bspace_url    | E.g. `https://myschool.brightspace.com`     |
| client_id     | From OAuth 2.0 application registration     |
| client_secret | From OAuth 2.0 application registration     |
| refresh_token | From `Prerequisites`                        |
| dbhost        | Hostname of the PostgreSQL server           |
| dbname        | Name of the database                        |
| dbuser        | Username for accessing the database         |
| dbpassword    | Password of the user accessing the database |

### Folder Structure

The following outlines the minimum number of files that should be present before
using this script.

```
.
+-- schema
|   +-- upserts
|       +-- ...
+-- config.json
+-- main.py
```

## Usage

```bash
python main.py
```

## Sample Query

Once the data has been loaded into the database, the following query should return a preview of the data:

```sql
SELECT
    u.user_name AS student_name,
    ou.name AS org_unit_name,
    go.name AS grade_object_name,
    CASE
        WHEN gr.points_denominator = 0
        THEN 0
        ELSE ROUND(gr.points_numerator / gr.points_denominator, 2)
    END AS grade
FROM grade_results gr

INNER JOIN users u
ON gr.user_id = u.user_id

INNER JOIN org_units ou
ON gr.org_unit_id = ou.org_unit_id

INNER JOIN grade_objects go
ON gr.grade_object_id = go.grade_object_id

LIMIT 50;
```
