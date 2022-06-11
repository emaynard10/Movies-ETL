## Movies-ETL
Columbia class introducing regular expressions and practing the ETL process.

### Overview
Scrape movie data from the internet to extract-transform-load the datasets. Clean files, merge dataframes, and publish to an SQL database. This code creates functions, uses list comprehension, and utilizes regular expressions to clean the web data. After cleaning the data step by step, the code was refactored to take three files in a function order to automate the pipeline. The beginning of the function loads in the three files and makes the first datat frame using the following code:

~~~
def data_function(wiki_file, kaggle_file, ratings_file):
    kaggle_metadata = pd.read_csv(kaggle_file, low_memory=False)
    ratings = pd.read_csv(ratings_file)
    
    # Open the read the Wikipedia data JSON file.
    with open(f'{file_dir}/Resources/wikipedia-movies.json', mode='r') as file:
        wiki_movies_raw = json.load(file)
    
    # 3. Write a list comprehension to filter out TV shows.
    wiki_movies = [movie for movie in wiki_movies_raw if 'No. of episodes' not in movie]

    # 4. Write a list comprehension to iterate through the cleaned wiki movies list
    # and call the clean_movie function on each movie.
    clean_movies = [clean_movie(movie) for movie in wiki_movies]


    # 5. Read in the cleaned movies list from Step 4 as a DataFrame.
    wiki_movies_df = pd.DataFrame(clean_movies)
~~~

The finction continues with a try/except block to find errors while extractign the ID on which the datasets will be merged further down the codeblock. regular expressions are used to clean the data, data is dropped and the best data is replaced using either the wiki files or the kaggel data.  The code then merges the dataframes and chooses the best data. In order to sort through the data, regular expressions are used. The ratings dataset is so large "chunks" are created to send the data to Posgresql. Finally, the code is loaded to a SQL database with two tables using the following code: 

~~~
 db_string = f"postgresql://postgres:{db_password}@127.0.0.1:5432/movie_data"
    engine = create_engine(db_string)
    movies_df.to_sql(name='movies', con=engine, if_exists='replace')
~~~
The ETL process is important for ensuring the good data is preserved as much as possible while bad data is eliminated. The data needs to be transformed to a more useable formatt and the three dataframes are merged at the end for comparisions. 

