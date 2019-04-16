# Udacity_Project1
<h3>Purpose of the database<h3>
<ol>
<li>     To help the company maintain the data systematically in a star schema database</li>
<li>To help them do analysis with simple queries and fast aggregations on the fact table and extra information can be fetcehd from the dimension tables by using joins</li>
<li>This database will help them keep the database consistent. For example if a user related information need to be updated they'll just update one table which is the user table</li>
<li>This database will occupy the minimal space</li>
</ol>
    
    
<h3>Justification of the database schema design<h3>
    <ol>
    <li>First the dimension tables will be created since the dependencies have been set within the fact table</li>
    <li>All the dimension tables have been created with a primary key that helps find unique records from the respective tables. All these primary keys are being referenced in the fact tables</li>
    <li>The fact table also contains a primary key and lot of foreign keys for each dimension table to fetch the related information that would have got duplicted in the fact table</li>
    <li>This is a very optimized solution for the database since the information that is more static is stored in the dimension tables and data with the most granularity is stored in the fact table</li>
    </ol>
    
    
<h3>Justification of the ETL pipeline<h3>
    <ol>
    <li>In the main function we're creating a connection to the postgres database</li>
    <li>We are creating a cursor to execute the queries</li>
    <li>We are calling a function called process_data by passing 4 parameters: cursor, connection, filepath and function name</li>
    <li>In this function process_data, a list called all_files is using filepath to append all json file paths in it</li>
    <li>Now we're calling process_song_file function to populate song and artist table</li>
    <li>A DataFrame called df is generated containing all song data related json files</li>
    <li>A subset of this DataFrame is created for fetching song table related columns</li>
    <li>Null records are being dropped by using dropna() function since we have a primary key that can't accept nulls and also null in any other columns will throw error with the insert query not being able to match number of columns with the number of values coming from the list song_data</li>
    <li>Then we are removing the duplicated since this a dimension table and should contain 1 record per song with song_id being our primary key</li>
    <li>Array is created by fetching DataFrame values, which is later converted to list</li>
    <li>This list's elements(containing values for each row) are then used to append with our insert query and execute on the database</li>
    <li>Same is repeated for artist table. There was one json file that had only had single record in it and had nulls on artist related columns. This record gets removed because of dropna() function and list becomes empty which throws error while executing insert query. Hence I have included a criteria that checks if the list not empty only then insert query will get executed</li>
    <li>Similarly the process_log_file function is also called through process_data function</li>
    <li>DataFrame containing all log file paths is created and null records are removed at the beginning itself because later we don't want any rows with null data to be inserted in songplay table. Since it won't find a match in user or songs table</li>
    <li>Next we subset the DataFrame based on page column value NextSong</li>
    <li>We are creating a series variable t from timestamp column ts in datetime format</li>
    <li>Then we create a list storing 'start_time', 'hour', 'day', 'week', 'month', 'year', 'weekday' values generated out of series variable t using pandas' dt attribute accessing datetimelike properties</li>
    <li>We combine this list with another containing the headings resulting in a dictionary which is then converted to a DataFrame</li>
    <li>After removing the nulls and duplicates from this DataFrame, data is inserted to the database table time using a for loop</li>
    <li>Similarly user table is also populated</li>
    <li>Finally based on the song, artist and length we use song_select query to fetch the song_id and artist_id and populate data in songplay table by using few columns from df DataFram and these 2 variables after converting ts timestamp data to datetime format</li>
    <li>Lastly we close the connection</li>
    </ol>
    
    
<h3>Example queries for song play analysis<h3>
    <ol>
    <li>To find out the most played song : select song_id, count(*) from songplays where song_id not in ('None') group by song_id order by count(*) desc limit 1</li>
    <li></li>
    <li></li>
    <li></li>
    <li></li>
    </ol>
