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

| userid | useragent                                                                                                                                          | ts           | status | song                              | sessionid | registration  | page     | method | location                      | level | length    | lastname  | iteminsession | gender | firstname | auth      | artist                        |
|--------|----------------------------------------------------------------------------------------------------------------------------------------------------|--------------|--------|-----------------------------------|-----------|---------------|----------|--------|-------------------------------|-------|-----------|-----------|---------------|--------|-----------|-----------|-------------------------------|
| 101    | "Mozilla/5.0 (Windows NT 6.3; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/36.0.1985.143 Safari/537.36"                                       | 1541121934796| 200    | Am I High (Feat. Malice)          | 184       | 1541033612796 | NextSong | PUT    | New Orleans-Metairie, LA      | free  | 288.9922  | Fox       | 0             | M      | Jayden    | Logged In | N.E.R.D. FEATURING MALICE     |
| 83     | "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/36.0.1985.143 Safari/537.36"                              | 1541122176796| 200    | None                              | 82        | 1540708070796 | Home     | GET    | Lubbock, TX                   | free  | None      | White     | 0             | F      | Stefany   | Logged In | None                          |
| 83     | "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/36.0.1985.143 Safari/537.36"                              | 1541122241796| 200    | A Lack Of Color (Album Version)   | 82        | 1540708070796 | NextSong | PUT    | Lubbock, TX                   | free  | 216.42404 | White     | 1             | F      | Stefany   | Logged In | Death Cab for Cutie           |
| 83     | "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/36.0.1985.143 Safari/537.36"                              | 1541122457796| 200    | I Have A Wish                     | 82        | 1540708070796 | NextSong | PUT    | Lubbock, TX                   | free  | 221.33506 | White     | 2             | F      | Stefany   | Logged In | Tracy Gang Pussy              |
| 66     | "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/36.0.1985.125 Safari/537.36"                              | 1541126568796| 200    | Monster (Album Version)           | 153       | 1540006905796 | NextSong | PUT    | Harrisburg-Carlisle, PA       | free  | 178.02404 | Arellano  | 0             | M      | Kevin     | Logged In | Skillet                       |


### staging_songs

``` SELECT * FROM staging_songs LIMIT 5; ```

| artist_id           | artist_name    | song_id              | title                                   | year | duration   | num_songs | artist_location      | artist_latitude | artist_longitude |
|---------------------|----------------|----------------------|-----------------------------------------|------|------------|-----------|----------------------|-----------------|------------------|
| ARJNIUY12298900C91  | Adelitas Way   | SOBLFFE12AF72AA5BA    | Scream                                  | 2009 | 213.9424   | 1         |                      | None            | None             |
| ARSVTNL1187B992A91  | Jonathan King  | SOEKAZG12AB018837E    | I'll Slap Your Face (Entertainment USA Theme) | 2001 | 129.85424  | 1         | London, England      | 51.50632        | -0.12714         |
| ARXR32B1187FB57099  | Gob            | SOFSOCN12A8C143F5D    | Face the Ashes                          | 2007 | 209.60608  | 1         |                      | None            | None             |
| ARZ5H0P1187B98A1DD  | Snoop Dogg     | SOAPERH12A58A787DC    | The One And Only (Edited)               | 0    | 230.42567  | 1         | Long Beach, CA       | 33.76672        | -118.1924        |
| AR1KTV21187B9ACD72  | Cristina       | SOSMJFC12A8C13DE0C    | Is That All There Is?                   | 0    | 343.87546  | 1         | California - LA      | 34.05349        | -118.24532       |


### artist

``` SELECT * FROM artist LIMIT 5; ```

| artist_id                | name          | location      | latitude | longitude |
|--------------------------|---------------|---------------|----------|-----------|
| ARJNIUY12298900C91       | Adelitas Way  |               | None     | None      |
| ARXR32B1187FB57099       | Gob           |               | None     | None      |
| ARV6GHH1187B9AED0D       | Sofia Talvik  | SWEDEN        | 62.19845 | 17.55142  |
| AR5AA4Q1187FB4CFBD       | Alisha's Attic|               | None     | None      |
| ARGS47D1187FB40225       | Peter And Gordon| London, England | None | None      |


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

| songplay_id | start_time           | user_id | level | song_id              | artist_id              | session_id | location                               | user_agent                                                                                                                                          |
|-------------|----------------------|---------|-------|----------------------|------------------------|------------|----------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|
| 0           | 2018-11-21 21:56:47  | 15      | paid  | SOZCTXZ12AB0182364   | AR5KOSW1187FB35FF4     | 818        | Chicago-Naperville-Elgin, IL-IN-WI       | "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Ubuntu Chromium/36.0.1985.125 Chrome/36.0.1985.125 Safari/537.36"         |
| 4           | 2018-11-26 15:33:56  | 72      | paid  | SODOLVO12B0B80B2F4   | AR6XPWV1187B9ADAEB     | 381        | Detroit-Warren-Dearborn, MI             | Mozilla/5.0 (Macintosh; Intel Mac OS X 10.9; rv:30.0) Gecko/20100101 Firefox/30.0                                                                   |
| 8           | 2018-11-13 19:11:19  | 97      | paid  | SOQSYGY12A8C137E0F   | AROF4LP1187FB41C51     | 537        | Lansing-East Lansing, MI              | "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/37.0.2062.94 Safari/537.36"                                          |
| 12          | 2018-11-14 15:14:54  | 25      | paid  | SOVXAXI12A8C1383D9   | ARY65231187FB46283     | 534        | Marinette, WI-MI                       | "Mozilla/5.0 (Windows NT 6.3; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/36.0.1985.143 Safari/537.36"                                      |
| 16          | 2018-11-26 13:31:57  | 36      | paid  | SOVPSWY12A58A7B83F   | ARF91NB1187B98BDB8     | 808        | Janesville-Beloit, WI                  | "Mozilla/5.0 (Windows NT 5.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/36.0.1985.143 Safari/537.36"                                             |


### time

``` SELECT * FROM time LIMIT 5 ```

| start_time           | hour | day | week | month | year | weekday |
|----------------------|------|-----|------|-------|------|---------|
| 2018-11-21 21:56:47  | 21   | 21  | 47   | 11    | 2018 | 3       |
| 2018-11-13 19:11:19  | 19   | 13  | 46   | 11    | 2018 | 2       |
| 2018-11-26 13:31:57  | 13   | 26  | 48   | 11    | 2018 | 1       |
| 2018-11-29 01:38:30  | 1    | 29  | 48   | 11    | 2018 | 4       |
| 2018-11-17 14:48:49  | 14   | 17  | 46   | 11    | 2018 | 6       |


### users

``` SELECT * FROM time LIMIT 5 ```

| user_id | first_name | last_name | gender | level |
|---------|-----------|-----------|--------|-------|
| 101     | Jayden    | Fox       | M      | free  |
| 66      | Kevin     | Arellano  | M      | free  |
| 86      | Aiden     | Hess      | M      | free  |
| 15      | Lily      | Koch      | F      | paid  |
| 95      | Sara      | Johnson   | F      | paid  |
