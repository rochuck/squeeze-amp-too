# SqueezeAmpToo: an all-in-one audio sub-system

The SqueezeAmpToo is designed by Dave Viberg and Chuck Rohs and was completely inspired by the [SqueezeAMP project](https://github.com/philippe44/SqueezeAMP) and the [SqueezeESP project](https://github.com/philippe44/squeezelite-esp32).

## HiFiBerry Prototype
This design was prototyped using the squeezelite-esp32 code on a WROVER board that was fly-wired to clone of the HiFiBerry AMP+. This audio board uses the TAS5713, and as such an appropriate driver needed to be written to use the squeezelite-esp32 code. That is now all ancient history but the picture and development is documented here for posterity (covered in CNC router dust):

  ![alt text](./images/IMG_4204.png "HifiBerry with SqueezeESP")

For anyone else wanting to replicate this configuration, The WROVER is hooked up to the HiFiBerry as follows:

|WROVER Pin#|HiFiBerry 40pin connector number|Function|
|----|----|---|
|8|40|I2S-SD|
|9|12|I2S-CLK|
|10|35|I2S-LRCLK|
|11|5|I2C-SCL|
|12|3|I2C-SDA|
|1|6|GND|

Depending on your dev board, you may be able to pull power from the pi amp, but I would recommend separate power supplies, and just tie the grounds together.

As of this writing [sle118's repo](https://github.com/sle118/squeezelite-esp32/tree/c78c66faf57f0aea6eb86f7e74e01c9b28b824be) contains the TAS5713 driver I developed.
To test this out I used the sdkconfig "SqueezeAmp8MBFlash-sdkconfig.defaults". 
The code [here](https://github.com/sle118/squeezelite-esp32/blob/c78c66faf57f0aea6eb86f7e74e01c9b28b824be/components/squeezelite/output_i2s.c#L192)
was modified to replace "CONFIG_DAC_CONFIG" with "NULL"
so that the dac_config NVS parameter would not be overridden.

The dac_config was set to:
  - model=TAS5713,bck=33,ws=25,do=32,sda=27,scl=26

Also note that the partition map needed to be changed so that the OTA application resides at 0x160000.

## Squeeze Amp Too V1 Prototype
Since then, the V1 squeeze-amp-too has been built, and while there are a few errors, it performs remarkably well. The (modified for mono) V1 board is pictured here:

 ![alt text](./images/IMG_2459.png "squeeze-amp-too v1")

It includes the following:

- Espressif's WROVER board based on ESP32 WiFi/BT chipset (built-in antenna)
- WiFi / BT connectivity
- TI's TAS575x stereo class-D amplifier for up to 2x20W
- 1"^2 heatsink to handle the TAS and WROVER heat
- Built-in battery charger 2 Li-ion cells in series and automatic switch between battery and external power
- Direct speaker output
- Jack 3.5mm for analogue line-out with insertion detection
- SDPIF optical output
- Connectors for GPIO
- Onboard CP2102N for programming, with rx/tx leds. (CP2102 must be programmed with silabs software for the leds to work, i.e. chaning the gpios for alternate functionality)
- Charge LED 
- Two general purpose LEDs (red/green)

Connectors:
- J1 - Power Jack rated for 5A (PJ-047AH)
- J2 - Headphone Jack
- J3 - USB Micro B (for initial programming/debug)
- J4, J9 - Battery connectors
- J10 - .15" header, alt power
- J5, J12 - .15" speaker connectors
- J6, J13 - 5 pin .1" headers, for various gpio.
- J7 - 4 pin .1" header for sh1105 OLED display
- J11 - 6 pin .1" header for JTAG

# What can it do

With the squeezelite-esp32 software, you can (as quoted from the SqueezeAMP)

- Stream from LMS and send audio to the build-in amplifier, the line-out jack, the spdif connector or another bluetooth speaker. You can also use an external I2S DAC if you connect it to the general purpose 5/8 pins connector and tweak the software. Synchronization works.
- Stream from a Bluetooth device and send audio to the same outputs, except of course for sending to another bluetooth speaker ... There is no guarantee of audio/video synchronization at this point
- Stream from an AirPlay1 device (iPhone, iTunes ...) to the same outputs, including to a bluetooth speaker. Synchronization works.
- Add your own buttons, rotary encoder and map/combine them to various functions (play, pause, volume, next ...
- Use IR control by adding a simple receiver compatible with 38kHz device (no pullup resistor or capacitor needed)
- Add a display like this [one](https://www.buydisplay.com/i2c-blue-0-91-inch-oled-display-module-128x32-arduino-raspberry-pi) which can be directly connected to the 6-pins header. Currently, SSD1306, SSD1326/7 and SH1106 displays are supported.

# Tools, source and BOM

We're using Protel 99SE for schematics and routing. More to come... stay tuned
