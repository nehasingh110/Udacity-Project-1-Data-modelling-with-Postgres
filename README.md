#Purpose of the database
1. To help the company maintain the data systematically in a star schema database
2. To help them do analysis with simple queries and fast aggregations on the fact table and extra information can be fetcehd from the dimension tables by using joins
3. This database will help them keep the database consistent. For example if a user related information need to be updated they'll just update one table which is the user table
4. This database will occupy the minimal space

    
    
###Justification of the database schema design   
1. First the dimension tables will be created since the dependencies have been set within the fact table
2. All the dimension tables have been created with a primary key that helps find unique records from the respective tables. All these primary keys are being referenced in the fact tables
3. The fact table also contains a primary key and lot of foreign keys for each dimension table to fetch the related information that would have got duplicted in the fact table
4. This is a very optimized solution for the database since the information that is more static is stored in the dimension tables and data with the most granularity is stored in the fact table

    
    
###Justification of the ETL pipeline
1. In the main function we're creating a connection to the postgres database
2. We are creating a cursor to execute the queries
3. We are calling a function called process_data by passing 4 parameters: cursor, connection, filepath and function name
4. In this function process_data, a list called all_files is using filepath to append all json file paths in it
5. Now we're calling process_song_file function to populate song and artist table
6. A DataFrame called df is generated containing all song data related json files
7. A subset of this DataFrame is created for fetching song table related columns
8. Null records are being dropped by using dropna() function since we have a primary key that can't accept nulls and also null in any other columns will throw error with the insert query not being able to match number of columns with the number of values coming from the list song_data
9. Then we are removing the duplicated since this a dimension table and should contain 1 record per song with song_id being our primary key
10. Array is created by fetching DataFrame values, which is later converted to list
11. This list's elements(containing values for each row) are then used to append with our insert query and execute on the database
12. Same is repeated for artist table. There was one json file that had only had single record in it and had nulls on artist related columns. This record gets removed because of dropna() function and list becomes empty which throws error while executing insert query. Hence I have included a criteria that checks if the list not empty only then insert query will get executed
13. Similarly the process_log_file function is also called through process_data function
14. DataFrame containing all log file paths is created and null records are removed at the beginning itself because later we don't want any rows with null data to be inserted in songplay table. Since it won't find a match in user or songs table
15. Next we subset the DataFrame based on page column value NextSong
16. We are creating a series variable t from timestamp column ts in datetime format
17. Then we create a list storing 'start_time', 'hour', 'day', 'week', 'month', 'year', 'weekday' values generated out of series variable t using pandas' dt attribute accessing datetimelike properties
18. We combine this list with another containing the headings resulting in a dictionary which is then converted to a DataFrame
19. After removing the nulls and duplicates from this DataFrame, data is inserted to the database table time using a for loop
20. Similarly user table is also populated
21. Finally based on the song, artist and length we use song_select query to fetch the song_id and artist_id and populate data in songplay table by using few columns from df DataFram and these 2 variables after converting ts timestamp data to datetime format
22. Lastly we close the connection

    
    
###Example use cases for song play analysis
1. To find out the most played song : select song_id, count(*) from songplays where song_id not in ('None') group by song_id order by count(*) desc limit 1
2. To find out which artist's songs are most played
3. To find out which user likes which artist the most
4. To find out which user likes which songs
5. To find out most famous songs per year, per month, per week
6. To find out yearly top 10 artists, songs
7. To find out artists specific to which location are more famous
8. To find out song preferences according to gender
9. Based on above query results the company can suggest more appropriate songs to the users



###How to run the Python scripts
1. Open Console for Python and run create_tables.py to create tables using the command '%run create_tables.py'
2. Run etl.py using the command '%run etl.py'
    - Look for output regarding how many files out of total number of files got processes



###Explanation of repository files
1. First we'll update the file called sql_queries.py. The drop, create and insert queries need to be created in this file. These queries will keep changing with time as we get to know more about the data types and the contraints needed.
    - song_select query is created later in the project while developing code for ETL process in etl.ipynb after understand the logic required for song_select query
2. After that we run create_tables.py that 
    - creates a connection to the default database and cursor to it
    - creates a new database called 'sparkifydb', closes previous connection and creates new connection and cursor to sparkifydb
    - imports the variables create_table_queries, drop_table_queries from sql_queries.py and executes them to create new tables and drop previous ones if existed
3. Run code in test.ipynb one by one to test if the new database and it's tables got created successfully
4. etl.ipynb is the main file where ETL code is developed in python language
    - this file has data extraction logic for just one json file and populating it in the tables
5. Based on the code written in etl.ipynb we update the etl.py file to extract data from all the json files using for loops and populating the data in our tables
6. Finally there's a README.md file that will contain the complete documentation on the project
