# fhs-balancing

Temperature sensors using [esphome](https://esphome.io/) for balancing an underfloor heating system.

Basic idea is taken from [energiesparhaus.at forum](https://www.energiesparhaus.at/forum-hydraulischer-abgleich-grundlagen/39756_1) (German only). Since I don't have a documentation of heating circuit lengths and the printing on the heating tube is gone, I had to use the return temperature method:
- measure the return temperature of every heating circuit under typical heating conditions
- compare the temperatures and adjust the flow using the taco-setters to level the return temperatures out
  - circuits with coolest return temperature are opened completely (= max flow)
  - the flow of circuits with higher return temperature get limited (a bit)
- rinse and repeat

## Hardware

- 2x [ESP32-S3 Board](https://docs.espressif.com/projects/esp-dev-kits/en/latest/esp32s3/esp32-s3-devkitc-1/user_guide.html#getting-started)
- 8x [DS18B20](https://datasheets.maximintegrated.com/en/ds/DS18B20.pdf) temperature sensors with 1-wire bus
- breadboards, wires, USB power supplies

## Software

Simple esphome configuration that publishes the sensor data as Prometheus metrics. See `fbh.yaml`.

Initial upload via JTAG USB:

```
esphome compile fbh.yaml

# press boot btn, then rst to enter firmware download mode
esphome upload fbh.yaml
```

Following updates can be uploaded using Wifi.

## Sensor Calibration

The DS18B20 sensors scatter quite a bit and need a calibration. I used a water bath with temperatures of ~15°C and ~30°C for the calibration. Lacking an exact thermometer, I simply used the average reading of all 8 sensors as reference value.

The calibration can be directly entered using the [calibrate-linear filter](https://esphome.io/components/sensor/#calibrate-linear).

## Prometheus

`fbh-scrape-config.yaml` contains an example scrape configuration for the [Prometheus Operator](https://prometheus-operator.dev/).

PromQL query to query the temperature sensors: `esphome_sensor_value{id=~"temp.*"}`

## Pictures

![fbh-temp-sensors](fbh-temp-sensors.jpg)
