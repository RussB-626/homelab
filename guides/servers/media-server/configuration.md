# Media-Server Configuration

## Notes

- On TrueNAS make sure the share has the correct permissions
  - Shares > Selected Share > Click Edit Button
  - Advanced Options
  - Mapall User: root
  - Mapall Group: root
  - Add Host IP addresses
    - <server_ip> - Media Server

## Online Guide Docs

- [https://trash-guides.info/](https://trash-guides.info/)

## List Of Apps And What They Do

- Gluetun:     vpn
- Jellyfin:    streaming service
- Prowlarr:    indexer
- Radarr:      movies
- SABnzbd:     downloader
- Sonarr:      television
- qBittorrent: downloader

## Install Apps And Mount Network File Shares

### Prepare Directories

- cd /mnt
- mkdir MediaServer

### Install Necessary Applications For Mounting

- apt update -y && apt upgrade -y
- apt install nfs-common -y

### Install Docker

- [https://docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)

### Mount Share To Directory

- mount <nas_ip>:/mnt/HDDs/MediaServer /mnt/MediaServer

### Auto Mount Share On Reboot

- nano /etc/fstab
- Add the following line to the bottom of the file
  - <nas_ip>:/mnt/HDDs/MediaServer /mnt/MediaServer nfs defaults 0 0

## MediaServer NFS Directory Setup

- /MediaServer
  - /data
    - /media
      - /anime_movies
      - /anime_tv
      - /movies
      - /tv
    - /usenet
      - /complete
        - /anime_movies
        - /anime_tv
        - /movies
        - /tv
      - /incomplete
  - /docker
    - /portainer
      - docker-compose.yaml
      - .env
      - /data
    - /stack
      - docker-compose.yaml
      - .env
      - /configs
        - /firefox
        - /gluetun
        - /huntarr
        - /jellyfin
        - /notifiarr
        - /prowlarr
        - /radarr
        - /sabnzbd
        - /sonarr

## Stacks Setup

### Update Filebrowser Container settings file

json
{
  "port": 80,
  "baseURL": "",
  "address": "",
  "log": "stdout",
  "database": "/database.db",
  "root": "/srv"
}


### SABnzbd - First Time Login Setup

#### SABnzbd - On Initial Setup

- Setup Newshosting login info
- Can be setup later

#### SABnzbd - Servers Setup

- Server Tab >
- Click Add Server
- Input Host
  - e.g. news.newshosting.com
- Input Username
- Input Password
- Click Test Server and verify connection
- Click Save Changes

#### SABnzbd - Folders Setup

- Folders Tab >
- Temporary Download Folder: /incomplete-downloads
- Completed Download Folder: /downloads

#### SABnzbd - Update Categories

- Categories Tab >
- If not added add the following new categories, else modify existing categories as such
- Make sure to click save after editing a row

  | Category     | Priority | Processing | Script | Folder/Path  | Indexer Categories/Groups |
  | :----------- | -------- | :--------- | ------ | -------------| :------------------------ |
  | anime_movies | Default  | Default    |        | anime_movies |                           |
  | anime_tv     | Default  | Default    |        | anime_tv     |                           |
  | movies       | Default  | Default    |        | movies       |                           |
  | tv           | Default  | Default    |        | tv           |                           |

#### SABnzbd - Record API Key

- General Tab >
- Under Security section
- Save the API Key somewhere safe

---

### Radarr - First Time Login Setup

#### Radarr - Authentication Popup Setup

- On First Time Login
- Select "Forms (Login Page)" or "Basic (Browser Popup)" for "Authentication Method"
- Input new Username and Password

#### Radarr - Download Clients Setup

- Setup for SABnzbd Download Client
  - Settings > Download Clients >
  - Under Download Clients
  - Click the box with +
  - Under Usenet select SABnzbd
  - Update Host field to ([^1]localhostip)
  - Input API Key from SABnzbd into the API Key field
  - Input Username and Password from SABnzbd website
  - Input the category created in SABnzbd for this Radarr instance created in  SABnzbd
    - e.g. "movies" or "anime"
  - Click Test and verify connection
  - Click Save

#### Radarr - Media Management Root Folders Setup

- Settings > Media Management >
- Under Root Folders:
  - Click "Add Root Folder"
  - Navigate to the appropriate folder directory for this Radarr instance
    - e.g. "/data/media/movies" or "/data/media/anime"

#### Radarr - Record API Key

- Settings > General >
- Under Security section
- Save the API Key somewhere safe

---

### Sonarr - First Time Login Setup

#### Sonarr - Authentication Popup Setup

- On First Time Login
- Select "Forms (Login Page)" or "Basic (Browser Popup)" for "Authentication Method"
- Input new Username and Password

#### Sonarr - Download Clients Setup

- Setup for SABnzbd Download Client
  - Settings > Download Clients >
  - Under Download Clients
  - Click the box with +
  - Under Usenet select SABnzbd
  - Update Host field to ([^1]localhostip)
  - Input API Key from SABnzbd into the API Key field
  - Input Username and Password from SABnzbd website
  - Input the category created in SABnzbd for this Radarr instance created in  SABnzbd
    - e.g. "tv" or "anime"
  - Click Test and verify connection
  - Click Save

#### Sonarr - Media Management Root Folders Setup

- Settings > Media Management >
- Under Root Folders:
- Click "Add Root Folder"
- Navigate to the appropriate folder directory for this Radarr instance
  - e.g. "/data/media/tv" or "/data/media/anime"

#### Sonarr - Record API Key

- Settings > General >
- Under Security section
- Save the API Key somewhere safe

---

### Prowlarr - First Time Login Setup

#### Prowlarr - Authentication Popup Setup

- On First Time Login
- Select "Forms (Login Page)" or "Basic (Browser Popup)" for "Authentication Method"
- Input new Username and Password

#### Prowlarr - Adding An Indexer

- Add NZBgeek Indexer
  - Indexers >
  - Click "Add Indexer"
  - Type in "NZBgeek" in the "Search indexers" field
  - Select the NZBgeek Indexer from the pool
  - Input API Key from NZBgeek website into the API Key field
  - Click Test and verify connection
  - Click Save

#### Prowlarr - Radarr APP Setup [Done after Radarr setup]

- Settings > Apps >
- Under Applications
- Click Box with +
- Select the Radarr App
- Update the Name to reflect the specific Radarr instance
  - e.g. "Radarr - Movies" or "Radarr - Anime"
- Update Prowlarr Server field to http://([^1]localhostip):9696
- Update Radarr Server field to http://([^1]localhostip):7878
  - make sure the appropriate port for the specific Radarr instance is used
- Input the API Key from the appropriate Radarr instance
- Click Test and verify connection
- Click Save

#### Prowlarr - Sonarr APP Setup [Done after Sonarr setup]

- Settings > Apps >
- Under Applications
- Click Box with +
- Select the Sonarr App
- Update the Name to reflect the specific Sonarr instance
  - e.g. "Sonarr - TV" or "Sonarr - Anime"
- Update Prowlarr Server field to http://([^1]localhostip):9696
- Update Sonarr Server field to http://([^1]localhostip):8989
  - make sure the appropriate port for the specific Sonarr instance is used
- Input the API Key from the appropriate Sonarr instance
- Click Test and verify connection
- Click Save

---

### Additional Radarr Settings

#### Radarr TRaSH Guides

- [TRaSH: Radarr Guide](https://trash-guides.info/Radarr/)
- Keep in mind which Radarr instance changes should be made to
  - e.g. "Radarr - Movies" or "Radarr - Anime"

#### Radarr - Recommended Naming Scheme Settings

- __Keep In Mind The Following__: We are using Jellyfin for our Media Server, and we want to use Community sourced information
- Use the following TRaSH guide for naming scheme settings
  - [TRaSH: Radarr - Recommended Naming Scheme](https://trash-guides.info/Radarr/Radarr-recommended-naming-scheme/)
- Settings > Media Management >
- Click "Show Advanced" at the top left
- Input the selected Jellyfin (TMDb) Movie Format, found from TRaSH guides, into the "Standard Movie Format" field
  - Make sure to select the appropriate Movie Format based on the Radarr instance
  - e.g. "Radarr - Movies" will use "Jellyfin (TMDb)" and "Radarr - Anime" will use "Jellyfin Anime (TMDb)"
- Input the selected "Optional Jellyfin" Movie Folder Format, found from TRaSH guides, into the "Movie Folder Format" field.
- Click "Save Changes" at the top left

#### Radarr - Profiles Setup (Part 1)

- Settings > Profiles >
- Delete all default Profiles
- Create a new profile based on the specific Radarr Instance
  - The specific profile to create depends on what quality of content you are looking for which can be determined using the flowchart found on [TRaSH: Radarr - Quality Profiles](https://trash-guides.info/Radarr/radarr-setup-quality-profiles/)
  - e.g. For "Radarr - Movies" create the "Remux + WEB 1080p" profile, and for "Radarr - Anime" create the "Anime" profile
- Select the appropriate Qualities for the specific Radarr Instance
  - The specific qualities come from the TRaSH guides based on the Profile selected
- Move them into the correct positions
- Check off "Upgrades Allows"
- Select "Remux-1080p" for "Upgrade Until"
- Click "Save" at the bottom right of the popup window

---

### Additional Sonarr Settings

#### Sonarr TRaSH Guides

- [TRaSH: Sonarr Guide](https://trash-guides.info/Sonarr/)
- Keep in mind which Sonarr instance changes should be made to
  - e.g. "Sonarr - TV" or "Sonarr - Anime"

#### Sonarr - Recommended Naming Scheme Settings

- __Keep In Mind The Following__: We are using Jellyfin for our Media Server, and we want to use Community sourced information
- Use the following TRaSH guide for naming scheme settings
  - [TRaSH: Sonarr - Recommended Naming Scheme](https://trash-guides.info/Sonarr/Sonarr-recommended-naming-scheme/)
- Settings > Media Management >
- Click "Show Advanced" at the top left
- Update the following fields with the format from TRaSH Guides:
  - Standard Episode Format
  - Daily Episode Format
  - Anime Episode Format
  - Season Folder Format
- For the "Series Folder Format" field:
  - Click on the "Optional Jellyfin" tab
  - copy and paste the format into the "Series Folder Format" field
- Select the "Multi Episode Style" shown in the TRaSH Guides
- Click "Save Changes" at the top left

#### Sonarr - Profiles Setup (Part 1)

- Settings > Profiles >
- Delete all default Profiles
- Create a new profile based on the specific Radarr Instance
  - The specific profile to create depends on the quality of content you are looking for which can be determined using the flowchart found on [TRaSH: Sonarr - Quality Profiles](https://trash-guides.info/Sonarr/sonarr-setup-quality-profiles/)
  - e.g. for "Sonarr - TV" create the "WEB-1080P" profile, and for "Sonarr - Anime" create the "Anime" profile
- Select the appropriate Qualities for the specific Sonarr Instance
  - The specific qualities come from the TRaSH guides based on the Profile selected
- Move them into the correct positions
- Check of "Upgrades Allowed"
- Select "WEB 1080p" for "Upgrade Until"
- Click "Save" at the bottom right of the popup window

---

### Notifiarr - First Time Login Setup

#### Notifiarr - Helpful Guides

- [Youtube - Elevate Your Media Automation Game With TRaSH and Notifiarr](https://www.youtube.com/watch?v=eDlWTze8EKo&t=509s)

#### Notifiarr - Setup a Notifiarr Account

- Proceed to the following website: [https://notifiarr.com/](https://notifiarr.com/)
- Setup a new account
- Add the "TRaSH Guides Sync" integration
- Link Discord to Notifiarr
- Authorize Discord to use a specific Discord Chanel and add the Notifiarr bot
- Proceed to the Profile page > API Keys section
- Save the Global API Key somewhere safe

#### Notifiarr - Change API Key, Username and Password

- SSH into the Media Server and edit the notifiarr.conf file
  - sudo nano /mnt/MediaServer/docker/stack/configs/notifiarr/notifiarr/conf
- Update the __api_key__ line with the API Key from your Notifiarr profile
  - e.g. api_key = "<API_KEY_FROM_NOTIFIARR.COM>"
- Update the __ui_password__ line with the a new username and password
  - e.g. ui_password = '''<username>:<password>'''
- Restart the container and verify changes
  - The username and password should be able to log you in
  - System > Patron > should have a value of __true__

#### Notifiarr - Verify Client integration with Notifiarr Website

- On the [Notifiarr](https://notifiarr.com/) website
- Setup (left-hand side menu) > Itegrations >
- Under the "Client Configuration" box
- Verify "Client" count is greater than 0

#### Notifiarr - Setup Starr Apps

- In the Notifiarr container web portal
- Starr Apps >
- Click + next to each app
- Enter a name for each container to be added
  - e.g. "Sonarr - TV"
- Enter the url for the container
- Enter the API for the container
- Enter the Username for the container
- Enter the Password for the container
- Click the green "Double Checkmark" button towards the left of the row to verify the connection works
- Once all apps have been added, click "Save & Reload" in the left-hand menu

#### Notifiarr - Add Starr App Integrations

- On the [Notifiarr](https://notifiarr.com/) website
- Setup (left-hand side menu) > Integrations >
- Under the "Add/Remove Integrations" box
- Click the "gear" icon within the header of the "Add/Remove Integrations" box
- Find the Radarr, Sonarr and Prowlarr integrations and make sure they are selected
- Click "Save"

#### Notifiarr - Connect Each Starr App Integration To Notifiarr Connect

- On the [Notifiarr](https://notifiarr.com/) website
- Setup (left-hand side menu) > Integrations >
- For each Starr App click the "Gear" icon found in the header of each integration's box
- In the Integration's popup window click "Add Notifiarr Connect"
- A "Success" popup window should appear if the Integration was able to connect
- Under the Triggers section
- Check off the following Triggers:
  - Movie Added
  - Grab
  - Updates
  - Database backup
  - Failed
  - Health Check
- Each Trigger can be configured independentaly
- Click "Save" on the right hand side
- A "Success" popup window should appear if the Save worked
- Close out of the popup

#### Notifiarr - Setup TRaSH Integration

- On the [Notifiarr](https://notifiarr.com/) website

- __Step 1__
  - Setup (left-hand side menu) > Integrations >
  - Under the "TRaSH Guides Sync" integration box
  - Click the "Gear" icon found in the header of the integration's box
  - Under the "Client Settings" section
  - Select "Monthly [Patron]" for the Interval
  - Check off "Instant Sync"
  - Click "Save Client Settings"
  - Close out of the Integration's popup window
  - Reopen the Integration's settings
  - Proceed to the "Integration Settings" section

--

- __Step 2__
  - Click on the specific Starr app then Notifications option
    - e.g. "Radarr > Notifications"
  - Check of all notification triggers

--

- __Step 3__
  - Click on the specific Starr app then Formats option
    - e.g. "Radarr > Formats"
  - Make sure "Sync TRaSH CF names" and "Sync TRaSH CF condition names" are checked off
  - Click the link "Use the interactive TRaSH flowcharts for help"
  - Select "Workflow: Default" for "Flowchart"
  - Click on the specific flowchart box based on your selections
    - e.g. for Radarr click on the "(1080p) Remux + WEB" box from the flowchart
  - If the "Include" and "Exclude" options look appropriate
  - Check off the profiles you wish to apply scores to
  - Click on the button corresponding to the Starr App instance
    - e.g. "Toggle Radarr - Movies"
  - Close out of the two popup windows until you get back to the Integration Settings window
  - Scroll down and find and click on "Save All" and confirm the saved changes

--

- __Step 4__
  - Click on the specific Starr app then Quality option and make sure the specific Starr app instance is selected
    - e.g. "Radarr > Quality" then "Radarr - Movies" or "Radarr - Anime"
  - Select the appropriate Definition Group for the specific Starr App Instance
    - e.g. select "movie" for "Instance Radarr - Movie Defition Group:" or "anime" for "Instance  Radarr - Anime Definition Group:"
  - Check off all definitions for the Instance type
  - Click "Save Instance Defitions" when finished

---

### Final Radarr Settings

#### Radarr - Profiles Setup (Part 2)

- Settings > Profiles >
- Select the profile for the specific Radarr Instance
- Update the value for "Upgrade Until Custom Format Score" to the defined value from TRaSH Guides
- Click "Save" at the bottom right of the popup window

---

### Final Sonarr Settings

#### Sonarr - Profiles Setup (Part 2)

- Settings > Profiles >
- Select the profile for the specific Sonarr Instance
- Update the value for "Upgrade Until Custom Format Score" to the defined value from TRaSH Guides
- Click "Save" at the bottom right of the popup window

[^1]: LocalHostIP - <server_ip>
