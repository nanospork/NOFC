# Nolo OSVR Fusion Configuration v0.3

Thank you for testing the BETA of the Nolo OSVR Fusion Configuration (NOFC)!

***UPDATE NOTICE:*** If you have already installed NOFC v0.1 or NOFC v0.2, please read the Release Notes and then start following the instructions from ***Step 1b***. You should not need to remove the official NOLO drivers again, unless you re-installed them for some reason.

## Public Request:

If you are familiar with the process of editing server config files for OSVR and you are interested in using NOLO with an Oculus DK2 or any other headset for which NOFC does not provide a server configuration file, please help us out here: https://github.com/nanospork/NOFC/issues/4

##Release Notes:

#### v0.3.0 RC1 - February 06, 2018

* **Release Candidate 1:** This branch has not been extensively tested, but functions well during short-term testing. Please provide as much feed back as possible at https://github.com/nanospork/NOFC/issues

* **Complete rework of Windows version of Nolo-OSVR** - we gave in and switched over to LYRobotix's "HIDAPI", which gives better tracking results than reading USB packets directly. Performance should be improved for Windows users on all versions of NOLO firmware.
	* You still should ***not*** install the official NOLO drivers. 
	* The Linux version of the driver was updated with some attempts at reading velocity, but the stability is unknown.
	* Note that the headset marker button was NOT implemented in the new version of the driver. We're sorry if you miss this, but we just didn't have the time to work it in.

* **Implemented velocity tracking** - Controllers now report velocity to SteamVR, meaning you can throw things in games, among other uses!
	* Throws specifically are sometimes poor, going the wrong direction or speed - if you can find any consistency to this behaviour, please report it at https://github.com/nanospork/NOFC/issues so we can find out what the cause is and improve on it.
	
* **Implemented ceiling mode** - You can now use one of the NOFC v0.3 "ceiling" configs to use your NOLO basestation in ceiling mode with NOFC. This opens up the possibility of 360 degree tracking.

This new configuration combines the Nolo-OSVR plugin, the OSVR-Fusion plugin, and the official SteamVR-OSVR driver for a more enjoyable experience using Nolo hardware with OSVR.

Please note that the dedicated community members working on NOFC have volunteered their time to make these improvements; please respect their efforts. You will _probably_ encounter bugs and glitches - please report these to https://github.com/nanospork/NOFC/issues

## Advantages of NOFC:

* __Platform agnostic! This configuration has been tested on both Windows and Linux, and should work on Mac.__ (We are only distributing Windows binaries at this time, but a Linux build guide and possibly binaries will be coming soon.) 
* __No dependence on the Nolo Windows App or on Nolo's fork of SteamVR!__ The Nolo-OSVR plugin talks directly to the hardware over USB, and from there OSVR talks to SteamVR using the *official* SteamVR-OSVR plugin.
* __Improved yaw drift with the OSVR HDK!__ While the Nolo hardware is still susceptible to some amount of yaw drift, especially in the controllers, this new configuration nearly eliminates yaw drift with the OSVR HDK. If you do experience some drift, you can easily recenter with a simple double-tap of the system (power) button on your controllers.
	* In fact, you should *not* press the button on the headset marker or the "Recenter" button in the HDK Tray App.
* __Better interaction with SteamVR!__ No hijacking of the room setup or other issues. Just use Room Setup like you would with a Vive!
* __(Future) Touchpad calibration!__ In the future, we will implement some form of auto-calibration or configurable calibration for the touchpads. For now, enjoy the hard-coded remapping which should allow most users to get the full range out of the trackpads (at the cost of some precision.)
* __And of course, it's fully open-source!__
	

## Installation

### Step 1: De-register any Nolo SteamVR drivers

The first thing you need to do is de-register any SteamVR Nolo drivers. This may include the NOLO_OSVR_SteamvrDriver, a Cardboard driver, iVRy, etc. **If you never installed anything Nolo-related before, skip this step.**

1. First, de-register the driver in SteamVR.
	1. Open a Command Prompt.
	2. Navigate to your SteamVR installation directory, then `\bin\win32\`.
		1. For a standard Steam installation, just run this command: `cd C:\Program Files (x86)\Steam\steamapps\common\SteamVR\bin\win32`
	3. Run the following command:
		1. `vrpathreg.exe show`
		2. This will display the currently registered SteamVR drivers.
	4. Now, remove any drivers related to NoloVR.
		1. You can probably just use this command: `vrpathreg.exe removedriver "C:\Program Files\LYRobotix\NOLO_driver_for_windows\nolo"`
		2. If there are any other Nolo-related lines, run the above command but change the path appropriately.
		3. Make sure not to accidentally remove the OSVR driver path registration. We're going to need that.
2. For extra robustness, we will disable the LYRobotix Nolo driver in SteamVR completely. Just to be sure ;)
	1. Open Windows Explorer
	2. Navigate to the `config` directory of your Steam installation, usually `C:\Program Files (x86)\Steam\config`
	3. Open `steamvr.vrsettings` in Notepad or your favorite text editor.
	4. Add the following lines:
	
```

"driver_nolo" : {
	"enable" : false
},

```

_I recommend putting these lines at the very top of the file, just below the very first line, which should be just a curly brace `{`_

### Step 1b: Ensure velocity reports are NOT disabled in SteamVR-OSVR

If your `steamvr.vrsettings` file contains the line `ignoreVelocityReports`, please ensure that the value is set to **false**, as below:

```
   "osvr" : {
      "ignoreVelocityReports" : false
   },
```
		
### Step 2: Install the latest SteamVR-OSVR driver

You now must uninstall the NOLO_OSVR_SteamvrDriver and install the latest SteamVR-OSVR driver. We're going to do this all in one step. If you never installed the NOLO_OSVR_SteamvrDriver, don't worry: it won't matter, but we do still need to update SteamVR-OSVR

1. Open Windows Explorer
	1. Navigate to your HDK-Software-Suite installation.
		1. This is most likely `C:\Progam Files\HDK-Software-Suite\`
	2. Create a COPY of the `OSVR-SteamVR` folder.
	3. Rename the copy of your `OSVR-SteamVR` folder to `OSVR-SteamVR-bak.bak`
		1. We're creating a backup just in case. If you want to go back to Nolo's driver, you can change the name of your `OSVR-SteamVR` folder to something else, rename the backup to `OSVR-SteamVR`, and then follow the steps above for vrpathreg but use `adddriver` instead of `removedriver`. Then go back to your `steamvr.vrsettings` file and remove those new lines we added.
	4. Copy and paste the contents of the `OSVR-SteamVR` folder in this download into the `OSVR-SteamVR` folder in your HDK Software Suite directory.
	
### Step 3: Install Nolo-OSVR and OSVR-Fusion

Next we need to install the Nolo-OSVR plugin so the Nolo devices can talk to OSVR, and a new version of OSVR-Fusion to get good performance out of it.

1. Open Windows Explorer
	1. Navigate to your HDK-Software-Suite installation.
	2. Copy and paste the contents of the `OSVR-Core` folder in this download into the `OSVR-Core` folder in your HDK Software Suite directory.
		1. If it asks "Do you want to merge?" or "Do you want to overwrite" for any folders or files, say Yes To All

### Step 4: Choose a server configuration

1. Extract one of the server config files from this download, and put it somewhere convenient (such as your desktop, or My Documents.)
2. Right click on your HDK Tray App (open it if it's not running)
3. Select Options -> OSVR Server -> Custom...
4. Navigate to the directory that contains the extracted config file and select it.
5. **Note for non-HDK and extended mode users:** There are two included server config files: one for the HDK 2.0 in direct mode, and one for the HDK 2.0 in direct mode using the new "v2" distortion profile, available [here](https://www.reddit.com/r/OSVR/comments/6g9lic/new_hdk2_distortion_mesh_testing/). 
	1. If you want to use NOFC with extended mode, or with an Oculus DK2, or some other configuration, you will have to copy the key components from the server config file.
	2. The key components to copy are the "driver" and "aliases" sections. You will need to update the first entry in the "driver" section to use a different tracker for pitch/roll/yawFast if you are not using the HDK.
	3. If you develop an alternative config file that might be useful to others, please share it in the Reddit thread!

## Using with OSVR Apps

Below is the best process I've found for starting up Nolo and OSVR.

1. First, start up all your Nolo devices and your HDK. 
	1. Make sure the headset marker has power. 
	2. Make sure the OSVR Server is not running. 
	3. Make sure the Windows Nolo Desktop App, if installed, is not running.
		1. The Windows Nolo Desktop App will actually interfere with your SteamVR Room Setup. Make sure it does not start on Windows startup. We recommend only using the Windows Nolo Desktop App for NoloVR firmware updates.
2. Open the HDK Tray App and start the OSVR Server.
3. Put the headset on, face the base station, then hold the controllers up in front of you, tops pointed toward the base station.
4. Double-tap the power button on both of the Nolo controllers to recenter.
	1. **NOTE:** Do NOT press "Recenter" in the HDK Tray App or run osvr_reset_yaw.exe. This will misalign your headset, since it does not apply any transformations to the controllers.
	2. **Only use the Nolo power buttons to recenter!**
	3. It is very important that you press *both* system(power) buttons on the Nolo controllers at the same time. This will ensure a "snappy" recentering. If you recenter using the left-hand controller and not the right-hand controller, you may experience a slow, nauseauting recentering.
5. Enjoy native OSVR Nolo content!
6. At any time, double-tap the right-hand controller's menu button to instantly flip your view 180 degrees so that you can interact with objects "behind" you.
	1. You can change the button assigned to this behavior by changing the "flipButton" property in your server config. It will always require a double-press.

**If you ever feel that the system is "drifting", e.g. the controllers are rotated incorrectly, repeat steps 3 and 4!**

## Using with SteamVR

Below is the best process I've found for starting up Nolo and OSVR when playing SteamVR games.

1. Do steps 1-4 from _Using with OSVR Apps_ above.
2. Start SteamVR
3. (First time only) Run SteamVR Room Setup to ensure your room and height are configured properly, and to optionally set up Chaperone.
4. At any time, double-tap the right-hand controller's menu button to instantly flip your view 180 degrees so that you can interact with objects "behind" you.
	1. You can change the button assigned to this behavior by changing the "flipButton" property in your OSVR server config. It will always require a double-press.

**If you ever feel that the system is "drifting", e.g. the controllers are rotated incorrectly, repeat steps 3 and 4 in the section above.**

## Source Code

Please note that some of the plugins in this BETA have not yet had their source code merged into the master fork of their respective repositories, but they will soon. You can access the development source for each at the following URLs:

* https://github.com/johnlajoie/nolo-osvr
* https://github.com/nanospork/OSVR-fusion/tree/velocity
* https://github.com/nanospork/steamvr-osvr/tree/controller-integration

