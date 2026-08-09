# Home Assistant AC External Sensor Proxy

Blueprint for Home Assistant that controls an air conditioner from an external room-temperature sensor while keeping the physical climate entity in `cool`.

## Features

- External room temperature sensor as the main control reference
- Configurable target temperature and hysteresis
- Overdrive when the AC reports `hvac_action: idle` while the room still needs cooling
- Comfort-zone logic that raises the physical setpoint to encourage `idle`
- Separate colder-room behavior
- Schedule with overnight support
- Sleep mode (`Low` fan + `quiet` preset)
- Fan, vertical swing, and horizontal swing control
- Optional second safety sensor
- Safety cutoff and restart threshold
- Can also be used with only one temperature sensor

## One-click import

[![Open your Home Assistant instance and show the blueprint import dialog with the blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fraw.githubusercontent.com%2Fkrolkamil16-creator%2Fha-ac-external-sensor-proxy%2Frefs%2Fheads%2Fmain%2Fblueprints%2Fautomation%2Fac_external_sensor_proxy.yaml)

Manual blueprint URL:

`https://github.com/krolkamil16-creator/ha-ac-external-sensor-proxy/blob/main/blueprints/automation/ac_external_sensor_proxy.yaml`

## How it works

For a target of `23.0 °C` with `0.5 °C` hysteresis:

- Room `>= 23.5 °C`: cooling demand
- Room between `22.5 °C` and `23.5 °C`: comfort zone; the blueprint raises the physical AC target above the AC's own measured temperature to encourage `idle`
- Room `<= 22.5 °C`: stronger idle bias

If the AC reports `idle` while the external room sensor still shows cooling demand, Overdrive sets the physical target below the AC's own measured temperature.

## Example: bedroom with two sensors

Main room sensor:

`room_sensor: sensor.templazienka_temperature`

Safety sensor near the crib:

`safety_sensor: sensor.tempsypialnia_temperature`

Recommended starting values:

- Safety cutoff: `22.5 °C`
- Safety restart: `23.0 °C`
- Hysteresis: `0.5 °C`
- Overdrive offset: `1.0 °C`
- IDLE offset: `1.0 °C`
- Cold IDLE offset: `1.5 °C`
- Normal correction step: `0.5 °C`
- Maximum proxy offset: `4.0 °C`

## Example: second Panasonic with one sensor

Use the room sensor as the main sensor.

If you do not want second-sensor protection, keep the associated safety toggle OFF. The safety branch is then ignored.

## Repository files

- `blueprints/automation/ac_external_sensor_proxy.yaml` — reusable blueprint
- `packages/ac.example.yaml` — example Home Assistant package
- `examples/configuration.yaml` — minimal configuration using packages
- `install_ac_proxy.sh` — migration helper for Home Assistant OS / Terminal & SSH

## Important

Before enabling the blueprint, disable older automations that also change the same climate entity's HVAC mode, target temperature, fan mode, preset, or swing settings. Two independent controllers acting on the same AC will conflict.

Review all example entity IDs before using the package or installer on another Home Assistant installation.

## License

MIT
