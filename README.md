#### Project Redshift Datawarehouse(ETL)

### Project Description

Sparkify is a music streaming startup with a growing user base and song database.

Their user activity and songs metadata data resides in json files in S3. The goal of the current project is to build an ETL pipeline that extracts their data from S3, stages them in Redshift, and transforms data into a set of dimensional tables for their analytics team to continue finding insights in what songs their users are listening to.


### Project Structure
```
Data-Warehouse-with-Redshift
│   README.md           # Project description
│   sql_queries.py      # Definition of all sql queries
|   create_tables.py    # Creates the tables
|   etl.py              # ETL script
|   validation.py       # Validates data load
|   dwh_redshift.ipynb  # 1. Creates the AWS assets, 2. Calls Database, ETL, and Validation python scripts, 3.  Deletes AWS assets
|   dwh.cfg             # Configuration file
```

### Requirements for running locally
- Python3
- AWS Account
- [Configured AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html)

### Datasets

You'll be working with two datasets that reside in S3. Here are the S3 links for each:

- Song data: `s3://udacity-dend/song_data`
- Log data: `s3://udacity-dend/log_data`

Log data json path: `s3://udacity-dend/log_json_path.json`

<br />

**Song dataset:**

It's a subset of real data from the [Million Song Dataset](https://labrosa.ee.columbia.edu/millionsong/).
Each file is in JSON format and contains metadata about a song and the artist of that song
```
{
    "num_songs":1,
    "artist_id":"ARD7TVE1187B99BFB1",
    "artist_latitude":null,
    "artist_longitude":null,
    "artist_location":"California - LA",
    "artist_name":"Casual",
    "song_id":"SOMZWCG12A8C13C480",
    "title":"I Didn't Mean To",
    "duration":218.93179,
    "year":0
 }
```

**Log dataset:**

It consists of log files in JSON format generated by this [event simulator](https://github.com/Interana/eventsim) based on the songs in the dataset above.
These simulate activity logs from a music streaming app based on specified configurations.
```
{
   "artist":null,
   "auth":"Logged In",
   "firstName":"Walter",
   "gender":"M",
   "itemInSession":0,
   "lastName":"Frye",
   "length":null,
   "level":"free",
   "location":"San Francisco-Oakland-Hayward, CA",
   "method":"GET",
   "page":"Home",
   "registration":1540919166796.0,
   "sessionId":38,
   "song":null,
   "status":200,
   "ts":1541105830796,
   "userAgent":"\"Mozilla\/5.0 (Macintosh; Intel Mac OS X 10_9_4) AppleWebKit\/537.36 (KHTML, like Gecko) Chrome\/36.0.1985.143 Safari\/537.36\"",
   "userId":"39"
}
```
### Database Schema

Using the song and log datasets, you'll need to create a star schema optimized for queries on song play analysis.

This includes the following tables.

### Fact Table
```
• songplays - records in log data associated with song plays i.e. records with page NextSong
  table schema: songplay_id, start_time, user_id, level, song_id, artist_id, session_id, location, user_agent
```
### Dimension Tables
```
• users - users in the app
  table schema: user_id, first_name, last_name, gender, level

• songs - songs in music database
  table schema: song_id, title, artist_id, year, duration

• artists - artists in music database
  table schema: artist_id, name, location, latitude, longitude

• time - timestamps of records in songplays broken down into specific units
  table schema: start_time, hour, day, week, month, year, weekday
```

#### Edit dwh.cfg file

This file holds the configuration variables used on the scripts to create and configure the AWS resources.

These are the variables the user needs to set up before running the `etl.py` script.

```
KEY = <ENTER AWS ACCESS KEY>   # paste your user Access Key
SECRET = <ENTER AWS SECRET KEY>  # paste your user Secret Key

Note: if VPC_ID is used
VPC_ID = <ENTER VPC ID>  # paste the VPC_ID you want to create the resources (If blank the first VPC on user's AWS account is considered)
```
<b>REMEMBER:</b> Never save your <b>AWS ACCESS KEY & SECRET KEY</b> on scripts.

```
[CLUSTER]
HOST=
DB_NAME=
DB_USER=
DB_PASSWORD=
DB_PORT=

[IAM_ROLE]
ARN=''

[S3]
LOG_DATA='s3:// * * log_data'
LOG_JSONPATH='s3://* * .json'
SONG_DATA='s3://* *song_data'


[AWS]
KEY= * * * *
SECRET= * * * *

[DWH]
DWH_CLUSTER_TYPE=multi-node
DWH_NUM_NODES=4
DWH_NODE_TYPE=dc2.large
DWH_CLUSTER_IDENTIFIER=
DWH_DB=
DWH_DB_USER=
DWH_DB_PASSWORD=
DWH_PORT=
DWH_IAM_ROLE_NAME=
```
### Project Steps

`dwh_redshift.ipynb` notebook acts as the main project notebook. Below are steps of the project execution

* Step 1: Load DWH parameters from `dwh.cfg`
* Step 2: Create clients for IAM, EC2, S3 and Redshift
* Step 3: Create an IAM Role that makes Redshift able to access S3 bucket (ReadOnly)
* Step 4: Create a RedShift Cluster
* Step 5: Open an incoming TCP port to access the cluster ednpoint
* Step 6: Run `sql_queries.py` to load the SQL queries for table creation and data load
* Step 7: Run `create_tables.py` to create tables 
* Step 8: Run `etl.py` to run the ETL of coping the data from json and load them to Stagging tables and then to fact and dimension tables
* Step 9: Run `validation.py` to check the data load into tables
* Step 10: Clean up the Redshift and IAM role resources
