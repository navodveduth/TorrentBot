# What is this repo?
This is a repo to create a Telegram bot that uses aria2 to mirror files from the internet or torrents into Google Drive. This can be deployed onto a personal server using Docker or on Heroku.

## Features supported:
- Mirroring direct download links to Google Drive
- Mirroring torrent magnet links to Google Drive
- Mirroring downloads into a Team Drive
- Download/upload speeds and ETAs
- Index Link support

## Disclaimer
- ### Credit for all code goes to https://github.com/lzzy12/
- This process will take approximately 30 minutes to setup
- You will need a Linux terminal to set this up. Windows user can also complete this using something called WSL. You can read about it online
- You will need to deploy this bot either on your own server using Docker or on Heroku using a free Heroku account
- I do not recommend using this bot on Telegram groups with large amounts of people as it will overload the bot and ultimately cause Heroku to ban your account
- All of this tutorial will be done for Ubuntu systems, so commands will be different for other Linux systems

# Tutorial
### Prerequisites
Before beginning on the Terminal you will need to run the following commands:
```
sudo apt-get update
sudo apt-get upgrade
sudo apt install python3
sudo apt install python3-pip
sudo apt install npm
sudo apt install snapd
sudo apt install python-pip
sudo apt install git-all
curl https://cli-assets.heroku.com/install-ubuntu.sh | sh
```
This will take a long time esspecially if you are running Linux on WSL or you have slow internet

Clone this repo:
```
git clone https://github.com/eliasbenb/torrentbot/
cd TorrentBot
```
For WSL users:
- If you are on WSL you will need to install a file explorer
- You will also need to learn how to use that file explorer
- I reccomend this one:
```
sudo apt-get install mc
```
### Setting up config file
Now you will need to create a config file. Run this command to copy my template into a new config file
```
cp config_sample.env config.env
```
- Remove the first line saying:
```
_____REMOVE_THIS_LINE_____=True
```
Fill up rest of the fields. Meaning of each fields are discussed below:
- BOT_TOKEN : The telegram bot token that you get from @BotFather
- GDRIVE_FOLDER_ID : This is the folder ID of the Google Drive Folder to which you want to upload all the mirrors.
- DOWNLOAD_DIR : The path to the local folder where the downloads should be downloaded to
- DOWNLOAD_STATUS_UPDATE_INTERVAL : A short interval of time in seconds after which the Mirror progress message is updated. (I recommend to keep it 5 seconds at least)  
- OWNER_ID : The Telegram user ID (not username) of the owner of the bot
- AUTO_DELETE_MESSAGE_DURATION : Interval of time (in seconds), after which the bot deletes it's message (and command message) which is expected to be viewed instantly. Note: Set to -1 to never automatically delete messages
- IS_TEAM_DRIVE : (Optional field) Set to "True" if GDRIVE_FOLDER_ID is from a Team Drive else False or Leave it empty. 
- INDEX_URL : (Optional field) Refer to https://github.com/maple3142/GDIndex/ The URL should not have any trailing '/'

Note: You can limit maximum concurrent downloads by changing the value of MAX_CONCURRENT_DOWNLOADS in aria.sh. By default, it's set to 2

Now you need to setup authorized_chats.txt. So find it in your files and open it
Remove all the data in it currently. In an empty line just put your OWNER_ID (No need to put anything else) then save the file
If you want to use the bot in a group you need the group's chat ID. To get this invite @RawDataBot to your group. The bot will output a bunch of data. The valua you want should look like this: -100000000

### Getting Google OAuth API credential file

- Visit the Google Cloud Console
- Go to the OAuth Consent tab, fill it, and save.
- Go to the Credentials tab and click Create Credentials -> OAuth Client ID
- Choose Other and Create.
- Use the download button to download your credentials.
- Move that file to the root of TorrentBot, and rename it to credentials.json
- Finally, run the script to generate token file (token.pickle) for Google Drive:
```
pip3 install google-api-python-client google-auth-httplib2 google-auth-oauthlib
python3 generate_drive_token.py
```
Note: for WSL users this won't work. You have to upload the credentials.json file to a cloud storage and download the file on WSL using 
```
wget www.insertlinkhere.com/downloadlink/credentials.json
```
### Deploying on Heroku
- Login into your heroku account with command:
```
heroku login
```
- Setup the git repositry:
```
git init
```
```
git add .
```
```
git commit -m "My first commit"
```
- Create a new heroku app:
```
heroku create appname	
```
- Select This App in your Heroku-cli: 
```
heroku git:remote -a appname
```
- Change Dyno Stack to a Docker Container:
```
heroku stack:set container
```
- Push Code to Heroku:
```
git push heroku master --force
```
- Restart Worker by these commands:
```
heroku ps:scale worker=0
```
```
heroku ps:scale worker=1
```
