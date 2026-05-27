# ESP32Display

**ESP32Display** is a DIY **ESP32 + Waveshare e-paper display** project for showing weather information and office smart-home data from **Home Assistant**.

The project uses an ESP32-WROOM-32 development board, a Waveshare 4.2-inch 400×300 e-paper display, ESPHome, and Home Assistant sensors to create a low-power dashboard for an office environment.

This build was mostly inspired by the excellent projects shared in the Home Assistant community thread below:

[Home Assistant Community — E-paper display thread](https://community.home-assistant.io/t/e-paper-display/)

---

## Project photos

<p float="left">
  <img src="images/IMG_0275.JPG" width="45%" alt="ESP32Display project photo 1" />
  <img src="images/IMG_0276.JPG" width="45%" alt="ESP32Display project photo 2" />
</p>

<p float="left">
  <img src="images/IMG_0280.JPG" width="45%" alt="ESP32Display project photo 3" />
  <img src="images/IMG_0304.JPG" width="45%" alt="ESP32Display project photo 4" />
</p>

<p float="left">
  <img src="images/IMG_0305.JPG" width="45%" alt="ESP32Display project photo 5" />
  <img src="images/IMG_0309.JPG" width="45%" alt="ESP32Display project photo 6" />
</p>

### Size comparison

<p float="left">
  <img src="images/IMG_0310.JPG" width="45%" alt="ESP32Display size comparison 1" />
  <img src="images/IMG_0314.JPG" width="45%" alt="ESP32Display size comparison 2" />
</p>

<p float="left">
  <img src="images/IMG_0316.JPG" width="45%" alt="ESP32Display size comparison 3" />
  <img src="images/IMG_0317.JPG" width="45%" alt="ESP32Display size comparison 4" />
</p>

---

## What it shows

The display is designed as an always-visible Home Assistant dashboard for office and weather data.

Depending on your Home Assistant entities and the ESPHome YAML version you use, it can show information such as:

- Current time and full date
- Turkish day name
- Sunrise and sunset times
- Moon phase icon
- Current weather condition icon
- Outside temperature, minimum/maximum forecast temperature, precipitation, wind speed, and pressure
- 3-day weather forecast with icons and values
- Office, kitchen, and outside temperature/humidity values
- Motion, door, and window sensor states with last-change times
- Air freshener and printer/toner percentage values
- Power consumption
- ESP32 uptime
- Download speed
- Wi-Fi signal indicator
- Optional mouse battery information in the newer YAML version

The layout is rendered directly in ESPHome using the Waveshare e-paper display component and custom fonts/icons.

---

## Hardware used

The original build uses:

- Waveshare 4.2-inch, 400×300 raw e-paper display
- Waveshare Universal e-Paper Raw Panel Driver HAT
- ESP32-WROOM-32 Lua development board
- 3D printed case  
  [Thingiverse model](https://www.thingiverse.com/thing:4772071)

---

## Software stack

- [Home Assistant](https://www.home-assistant.io/)
- [ESPHome](https://esphome.io/)
- Waveshare e-paper display support in ESPHome
- Home Assistant template sensors for date, forecast, uptime, and last-change information
- Custom fonts and Material Design Icons included in the repository

---

## Repository contents

```text
.
├── README.md
├── configuration.yaml
├── esp32display.yaml
├── esp32displaynewversion28112024.yaml
├── fonts/
│   ├── Google_Sans_Bold.ttf
│   ├── Google_Sans_Medium.ttf
│   └── materialdesignicons-webfont.ttf
├── images/
│   ├── IMG_0275.JPG
│   ├── IMG_0276.JPG
│   ├── IMG_0280.JPG
│   ├── IMG_0304.JPG
│   ├── IMG_0305.JPG
│   ├── IMG_0309.JPG
│   ├── IMG_0310.JPG
│   ├── IMG_0314.JPG
│   ├── IMG_0316.JPG
│   └── IMG_0317.JPG
└── secrets.yaml
```

### Main files

- `esp32display.yaml` — original ESPHome configuration for the ESP32 e-paper dashboard.
- `esp32displaynewversion28112024.yaml` — newer ESPHome configuration version with updated entities and ESPHome OTA syntax.
- `configuration.yaml` — Home Assistant template sensors used by the display, including date, forecast, sunrise/sunset, motion times, and uptime formatting.
- `fonts/` — font files used by the ESPHome display layout.
- `images/` — build and size-comparison photos.

---

## Display wiring / pin configuration

The ESPHome configuration defines the e-paper SPI and control pins through substitutions:

```yaml
substitutions:
  devicename: ESP32Display
  gpio_spi_clk_pin: GPIO25
  gpio_spi_mosi_pin: GPIO26
  gpio_cs_pin: GPIO32
  gpio_busy_pin: GPIO13
  gpio_reset_pin: GPIO14
  gpio_dc_pin: GPIO27
```

The SPI bus is configured as:

```yaml
spi:
  clk_pin: $gpio_spi_clk_pin
  mosi_pin: $gpio_spi_mosi_pin
  id: epaper_display
```

And the Waveshare display component uses:

```yaml
display:
  - platform: waveshare_epaper
    id: epaper
    cs_pin: $gpio_cs_pin
    busy_pin: $gpio_busy_pin
    reset_pin: $gpio_reset_pin
    dc_pin: $gpio_dc_pin
    model: 4.20in
    update_interval: 60s
```

---

## Home Assistant integration

Most data shown on the display comes from Home Assistant entities. The ESPHome YAML pulls those values using `homeassistant` sensors and text sensors.

Examples include:

- Weather entities and forecast template sensors
- Temperature and humidity sensors
- Motion, door, and window binary sensors
- Printer/toner and air freshener percentage sensors
- Speedtest/download sensor
- Power consumption sensor
- Sun, moon, and date/time template sensors

The included `configuration.yaml` contains the Home Assistant template sensors needed by the display, such as:

- `sensor.turkishdayname`
- `sensor.fulldate`
- `sensor.day_1_*`, `sensor.day_2_*`, `sensor.day_3_*`
- `sensor.sun_rising_template`
- `sensor.sun_setting_template`
- Last-change time sensors for motion, doors, and windows
- `sensor.esp32display_uptime_template`

---

## Customization notes

Before using this project in another Home Assistant setup, review and update the entity IDs in the YAML files.

You will likely need to customize:

- Wi-Fi secrets
- ESPHome OTA password
- Fallback hotspot password
- Timezone
- Weather entity
- Temperature/humidity sensor entity IDs
- Motion, door, and window entity IDs
- Printer, air freshener, power, speedtest, and other optional sensor entity IDs
- Display labels if you want to use different room names or another language
- Font paths if your ESPHome directory layout is different

The display code uses fonts from this repository:

```text
fonts/Google_Sans_Bold.ttf
fonts/Google_Sans_Medium.ttf
fonts/materialdesignicons-webfont.ttf
```

Make sure these files are available in your ESPHome project directory when compiling/flashing.

---

## Notes about the YAML versions

There are two ESPHome YAML files in this repository:

- `esp32display.yaml` — the earlier/original configuration.
- `esp32displaynewversion28112024.yaml` — a newer version dated 28.11.2024.

The newer version keeps the same general dashboard concept but updates some Home Assistant entity references and includes the newer ESPHome OTA platform syntax.

---

## Credits

- Inspired by the Home Assistant community e-paper display projects:  
  [https://community.home-assistant.io/t/e-paper-display/](https://community.home-assistant.io/t/e-paper-display/)
- 3D printed case model:  
  [https://www.thingiverse.com/thing:4772071](https://www.thingiverse.com/thing:4772071)
- Built with ESPHome, Home Assistant, ESP32, and Waveshare e-paper hardware.

---

## Disclaimer

This is a personal DIY project shared as a reference for similar Home Assistant e-paper dashboards. The configuration is specific to the original office setup, so entity IDs, secrets, sensors, fonts, and layout details will need to be adjusted for your own installation.
