# Music Recommender System

A simple web-based music recommender system built with Streamlit and Spotify API. This app suggests similar songs based on your selection and displays their album covers.

---

## Features
- Select a song from a dropdown list
- Get 5 similar song recommendations
- View album covers for each recommended song

---

## Demo
![Demo Screenshot](https://i.postimg.cc/0QNxYz4V/social.png)

---

## Getting Started

### Prerequisites
- Python 3.7+
- [Streamlit](https://streamlit.io/)
- [Spotipy](https://spotipy.readthedocs.io/en/2.22.1/)

### Installation

1. **Clone the repository:**

```bash
# Copy this command
git clone https://github.com/arkapratimdnath/music-recomender.git
cd music-recomender/arkapratimdnath-music-recomender
```

2. **Install dependencies:**

```bash
# Copy this command
pip install streamlit spotipy
```

3. **Ensure the following files are present:**
- `app.py`
- `df.pkl`
- `similarity.pkl`

> **Note:** The `.pkl` files are required for the app to function. They contain the song data and similarity matrix.

---

## Usage

Run the Streamlit app:

```bash
# Copy this command
streamlit run app.py
```

This will open the app in your default web browser.

---

## How It Works

- The app loads a DataFrame of songs and a similarity matrix from pickle files.
- When a user selects a song, the app finds the 5 most similar songs using the similarity matrix.
- For each recommended song, the app fetches the album cover using the Spotify API.

---

## Code Overview

### Main Application (`app.py`)

```python
import pickle
import streamlit as st
import spotipy
from spotipy.oauth2 import SpotifyClientCredentials

CLIENT_ID = "<your_spotify_client_id>"
CLIENT_SECRET = "<your_spotify_client_secret>"

# Initialize the Spotify client
client_credentials_manager = SpotifyClientCredentials(client_id=CLIENT_ID, client_secret=CLIENT_SECRET)
sp = spotipy.Spotify(client_credentials_manager=client_credentials_manager)

def get_song_album_cover_url(song_name, artist_name):
    search_query = f"track:{song_name} artist:{artist_name}"
    results = sp.search(q=search_query, type="track")
    if results and results["tracks"]["items"]:
        track = results["tracks"]["items"][0]
        album_cover_url = track["album"]["images"][0]["url"]
        return album_cover_url
    else:
        return "https://i.postimg.cc/0QNxYz4V/social.png"

def recommend(song):
    index = music[music['song'] == song].index[0]
    distances = sorted(list(enumerate(similarity[index])), reverse=True, key=lambda x: x[1])
    recommended_music_names = []
    recommended_music_posters = []
    for i in distances[1:6]:
        artist = music.iloc[i[0]].artist
        recommended_music_posters.append(get_song_album_cover_url(music.iloc[i[0]].song, artist))
        recommended_music_names.append(music.iloc[i[0]].song)
    return recommended_music_names, recommended_music_posters

st.header('Music Recommender System')
music = pickle.load(open('df.pkl','rb'))
similarity = pickle.load(open('similarity.pkl','rb'))

music_list = music['song'].values
selected_movie = st.selectbox(
    "Type or select a song from the dropdown",
    music_list
)

if st.button('Show Recommendation'):
    recommended_music_names, recommended_music_posters = recommend(selected_movie)
    col1, col2, col3, col4, col5 = st.columns(5)
    with col1:
        st.text(recommended_music_names[0])
        st.image(recommended_music_posters[0])
    with col2:
        st.text(recommended_music_names[1])
        st.image(recommended_music_posters[1])
    with col3:
        st.text(recommended_music_names[2])
        st.image(recommended_music_posters[2])
    with col4:
        st.text(recommended_music_names[3])
        st.image(recommended_music_posters[3])
    with col5:
        st.text(recommended_music_names[4])
        st.image(recommended_music_posters[4])
```

---

## Environment Variables

Replace `<your_spotify_client_id>` and `<your_spotify_client_secret>` in `app.py` with your own Spotify API credentials. You can get them from the [Spotify Developer Dashboard](https://developer.spotify.com/dashboard/applications).

---

## License

This project is licensed under the MIT License.

---

## Acknowledgements
- [Streamlit](https://streamlit.io/)
- [Spotipy](https://spotipy.readthedocs.io/en/2.22.1/)
- [Spotify Developer Platform](https://developer.spotify.com/) 