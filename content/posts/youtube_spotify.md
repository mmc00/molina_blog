---
title: "Youtube playlist songs to spotify"
date: 2022-08-31T19:40:41Z
draft: false
tags: ["personal", "youtube", "mix", "spotify"]
categories: ["projects"]
featuredImage: "/img/spotify_youtube.jpg"
---

Since I started working out, the music proved to be a motivator. 
When I work I usually have a **Mix** of youtube in the background and 
I love how youtube adapts to my preferences, spotify also does a good job but not as accurate.
So my "motivational" mix was on youtube. When I jog I don't have too good of an
internet connection and I spend my data on playing songs as videos because I don't have those songs on spotify. 

So I decided to move my songs between platforms using python. it's a pretty easy job!
First of all, I realize that a playlist is different from a mix. The latter is not easy to manipulate and the preferred way to get the songs is to use web scraping.
to get the songs is to use web scraping. I didn't want that, so thanks to a guy who shows how to convert a mix into a playlist in 
a mix into a playlist on [reddit](https://www.reddit.com/r/youtube/comments/b33ecs/how_can_you_save_a_youtube_mix_into_a_playlist/), you basically 
you just add a random song in the mix (I did it with a random song that usually appears below the mix list), and after that
a button magically appears at the top:

<br>

![botom](/img/yt_bottom.png#center "Bottom")

We have a playlist, is easier now. Using the library pytube with four lines of
code you have the songs title.

{{< highlight python >}}
import pytube
playlist = pytube.Playlist("https://www.youtube.com/playlist?list=PLWYOEQl110tLJZ1ndhpbVsCRpdoF1Wj8T")
raw_songs = []
for song in playlist:
    raw_songs.append(pytube.YouTube(song).streams[0].title)
{{< /highlight >}}

Obviously, the song titles needed some cleanup. By using some stop words
we can eliminate words that can confuse the search in the Spotify app.
We also transformed the list into a dict, where each title is the key and 
the artist is the value (because most likely the artist can appear several times
with different songs).

{{< highlight python >}}
words = raw_songs
stopwords = [' (Official Music Video)', ' [Official Music Video]',
' (Official 4K Video)', '(Official Video)',
' (from the series Arcane League of Legends)', ' (Performance Edit)',
' (Alternate Version)', '│ Subtitulado al español']
new_list = []
for word in words:
    new_word = word
    for stop in stopwords:
        if stop in new_word:
            new_word = new_word.replace(stop, '')
        else:
            new_word = new_word
    new_list.append(new_word.strip())

songs_dict = {}
for song in new_list:
    songs_dict[song.split("-")[1].strip()] = song.split("-")[0].strip()
songs_dict

{{< /highlight >}}

It's time to switch to Spotify, which has a wonderful API. 
You need to have a client id, the Spotify secret and the redirect url.
If you don't know how to get those values,
[Dan Arwady](https://www.youtube.com/watch?v=3RGm4jALukM) has a great video that explains it all.

![youknow](/img/youknow.jfif#center "You know")

Finally, using the spotipy library, it is easy to create a playlist with the previous dict:

{{< highlight python >}}
import requests
import spotipy
from spotipy.oauth2 import SpotifyOAuth
from spotipy.oauth2 import SpotifyClientCredentials
from pprint import pprint

## params
SPOTIFY_CLIENT_ID = ""
SPOTIFY_SECRET = ""
REDIRECT_URL = ""

## api call
sp = spotipy.Spotify(
    auth_manager=SpotifyOAuth(
        scope="playlist-modify-private",
        redirect_uri=REDIRECT_URL,
        client_id=SPOTIFY_CLIENT_ID,
        client_secret=SPOTIFY_SECRET,
        cache_path="token.txt"
    )
)

## searching
spotify_song_uris = []
for key, value in songs_dict.items():
    spotify_result = sp.search(q=f"artist:{value} track:{key}", type="track")
    try:
        song_uri = spotify_result['tracks']['items'][0]['uri']
        spotify_song_uris.append(song_uri)
    except IndexError:
        print(f"{value} doesn't exist in Spotify. Skipped.")
{{< /highlight >}}

In my case, four of the song titles that appear on youtube are not identifiable
in Spotify, so I had to clean them manually:

{{< highlight python >}}
## fixing some songs titles
new_list2 = new_list.copy()
new_list2[4] = 'Imagine Dragons - Enemy (with JID)'
new_list2[9] = 'DJ Snake, Justin Bieber - Let Me love You'
new_list2[12] = 'Eminem - Lose Yourself'
new_list2[13] = 'David Guetta, Sia - Titanium (feat. Sia)'
new_list2[16] = 'Sia - Elastic Heart'

## creating the dict again
songs_dict2 = {}
for song in new_list2:
    songs_dict2[song.split("-")[1].strip()] = song.split("-")[0].strip()
songs_dict2
{{< /highlight >}}

When the search was performed again, all songs were identified:

{{< highlight python >}}
spotify_song_uris = []
for key, value in songs_dict2.items():
    spotify_result = sp.search(q=f"artist:{value} track:{key}", type="track")
    try:
        song_uri = spotify_result['tracks']['items'][0]['uri']
        spotify_song_uris.append(song_uri)
    except IndexError:
        print(f"{value} doesn't exist in Spotify. Skipped.")

print(len(spotify_song_uris))
{{< /highlight >}}

We add the songs to a playlist called: **running_songs**:

{{< highlight python >}}
user_id = ''
my_playlist = sp.user_playlist_create(user=f"{user_id}",
                                      name="running_songs",
                                      public=False,
                                      description="songs to run")
sp.user_playlist_add_tracks(user=f"{user_id}",
                            playlist_id=my_playlist["id"],
                            tracks=spotify_song_uris)
{{< /highlight >}}

If you look at your spotify playlist, you have a new one:

![appsong](/img/sposongs.jpeg#center "New playlist")

So, you can do the same with every mix you have.
I've created a class with this code and a notebook on [github](https://github.com/mmc00/youtb2spotify) in case you need it