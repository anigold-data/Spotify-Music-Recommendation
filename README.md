# Spotify Music Analysis Overview

---

## Dataset Structure
This comprehensive music analysis utilizes four primary datasets to explore musical trends, genre classification, and recommendation systems:
data.csv - The main dataset containing comprehensive music information with predicted genre classifications mapped to individual songs, serving as the foundation for recommendation algorithms.
data_by_artists.csv - Artist-level aggregated data providing insights into performer statistics, popularity metrics, and characteristic audio features across different artists.
data_genres.csv - Contains all genres with defined values for audio features. Clustering analysis was performed on this dataset to select representative genres for each cluster, leveraging Euclidean distances to find the closest genre matches in PCA space.
data_w_genres.csv - Contains artist records with audio features and applicable genres for each entry. Each record represents a song (though song names were not provided as field information), enabling genre-based analysis and classification.
data_by_year.csv - Defines audio features aggregated by year from 1921 to 2020, including popularity metrics, mode, year, and key information for temporal trend analysis.

---

## Key Field Values
The analysis centers on several critical audio features and metadata:

### Audio Features:

Acousticness - Measure of whether a track is acoustic (0.0 to 1.0)
Danceability - How suitable a track is for dancing (0.0 to 1.0)
Energy - Perceptual measure of intensity and power (0.0 to 1.0)
Instrumentalness - Predicts whether a track contains vocals (0.0 to 1.0)
Liveness - Detects presence of audience in recording (0.0 to 1.0)
Speechiness - Detects presence of spoken words (0.0 to 1.0)
Valence - Musical positivity/emotional tone (0.0 to 1.0)
Tempo - Overall estimated tempo in beats per minute


### Metadata Fields:

Year - Release year (1921-2020)
Popularity - Popularity score metric
Mode - Musical mode (major/minor)
Key - Musical key signature
Genre - Predicted/assigned genre classifications

---

## Feature Distribution
Audio features like Acousticness and Instrumentalness are bimodal, dividing tracks into clear categories, while Danceability and Energy follow more normal distributions. This could indicate that majority of the tracks contain vocals, with very few being purely instrumental, suggesting a focus on music with lyrics. Features like Loudness and Valence show fairly uniform distributions, indicating that most songs are mastered at high volumes and convey a range of emotions.
These characteristics will further be used for various applications, including recommendation models focusing on mood or energy, and playlist generation tailored to specific genres or moods. Additionally, features like Speechiness and Liveness can help distinguish between spoken-word tracks or live concert recordings, while Valence is an excellent indicator for mood-based recommendations (e.g., happy vs. sad songs).

---

## Correlation Analysis
The strongest positive correlation exists between energy and loudness (0.78), indicating that louder songs tend to feel more energetic. Similarly, energy and valence are moderately correlated (0.35), which suggests that high-energy songs could often be perceived as happier. Danceability and valence also show a moderately strong relationship (0.56), implying that happy songs are usually more danceable.
On the other hand, acousticness has a strong negative correlation with energy (-0.75) and loudness (-0.56), meaning that acoustic tracks are generally quieter and less energetic. Instrumentalness negatively correlates with loudness (-0.41), suggesting instrumental songs tend to be softer.
Some features like liveness, speechiness, and tempo show weak or no correlation with other features, indicating that they provide unique information that is not strongly tied to the rest.

---

## Trend Analysis Over Time
From 1925 to 2020, music has evolved significantly in sound and style, as reflected in various audio feature trends. Danceability gradually increased, particularly from the 1970s onward, aligning with the rise of disco, electronic dance music, and rhythm-focused pop. This shift is supported by the digital revolution, which has made it easier to produce tracks with consistent beats suitable for dancing.
Energy and loudness have both shown a steady upward trend, pointing to the growing intensity and power of modern music. This reflects the influence of high-energy genres like rock, hip-hop, and EDM, as well as advances in production that allow for punchier, more compressed sound.
Acousticness has declined over time, highlighting the movement away from organic instrumentation in favor of electronic and digital sounds. As music production has increasingly relied on synthesizers and drum machines, purely acoustic elements have become less common in mainstream tracks.
Valence, which measures the emotional positivity of music, has fluctuated but tends to trend lower in recent decades. This suggests a shift toward darker or more introspective themes, possibly reflecting broader cultural and emotional dynamics among listeners.
Instrumentalness has remained low, emphasizing the continued dominance of vocals in popular music. However, speechiness has slightly increased, likely due to the growing presence of rap, hip-hop, and spoken-word elements across genres.

---

## Popularity Analysis
- Scatter Plot: Popularity vs. Valence – This graph explores how Valence of a music correlates with its popularity. The distribution suggests a spread across the range, indicating that both happy (high valence) and moody (low valence) songs can achieve popularity. There isn’t a clear linear relationship, suggesting that popularity isn’t strictly tied to how emotionally positive a song sounds. 
- Box Plot: Popularity vs. Explicit Content – The explicit songs seem to have a higher median popularity, suggesting that they might attract more listeners overall.

---

## Genre Classification Strategy Prior to Recommendation System Development
Before building the music recommendation system, it was essential to ensure that each song in the main dataset was labeled with a genre. This was particularly important for enabling content-based and mood-based recommendation functionalities, as well as for improving cluster interpretability and user personalization. However, the main dataset lacked genre labels. To address this, a data-driven genre assignment strategy was developed.

### Approach: Representative Genre Mapping via Centroid Proximity
The genre assignment process followed a structured unsupervised learning approach, described as Representative Genre Mapping via Centroid Proximity (RGCP). This method involves clustering existing genre data and using those clusters to label songs in the main dataset.

Step 1: Clustering Genre Audio Profiles
Using the 'data_by_genres' dataset; which aggregates songs by their associated genres and averages their audio features; KMeans clustering was applied to group genres based on similar acoustic properties. The clustering was performed in a lower-dimensional space using PCA-transformed features to enhance separation and interpretability.

Step 2: Selecting Representative Genres
Each cluster produced by KMeans was represented by a centroid. To label these clusters:

- The Euclidean distance was computed between each cluster centroid and all genres in the PCA-transformed space.
- The closest genre (i.e., the genre whose average feature vector was nearest to the centroid) was selected as the representative label for that cluster.

This ultimately enabled the mapping of each cluster to a single, prototypical genre.

Step 3: Assigning Genres to Songs
Consequently, each song in the main dataset was predicted into one of the KMeans clusters using the .predict() method. The song was then assigned the representative genre corresponding to the predicted cluster.
This allowed for consistent and scalable genre labeling of all songs, based purely on their acoustic signatures.

By applying the Representative Genre Mapping via Centroid Proximity (RGCP) method, the dataset was effectively enhanced with genre labels. This step was critical in preparing the data for downstream tasks such as clustering visualization, mood-based filtering, and personalized music recommendations.

In this project, five different recommendation systems were implemented to provide music suggestions tailored to different user needs and listening contexts. Each system utilizes unique logic and data attributes to generate song recommendations. Below is a detailed explanation of each method, including its purpose, working principle, strengths, and limitations.

---

## Recommendation Systems

1. Content-Based Recommendation Using Song Features
The content-based recommendation system has been designed to recommend songs that are sonically similar to a given input song by leveraging the song's audio features.  The recommendation logic calculates the cosine similarity between the feature vector of the selected song and all other songs in the dataset. Based on this similarity score, it ranks and returns the top songs that are closest in sound and style to the input song. This system is based on the intrinsic qualities of the music itself rather than external metadata and as such will be vital for personalized recommendations. However, it can also lead to limited variety, since the recommendations tend to be acoustically very similar to the selected song. Moreover, it does not take user behavior or song popularity into account, which may result in less relevant suggestions for general audiences. 

2. Genre-Based Recommendation
The adopted genre-based recommendation system focuses on suggesting songs within a specified music genre. It works by filtering the dataset to include only those songs that belong to the chosen genre and then sorting them by popularity to select the top entries. This system leverages the genre labels assigned during preprocessing, which were obtained by clustering audio features and assigning representative genres to each cluster. This approach is useful for listeners who are interested in discovering or sticking to a particular genre. It offers quick access to the most popular songs in any given category, making it ideal for casual users or those looking to explore trending tracks. However, it lacks personalization since all users receive the same results for a given genre. Additionally, the approach's effectiveness is limited by the clustering methodology used during preprocessing. The elbow method determined only 10 clusters for genre classification, which appears insufficient to capture the nuanced diversity and sophisticated variations present across the musical landscape. This limitation increases the likelihood of genre misclassification and outliers, potentially leading to recommendations that don't align with users' expectations for their selected genre.
The system's reliance on popularity-based ranking, while ensuring mainstream appeal, may also overlook hidden gems or emerging artists that could better serve users seeking genre-specific discovery experiences.

3. Hybrid Recommendation System (Content + Popularity)
The hybrid recommendation system combines the strengths of both content-based and popularity-based approaches to produce more balanced and appealing recommendations. It begins by calculating cosine similarity between the input song and others based on audio features. Then, it normalizes each song’s popularity score to a 0–1 scale and combines it with the similarity score using a weighted average formula. This hybrid score ensures that the recommended songs are not only similar in audio profile to the original song but are also among the more popular selections in the dataset. The popularity weight parameter can be adjusted to control the balance between similarity and mainstream appeal. This system is effective for generating recommendations that are both relevant and recognizable. However, the approach's effectiveness relies on precise parameter calibration. Inadequate tuning may result in recommendations that skew heavily toward either popularity or similarity, potentially diminishing the hybrid system's core advantage. Additionally, the reliance on popularity metrics may inadvertently bias recommendations toward established artists, potentially limiting exposure to emerging or niche content that could better serve specific user preferences.

4. Mood-Based Recommendation System
The mood-based recommendation system is built to suggest songs based on the listener’s current emotional state. It defines five moods—happy, sad, energetic, calm, and danceable and uses a combination of audio feature thresholds (e.g., valence, energy, tempo, danceability) to filter songs that match the mood criteria. For instance, happy songs are identified by high valence and energy, while calm songs are selected based on low energy and tempo.

Happy Mood:
- Valence threshold: 75th percentile (top 25% of valence values)
- Energy threshold: 60th percentile (top 40% of energy values)
- Criteria: Songs with valence AND energy above their respective thresholds

Sad Mood:
- Valence threshold: 25th percentile (bottom 25% of valence values)
- Energy threshold: 40th percentile (bottom 40% of energy values)
- Criteria: Songs with valence AND energy below their respective thresholds

Energetic Mood:
- Energy threshold: 85th percentile (top 15% of energy values)
- Tempo threshold: 75th percentile (top 25% of tempo values)
- Criteria: Songs with energy AND tempo above their respective thresholds

Calm Mood:
- Energy threshold: 30th percentile (bottom 30% of energy values)
- Tempo threshold: 40th percentile (bottom 40% of tempo values)
- Criteria: Songs with energy AND tempo below their respective thresholds

Danceable Mood:
- Danceability threshold: 75th percentile (top 25% of danceability values)
- Criteria: Songs with danceability above the threshold

This method allows users to explore music that resonates with their mood or activity, making it ideal for emotional and situational listening. It enhances the user experience by offering mood-aligned music without needing explicit song names or genres. However, the thresholds used are rough estimates and might not match how people actually feel about music. The system also treats all users the same way and doesn't learn what each person likes.

5. Era-Based Recommendation System
The era-based recommendation system enables users to discover music from a specific time period by selecting a range of years. The system filters the dataset based on the song release year and sorts the results by popularity. This is particularly useful for listeners interested in nostalgia, historical exploration, or those who want to explore the musical landscape of a particular decade or era. While this system effectively supports time-based exploration, it is entirely dependent on the accuracy and availability of the song release year in the dataset.

---

## Unified Music Recommendation Tool
This helps users get song suggestions based on different types of input — such as a specific song, genre, mood, or time period. Depending on what you enter, it uses different methods behind the scenes to give the best recommendations.
The function supports four main types of input:
Song – You give the name of a song you like.
Genre – You enter a music genre like pop, hip-hop, or jazz.
Mood – You choose a mood like happy, sad, calm, energetic, or danceable.
Era – You provide a time range (like 2000–2010) to get songs from that period.

---

## Evolution of Music Features over Decades
This focused on visualizing how music characteristics have changed over time based on the analyzed data. Specifically, it:

- Groups songs by decade (1980s, 1990s, 2000s, etc.)
- Calculates the average values for 7 audio features (like energy, danceability, valence) for each decade
- Creates a radar chart that shows these feature patterns as  polygons, with each decade represented by a different colored shape

- Acousticness - Shows a dramatic decline over time, with 1920s music being highly acoustic (around 0.8) and steadily decreasing to about 0.6 by the 1950s, reflecting the shift from acoustic instruments to electric/amplified music.
- Valence - Remained relatively stable across decades, with all eras maintaining moderate to high valence levels (around 0.5-0.6), suggesting consistent emotional tone in popular music.
- Energy - Gradually increased from the 1920s to 1950s, indicating music became more energetic and dynamic over time.
- Danceability - Shows steady growth, particularly noticeable from the 1940s to 1950s, likely reflecting the rise of swing, big band, and early rock influences.
- Speechiness, Liveness, and Instrumentalness - All remained relatively low and stable across decades, indicating consistent focus on musical rather than spoken content, studio recordings, and vocal-centered compositions.
In summary the chart revealed music's transition from acoustic, folk-influenced styles of the 1920s toward more energetic, danceable, and electronically-enhanced music by the 1950s.

---

## Evolution of Music popularity over the century
This chart shows the evolution of music popularity metrics over nearly a century (1920-2020). Here are the observable key patterns for three distinct eras:
- 1920s-1940s: Low, Stable Period - Popularity scores remained very low (under 10) with minor fluctuations, likely reflecting limited recording technology, distribution methods, and measurement capabilities of the early music industry.
- 1950s-1960s: The Great Acceleration - A sharp, dramatic increase begins around 1950, with popularity scores jumping from near zero to around 35-40 by the late 1960s. This coincides with the rise of rock and roll, improved recording technology, radio expansion, and the birth of modern popular music.
- 1970s-2020s: Steady Exponential Growth - Consistent upward trajectory with popularity scores climbing from about 35 to over 60. The growth accelerates notably after 2000, likely reflecting the digital music revolution, streaming platforms, and global music accessibility.

## Dataset Shortcomings

Limited Genre Granularity
- Only 10 clusters identified through elbow method appears insufficient for capturing musical diversity
- Risk of genre misclassification and outliers due to oversimplified clustering
- Lack of hierarchical genre structures or sub-genre classifications

Missing Critical Information
- Song titles absent from data_w_genres.csv, limiting track-specific analysis
- Artist metadata incomplete across datasets
- Limited temporal granularity in some historical periods

Threshold Limitations
- Mood classification relies on heuristic percentile-based thresholds
- Fixed thresholds may not account for individual or cultural differences in musical perception
- Lack of validation against actual user mood preferences

Personalization Gaps
- Most recommendation systems lack user preference integration
- No demographic or behavioral data for personalized experiences
- Limited feedback mechanisms for system improvement

## Recommendations for Future Research

Enhanced Genre Classification
- Hierarchical Clustering: Implement multi-level genre classification capturing main genres, sub-genres, and micro-genres
- Dynamic Clustering: Develop adaptive clustering methods that can adjust to evolving musical styles
- Expert Validation: Incorporate musicologist input to validate and refine genre classifications
- Cross-Dataset Validation: Use multiple music databases to verify genre accuracy

Improved Recommendation Systems
- User Profiling: Integrate demographic data, listening history, and explicit preferences
- Contextual Awareness: Consider time of day, activity, and location for context-aware recommendations
- Feedback Integration: Implement rating systems and implicit feedback collection
- A/B Testing Framework: Systematic testing of different recommendation approaches

Technical Improvements
- Scalability Solutions: Implement distributed computing for large-scale analysis
- Real-Time Processing: Develop streaming analytics for live recommendation updates
- API Integration: Connect with major music platforms for comprehensive data access
- Privacy-Preserving Methods: Implement differential privacy for user data protection
