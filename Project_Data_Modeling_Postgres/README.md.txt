### Project:Data Modeling with Postgres

#### Overall Project Summary

The requirement of the project is to create a database and ETL pipeline for the analytics team of Sparkify.

Creating an ETL pipeline would be beneficial to the analytics team as it allows for a convenient method to transform data for songs and user activity into a more structured format that would be more readable compared to the raw data's original format (JSON). This in turn allows for immediate querying compared to trying to view data from a JSON file.

This would also be beneficial as moving forward as the ETL pipeline can be reused to bring in additional data that has been collected into the database.

The creation of the database is beneficial as it provides a place to store the data that has been transformed into a more structured format. The immediate benefit of this would be for the analytics team to understand what songs the users are listening to using simple queries.

Moving forward, they would also be able to make use of the data to build machine learning models. An example of use cases would be to create a recommender model to suggest similar songs to users based on what music they have already listened to.

#### Database Schema

The database has been split into 5 different tables, 1 fact table and 4 dimension tables.

##### Songplays Table
![songplay table](https://i.imgur.com/a4e4Qim.jpg)
This is the only fact table in the current database and contains songs that have been listened to by a particular user along with the statistics of the song being played and the information of the song.

##### User Table
![Users table](https://i.imgur.com/GpDsGph.jpg)
A dimension table that lists all users of Sparkify provides information on them.

##### Songs Table
![Song Table](https://i.imgur.com/TVCO492.jpg)
A dimension table that lists all songs on the Sparkify app as well as its respective information.

##### Artists Table
![Artist Table](https://i.imgur.com/TVCO492.jpg)
A dimension table that lists all artists that provide songs on the Sparkify app as well as their respective information.

##### Time Table
![Time table](https://i.imgur.com/50dMGBg.jpg)
A dimension table that contains timestamps of records in songplays broken down into specific units.

#### Explanation of Scripts

##### README.md
Contains information on the project, database and ETL pipeline. It also contains information on how the database is populated using the ETL pipeline.

##### create_tables.py

This script defines 4 functions:

The first function is the create_datebase function. This function drops the sparkifydb database if it exists then creates a new one and connects to it.

The second function is the drop_tables function where it drops all tables in the database based on the DROP queries listed in sql_queries.py.

The third function is the create_tables function which creates tables in the database based on the CREATE queries listed in sql_queries.py.

The last function, the main function is used to run the previous three functions before closing the connection.

##### sql_queries.py

This script lists:

1. Drop Table Queries
This contain the queries to drop all the tables in the database. This query will be read by create_tables.py to be run.

2. Create Table Queries
This contain the queries to create all the tables in the database, including the column names and datatypes that will be present in these tables. This query will be read by create_tables.py to be run.

3. Insert Records
This contains the queries for the insertion of records into the various tables. This query will be read by etl.py.

4. Find Songs
This query is read by etl.py in order to combine data from the tables songs and artists that will be used for insertion of records into the songplays table.

5. Query Lists
The last 2 queries from this section are used to create an array for the CREATE tables and DROP tables so that they can be easily read by create_tables.py from an array instead of being read one by one by create_tables.py.

##### test.ipynb

This python notebook allows us to test the following using simple queries and to view part of the data:
1. Connection to the database
2. Check if tables have been created or dropped properly
3. Check if records have been properly inserted into the tables

##### etl.ipynb

This python notebook allows us to test data transformation and insertion of records on part of the data to reduce time needed for querying and so that these transformations can be used on the whole dataset in etl.py.

##### etl.py

Once testing has been completed, the necessary components from etl.ipynb will be transferred into etl.py so that the whole process of transformation and inserting of all data into the tables can be done. The whole process is done in 4 functions:

1. process_song_file
All song files are placed into a dataframe.

df = pd.read_json(filepath, lines=True)

Then data is inserted into the tables artists and songs.

2. process_log_file
All log files are placed into a dataframe. And data is filtered by 'NextSong':

df = df[df['page']=='NextSong']

Transformation is done on the log files to convert them into the correct format, for example to convert timestamp to datetime:

t = pd.to_datetime(df.ts, unit='ms')

Data is then inserted into the tables time, users and songplays.

3. process_data
This function iterates over all files of a certain type (ie. song or log), so that queries 1 and 2 can be run over all files instead of being called multiple times for each file.

4. main
Combines queries 1, 2 and 3 to read all song and long files and to insert records into the respective tables.

#### ETL Pipeline
The ETL pipeline consists of 3 python scripts, create_tables.py, sql_queries.py and etl.py.

However only two of these files need to be run in the following order:

1. create_tables.py (One-Time)
The first table that needs to be run is the create_tables.py in order to create the tables in the database. This query only has to be run once as creation of tables is only done once.

2. etl.py
The next table that has to be run is the etl.py which will populate the tables from the previous script. Assuming data is consistent, this will also be a one-time run script. However as Sparkify is most likely collecting new data with each stream, data will probably have to be loaded on a daily or weekly basis.

When new data comes in, only etl.py will have to be run to add on the new data into the respective tables.
