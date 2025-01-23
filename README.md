# Zotify Fork
### See https://github.com/zotify-dev/zotify for docs

This fork provides workarounds for the audio key error which occurs when you hit spotify's rate limit (see https://github.com/zotify-dev/zotify/issues/186). Once you hit the rate limit the it takes a significant amount of time to cool off. So there are 2 steps to the solution.
1. Sleep after each successful download. @HxDxRx found that the optimal sleep time is 30 seconds (see https://github.com/zotify-dev/zotify/issues/186#issuecomment-2608381052). This prevents from   hitting the rate limit
  **Implementation:** Set --get-bulk-wait-time 30
2. If rate limit is hit and audio key error is raised, sleep and retry

    **Implementation:** The version of librespot referenced in the setup.cfg is different to the version of librespot referenced in the requirements.txt. The former does not implement retries for audio key errors where as the latter does. This means that to get the version with retry logic implemented you have to clone the repo into your local environment and then install from that (which uses the requirements.txt rather than the setup.cfg).

## Instructions  
I am running ubuntu 22.04. If you are on windows I suggest you install Windows Subsytem for Linux (https://learn.microsoft.com/en-us/windows/wsl/install) because Windows Sux

### Requirements
pyenv ffmpeg and python 3.11
```
Install ffmpeg
sudo apt update
sudo apt install ffmpeg

Install pyenv, or your preferred env tool (https://github.com/pyenv/pyenv)
curl -fsSL https://pyenv.run | bash

Install python 3.11
pyenv install 3.11
```
### Get credentials
The current code on the main branch does not authenticate correctly. As a work around for this clone the dev branch, log in and extract the credentials.json file
```
Create venv
pyenv virtualenv 3.11 {env_name}

Clone repo and install
git clone --branch v1.0-dev https://github.com/zotify-dev/zotify.git
cd zotify
pip install .

Run any zotify command and follow the steps to authenticate. This will out put credentials to /.config/Zotify/credentials.json . Save this credentials file somewhere for later.

Deactivate env
pyenv deactivate {venv_name}
```

### Install

```
Create a fresh environment
pyenv virtualenv 3.11 {venv_name}

Clone this repo and install zotify
git clone https://github.com/bgeorgakas/zotify.git
cd zotify
pip install .
```

### Usage
links.txt is a text file with all the links i want to download

```
zotify -d {links.txt} --credentials-location {path to credentials.json extracted from other version} --bulk-wait-time 30
```
### Notes
I have made modifications to suit my use case. They are:
- Only one artist is stored in metadata
- Every song is stored as {artist}/{album}/{title}.{ext}
- Every album stores a cover.jpg file which is the cover photo for that album
- For playlist downloads, an m3u file is created which lists of the songs in the playlist. These files are stored in Music/Playlists. The pats in the m3u files use 'A:/Songs' as root as this is what is requirement for my mp3 player. Feel free to change this to whatever by modifyying the PLAYLIST_ROOT variable in app.py
