# Cloud-Data-Warehouse

## Project Goal
The project's goal is to build an ***ELT pipeline*** that extracts data from CSV stored in an ***S3 bucket***, loads them into a ***Redshift Cluster***, and then transforms the data into ***dimensional tables*** inside a database.


## Explaining each file

### etl.py
#### ELT script for extracting the data from the bucket, loading them into the staging tables, and then transforming these staging tables into dimensional tables

1. `load_staging_tables`
	* Loading the data from S3 buckets to the Redshift staging tables
2. `insert_tables`
	* Transforming the staging tables data to dimensional tables

### create_tables.py
#### The file will execute the dropping and creation  of the staging and the dimensional tables that will be used for this project

1. `drop_tables`
    * Dropping the tables if they already exist
2. `create_tables`
    * Creating the tables

### sql_queries.py
#### a file containing the SQL queries that will be used in the file `create_tables.py` and the file `etl.py`

1. `*_table_drop`
    * a variable containing the `DROP TABLE IF EXIST` queries
2. `*_table_create`
    * a variable containing the `CREATE TABLE IF NOT EXIST` queries
3. `staging_*_copy`
    * a variable containing the `Copy TABLE FROM` queries
4. `*_table_insert`
    * a variable containing the `INSERT INTO TABLE` queries


## The Tables Architecture
in this section, I will showcase the tables definitions and their columns
### The Staging tables
```
staging_events
    artist VARCHAR,
    auth VARCHAR,
    firstName VARCHAR,
    gender CHAR(1),
    itemInSession INT,
    lastName VARCHAR,
    length FLOAT,
    level VARCHAR,
    location TEXT,
    method VARCHAR,
    page VARCHAR,
    registration VARCHAR,
    sessionId INT,
    song VARCHAR,
    status INT,
    ts BIGINT,
    userAgent TEXT,
    userId INT

staging_songs
    artist_id VARCHAR,
    artist_latitude FLOAT,
    artist_location TEXT,
    artist_longitude FLOAT,
    artist_name VARCHAR,
    duration FLOAT,
    num_songs INT,
    song_id VARCHAR,
    title VARCHAR,
    year INT
```

### The Fact table 
```
songplays
    songplay_id INT IDENTITY(0,1),
    start_time TIMESTAMP,
    user_id INT,
    level VARCHAR,
    song_id VARCHAR,
    artist_id VARCHAR,
    session_id INT,
    location TEXT,
    user_agent TEXT
```

### The Dimension tables
```
users
    user_id INT,
    first_name VARCHAR,
    last_name VARCHAR,
    gender CHAR(1),
    level VARCHAR

songs
    song_id VARCHAR,
    title VARCHAR,
    artist_id VARCHAR,
    year INT,
    duration FLOAT

artists
    artist_id VARCHAR,
    name VARCHAR,
    location TEXT ,
    latitude FLOAT ,
    longitude FLOAT

time
    start_time TIMESTAMP,
    hour INT,
    day INT,
    week INT,
    month INT,
    year INT,
    weekday VARCHAR
```

## Example queries
The following are queries results to check if the **ETL** was successful

### staging_events

``` SELECT * FROM staging_events LIMIT 5; ```

| userid | firstname | lastname | gender | level | location | useragent | registration | sessionid | ts | page | method | status | iteminsession | auth | artist | song | length |
|--------|----------|---------|--------|-------|----------|-----------|-------------|-----------|----|------|--------|--------|--------------|------|--------|------|--------|
| 101    | Jayden   | Fox     | M      | free  | New Orleans-Metairie, LA | "Mozilla/5.0 (Windows NT 6.3; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/36.0.1985.143 Safari/537.36" | 1541033612796 | 184 | 1541121934796 | NextSong | PUT | 200 | 0 | Logged In | N.E.R.D. FEATURING MALICE | Am I High (Feat. Malice) | 288.9922 |
| 83     | Stefany  | White   | F      | free  | Lubbock, TX | "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/36.0.1985.143 Safari/537.36" | 1540708070796 | 82 | 1541122176796 | Home | GET | 200 | 0 | Logged In | None | None | None |
| 83     | Stefany  | White   | F      | free  | Lubbock, TX | "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/36.0.1985.143 Safari/537.36" | 1540708070796 | 82 | 1541122241796 | NextSong | PUT | 200 | 1 | Logged In | Death Cab for Cutie | A Lack Of Color (Album Version) | 216.42404 |
| 83     | Stefany  | White   | F      | free  | Lubbock, TX | "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/36.0.1985.143 Safari/537.36" | 1540708070796 | 82 | 1541122457796 | NextSong | PUT | 200 | 2 | Logged In | Tracy Gang Pussy | I Have A Wish | 221.33506 |
| 66     | Kevin    | Arellano | M      | free  | Harrisburg-Carlisle, PA | "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/36.0.1985.125 Safari/537.36" | 1540006905796 | 153 | 1541126568796 | NextSong | PUT | 200 | 0 | Logged In | Skillet | Monster (Album Version) | 178.02404 |



### staging_songs

``` SELECT * FROM staging_songs LIMIT 5; ```

| artist_id | artist_name | artist_location | artist_latitude | artist_longitude | song_id | title | duration | num_songs | year |
|-----------|------------|-----------------|------------------|------------------|---------|-------|----------|-----------|------|
| AR73AIO1187B9AD57B | Western Addiction | San Francisco, CA | 37.77916 | -122.42005 | SOQPWCR12A6D4FB2A3 | A Poor Recipe For Civic Cohesion | 118.07302 | 1 | 2005 |
| ARC1IHZ1187FB4E920 | Jamie Cullum |  | None | None | SOXZYWX12A6310ED0C | It's About Time | 246.9873 | 1 | 0 |
| ARGE7G11187FB37E05 | Cyndi Lauper | Brooklyn, NY | None | None | SONRWUU12AF72A4283 | Into The Nightlife | 240.63955 | 1 | 2008 |
| ARBZIN01187FB362CC | Paris Hilton | 27 | 1.32026 | 103.78871 | SOERIDA12A6D4F8506 | I Want You (Album Version) | 192.28689 | 1 | 2006 |
| ARTC1LV1187B9A4858 | The Bonzo Dog Band | Goldsmith's College, Lewisham, Lo | 51.4536 | -0.01802 | SOAFBCP12A8C13CC7D | King Of Scurf (2007 Digital Remaster) | 301.40036 | 1 | 1972 |



### artist

``` SELECT * FROM artist LIMIT 5; ```

| artist_id | name                  | location          | latitude  | longitude  |
|-----------|-----------------------|-------------------|-----------|------------|
| AR5LMPY1187FB573FE | Chaka Khan_ Rufus   | Chicago, IL       | 41.88415  | -87.63241  |
| AR5AA4Q1187FB4CFBD | Alisha's Attic      |                   | None      | None       |
| ARGS47D1187FB40225 | Peter And Gordon    | London, England   | None      | None       |
| ARNQAVF11F4C844C04 | Despina Vandi       |                   | None      | None       |
| ARWYVP51187B98C516 | The Suicide Machines | Detroit, MI      | 42.33168  | -83.04792  |


### song

``` SELECT * FROM song LIMIT 5 ```

| song_id              | title                           | artist_id             | year | duration   |
|----------------------|---------------------------------|-----------------------|------|------------|
| SOIGICF12A8C141BC5   | Game & Watch                    | AREWD471187FB49873    | 2004 | 580.54485  |
| SONHGLD12AB0188D47   | Our Father                      | AR1S3NH1187B98C2BC    | 1999 | 202.4224   |
| SOBBUGU12A8C13E95D   | Setting Fire to Sleeping Giants | ARMAC4T1187FB3FA4C    | 2004 | 207.77751  |
| SOAFBKM12AB01837A7   | Brain Dead                      | ARL14X91187FB4CF14    | 1995 | 94.22322   |
| SOINBCU12A6D4F94C0   | Human Cannonball                | ARV1JVD1187B9AD195    | 1995 | 190.48444  |


### songplay

``` SELECT * FROM songplay LIMIT 5 ```

| songplay_id | start_time           | user_id | level | song_id            | artist_id          | session_id | location                                | user_agent |
|------------|----------------------|---------|-------|--------------------|--------------------|------------|-----------------------------------------|------------|
| 2          | 2018-11-08 15:01:57  | 29      | paid  | SOFVOQL12A6D4F7456 | ARPN0Y61187B9ABAA0 | 372        | Atlanta-Sandy Springs-Roswell, GA      | "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_4) AppleWebKit/537.78.2 (KHTML, like Gecko) Version/7.0.6 Safari/537.78.2" |
| 4          | 2018-11-26 15:33:56  | 72      | paid  | SODOLVO12B0B80B2F4 | AR6XPWV1187B9ADAEB | 381        | Detroit-Warren-Dearborn, MI            | Mozilla/5.0 (Macintosh; Intel Mac OS X 10.9; rv:30.0) Gecko/20100101 Firefox/30.0 |
| 10         | 2018-11-16 16:27:21  | 90      | free  | SOMUJKC12AB01865AD | AR9RYZP1187FB36C6A | 148        | Pensacola-Ferry Pass-Brent, FL         | Mozilla/5.0 (X11; Linux x86_64; rv:31.0) Gecko/20100101 Firefox/31.0 |
| 16         | 2018-11-26 13:31:57  | 36      | paid  | SOVPSWY12A58A7B83F | ARF91NB1187B98BDB8 | 808        | Janesville-Beloit, WI                  | "Mozilla/5.0 (Windows NT 5.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/36.0.1985.143 Safari/537.36" |
| 18         | 2018-11-26 07:08:28  | 49      | paid  | SOYQYTX12AB0186FFA | ARWVF341187B9B55D8 | 930        | San Francisco-Oakland-Hayward, CA      | Mozilla/5.0 (Windows NT 5.1; rv:31.0) Gecko/20100101 Firefox/31.0 |


### time

``` SELECT * FROM time LIMIT 5 ```

| start_time           | hour | day | week | month | year | weekday |
|----------------------|------|-----|------|-------|------|---------|
| 2018-11-26 13:31:57 | 13   | 26  | 48   | 11    | 2018 | 1       |
| 2018-11-26 07:08:28 | 7    | 26  | 48   | 11    | 2018 | 1       |
| 2018-11-29 01:38:30 | 1    | 29  | 48   | 11    | 2018 | 4       |
| 2018-11-19 07:37:44 | 7    | 19  | 47   | 11    | 2018 | 1       |
| 2018-11-19 03:58:05 | 3    | 19  | 47   | 11    | 2018 | 1       |


### users

``` SELECT * FROM time LIMIT 5 ```

| user_id | first_name | last_name | gender | level |
|---------|-----------|-----------|--------|-------|
| 101     | Jayden    | Fox       | M      | free  |
| 83      | Stefany   | White     | F      | free  |
| 66      | Kevin     | Arellano  | M      | free  |
| 86      | Aiden     | Hess      | M      | free  |
| 15      | Lily      | Koch      | F      | paid  |

