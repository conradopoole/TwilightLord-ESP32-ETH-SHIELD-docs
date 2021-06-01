## Ethernet Shield (10/100Mbps) for ESP32 boards

Living in a condo building with heavily congested WiFi channels was causing issues when trying to control individually addressable LED strips over E1.31, especially notable when running audio reactive effects with high frame rates. The solution, a good old cable ethernet connection. To be able to my reuse all TwilightLord-ESP32 boards and the many D1 Mini32 ones that I have, I decided to design a shield to be able to stack it on top (or below).

Big shout-out to the developers of [WLED][1] and [LedFX][2] which sparked my interest in the world of addressable LEDs and audio reactive. Please consider donating to their projects if you are using them.

And if you are looking for a great Shield for your WLED ESP based projects, I highly recommend looking at the [WLED Wemos Shield][4] by [Serg74][3]. You can order his PCBs and assemble it by yourself or buy one from his [Tindie store][5]

[1]:https://github.com/Aircoookie/WLED 
[2]:https://github.com/LedFx/LedFx
[3]:https://github.com/srg74
[4]:https://github.com/srg74/WLED-wemos-shield
[5]:https://www.tindie.com/stores/serg74/

[<img src="https://github.com/srg74/WLED-wemos-shield/raw/master/resources/Images/wiki/board-powered.jpg" width="250"><br/>][4][<img src="https://raw.githubusercontent.com/Aircoookie/WLED/master/images/wled_logo_akemi.png" height="30">][1][<img src="https://camo.githubusercontent.com/40ff4643445fb91a250438a7d3460ce9e225b1638a43694bb0d42f02d9e3d260/68747470733a2f2f692e696d6775722e636f6d2f534657666846722e706e67" height="30"><br/>][2]


### What is it?
TwilightLord-ESP32 Ethernet Shield is a small form factor board that adds 10/100Mbps Ethernet to your ESP32 projects. It can be used with any ESP32 based project so as long the pinout described below is free in your project, yes running Ethernet on an ESP32 requires a lot of pins. It also works as a Shield to a D1 Mini32 (like MH-ET LIVE MiniKit for ESP32) or compatible board like my TwilightLord-ESP32 8MB and 16MB boards.

<img src="TwilightLord-ESP32-ETH_SHIELD-v1.0r1-004.png" width="300"><br/>
*Shield on a MH-ET LIVE MiniKit for ESP32 board. NOTE: Align the modules so that the first pins for every column starting from the USB side are skipped.*

<br/><img src="TwilightLord-ESP32-ETH_SHIELD-v1.0r1-005.png" width="300"><br/>
*Shield on a TwilightLord-ESP32 board*

### Pinout

Board is powered with 5v to the 5v0 pin and it has an onboard LDO 3v3 regulator to power the LAN7820 IC.

Bottom View (component side) RJ45 at the bottom

|      |      |      |      |             |      |      |      |      |
| :--: | :--: | ---- | ---- | ----------- | ---- | ---- | :--: | :--: |
| GND  | --   |      |      |             |      |      | --   | GND  |
| IO27 | --   |      |      |             |      |      | --   | GND* |
| IO25 | IO22 |      |      |             |      |      | IO26 | --   |
| --   | IO21 |      |      |             |      |      | IO18 | --   |
| --   | IO17 |      |      |             |      |      | IO19 | --   |
| --   | --   |      |      |             |      |      | IO23 | --   |
| --   | GND  |      |      |             |      |      | IO5  | --   |
| --   | 5v0  |      |      |             |      |      | --   | --   |
| --   | --   |      |      |             |      |      | --   | --   |


__--__ NC (not connected)

-----------------------------------------------------------------------


Top View (over RJ45) RJ45 at the bottom

|      |      |      |      |             |      |      |      |      |
| :--: | :--: | ---- | ---- | ----------- | ---- | ---- | :--: | :--: |
| GND  | --   |      |      |             |      |      | --   | GND  |
| GND* | --   |      |      |             |      |      | --   | IO27 |
| --   | IO26 |      |      |             |      |      | IO22 | IO25 |
| --   | IO18 |      |      |             |      |      | IO21 | --   |
| --   | IO19 |      |      |             |      |      | IO17 | --   |
| --   | IO23 |      |      |             |      |      | --   | --   |
| --   | IO5  |      |      |             |      |      | GND  | --   |
| --   | --   |      |      |             |      |      | 5v0  | --   |
| --   | --   |      |      |             |      |      | --   | --   |

__--__ NC (not connected)

Most of the pins are fixed by the ESP32 Ethernet implementation and cannot be changed. Bear in mind the pins on the table below will not be available to your ESP32 projects if the Ethernet is enabled. 

|   GPIO |   USED BY   | COMMENTS   |     
| :----: | :--------- | :-------- |
| GPIO5  | Power on/off| Active High signal to power on the LAN7820 IC |
| GPIO17 | RMII Clock  | 50 MHz Clock sourced from ESP32 APLL          |
| GPIO18 | PHY MDIO    |                                               |
| GPIO19 | ETH TXD\[0] |                                               |
| GPIO21 | ETH TX_EN   |                                               |
| GPIO22 | ETH TXD\[1] |                                               |
| GPIO23 | PHY MDC     |                                               |
| GPIO25 | ETH RXD\[0] |                                               |
| GPIO26 | ETH RXD\[1] |                                               |
| GPIO27 | ETH CRS_DV  |                                               |

When configuring your ESP-IDF or Arduino projects please take the following into account:

- Set the type to LAN8720
- RMII clock is sourced by the ESP32 so set it to ETH_CLOCK_GPIO17_OUT
- The LAN IC is powered off by default, it needs an enable (HIGH) signal on GPIO5, then the board will supply power to the LAN IC.
- The Physical Address (PHY Address) for the IC is 0.

If you are using ArduninoEspressif32 ETH library below are the values that must be used when configuring it:

```c
ETH.begin(
        (uint8_t)      phy_addr,       # 0
        (int)             power,       # 5
        (int)               mdc,       # 23
        (int)              mdio,       # 18
        (eth_phy_type_t)   type,       # ETH_PHY_LAN8720
        (eth_clock_mode_t) clock_mode  # ETH_CLOCK_GPIO17_OUT
        );
```
### Board Pictures

<img src="TwilightLord-ESP32-ETH_SHIELD-v1.0r1-001.png" width="300"><br/>
<img src="TwilightLord-ESP32-ETH_SHIELD-v1.0r1-002.png" width="300"><br/>
<img src="TwilightLord-ESP32-ETH_SHIELD-v1.0r1-003.png" width="300"><br/>