# P-BON


An on-site cellular pentesting suite. Bring your own network, so you never have to worry about sneaking out through theirs.

---
### Hey!

So if you are here, you probably want to make my Bring-Your-Own-Network Pentesting tool. Whew! That’s a mouthful! Let’s just call it P-BON (Pentesting Bring-Your-Own-Network, but I dropped the Y)

### What is it!?1?!
**CALM DOWN.** Oh wait I wrote that, you probably weren’t screaming…

It’s a lot of things! Well, it can be at least. Simply put, the P-BON is a penetration testing platform designed with multi-factor persistence in mind. With P-BON, maintaining access to a remote network has never been easier. With the power of a 3G cellular connection, you make sure that P-BON keeps you connected, even if the internet goes out at the target location.

That was quite vague… What can I do with it?

### P4wnP1 3G:
Get the whole [P4wnP1](https://github.com/mame82/P4wnP1) suite, An expansive library of USB interface attacks, controlled from anywhere in the world.

### Remote Network access:
Keep remote access to a network, without having to worry many intrusion-detection systems that may be in place. Communicating with the Pi through a 3G connection means you don’t have to make any additional traffic on the remote network, preventing many red flags from being raised. With the power of a full linux box, perform network security audits, vulnerability exploitation,data exfiltration, and anything else you desire with a local connection. Internet goes down at the target location? No biggie! You still have remote access to the internal network, because you brought your own.

### Well then let’s build it!

**Required items:**
* Most basic Version:
    * Raspberry Pi Zero W
    * Hologram Nova
    * Nova Sim & Data plan
    * Micro-USB hub with USB and ethernet ports (for maximum flexibility)
    * An ethernet cable
    * A Micro-USB cable
    * Some sort of USB power source (at least 1.5A to be able to power both the Pi and the Nova)

* Fancy Version:
    * (image)
    * Case for the Pi (that can fit the Nova inside, while also allowing the antenna to stick out)
    * Female USB socket
    * Flexible wire and Soldering skills **OR** [this flexible MicroUSB to USB adapter](https://smile.amazon.com/gp/product/B01LWOASY7/ref=oh_aui_search_detailpage?ie=UTF8&psc=1). If you are using a full-size RPi, or something with a full size USB port, use [something short and flexible like this](https://smile.amazon.com/MXTECHNIC-Expansion-Extension-Processors-Computers/dp/B00VULGQAQ/ref=sr_1_12?s=electronics&ie=UTF8&qid=1513736853&sr=1-12&keywords=flexible+usb+extension+cable) to get it inside the case
    * Time

## Creation & Installation:

### Install raspbian on a Micro-SD card:
Head on over to the raspbian download page here (link), and download the latest version of raspbian. You can use either version, although I prefer lite since you will probably never use the desktop environment with this. If you are tackling this project, I imagine you are familiar with the process of installing Raspbian on a Micro-SD card.

### Register your Nova’s sim card:
[See the tutorial here on how to do that](https://hologram.io/docs/guide/connect/connect-device/#sim-activation). 
*(I hate to have to point you to other tutorials, but Hologram does a REALLY good job at making these easy to understand, and they are bound to update well before mine would if there were changes in the process. This goes for all links.)*

### (Fancy Version only) Solder up that USB port:
We are now going to solder the USB port to the Pi. This will create a full-size USB port in which we can plug in the Nova to, and hide inside the case.

SSH into your Pi and install the Hologram SDK:
For a really great tutorial on how to do that check [the benstr’s tutorial here](https://www.hackster.io/hologram/add-cellular-to-a-raspberry-pi-with-hologram-nova-ea5926#toc-accessing-your-pi-2). BUT WAIT! There are a couple things to note before you go!
1) The tutorial uses a Raspberry Pi 3, the steps are the EXACT SAME for the Pi Zero W (also I do suggest the WiFi route for SSH in the tutorial)

2) After running the `curl -L hologram.io/python-install | bash` command, you may get some errors when running the next few commands. I got 2 every time I have tried installing. But no worries! They are real easy to fix!
If you are getting the first error I got that looks like this:
```sh
Traceback (most recent call last):
  File "/usr/local/bin/hologram", line 16, in <module>
    from Hologram.CustomCloud import CustomCloud
ImportError: cannot import name CustomCloud
```
You simply need to uninstall and reinstall the hologram-python python package. To do this, run:
```sh
sudo pip uninstall hologram-python
```
After that is done, run:
```sh
sudo pip install hologram-python
```

The next error I got was in regard to PPP. To fix this, it also has to be uninstalled, then reinstalled.
* To uninstall, run:
```sh
sudo apt-get purge ppp -y
```
* To then reinstall, run:
```sh
sudo apt-get install ppp -y
```

3) I also did have to run the `curl` command twice, but it’s real fast the second time so I would suggest doing it.

Now you should have no issues using the hologram SDK. Once you are able to get a modem location and send some test data to your Hologram Dashboard, proceed to the next step.

### Make sure your hologram connects on startup:
So we need to make sure that the Hologram Nova gets connected as a routing device on startup. Without it connecting, you can still use the hologram SDK, however you won’t be able to SSH into the Pi over 3G without connecting it. If you want to see what I mean, run this inside of the Pi’s terminal:
```sh
route
```
You should see around 2 lines. None of those are your Hologram Nova. So, we are going to edit the rc.local file to run a command on startup, which will ensure that the Hologram Nova is always ready to accept an inbound SSH connection. First go ahead and run:
```sh
sudo nano /etc/rc.local
```
Scroll down and add the following line right above exit 0:
```sh
sudo hologram modem connect
```
Yours should look like this:
![alt text](https://i.imgur.com/crbMZkF.png)

Now go ahead and hit **Control + X** and press **Y** when it prompts you to save or discard changes.

Now might also be a good time to reboot the Pi for good measure (it will also allow us to test out our new 3G SSH capabilities too!). Go ahead and run:
```sh
sudo reboot
```

### SSH into the P-BON over 3G:
Now here comes the exciting part. You finally get to see some real progress from your work thus far! Hologram has written a really good tutorial on how to get Spacebridge working. First you want to enable tunneling on your Nova [as seen here in their Spacebridge tutorial](https://hologram.io/docs/guide/cloud/spacebridge-tunnel/#spacebridge-instructions). Next, you can either use their Spacebridge client, or you can do it without. I would HIGHLY suggest doing it without the client. In order to do that, [follow this part of the tutorial](https://hologram.io/docs/guide/cloud/spacebridge-tunnel/#tunneling-without-the-spacebridge-client) and come right back after you upload the keys.

You’re back, great! The reason I asked you to come back rather than finishing their tutorial is for some clarification. First, you need to get your Link ID number. This can be found by going to your device in the Hologram Dashboard, and is located in the top right of the device page. Next you want to fill it in into the following command, replacing `<link#>` with your actual link number:
```sh
ssh -i ~/.hologram/spacebridge.key htunnel@tunnel.hologram.io -p 999 -L 5000:link<link#>:22 -N
```
This command creates the tunnel for you to SSH through Spacebridge into the P-BON. In order to SSH into the P-BON, you need to SSH to localhost on port 5000 like so:
```sh
ssh pi@localhost -p 5000
```
*You may have changed your user on the P-BON, go ahead and replace pi to whatever the user you are trying to SSH into.*

It will stall for a few seconds, and eventually connect. At the time, the SSH connection is quite slow, and there is a significant delay between hitting a key and seeing it pop up in the terminal. But this will get much better as development continues.

### CONGRATS!
At this point we have completed getting your linux box connected to 3G. You are now able to deploy a full linux machine to a remote network, and connect to it through the 3G connection. If you want to see how to make it THE ULTIMATE 3G ATTACK PLATFORM, keep follow the tutorial!

### Install P4wnP1 onto the P-BON:
Now we are going to slap on the extremely powerful attack platform, [P4wnP1 by mame82](https://github.com/mame82/P4wnP1). To do this, we need to run a few commands on the P-BON:
```sh
sudo apt-get -y install git
cd /home/pi
git clone --recursive https://github.com/mame82/P4wnP1
cd P4wnP1
./install.sh
```
Now after you have restarted, you should be able to see the WiFi network it creates. In order to keep everything stealthy, we probably want to remove that part. Go ahead and run:
```sh
sudo nano /boot/config.txt
```
Then we want to add a line to disable the wifi chip. To do so, add the following to the bottom of the file:
```sh
dtoverlay=pi3-disable-wifi
```
That will go ahead and disable the wifi chip. If you did it correctly, the bottom of your config.txt file should look like this:
![alt text](https://i.imgur.com/oKod4GO.png)

Now go ahead and hit **Control + X** and press **Y** when it prompts you to save or discard changes like before.

### Solder up that USB socket and stuff away the Nova… Or not...:
Personally, I ended up just using the [Adafruit Zero4U Hat](https://www.adafruit.com/product/3298), giving me extra USB ports to add other devices.

My suggestion? Do this instead: Get these flexible MicroUSB to USB adapters, and use it to bend the cable easily back into whatever case you are using. I saves time, effort, and doesn’t risk destroying your Pi like I did! If you are using a full size Pi, or something else with normal USB ports, I would suggest using this

*But if you really want to make something super custom...*
We are going to want to solder some wires up to the gold pads on the bottom of the Pi, and onto the pins of a female USB port. I originally wrote a tutorial, but then I remembered [this video by N-O-D-E](https://n-o-d-e.net/dongle3.html) and found it to be much better, but the middle two pads are going to be switched for a female connector. Plus it shows how you could make your own case if you were inclined to do so.

**BE VERY CAREFUL DOING THIS!** I destroyed the data for the usb port somehow in the process of this on my RPi Zero W! That's the main reason I am not writing a super detailed tutorial for custom USB soldering, I really don't suggest it. It should only be used for super professional applications, and if that is the case, a professional most likely isn't going to need me to tell them how to do it.

I would also suggest making sure those wires won’t make bad connections in any way by adding a few dabs of hot glue to the pads, as well as a glob around all the connections to the solder points, and maybe a final layer of electrical tape for protection.

Now one last thing: check the functionality to make sure everything works before deploying!


### CONGRATS AGAIN!
You have now installed the P4wnP1 suite onto your P-BON! Now, just make sure you have a Micro-USB cable to plug into someone’s USB port, and make sure the P-BON gets at least 1.5A power input into the power Micro-USB port (The one closest to the edge, marked PWR). 

One important thing to note about doing the P4wnP1 version: Depending on the payload (other than the network only payload), it can kill useage of the Nova to the USB port because it turns the USB port into a HID interface (going away from the Pi). This means that you can probably only set it once before you can no longer access it from SSH.

**The Solution?** 2 RPis! You can have one set as a dedicated cellular SSH device, using it to connect to the WiFi network that the other RPi (running P4wnP1) creates. This works hilariously well! Just don't disable the WiFi on the RPis, and make the P4wnP1 RPi a hidden network (P4wnP1 has support for that), because otherwise that would ruin the stealth!

Making this as compact as possible is up to your imagination and your tinkering skills. This whole setup can fit into some quite small cases, however there is a balance between ease of use/reliability and form factor. Just be careful not to destroy anything!

### Next time...
I plan to build something significantly more complicated involving the Nova. This tool is amazingly easy to use, and super powerful at the same time.

I have always had fun messing around with pentesting and drones. One of my favorites, a flying WiFi/Bluetooth/Wireless Mouse hacking drone, is impressive to both technicals and non-technicals, fun to play with, and a little scary. One gripe I have had with my current solution is the fact that my drone can fly miles away, but the connection to my RPi (or what ever mini computer I have taped to the drone) is restricted based on the WiFi card's range.

In order to fix this, so I can SSH into the computer from a much farther distance, we slap a Hologram Nova on it! From my tests, this has worked hilariously well! The best part is that the effective range of attacks is no longer restricted by the computer, but the sight-line/range of the drone! Maybe I just need to use a drone that can be controlled over cellular too... hmmm... Can't wait to make a detailed tutorial on how I got these wireless attacks flying. Hopefully you guys want to come back and see how I've done that when it's complete!
