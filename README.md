# ha-blueprints
Custom Home Assistant blueprints and scripts

## Contents

### Blueprints

- **[Hue Dimmer Switch](blueprints/controllers/hue-dimmer.yaml)** - Multi-press and hold automation for Philips Hue Dimmer Switch via Zigbee2MQTT

### Scripts

- **[Smart Brightness Control (v1.0.0)](scripts/smart_brightness_control.yaml)** - Adjust brightness only for lights that are currently on (minimum 1%), with optional transitions
- **[Smart Light Toggle (v1.0.0)](scripts/smart_light_toggle.yaml)** - Toggle only lights that were previously on, leaving off lights unchanged

See the [scripts README](scripts/README.md) for detailed usage instructions and examples.

## Installation

### Blueprints

Import blueprints directly in Home Assistant using the My Home Assistant links.

### Scripts

#### Smart Brightness Control

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FMPFGlaser%2Fha-blueprints%2Fblob%2Fmain%2Fscripts%2Fsmart_brightness_control.yaml)

#### Smart Light Toggle

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FMPFGlaser%2Fha-blueprints%2Fblob%2Fmain%2Fscripts%2Fsmart_light_toggle.yaml)

## Features

### Smart Light Control

The included scripts provide intelligent light control that:
- Only adjusts lights that are already on
- Prevents brightness from going to 0% (minimum 1%)
- Optional transition time for smooth brightness changes
- Works with areas, floors, devices, and individual entities
- Can be called from any automation, script, button, or dashboard

Perfect for use with dimmer switches, automations, buttons, or any scenario where you want precise control over which lights respond to commands.

## License

These blueprints and scripts are provided as-is for use with Home Assistant. Feel free to modify and share!
