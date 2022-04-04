# placebot

This is a functional bot for the r/place 2022 event, supporting at the time of writing all (2) canvases using GCP for free

Based on code from https://github.com/goatgoose/PlaceBot and https://github.com/rdeepak2002/reddit-place-script-2022.

## Functionality
- does NOT use the reddit API
- Convert input image to be drawn to target configuration, ignores transparent pixels
- Supports multiple accounts
- Supports obtaining the target configuration from a server (or local file) to prevent outdated templates
- Supports multiple canvases (2 at the time of writing)
- Goes to sleep when only few mismatched pixels remain

### Server Specs:
- **OS:** Debian GNU/Linux 10 (buster) x86_64
- **RAM:** 8GB to 16GB
- **Location:** Based on your physical location.
- **Storage:** Approx 5GB
- **CPU:**  AMD EPYC (2) 7B12 @ 2.249GHz or Intel Xeon (4) @ 2.199GHz

You can view your exact specs by doing `./specinfo`

## Launching from the Cloud Console
To launch a Cloud Shell session from the Cloud Console, click Activate Shell Button Activate Cloud Shell in the Cloud Console(https://console.cloud.google.com/). This launches a session in the bottom pane of Cloud Console.

You can also transfer this session to a full screen experience by clicking Open in new window button Open in new window.

## Installation
* Activate a [Google Cloud Shell](https://console.cloud.google.com/) on Google cloud.
* Clone this GitHub Project into the Console:
```
git clone https://github.com/Powerful845tiger/placebot-gcp
```
* Go into `placebot-gcp` directory:
```
cd placebot-gcp
```
* Allow all commands executable:
```
chmod +x *
```
* Run the Installation Script:

Create a virtual environment
```
python3 -m virtualenv .venv
```

and install the dependencies (or just install them globally):
```
python3 -m pip install -r requirements.txt
```

Source the environment (for bash):
```
source .venv/bin/activate
```

### Generate target configuration
To paint an image you need a template in the form of a PNG that will be converted into a configuration file. 
```
python3 src/image_converter.py [filename] [x] [y]
```
The position specified is that of the top left pixel.
The generated file contains the coordinates and closest available place color for each pixel of the input image that hasn't set alpha to 0.

You can then put the generated file on a web server of your choosing.
Alternatively, just put it in the folder next to the local configuration file.

**If you are using Windows** with a local target configuration file, make sure to use double backslashes `C:\\path\\to\\the\\file.cfg` as the backslash `\` is an escape character in JSON.

## Viewing and editing files
Cloud Shell comes with a built-in code editor that allows you to browse file directories as well as view and edit files, with continued access to the Cloud Shell. The Cloud Shell Editor is available by default with every Cloud Shell instance, and is based on Theia.

To work with the Cloud Shell Editor, follow these steps:

Launch the Cloud Shell Editor by clicking Code Editor Button Open Editor on the toolbar of the Cloud Shell window. This sets up the editor in a new tab with continued access to Cloud Shell.

Tip: You can also launch the Cloud Shell Editor by navigating to ide.cloud.google.com.

Use the left-hand pane to browse through the file directories. Use the right-hand pane to view and edit your files.

Access the functionality to toggle the editor and the Cloud Shell window, upload and download files, preview web applications on a Cloud Shell virtual machine instance, and view usage statistics, from the Cloud Shell toolbar in the upper right-hand corner.

### Configure local

Copy the configuration template:
```
cp config.template.json config.json
```

Configure the accounts to be used in `config.json`.
Set the ```target_configuration_url``` to the URL of the web server serving the target configuration file you generated using the image converter above. In case you do not want to use a web server for coordination of multiple people, you can also enter a local file path.

You can then run the bot using:
```
python3 src/placebot.py
```
## Keeping the Server Alive
There's only **ONE** way to keep your server online longer than 20 minutes:
- You must Keep your browser open to keep your server online. This so the shell doesn't get terminated when it's unused or the browser is closed. You also need to use the Afk script which can be started using the commmand
```
./startAfk
```

## Placement order
When placing pixels, the bot will prioritize them in the order specified in the target configuration file as supplied by the server. By default, the `image_converter.py` generates pixels from top to bottom, but this can be change by exchanging the loops.

The bot will scan the image for mismatched pixels, order them according to priority and pick one from the first 8 randomly to avoid conflicting with other bots that happen to be placing at the same time.

## To be noted
The bot only pulls information for canvases specified in the target configuration. This is generated by the image converter automatically.

The bot goes to sleep when it detects that there are only a few pixels left to be placed, only refreshing every 120s (till it detects it should wake up again).



## Limitation of this project
### Google Cloud Shell Quota
While I was editing this project, I notice that Google has added a quota for each Cloud Shell uses and you can only host your server for the max of `50` hours per `Week`.
Once you used all of the hours, you can't use it again until next week.
### Mixed Server Specs
Each Cloud Shell session will have different specs of your server based on your physical location so you won't always get the best performance of your server but good news being that it's always the range between `8GB` to `16GB` 
