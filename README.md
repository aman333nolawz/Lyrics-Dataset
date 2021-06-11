
# Lyrics-Dataset

A Dataset of lyrics by some of the best singers, Djs, musicians, etc.

# Contributing Lyrics

You can run this code and pull that folder into this repo.
```python
import requests
from bs4 import BeautifulSoup
import json
import os
import threading

GENIUS_API_TOKEN = "<Your API Token Here>"

def request_artist_info(artist_name, page):
    base_url = 'https://api.genius.com'
    headers = {'Authorization': 'Bearer ' + GENIUS_API_TOKEN}
    search_url = base_url + '/search?per_page=25&page=' + str(page)
    data = {'q': artist_name}
    response = requests.get(search_url, data=data, headers=headers)
    return response.text

def scrape_song_lyrics(url):
    print("Scraping: ", url, "...", sep="")
    page = requests.get(url)
    html = BeautifulSoup(page.text, 'html.parser')
    lyrics = html.find('div', class_='lyrics').get_text()
    lyrics = os.linesep.join([s for s in lyrics.splitlines() if s])
    return lyrics

def dump_lyrics(song):
    url = song["result"]["url"]
    title = song["result"]["title"]
    try:
        lyrics = scrape_song_lyrics(url)

        with open(f"./{artist.lower()}/{title.replace('/', ' or ')}.txt", "w") as f:
            f.write(lyrics)
    except AttributeError:
        dump_lyrics(song)

def thread_scraping(index):
    dump_lyrics(songs[index])


artist = "Justin Bieber"

if not os.path.exists(f"./{artist.lower()}"):
    os.mkdir(f"./{artist.lower()}")

songs = json.loads(request_artist_info(artist, 1))["response"]["hits"]
for j in range(len(songs)):
    threading.Thread(target=thread_scraping, args=(j,)).start()

```
