# 🎵 Music Recommender Simulation

## Project Summary

This project simulates a content-based music recommender system. Given a user's taste profile — a preferred genre, mood, and energy level — the system scores every song in a CSV catalog and returns the top matches ranked by relevance. It is designed to show how real platforms like Spotify translate user preferences into ranked lists using simple math, without requiring listening history or other users' data.

---

## How The System Works

Real-world platforms like Spotify combine two main strategies: **collaborative filtering** (finding users who listen to the same things as you and borrowing their taste) and **content-based filtering** (matching songs to your preferences based on audio features like tempo, energy, or mood). This simulation focuses on content-based filtering because it only requires song attributes and a single user profile, making the logic transparent and easy to inspect.

**Algorithm Recipe:**

- `+2.0` points if the song's genre matches the user's favorite genre
- `+1.0` point if the song's mood matches the user's favorite mood
- `+0.0 to 1.0` points based on how close the song's energy is to the user's target (calculated as `1.0 - |song_energy - target_energy|`)
- `+0.5` bonus if the user likes acoustic music and the song has acousticness ≥ 0.7

The song with the highest total score is ranked first.

**Features used per Song:** `genre`, `mood`, `energy`, `acousticness`

**Features stored in UserProfile:** `favorite_genre`, `favorite_mood`, `target_energy`, `likes_acoustic`

**Potential bias note:** Genre is worth twice as much as mood, so songs with a matching genre will almost always outrank songs that match in every other way. This could create a genre filter bubble.

---

## Getting Started

### Setup

1. Create a virtual environment (optional but recommended):

   ```bash
   python -m venv .venv
   source .venv/bin/activate      # Mac or Linux
   .venv\Scripts\activate         # Windows
   ```

2. Install dependencies:

   ```bash
   pip install -r requirements.txt
   ```

3. Run the app:

   ```bash
   python -m src.main
   ```

### Running Tests

```bash
pytest
```

---

## Experiments You Tried

- **Default pop/happy profile:** Top results were "Sunrise City," "Bubble Tea Drive," and "Gym Hero" — all pop songs, which confirmed the genre weight dominates.
- **Chill lofi profile** (`genre: lofi, mood: chill, energy: 0.4`): Correctly surfaced "Library Rain" and "Midnight Coding" at the top.
- **High-energy rock profile** (`genre: rock, mood: intense, energy: 0.9`): "Storm Runner" and "Iron Sky" ranked first, which matched expectations.
- **Weight experiment:** Doubling energy weight and halving genre weight caused more mixing across genres — "Gym Hero" (pop) appeared in rock results because of matching energy.

---

## Limitations and Risks

- The catalog is tiny (20 songs), so results are not truly diverse.
- Genre dominates scoring — a chill lofi song will beat a great mood/energy match in another genre.
- The system cannot learn from listening history; it always uses the same static profile.
- Niche genres like folk or ambient are underrepresented, so users with those tastes get fewer good matches.

---

## Reflection

See [model_card.md](model_card.md) for the full model card and personal reflection.

Building this showed how even the simplest scoring rules can produce results that feel surprisingly "smart" — matching a genre and energy level is enough to make recommendations feel personal. However, it also revealed how easy it is to accidentally bake in bias: because genre is worth 2 points and mood is worth 1, the system quietly tells users that their genre matters twice as much as how a song makes them feel, which is not necessarily true. Real platforms have to make these same tradeoffs, just with thousands of features instead of five.
