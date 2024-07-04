# Spotify_SQL
SQL project: Spotify's Most Streamed Songs of 2023
Overview
This project involves analyzing the "Most Streamed Spotify Songs 2023" dataset, obtained from Kaggle. The aim is to answer several questions about the trends and characteristics of the top-streamed songs of the year.

Project Objectives
In this project, we aim to answer the following questions:

Which artist has the most songs in the top streamed list of 2023?
What is the average number of streams for songs released in each month of 2023?
Are songs with higher danceability percentages generally more popular (i.e., have more streams)?
What percentage of songs in the 'Top Spotify Songs 2023' were actually released in 2023?
Steps and Methodology
Step 0: Data Exploration
To begin, a quick glance at the dataset was taken:

sql
Copy code
SELECT *
FROM emilio-playground.raw.top_spotify_songs_2023
LIMIT 50;
The dataset contains information such as the song name, artist, release year, and Spotify ranking, providing ample data for analysis.

Step 1: Data Cleaning
Checking for null values in the streams and artist_s__name columns:

sql
Copy code
SELECT COUNT(*)
FROM emilio-playground.raw.top_spotify_songs_2023
WHERE streams IS NULL OR artist_s__name IS NULL;
Output: 0 null values.

Normalizing text data and simplifying column names:

sql
Copy code
SELECT
    LOWER(track_name) AS track_name,
    LOWER(artist_s__name) AS artist_name,
    artist_count,
    released_year,
    released_month,
    released_day,
    in_spotify_playlists,
    in_spotify_charts,
    CAST(streams AS INT64) AS streams,
    danceability__ AS danceability
FROM table;
Step 2: Data Analysis
Question 1: Which artist has the most songs in the top streamed list of 2023?
sql
Copy code
SELECT 
  artist_name,
  COUNT(*) AS song_count
FROM emilio-playground.raw.top_spotify_songs_2023
WHERE released_year = 2023
GROUP BY artist_name
ORDER BY song_count DESC
LIMIT 1;
Output: Morgan Wallen.

Question 2: What is the average number of streams for songs released in each month of 2023?
sql
Copy code
SELECT
    released_month,
    ROUND(AVG(streams), 0) AS avg_streams
FROM emilio-playground.raw.top_spotify_songs_2023
WHERE released_year = 2023
GROUP BY released_month
ORDER BY released_month;
Output:

January: 287,635,463 streams
February: 231,191,625 streams
March: 170,092,261 streams
April: 150,805,435 streams
May: 94,420,746 streams
June: 70,200,253 streams
July: 41,504,665 streams
Question 3: Are songs with higher danceability percentages generally more popular?
sql
Copy code
SELECT
    CASE
        WHEN danceability <= 25 THEN '0-25%'
        WHEN danceability <= 50 THEN '26-50%'
        WHEN danceability <= 75 THEN '51-75%'
        ELSE '76-100%'
    END AS danceability_group,
    AVG(SAFE_CAST(streams AS INT64)) AS avg_streams
FROM emilio-playground.raw.top_spotify_songs_2023
GROUP BY danceability_group
ORDER BY avg_streams DESC;
Output:

26-50%: 626,777,453.61 streams
51-75%: 520,031,631.98 streams
76-100%: 455,962,557.01 streams
0-25%: 452,250,817.67 streams
Question 4: What percentage of songs in the 'Top Spotify Songs 2023' were actually released in 2023?
sql
Copy code
WITH SongCounts AS (
    SELECT
        COUNT(*) AS total_count,
        SUM(CASE WHEN released_year = 2023 THEN 1 ELSE 0 END) AS count_2023
    FROM emilio-playground.raw.top_spotify_songs_2023
)
SELECT
    (count_2023 * 100.0 / total_count) AS percentage_released_in_2023
FROM SongCounts;
Output: Only 18% of songs were actually released in 2023.

Step 3: Conclusions
Morgan Wallen is the artist with the most songs in the top streamed list of 2023, with 8 songs.
January has the highest number of streams, likely due to ongoing festivities.
Danceability does not correlate strongly with the popularity of a song, but songs with 26-50% danceability have the most streams.
Only 18% of the top songs of 2023 were actually released in 2023, indicating the enduring popularity of older songs.
Installation and Usage
Clone the repository:

bash
Copy code
git clone https://github.com/your-username/spotify-top-songs-2023.git

Technologies Used
SQL
Kaggle Dataset
Contributors
Saranya Vanapalli [https://github.com/saranyavanapalli]

Acknowledgments
Kaggle for the dataset.
Spotify for providing the platform for data collection.
