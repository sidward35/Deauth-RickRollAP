# Deauth-RickRollAP

## Introduction ##

### What it is

Basically it’s a device which performs a [deauth attack](https://en.wikipedia.org/wiki/Wi-Fi_deauthentication_attack) and sends [beacon frames](https://en.wikipedia.org/wiki/Beacon_frame) to flood the list of available WiFi networks with "fake" Access Points. The fake networks will appear in the list of available networks, but will not work when the user tries to connect to them.

### How it works

The 802.11 Wi-Fi protocol contains a so called [deauthentication frame](https://mrncciew.com/2014/10/11/802-11-mgmt-deauth-disassociation-frames/). It is used to disconnect clients safely from a wireless
network.

Because these management packets are unencrypted, you just need the mac address of the Wi-Fi router and of the client device which you want to disconnect from the network. You don’t need to be in the network or know the password, it’s enough to be in its range.

### What an ESP8266 is

The [ESP8266](https://en.wikipedia.org/wiki/ESP8266) is a cheap micro controller with built-in Wi-Fi. It contains a powerful 160 MHz processor and it can be programmed using [Arduino](https://www.arduino.cc/en/Main/Software).  

You can buy these chips for under $2 from China!

### How to protect against it

With [802.11w-2009](https://en.wikipedia.org/wiki/IEEE_802.11w-2009) Wi-Fi got an update to encrypt management frames.
So make sure your router is up to date and has management frame protection enabled. But note that your client device needs to 
support it too, both ends need to have it enabled!

The only problem is that most devices don’t use it. I tested it with different Wi-Fi networks and devices, it worked every time! It seems that even newer devices which support frame protection don’t use it by default.

I made a [Deauth Detector](https://github.com/spacehuhn/DeauthDetector) using the same chip to indicate if such an attack is running against a nearby network. It doesn't protect you against it, but it can help you figure out if and when an attack is running.  

## Disclaimer

Use it only for testing purposes on your own devices!  
I don't take any responsibility for what you do with this program.  

Please check the legal regulations in your country before using it.  
**It is not a frequency jammer as claimed falsely by many people.** Its attack, how it works and how to protect against it is described above. It uses valid Wi-Fi frames described in the official 802.11 standard and doesn't block or disrupt any other communications or frequencies.  

Referring to this project as "jammer" is prohibited! Name the project by its correct name.

My intention with this project is to draw more attention to this issue.  
This attack shows how vulnerable the 802.11 Wi-Fi standard is and that it has to be fixed.  
**A solution is already there, why don't we use it?**  

## Supported Devices

You can flash the code to every ESP8266. Depending on the module or development board, there might be 
differences in the stability and performance.

**Officially supported devices:**
- WiFi Deauther (Pocket WiFi)
	- [AliExpress](https://goo.gl/JAXhTg)
	- [tindie](https://goo.gl/hv2MTj)
- WiFi Deauther OLED (Pocket WiFi)
	- [AliExpress](https://goo.gl/P30vNz)
	- [tindie](https://goo.gl/XsCoJ6)
	
Any other products that come with this projects preflashed are not approved and commit a copyright infringement!

## Installation

The only things you will need are a computer and an ESP8266 board.  

I recommend you to buy a USB breakout/developer board, because they have 4Mb flash and are very simple to use.
It doesn’t matter which board you use, as long as it has an ESP8266 on it.  

You have 2 choices here. Uploading the bin files is easier but not as good for debugging.  
**YOU ONLY NEED TO DO ONE OF THE INSTALLATION METHODS!**

### Uploading the bin files  

**Note:** the 512kb version won't have the full MAC vendor list.  
The NodeMCU and every other board use the ESP-12 which has 4mb flash on it.

**0** [Download](https://github.com/sidward35/Deauth-RickRollAP/raw/master/Deauth-RickRollAP.bin) the current release  

**1** Upload using the ESP8266 flash tool of your choice. I recommend using the [nodemcu-flasher](https://github.com/nodemcu/nodemcu-flasher). If this doesn't work you can also use the official [esptool](https://github.com/espressif/esptool) from espressif. On Linux machines, run ```python
esptool.py --port /dev/ttyUSB0  write_flash 0x00000 Deauth-RickRollAP.bin```

**That's all! :)**

Make sure you select the right com-port, the right upload size of your ESP8266 and the right bin file.  

If flashing the bin files with a flash tool is not working, try flashing the esp8266 with the Arduino IDE as shown below.

### Compiling the source with Arduino

**0** [Download the source code](https://raw.githubusercontent.com/sidward35/Deauth-RickRollAP/master/Deauth-RickRollAP.ino) of this project, placing it in a new folder with the same name as the file itself (e.g. if you're downloading the file as "Deauth-RickRollAP.ino" then place the file in a folder called "Deauth-RickRollAP").

**1** Install [Arduino](https://www.arduino.cc/en/Main/Software) and open it.

**2** Go to `File` > `Preferences`

**3** Add `http://arduino.esp8266.com/stable/package_esp8266com_index.json` to the Additional Boards Manager URLs. (source: https://github.com/esp8266/Arduino)

**4** Go to `Tools` > `Board` > `Boards Manager`

**5** Type in `esp8266`

**6** Select version `2.0.0` and click on `Install` (**must be version 2.0.0!**)

![screenshot of arduino, selecting the right version](https://raw.githubusercontent.com/spacehuhn/esp8266_deauther/master/screenshots/arduino_screenshot_1.JPG)

**7** Go to `File` > `Preferences`

**8** Open the folder path under `More preferences can be edited directly in the file`

![screenshot of arduino, opening folder path](https://raw.githubusercontent.com/spacehuhn/esp8266_deauther/master/screenshots/arduino_screenshot_2.JPG)

**9** Go to `packages` > `esp8266` > `hardware` > `esp8266` > `2.0.0` > `tools` > `sdk` > `include`

**10** Open `user_interface.h` with a text editor

**11** Scroll down and before `#endif` add following lines:

`typedef void (*freedom_outside_cb_t)(uint8 status);`  
`int wifi_register_send_pkt_freedom_cb(freedom_outside_cb_t cb);`  
`void wifi_unregister_send_pkt_freedom_cb(void);`  
`int wifi_send_pkt_freedom(uint8 *buf, int len, bool sys_seq);`  

![screenshot of notepad, copy paste the right code](https://raw.githubusercontent.com/spacehuhn/esp8266_deauther/master/screenshots/notepad_screenshot_1.JPG)

**don't forget to save!**

**12** Download [ESP8266Wi-Fi.cpp](https://raw.githubusercontent.com/spacehuhn/esp8266_deauther/master/sdk_fix/ESP8266WiFi.cpp) and [ESP8266Wi-Fi.h](https://raw.githubusercontent.com/spacehuhn/esp8266_deauther/master/sdk_fix/ESP8266WiFi.h)

**13** Paste these files here `packages` > `esp8266` > `hardware` > `esp8266` > `2.0.0` > `libraries` > `ESP8266WiFi` > `src`

**14** Open the source code file (downloaded in the first step) in Arduino

**15** Select your ESP8266 board at `Tools` > `Board` and the right port at `Tools` > `Port`  
If no port shows up you may have to reinstall the drivers.

**16** Depending on your board you may have to adjust the `Tools` > `Board` > `Flash Frequency` and the `Tools` > `Board` > `Flash Size`. In my case i had to use a `80MHz` Flash Frequency, and a `4M (1M SPIFFS)` Flash Size

**17** Upload!

**Your ESP8266 Deauther is now ready!**

**Referring to this project as "jammer" is prohibited! Name the project by its correct name.**  

## Sources and additional links

deauth attack: https://en.wikipedia.org/wiki/Wi-Fi_deauthentication_attack

deauth frame: https://mrncciew.com/2014/10/11/802-11-mgmt-deauth-disassociation-frames/

ESP8266: 
* https://en.wikipedia.org/wiki/ESP8266
* https://espressif.com/en/products/hardware/esp8266ex/overview

packet injection with ESP8266: 
* http://hackaday.com/2016/01/14/inject-packets-with-an-esp8266/
* http://bbs.espressif.com/viewtopic.php?f=7&t=1357&p=10205&hilit=Wi-Fi_pkt_freedom#p10205
* https://github.com/pulkin/esp8266-injection-example

802.11w-2009: https://en.wikipedia.org/wiki/IEEE_802.11w-2009

Wi-Fi_send_pkt_freedom function limitations: https://esp32.com/viewtopic.php?t=586
