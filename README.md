# Home-Assistant-ESPHome-Weather-Station
Nextion display with ESP8266 microcontroller (Wemos D1 mini) showing Home Assistant temperature sensors (temperature inside, outside and in the swiming pool) and weather forecast from the internet. But you might display any Hassio sensors, so if you wish to create own weather forecast, you can do that.

![Temperatures](/images/1.temperature.jpg "Page 1 - temperature sensors")  ![Forecast](/images/2.forecast.jpg "Page 2 - weather forecast")

[![Buy me a coffee](https://img.shields.io/static/v1.svg?label=Buy%20me%20a%20coffee&message=🥨&color=black&logo=buy%20me%20a%20coffee&logoColor=white&labelColor=6f4e37)](https://www.buymeacoffee.com/3nXx0bJDP)

## Getting Started

### Architecture

The Nextion display is connected via 4 wires: GND, +5V, RX, and TX. So to control the display is extremely easy - just connect these 4 wires to the corresponding ports in an Arduino board and you are set. In this project, I am using Wemos D1 mini. But in some other projects, I have the display connected to Sonoff 4ch or even Sonoff Basic (the only challenge with these is to find the pin with 5V, but this is not that hard).

### Design

The principle is very simple: I used Nextion Editor to create a layout for the display screen. There is a static image background with all graphics that do not change (including text, icons, frames, or color background). On top of the background, I created text or image objects for all the values that change. When the design is finished, you can upload the layout to the display from the NextionEditor using the serial connection (via USB FTDI board).

The program  is then very simple - it just sets the text value for the individual text objects. For example, for the inside temperature it does this:
```
it.set_component_text_printf("inside","%2.1f",id(temperature_inside).state);
```

ESPHome can read Home Assistant sensors through API, so the ESPHome sensor "teplota_uvnitr" is linked to Home Assistant sensor sensor.teplota_uvnitr this way:
```
sensor:
  - platform: homeassistant   # Inside temperature
    id: teperature_inside
    entity_id: sensor.temperature_inside
```

The weather forecast uses a picture - I have loaded set of icons through the NextionEditor, so all I do us to change the picture id this way:
```
it.send_command_printf("%s.pic=%.0f", "weather",id(today_icon).state);
```

It is using the [2.4 inch Nextion display](https://www.banggood.com/Nextion-NX3224T024-2_4-Inch-Man-machine-Interface-HMI-Screen-Kernel-In-English-p-1105052.html?utm_campaign=19381694_november&utm_content=2635&p=@K220219381694201802&cur_warehouse=CN).

### Additional features

There are two more "nice to have" features:
1. The display has 2 pages. I made it so it does change the page on each refresh (every 5 seconds). This is optional, you can make it either to show one page only. Or to flip the page on touching the screen (this can be easily done directly from the NextionEditor, without any programming).
2. On my Home Assistant, I also have a sensor showing whether somebody is home. When the alarm is on, nobody is home - so I am turning off the display light. And I only update the information when the display is on. This is entirely optional.
3. I also display the internet time, using ESPHome's time service.

### Source files

- **display_weather.yaml** - this is the main file - the actual ESPHome configuration
- **display.HMI** - this is the actual design of the display I use - the file can be edited via the NextionEditor (can be [downloaded](https://nextion.itead.cc/resources/download/nextion-editor/) from ITEAD pages)
- **weather.yaml** - Home Assistant configuration of the weather sensors I use. You can obviously use any sensors or any other weather service you like. Additionally, I have 3 temperature sensors - one of them is Z-Wave temperature sensor (internal). The other sensors are created through ESPHome. I also have luminosity sensor and rain sensor (but I do not show these, they are used in various automations). It is really up to you what information you find useful to show.
- **images (folder)** - I have included the source images for the Nextion display editor. These are not essential for the project, but if you want to create own design, you can start from there.
- **3D model front.stl, 3D model back.stl** - 3D model of the box the display is mounted in (Wemos D1 mini fits in the back).

## Author

* **Václav Chaloupka** - *Initial work* - [bruxy70](https://github.com/bruxy70)
