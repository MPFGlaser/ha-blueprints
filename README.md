# ha-blueprints
Custom Home Assistant blueprints and scripts

## Contents

### Blueprints

- **[Hue Dimmer Switch](blueprints/controllers/hue-dimmer.yaml)** - Multi-press and hold automation for Philips Hue Dimmer Switch via Zigbee2MQTT

### Scripts

- **[Smart Brightness Control](scripts/smart_brightness_control.yaml)** - Adjust brightness only for lights that are currently on (minimum 1%)
- **[Smart Light Toggle](scripts/smart_light_toggle.yaml)** - Toggle only lights that were previously on, leaving off lights unchanged

See the [scripts README](scripts/README.md) for detailed usage instructions and integration examples with the Hue Dimmer blueprint.

## Installation

### Blueprints
Import blueprints directly in Home Assistant via the blueprint import URL or copy the YAML files to your `blueprints/automation` directory.

### Scripts
Copy the script files to your Home Assistant configuration and include them in your `configuration.yaml`:

```yaml
script: !include_dir_merge_named scripts/
```

Then restart Home Assistant.

## Features

### Smart Light Control
The included scripts provide intelligent light control that:
- Only adjusts lights that are already on
- Prevents brightness from going to 0% (minimum 1%)
- Works with areas, floors, devices, and individual entities
- Integrates seamlessly with dimmer switch blueprints

Perfect for use with the Hue Dimmer blueprint or any automation where you want more control over which lights respond to commands.

## License

These blueprints and scripts are provided as-is for use with Home Assistant. Feel free to modify and share!
