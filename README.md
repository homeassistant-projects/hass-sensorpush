# SensorPush Integration for Home Assistant

[🥈](https://www.home-assistant.io/docs/quality_scale/)
![beta_badge](https://img.shields.io/badge/maturity-Beta-yellow.png)
![release_badge](https://img.shields.io/github/v/release/rsnodgrass/hass-sensorpush.svg)
![release_date](https://img.shields.io/github/release-date/rsnodgrass/hass-sensorpush.svg)
[![hacs_badge](https://img.shields.io/badge/HACS-Default-orange.svg)](https://github.com/hacs/integration)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

Home Assistant integration for wireless SensorPush temperature and humidity/hygrometer sensors.

## NATIVE HOME ASSISTANT SUPPORT

As of the Home Assistant 2022.08 release [SensorPush](https://rc.home-assistant.io/integrations/sensorpush) sensors are natively supported WITHOUT require a gateway!

## Hardware Supported

| Model                             | Temp | Humidity | Pressure | Dewpoint | VPD |
| --------------------------------- |:----:|:--------:|:--------:|:--------:|:---:|
| [HT.w](https://amzn.to/3kHq02j)   |   X  |     X    |          |     X    |  X  |
| [HTP.xw](https://amzn.to/2MH4gXx) |   X  |     X    |    X     |     X    |  X  |
| [HT1](https://amzn.to/3b9GWLB)    |   X  |     X    |          |     X    |  X  |

1. For constant updates of sensor data without opening the iOS or Android app to synchronize data, the [SensorPush G1 WiFi Gateway](https://amzn.to/30b4ycg) is required to continually stream data from the sensors to SensorPush's cloud service However, SensorPush sensors can also synchronize historical data over Bluetooth when nearby an iOS or Android device with the SensorPush app).

2. If you register a new physical sensor with SensorPush, you must restart Home Assistant to discover the new device(s).

## Support

If you have trouble with installation and configuration, visit the [SensorPush Home Assistant community discussion](https://community.home-assistant.io/t/sensorpush-humidity-and-temperature-sensors/105711).

This integration was developed to cover use cases for my home integration and released as a contribution to the community. Implementing new features beyond what exists is the responsibility of the community to contribute.

## Installation

### Step 1: Install Custom Components

Make sure you have installed [Home Assistant Community Store (HACS)](https://github.com/custom-components/hacs), then add the "Integration" repository: *rsnodgrass/hass-sensorpush*.

#### Versions

The 'master' branch of this custom component is considered unstable, alpha quality, and not guaranteed to work.
Please make sure to use one of the official release branches when installing using HACS, see [what has changed in each version](https://github.com/homeassistant-projects/hass-sensorpush/releases).

### Step 2: Enable API Access

If you've never accessed the [SensorPush Gateway account dashboard](https://beta.sensorpush.com) you must sign in once to agree to Sensorpush terms before this integration can access data in your SensorPush account. (If you've done this any time in the past, you may skip this step.) If you see errors in your log stating `[pysensorpush] Could not authenticate to SensorPush service with <your_email> and password`, and you've checked to ensure your credentials are correct, you probably need to do this.

### Step 3: Configure SensorPush

Example configuration.yaml entry:

```yaml
sensorpush:
  username: your@email.com
  password: your_password

sensor:
  - platform: sensorpush
```

#### Lovelace

![Lovelace Example](https://github.com/homeassistant-projects/hass-sensorpush/blob/master/docs/sensorpush-entities.png?raw=true)

```yaml
entities:
  - entity: sensor.warehouse_humidity
  - entity: sensor.warehouse_temperature
show_header_toggle: false
title: SensorPush
type: entities
```

Lovelace gauge example:

```yaml
entity: sensor.warehouse_humidity
max: 100
min: 0
name: Office
severity:
  green: 45
  red: 15
  yellow: 25
theme: Backend-selected
type: gauge
```

More complex example using mini-graph-card and color thresholds:

![Lovelace Example](https://github.com/homeassistant-projects/hass-sensorpush/blob/master/docs/sensorpush-graph.png?raw=true)

```yaml
cards:
  - color_thresholds:
      - color: '#00ff00'
        value: 0
      - color: '#abf645'
        value: 30
      - color: '#FFD500'
        value: 50
      - color: '#ff0000'
        value: 60
    decimals: 0
    entities:
      - entity: sensor.warehouse_humidity
        name: Humidity
    font_size: 75
    hours_to_show: 12
    line_color: blue
    line_width: 8
    points_per_hour: 2
    show:
      fill: true
      icon: false
    type: 'custom:mini-graph-card'
  - color_thresholds:
      - color: '#abf645'
        value: 0
    decimals: 0
    entities:
      - entity: sensor.warehouse_temperature
        name: Temperature
    font_size: 75
    hours_to_show: 12
    line_color: var(--accent-color)
    line_width: 8
    points_per_hour: 2
    show:
      icon: false
    type: 'custom:mini-graph-card'
type: horizontal-stack
```

#### Tracking Battery

The battery level of sensors are attributes on each sensor, a separate sensor is not provided. However, if you wish to track battery levels, you can add a template sensor.

```yaml
sensor:
  - platform: template
    sensors:
      fridge_sensor_battery_voltage:
        friendly_name: 'Fridge SensorPush battery voltage'
        unit_of_measurement: 'V'
        value_template: '{{ state_attr("sensor.fridge_humidity", "battery_voltage") }}'
```

## See Also

* [Community support for Home Assistant SensorPush integration](https://community.home-assistant.io/t/sensorpush-humidity-and-temperature-sensors/105711)
* [pysensorpush](https://github.com/homeassistant-projects/pysensorpush) - Python interface to SensorPush cloud API
* [SensorPush](https://sensorpush.com) (official product page)
* [ReviewGeek's review of SensorPush](https://www.reviewgeek.com/3291/sensor-push-review-the-best-smart-hygrometer-and-thermometer-around/)

## Out of Scope

No plans to implement the following at this time. However, community contributions to add these features would be greatly appreciated!

- [applying calibration adjustments to individual sensors made within the SensorPush app](https://github.com/homeassistant-projects/hass-sensorpush/issues/18)
- poll data directly from sensors via Bluetooth (no cloud dependency required)
- supporting multiple SensorPush accounts within a single Home Assistance instance

#### Alternative Devices

The following hardware is not supported. These are just recorded here as these devices share the same internal design; were tested and approved on the same day; but likely require custom firmware flash to be able to communicate with SensorPush.

- [Mitsubishi Kumo Cloud temp/humidity sensor (PAC-USWHS003-TH-1)](https://www.ecomfort.com/Mitsubishi-PAC-USWHS003-TH-1/p81573.html?gclid=CjwKCAiA6vXwBRBKEiwAYE7iSxgq2RjFPeO1yAODQGvRlAAGtobvCq7w2Ay8R7yU9WY4CbK3jVnBxhoCjZ8QAvD_BwE)
- Oasis OH-31 HT Tracker (FCC Grantee [2AL92](https://fccid.io/2AL92-OH31/Test-Report/Test-Report-3428874), ID: 2AL92-OH31) like the SensorPush (FCC Grantee 2AL9W and [2AL9X HT1](https://fccid.io/2AL9X-HT1/Test-Report/Test-Report-3433404))
- [iBeTag Beacon IB004NPLUSSHT](https://fccid.io/2AB4P-IB004NPLUSSHT/External-Photos/External-photos-3446863) (FCC Grantee [2AB4P](https://fccid.io/2AB4P))
- [Jaalee Beacon IB004NPLUSSHT](https://fccid.io/2ABRO-IB004NPLUSSHT/Test-Report/Test-Report-3431944) (FCC Grantee 2ABRO)
- [Saalee iB004N-Plus-SHT](https://www.dhgate.com/product/wireless-digital-bluetooth-sensor-beacon/451751881.html?skuid=568611302727536642)
- [AnkhMaway iB004N-Plus-SHT LT](https://ankhmaway.en.alibaba.com/product/60602605562-806002398/Ble_Beacon_With_Temperature_and_Humidity_Sensor_Bluetooth_Programmable_iBeacon.html) / (https://www.beaconzone.co.uk/iB004NPLUSLight)
- [BLW Eddystone iBeacon](https://www.alibaba.com/product-detail/BLE-Eddystone-iBeacon-Temperature-And-Humidity_60611834273.html?spm=a2700.details.maylikeexp.2.12f71911uMO9SV)
- [iBeTag AKMW-iB004N-5](https://www.globalsources.com/si/AS/Shenzhen-AnkhMaway/6008840431707/pdtl/Apple-Certified-iBeacon-NRF51822-Low-Energy-Blueto/1100456449.htm)
