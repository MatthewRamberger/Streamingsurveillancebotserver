# Streamingsurveillancebotserver

<b>Outline (Delete once web page finished):</b>
1. Explain project idea
2. Provide instructions, code, and hardware setups
   - <i>add photo or graph that might help instructing</i>
   - lighttpd setup
   - wiringpi setup
   - mjpeg (video streaming) setup
   - servoblaster setup
   - darkice and icecast2 setup
   - speaker setup
   - cgi file location and creation
   - web page location and creation (index.html)
   - hardware wiring 
3. Photo and video of the final result


_____

   The goal of this project is to create a room surveillance droid or robot that could be controlled remotely through a web page. The droid will be operated using a Pi 4B board and will comes with multitude of different features:
   
   1. Web page GUI<br>
      Allowing the user to control the droid using a control GUI hosted on the pi web server. User will be able to log into the web page with any device that support web browser capability and are connected onto the same network as the droids.
   2. Video live steam with IR night vision pi camera<br>
   A combination of noIR pi camera and 2 IR LED light connected with light sensor for automatic switch, that will stream the visual input of the room (dark or bright) onto the web page 
   3. Speaker warning signals<br>
   Ouputing warning singals available on the GUI to the target room
   4. Live Broadcast target room audio<br>
   Allowing user to get live audio feedback of the room while surveilling
   5. Pan and tilt camera<br>
   Giving the user freedom to adjust camera viewing angle

_____________________________________
# Hardware setup
* Raspberry pi-4
* [4WD DC-motor and chassis](https://www.amazon.com/Robot-Chassis-Motor-Arduino-Raspberry/dp/B07F759T89/ref=asc_df_B07F759T89/?tag=hyprod-20&linkCode=df0&hvadid=312123579962&hvpos=1o2&hvnetw=g&hvrand=4023891843030921682&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=1015254&hvtargid=pla-572041604638&psc=1&tag=&ref=&adgrpid=65834404201&hvpone=&hvptwo=&hvadid=312123579962&hvpos=1o2&hvnetw=g&hvrand=4023891843030921682&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=1015254&hvtargid=pla-572041604638)
* sparkfun h-bridge motor driver  
* [Mini Portable Speaker, 3W Mobile Phone Speaker Line-in Speaker 3.5mm AUX Audio Interface ](https://www.amazon.com/gp/product/B07RJR1XPH/ref=ppx_yo_dt_b_asin_title_o00_s00?ie=UTF8&psc=1) 
* 2 servos
* [no Infared filter pi camera and Infared LED lights](https://www.amazon.com/Raspberry-Haiworld-Version-Megapixel-Infrared/dp/B01MYUOQ0A/ref=sr_1_5?keywords=pi+module+ir+camera&qid=1573746119&sr=8-5)
* Portable cellular battery charger
* 4 AA batteries and holder
* [Top plate of red robot chassis](https://www.sumozade.com/magician-robot-chassis-kit---red-455)


_____________________________________
# Hook up Guide

<b> H-bridge Pin Connections</b>

| Raspberry pi pin  | H-bridge pin |
| ------------- | ------------- |
| 3.3V  |  logic for motor driver for speed information  |
| GPIO 6  | connect to H-bridge- Motor left -  |
| GPIO 5  | connect to H-bridge- Motor left +  |
| GPIO 19  | Connect to H-bridge- Motor Right -  |
| GPIO 13  | Connect to H-bridge- Motor right -  |
| AA batery rail  | H-bridge power input  |

<b>Connecting Servos</b>

Red and black power wires connects to 4AA batteries power rail

| Raspberry pi pin  | Servo|
| ------------- | ------------- |
| GPIO 17  |  connects to control tilt servo |
| GPIO 23  |  connects to control pan servo |

<b> Connecting Misc. Parts </b>

* connect 3.5 aux jack to portable mini speaker

* Usb microphone to pi-4 usb input

* Pi-camera to pi-4 camera ribbin cable connector

* Connect Pi-4 Usb-C pwr port to portable cell phone power pack

![Pi 4 Pinout](/Assets/pi4PinOut.png)

![Top view](/Assets/IMG_1382.jpg)

![Back view](/Assets/IMG_1389.jpg)


Link to video Demo
[https://youtu.be/eEnGLcFCr18](https://youtu.be/eEnGLcFCr18)


_____
# Software setup

Setting up and install wiringPi

<b>$ git clone git://git.drogon.net/wiringPi</b>

<b>$ cd wiringPi</b>

<b>$ ./build</b>


<br><br>
Setting up and install lighttpd 

<b>$ sudo apt-get -y install lighttpd</b>

<b>$ sudo lighttpd-enable-mod cgi</b>

<b>$ sudo lighttpd-enable-mod fastcgi</b>

Changing the lighttpd config file 

Changing where the config file is looking for index.html file to "/var/www" instead of its default of "/var/www/html"

Config file location " sudo nano /etc/lighttpd/lighttpd.conf "

Other than configuring the path in lighttpd.conf to find the index.html, the cgi enable need to be configure too using<br><br>

<b>sudo nano /etc/lighttpd/conf-enabled/10-cgi.conf</b><br><br> 

In the $http ["url"] section make sure the alias.url is directed to the right file path. In our case it should be /var/www/cgi-bin. Some people also have success configuring it in the lighttpd.conf file and follow the same step, but we have not try it.

Start and stop lighttpd service

<b>$ sudo /etc/init.d/lighttpd stop</b>

<b>$ sudo /etc/init.d/lighttpd start</b>


<br><br>
Install and setup mjpg-streamer for Video streaming over web

<b>$ sudo apt-get update</b>

<b>$ sudo apt-get upgrade</b>

<b>$ sudo apt-get install libjpeg62-turbo-dev</b>

<b>$ sudo apt-get install cmake</b>

<b>$ git clone https://github.com/jacksonliam/mjpg-streamer.git ~/mjpg-streamer</b>

<b>$ cd ~/mjpg-streamer/mjpg-streamer-experimental</b>

<b>$ make clean all</b>

<b>$ sudo rm -rf /opt/mjpg-streamer</b>

<b>$ sudo mv ~/mjpg-streamer/mjpg-streamer-experimental /opt/mjpg-streamer</b>

<b>$ sudo rm -rf ~/mjpg-streamer</b>

-Test stream<br>
<b>LD_LIBRARY_PATH=/opt/mjpg-streamer/ /opt/mjpg-streamer/mjpg_streamer -i "input_raspicam.so -fps 15 -q 50 -x 640 -y 480" -o "output_http.so -p 9000 -w /opt/mjpg-streamer/www" </b>

<br><br>
Servo Blaster library setup

Servo blaster allows you to control servos with GPIO pins instead of pwm pins

you must also change the libary default timing method from "PWM" to using the "PCM" so it doesnt interfere with 3.5mm aux jack

Copy and setup Servo blaster library

<b>$ cd</b>

<b>$ sudo git clone https://github.com/richardghirst/PiBits</b>

<b>$ cd PiBits/ServoBlaster/user</b>

<b>$ sudo make servod</b>

<b>$ sudo make install</b>

<b>$ sudo chmod 755 servod</b>

<b>$ sudo ./servod</b>

Define pins

<b>$ sudo ./servod --p1pins=11,16</b>


-Aditional config change

<b>$ sudo nano /etc/init.d/servoblaster</b>

...

case "$1" in
start)

/usr/local/sbin/servod $OPTS >/dev/null

change to:

/usr/local/sbin/servod --p1pins=11,16 $OPTS >/dev/null

<br>
-To run this on startup use the follow in the rc.local file

<b>cd /home/pi/PiBits/ServoBlaster/user</b>

<b>sudo ./servod --p1pins=11,16</b>

<b>cd</b>

<br><br>
Darkice and Icecast2 setup for audio broadcast

To install Darkice into raspbian, first in terminal run,<br>

<b>$ sudo apt-get update</b>

Then add a deb-src repository to your sources list at /home/pi:

<b>$ cd</b>

<b>$ wget https://github.com/x20mar/darkice-with-mp3-for-raspberry-pi/blob/master/darkice_1.0.1-999~mp3+1_armhf.deb?raw=true</b>

<b>$ mv darkice_1.0.1-999\~mp3+1_armhf.deb?raw=true darkice_1.0.1-999~mp3+1_armhf.deb</b>

Install Darkice using the newly added repository

<b>$ sudo apt-get install libmp3lame0 libtwolame0</b>

<b>$ sudo dpkg -i darkice_1.0.1-999~mp3+1_armhf.deb</b>

Once it is installed, from the Github, download and move the config file for Darkice , darckice.cfg, and the shell script darkice.sh to /home/pi. Make darkice.sh into executable 

<b>$ sudo chmod 777 /home/pi/darkice.sh</b>

Then go to rc.local to execute this shell script on boot up

<b>$ sudo nano /etc/rc.local</b>

At the bottom of the page before exit 0 add

<b>sudo /home/pi/darkice.sh</b>

Once darkice is installed and configure, follow these steps to activate the Icecast2 server.

<b>$ sudo apt-get install icecast2</b>

Follow through the configuration window, I suggest keep the default setting and press yes and ok for all setting. Next, start the Icecast2 server service by typing this command 

<b>$ sudo service icecast2 start</b>

<br><br>
Omxplayer setup

since omxplayer is build-in not much setup to be done other than some permission setting

To allow the actual audio to be streamed from web page the following command needed to be used to give execution access to "vchiq"

<b>$ sudo chmod 777 /dev/vchiq</b>

<br></br>
CGI file location and creation

Once the library is intalled and setup, from the Github, download and move the cgi-bin folder into /var/www/
to make sure all the file are executable, run,

<b>$ cd /var/www/cgi-bin</b>

<b>$ sudo chmod 755 [filename]</b>

For each of the cgi file such as,

<b>$ sudo chmod 755 forward.cgi</b>

Other than the given shell script, When creating the shell script file make sure that the first line (the Hash-Bang Hack, #!/bin/bash) is not begin with a space. If the shell script is generated properly, when reopening it after saving, the auto-coloring will be applied.

<br><br>
Web page location

With all the shell scripts and library in place, the web page content can be obtain by downloading and moving the index.html, indexMobile.html, and image file and folder to /var/www/ for the lighttpd web server to read. 

______

When creating the shell script file make sure that the first line (the Hash-Bang Hack) is not begin with a space. If the shell script is generated properly, when reopening it after saving, the auto-coloring will be applied.

_______________________________________

Other than configuring the path in lighttpd.conf to find the index.html, the cgi enable need to be configure too using<br><br>

<b>sudo nano /etc/lighttpd/conf-enabled/10-cgi.conf</b><br><br> 

In the $http ["url"] section make sure the alias.url is directed to the right file path. In our case it should be /var/www/cgi-bin. Some people also have success configuring it in the lighttpd.conf file and follow the same step, but we have not try it.
      
_______________________________________
camera stream window added to Web page and start up stream added to rc.local


LD_LIBRARY_PATH=/opt/mjpg-streamer/ /opt/mjpg-streamer/mjpg_streamer -i "input_raspicam.so -fps 15 -q 50 -x 640 -y 480" -o "output_http.so -p 9000 -w /opt/mjpg-streamer/www" &
_______________________________________
 # Setup of Audio streaming
 
 For audio streaming service used
 
 To allow the actual audio to be streamed the following command needed to be used to give execution access to "vchiq"
<b>sudo chmod 777 /dev/vchiq</b>
https://stackoverflow.com/questions/42583835/failed-to-open-vchiq-instance/42584382


Adding the microphone through bash commands

____________________________________________
https://www.seeedstudio.com/blog/2019/08/08/how-to-use-usb-mini-microphone-on-raspberry-pi-4/
_____________________________________________________________


The pwm messed up the sound from aux cord, must define explicit pwm signal in a precompiled .cpp program that way it doesnt leak and ruin the audio quality

Wiring -pi does not allow for graceful using of the aux port for audio

_____________________________________________________________
audio live streaming/broadcasting with darkice and icecast2
https://technicalustad.com/live-streaming-of-mp3-using-darkice-and-icecast2-on-raspberry-pi/


