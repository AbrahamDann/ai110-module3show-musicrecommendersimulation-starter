# 🎧 Model Card: Music Recommender Simulation

## 1. Model Name

**VibeFinder 1.0**

---

## 2. Intended Use

This system suggests up to 5 songs from a small catalog based on a user's preferred genre, mood, and energy level. It is designed for classroom exploration of how content-based recommendation algorithms work. It is not intended for real users or production music platforms — the catalog is too small and the scoring logic too simple for meaningful real-world use.

---

## 3. How the Model Works

The recommender compares each song in the catalog to a user's taste profile using three rules. First, it checks whether the song's genre matches the user's favorite genre — if it does, the song earns 2 points. Second, it checks whether the mood matches — a match earns 1 point. Third, it measures how close the song's energy level is to the user's target energy on a 0 to 1 scale; a song with identical energy earns a full point, and the score shrinks the farther away it is. Finally, if the user prefers acoustic music and the song is highly acoustic, it earns a small bonus. Every song in the catalog gets scored this way, then they are sorted from highest to lowest. The top 5 are the recommendations.

---

## 4. Data

The dataset contains 20 songs stored in `data/songs.csv`. Each song has the following attributes: id, title, artist, genre, mood, energy (0.0–1.0), tempo_bpm, valence, danceability, and acousticness. Genres represented include pop, lofi, rock, ambient, jazz, synthwave, folk, electronic, hip-hop, and indie pop. Moods include happy, chill, intense, relaxed, moody, focused, nostalgic, and melancholic. The dataset was partially generated for this simulation and does not reflect real chart data or any particular demographic's listening habits.

---

## 5. Strengths

The system works well for users with clear, common preferences. A "pop/happy/high-energy" profile consistently surfaces upbeat pop songs that match the vibe. The scoring logic is fully transparent — every recommendation comes with a written explanation of exactly why it ranked where it did. The simplicity also makes it easy to audit: if a result looks wrong, you can inspect the weights and understand the cause immediately.

---

## 6. Limitations and Bias

The genre weight (2.0) is double the mood weight (1.0), which means genre match dominates results. A song that perfectly matches the user's mood and energy but has the wrong genre will usually lose to a song that only matches on genre. This creates a genre filter bubble — users may never discover great songs outside their stated genre. Additionally, pop is the most represented genre in the dataset (4 songs), which means pop listeners get more variety while folk or ambient listeners have fewer good matches. The system also assumes users have a single fixed energy preference, which does not reflect how real listening habits shift throughout the day.

---

## 7. Evaluation

Three user profiles were tested:

- **High-Energy Pop / Happy:** Returned "Sunrise City," "Bubble Tea Drive," "Gym Hero," "Rooftop Lights," and "Night Drive Loop." The first three felt right; "Night Drive Loop" appeared because of strong energy match despite being synthwave/moody, which was a surprise.
- **Chill Lofi / Focused:** Returned lofi catalog entries as expected. Results felt accurate.
- **Intense Rock:** Returned "Storm Runner" and "Iron Sky" at the top, which matched intuition. The third slot went to an electronic song with high energy, showing how energy score bleeds across genres.

One experiment doubled the energy weight. This caused more genre mixing and surfaced unexpected cross-genre results, suggesting energy alone is a strong differentiator even without genre alignment.

---

## 8. Future Work

- Add collaborative filtering so the system can use listening history and improve recommendations over time.
- Introduce a diversity penalty to prevent the top 5 from being dominated by a single genre or artist.
- Expand the catalog to hundreds of songs so niche genre and mood combinations have enough matches.
- Allow users to specify a range for energy instead of a single target value, which would better capture how taste shifts with context (working out vs. studying).

---

## 9. Personal Reflection

Building VibeFinder 1.0 made it clear how much "intelligence" in a recommender comes from the weights, not from any deep understanding of music. The system does not know what a song sounds like — it just does math on a few numbers. What surprised me most was how convincing the results felt even with such simple logic. It also changed how I think about Spotify's recommendations: every time it suggests something, there is a scoring function underneath that is not that different in concept from this one, just trained on vastly more data and features. The hardest design decision was choosing how much to weight genre versus mood — and realizing there is no objectively correct answer makes it obvious why algorithmic bias is a real problem in production systems.
