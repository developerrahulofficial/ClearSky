# Stellarium-To-Video
Automatically creating videos of the night sky with [Stellarium](https://stellarium.org) (0.20.4 or higher) and Linux/BSD or Windows 11.
 
This python script will automate the process of creating videos of the night sky with stellarium. It will take an observation position and other observation parameters as command line options and then create a [script](https://beltoforion.de/en/stellarium_video/index.php?da=1#idStellariumScript) for Stellariums built in scripting engine to compute the animation frames. Once the frames are created the script will invoke ffmpeg to combine the 
frames into an mp4 video file.

For more details please go to the [web page of this project](https://beltoforion.de/en/stellarium_video/)

https://user-images.githubusercontent.com/2202567/184002878-0f915485-8a63-49ae-8221-273f61b2c728.mp4

# Prerequisites:
In order to use this script [Stellarium](https://stellarium.org), [ffmpeg](https://www.ffmpeg.org/) and [vlc](https://www.videolan.org/vlc/) need to be present on your system. You must also
make sure that the location of their executables is in the global search path! This is automatically the case when you are on Linux on Windows however you have to manually add the
corresponding folders to the PATH environment variable.

# Download and Installation

```bash
git clone https://github.com/beltoforion/stellarium-to-video.git
cd stellarium-to-video
pip install -r requirements.txt
python3 stellarium-to-video.py -l Boston
```

# Command Line Options:

| Parameter | Parameter Type | Description |
| --- | --- | --- |
| -c (optional) | string | The caption of the video. The video title will be superimposed onto the video. If this parameter is omitted a default caption will be used. |
| -d (optional) | [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) Date and Time | The simulation date as ISO 8601 string (i.e. 2024-02-20T16:37:05). If the time portion is omitted and the selected base planet is Earth the script will automatically start at sunset of the location given with the -l option. If this parameter is omitted the simulation will start at the sunset time of the current day. |
| -dt (optional) | [ISO 8601 Duration](https://www.digi.com/resources/documentation/digidocs/90001488-13/reference/r_iso_8601_duration_format.htm) | The time difference between two successive frames as a string representing an ISO 8601 duration (i.e. PT20S for 20 seconds). |
| -fps (optional) | int | Frame rate of the output video. |
| -l | float, float<br/>string | Location of the observer. This is either a string containing longitude and latitude as a comma separated list or the name of a city. If a city name is given the script will automatically try to find the proper coordinates for it. Examples: -l Berlin, -l 52.5186,13.4083 |
| -o (optional) | string | The name of the output video file. |
| -p (optional) | string | The planet on which the simulation shall take place. ('Earth', 'Mars', 'Venus', 'Mercury', ...) If this parameter is omitted the simulation will be assuming Earth as its celestial home. Examples: -p Mercury |
| -s (optional) | - | When this flag is specified an instance of VLC will be started once the video is created. VLC must be installed and the VLC binary must be in the search path. |
| -sz (optional) | WIDTHxHEIGHT | The resolution of the video to be created. Must be a string with format "WIDTHxHEIGHT" (i.e. 1920x1080) |
| -wsz (optional) | WIDTHxHEIGHT | If this parameter is given Stellarium will be executed in a window with the given resolution. Use this parameter if you want the output video in portrait orientation. Must be a string with format "WIDTHxHEIGHT" (i.e. 1080x1920). ***Please note: This option works only on Windows or Linux in combination with the GNOME Window manager!*** |
| -t (optional) | string | The stellarium script template. This is a ssc file with placeholders that must be located in the scripts folder. It contains placeholders that will be replaced with the parameters of the python script to define the time and location of the simulation. You can create different script templates to customize the look of the animation. If this parameter is omitted the script will use a default template. |
| -ts (optional) | [ISO 8601 Duration](https://www.digi.com/resources/documentation/digidocs/90001488-13/reference/r_iso_8601_duration_format.htm) | The simulation time span as an ISO 8601 Duration string (i.e. PT2H for 2 hours). If this parameter is omitted the duration is set to 2 hours. |
| -v (optional) | float,float,float | A comma separated list of three floating point values defining the direction of view. The values represent azimuth, altitude and field of view. |

# Examples:

The following command will compute the first 2 hours of night sky in Berlin (Germany) on the 25th September of the year 2023. 

```python
python3 stellarium-to-video.py -l Berlin -c "The Sky over Berlin" -v 90,25,70 -d 2024-09-25 -ts PT2H -s -o berlin-sky.mp4 -dt PT1M
```
https://github.com/beltoforion/stellarium-to-video/assets/2202567/7fad5e5d-1c34-49ed-bc37-295ef63cfebe

The next example will create an analemma for my home town, the city of Freiberg (Germany) starting on 2024-06-20. The time between two frames is two days. The simulated time is an entire year.
The command uses a different stellarium script template named "suntrack.ssc". The only difference to the default template is that in each frame the position of the sun is marked
with a red circle. This demonstrates how you can customize the content of your videos with specific stellarium script templates.
```python
python3 stellarium-to-video.py -l Freiberg -t suntrack.ssc -c Analemma -d2024-06-20T12:00:00+01:00 -ts P1Y -s -o output.mp4 -dt P2D
```

https://github.com/beltoforion/stellarium-to-video/assets/2202567/c40f8c4f-f63a-4487-ad46-29602272e501

The last example is showing the apparent motion of Mars, Mercury, Venus, Saturn and Jupiter as they danced around the sun in 1961. It was a rare occurence were 5 Planets 
were just within 17 degrees of one another in the constellation aquarius. This example was created with a customized stellarium script template that disabled the atmosphere
(the alignment happend at daylight) and adds tracking marker to the planets.

```python
python3 stellarium-to-video.py -v180,30,70 -l Freiberg -c "Dance of Planets" -d1961-10-08T12:00:00+01:00 -ts P6M -s -o "dance.mp4" -dt P1D -tplanet_track.ssc -sz960x540
```

https://github.com/beltoforion/stellarium-to-video/assets/2202567/2faa0861-ecc2-484a-9a12-082c51b60166

You can also create videos in portait mode by using the -wsz option to set a specific window size for the stellarium client window. This window size can also be greater than 
the screen size. Using this option only works it you are using either Windows or Linux in combination with the GNOME window manager.

```python
python3 stellarium-to-video.py -v180,30,70 -l Freiberg -c "Dance of Planets" -d1961-10-08T12:00:00+01:00 -ts P6M -s -o "dance.mp4" -dt P1D -tplanet_track.ssc -sz1080x1920 -wsz1080x1920
```
https://github.com/beltoforion/stellarium-to-video/assets/2202567/baee9422-7594-4fc3-a7c0-fe4562d6a518

# Acknowledgements:
This project would not be possible without [Stellarium](https://stellarium.org)! It is a fantastic program that is also open source and free to use! Special thanks to 
the people behind who created and maintain it. This script is also using [Skyfield](https://rhodesmill.org/skyfield/) for computing sunset times. Skyfield is great and 
does so much more than mere sunset computations on earth and finally I'd like to thank the makers of ffmpeg and VLC.
